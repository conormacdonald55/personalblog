---
title: "Getting Ollama GPU Acceleration on AMD Radeon 780M with Proxmox"
subtitle: "ROCm wouldn't cooperate. Vulkan did. Here's the step-by-step fix for Ryzen 7000-series iGPUs on Proxmox."
date: 2026-03-18
series: ["homelab"]
tags: ["homelab", "ollama", "AMD", "GPU", "Proxmox", "Vulkan", "LXC", "self-hosting", "AI"]
categories: ["The Homelab"]
draft: true
description: "ROCm kept failing on Proxmox's patched kernel. The fix was Vulkan — and it works beautifully. Full step-by-step guide for AMD Radeon 780M (RDNA3) on Proxmox."
---

## Why This Matters

Running a local language model on CPU is fine, right up until you're watching a progress bar tick along at five tokens per second and questioning all your life choices. Local LLMs are one of the better reasons to run a homelab in 2026, but they're only genuinely useful if the inference speed is fast enough to be practical. That means GPU acceleration, which is where things get interesting, and sometimes infuriating.

My setup for this project is the [MINISFORUM UM790 Pro](https://www.minisforum.com/products/minisforum-um790-pro): a compact, fanless-class mini PC running an AMD Ryzen 9 7940HS with 32GB RAM and an integrated AMD Radeon 780M GPU. The 780M is RDNA3 architecture, device ID gfx1103, and with BIOS configuration it can claim up to 16GB of unified memory as VRAM. On paper, that is a capable local inference engine. Getting it to actually work that way on Proxmox took considerably more effort than I expected.

This is the guide I wish I'd had at the start.

---

## The Problem: ROCm and the Proxmox Kernel

The standard approach for AMD GPU acceleration with Ollama is ROCm. It's AMD's compute platform, analogous to CUDA, and Ollama has solid support for it. My Proxmox node is running PVE kernel 6.17.2 on Debian Trixie, and ROCm simply would not work.

The root cause is structural. Proxmox ships a patched Debian kernel with custom virtualisation features baked in. AMD's ROCm stack relies on a kernel module called `amdkfd` (the Kernel Fusion Driver) to handle compute workloads. Without it loading, ROCm can see your GPU for display purposes but cannot use it for computation. The PVE kernel's patches create recurring API mismatches that prevent `amdkfd` from loading cleanly, and there are no confirmed reports (as of March 2026) of ROCm compute working on PVE kernels with RDNA3 hardware.

Here is what I tried before finding the actual solution:

| Attempt | Result | Root Cause |
|---|---|---|
| ROCm 6.1/6.2 via amdgpu-install | SHA1 signature rejection | Debian Trixie hardened SHA1 trust in Feb 2026 |
| ROCm 7.1 (noble/jammy amdgpu-install) | Installs, `rocminfo` shows gfx1103, `/dev/kfd` exists — but Ollama runs at 100% CPU | PVE kernel does not load `amdkfd` compute module |
| HSA_OVERRIDE_GFX_VERSION=11.0.3 | No effect | KFD module not loaded; override has nothing to override |
| DKMS amdgpu driver | DKMS added, `lsmod \| grep kfd` shows nothing | Conflict between PVE kernel patches and DKMS module |

That last column is doing a lot of work. Every attempt failed for the same underlying reason: the `amdkfd` module would not load against the PVE kernel. `rocminfo` could see the GPU because the display driver (`amdgpu`) loads fine. But display and compute are different drivers, and the compute side was dead every time.

---

## The Breakthrough: Vulkan

The fix came from a GitHub issue (#13677, January 2026) where someone running the same RDNA3 hardware in a Docker container found that Ollama's **Vulkan backend** worked perfectly, completely bypassing ROCm.

The key environment variable is:

```bash
OLLAMA_VULKAN=true
```

Why Vulkan works where ROCm doesn't comes down to what it depends on. ROCm needs `amdkfd` and the HSA stack. Vulkan uses Mesa's RADV driver, which ships as part of the standard Mesa graphics stack and only needs `/dev/dri/renderD128`. No KFD, no HSA, no compute modules. The RADV driver has excellent RDNA3 support because it's the same driver used for gaming on Linux, and it has been actively maintained for gfx1103 for years.

When Vulkan is working correctly, Ollama's log shows:

```
library=Vulkan name=Vulkan0 description="AMD Radeon 780M Graphics (RADV PHOENIX)"
```

That line, when you finally see it after days of debugging, is genuinely satisfying.

---

## Step-by-Step Guide

### Step 1: Verify Host GPU Device Nodes

On the Proxmox host, confirm the GPU is visible and AMD-owned:

```bash
ls -la /dev/dri/
# Should show: card0, renderD128

cat /sys/class/drm/card0/device/vendor
# Output: 0x1002  (AMD)
```

Note the major device number for the DRI devices — you'll need it for the cgroup config. Looking at `ls -la /dev/dri/` output, you'll see something like `crw-rw----+ 1 root video 226, 0` — that `226` is the major number.

### Step 2: Create a Privileged LXC Container

In the Proxmox web UI or via CLI, create a new container with these specs:

- **Template:** Ubuntu 22.04 or Debian 12 (Bookworm). Avoid Trixie in the container to sidestep the SHA1 issues.
- **Privileged:** Yes. Unprivileged containers have UID mapping issues with render devices that make GPU passthrough much harder.
- **RAM:** 8GB minimum
- **Storage:** 40GB or more (llama3.1:8b alone is ~5GB)
- **CPU:** 4 cores minimum

Privileged is the key choice here. It simplifies device access considerably and there are no strong security reasons to avoid it for a local homelab container that isn't internet-exposed.

### Step 3: Configure LXC for GPU Passthrough

On the Proxmox host, edit the container's config file:

```bash
nano /etc/pve/lxc/<CTID>.conf
```

Add the following lines:

```ini
# GPU device passthrough
lxc.cgroup2.devices.allow: c 226:* rwm
lxc.mount.entry: /dev/dri dev/dri none bind,optional,create=dir
lxc.mount.entry: /dev/dri/renderD128 dev/dri/renderD128 none bind,optional,create=file
```

The `c 226:* rwm` rule grants the container read/write/mknod access to all DRI character devices (major 226). The mount entries bind the host's `/dev/dri` directory and the specific render node into the container's filesystem. The `optional` flag means the container will still start even if the device doesn't exist, which avoids boot failures if something changes on the host.

### Step 4: Install Mesa Vulkan Drivers

Start and enter the container:

```bash
pct start <CTID>
pct enter <CTID>
```

Inside the container, install the Mesa Vulkan stack:

```bash
apt update && apt install -y \
  mesa-vulkan-drivers \
  libvulkan1 \
  vulkan-tools \
  libgl1-mesa-dri \
  libgles2-mesa
```

Verify that Vulkan can see the AMD GPU:

```bash
vulkaninfo --summary
```

You should see `AMD RADV PHOENIX` in the output with `gfx1103` noted as the GPU. If this works, the hard part is done.

### Step 5: Install Ollama with Vulkan Enabled

Run the standard Ollama install script:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Then configure the systemd service to use Vulkan:

```bash
mkdir -p /etc/systemd/system/ollama.service.d/

cat > /etc/systemd/system/ollama.service.d/override.conf << 'EOF'
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
Environment="OLLAMA_VULKAN=true"
Environment="OLLAMA_NUM_GPU=999"
EOF

systemctl daemon-reload
systemctl enable --now ollama
```

Setting `OLLAMA_HOST=0.0.0.0:11434` makes Ollama accessible from other machines on your network, which is useful if you want to serve models to other containers or devices. `OLLAMA_NUM_GPU=999` is a shorthand way of telling Ollama to use all available GPU layers rather than splitting inference between GPU and CPU.

Also add the ollama user to the relevant groups so it has permission to access the render device:

```bash
usermod -aG video ollama
usermod -aG render ollama
```

### Step 6: Verify GPU Is Being Used

Check the Ollama logs for the GPU detection line:

```bash
journalctl -u ollama -n 50 | grep -i "vulkan\|gpu\|inference"
```

The line you are looking for:

```
library=Vulkan name=Vulkan0 description="AMD Radeon 780M Graphics (RADV PHOENIX)"
```

Pull a model and run a quick inference test:

```bash
ollama pull phi3:mini
ollama run phi3:mini "Say hello"
```

While inference is running, you can watch GPU utilisation from the Proxmox host:

```bash
watch -n 1 'cat /sys/class/drm/card0/device/gpu_busy_percent'
```

If that number is climbing during inference, GPU acceleration is working.

---

## BIOS Tuning: Getting More VRAM

By default, the UM790 Pro BIOS allocates a relatively small amount of system RAM as dedicated VRAM for the iGPU. More VRAM means larger models can be loaded entirely onto the GPU, which is significantly faster than splitting across GPU and system RAM.

In the UM790 Pro BIOS, look for **UMA Frame Buffer Size** (sometimes labelled iGPU Memory). With 32GB of system RAM, setting this to 8GB or 16GB is safe. The 780M with 16GB allocated will comfortably fit llama3.1:8b at Q4 quantisation entirely in GPU memory.

The UM790 Pro BIOS is accessed at boot via the Delete key, and the UMA Frame Buffer setting is typically found under Advanced or AMD CBS settings depending on the firmware version.

---

## Performance Expectations

To set realistic expectations: Vulkan is not ROCm. ROCm uses HIP kernels that are specifically optimised for AMD compute workloads and would be faster for ML inference if it worked. Vulkan is a graphics API being used for compute, and while Mesa's RADV driver is excellent, it is not matching ROCm's optimised kernels.

That said, Vulkan is substantially faster than CPU-only inference:

| Backend | llama3.1:8b Q4 (approx.) |
|---|---|
| CPU only | ~5–8 tokens/sec |
| Vulkan (780M) | ~10–20 tokens/sec |
| ROCm (780M, if working) | ~25–40 tokens/sec (estimated) |

For heartbeat tasks, simple queries, and models like phi3:mini, Vulkan speed is more than adequate. For longer context or more demanding models, the gap versus ROCm becomes more noticeable. If a future Proxmox kernel update fixes the `amdkfd` loading issue, switching to ROCm is straightforward: remove `OLLAMA_VULKAN=true` from the service override and restart.

---

## Connecting to OpenClaw

With Ollama running in the LXC container at, say, `192.168.4.52:11434`, pointing OpenClaw at it is straightforward. The important detail is the `api` field: using `api: "ollama"` rather than the OpenAI-compatible mode preserves native tool calling, which matters for agent workflows.

Edit your OpenClaw config:

```json5
{
  models: {
    providers: {
      ollama: {
        apiKey: "ollama-local",
        baseUrl: "http://192.168.4.52:11434",  // no /v1 suffix
        api: "ollama",
      }
    }
  }
}
```

A useful split for a tiered local/cloud setup: use `ollama/phi3:mini` for heartbeats and simple tasks (free, fast, local), `ollama/llama3.1:8b` for moderate reasoning, and reserve the Anthropic API for genuinely complex work. Most routine agent activity doesn't need Claude, and routing it locally keeps the API bill down considerably.

---

## Wrapping Up

The short version: if you have a Ryzen 7000-series machine with a Radeon 780M running Proxmox, ROCm is probably not going to cooperate, and it's not worth the effort of forcing it. The Vulkan backend gets you real GPU acceleration with minimal fuss, using drivers that are already well-supported and well-maintained. Set `OLLAMA_VULKAN=true`, pass through `/dev/dri`, install Mesa, and you'll have a working local inference node in an afternoon.

The homelab is supposed to be fun. This approach keeps it that way.

---

## References

- [Ollama GitHub Issue #13677](https://github.com/ollama/ollama/issues/13677) — 780M Vulkan backend in container, January 2026
- [Ollama GitHub PR #6282](https://github.com/ollama/ollama/pull/6282) — AMD iGPU GTT memory allocation fix (kernel 6.9.9+)
- [Ollama GitHub Issue #5471](https://github.com/ollama/ollama/issues/5471) — 780M ROCm GTT+VRAM memory access (kernel 6.10+)
- [Ollama AMD Preview Announcement](https://ollama.com/blog/amd-preview) — Official supported GPU list
- [OpenClaw Ollama Provider Docs](https://docs.openclaw.ai/providers/ollama)
- [MINISFORUM UM790 Pro](https://www.minisforum.com/products/minisforum-um790-pro)

---

*Previous post: [What is a Metamaterial, and Why Should You Care? →](/posts/metamaterials-01-what-is-a-metamaterial/)*

{{< alert icon="circle-info" cardColor="#f1f5f9" iconColor="#94a3b8" textColor="#475569" >}}
**AI Assistance:** This post was researched and drafted with the assistance of Claude Sonnet 4.6 (Anthropic) via [OpenClaw](https://openclaw.ai). The research direction, editorial decisions, and code review are the author's own.
{{< /alert >}}
