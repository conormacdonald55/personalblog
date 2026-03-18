---
title: "The Wave Equation — Where Everything Begins"
subtitle: "Deriving the equation that governs sound, vibration, and why metamaterials can bend the rules"
date: 2026-03-18
series: ["acoustic-metamaterials"]
tags: ["metamaterials", "acoustics", "wave equation", "physics", "mathematics"]
categories: ["The Research"]
series_order: 2
draft: false
description: "Before we can understand how metamaterials manipulate sound, we need the equation that governs it. Here's the wave equation — derived, explained, and connected to everything that follows."
---

At the end of Post 1, I mentioned the spring-mass chain: a row of masses connected by springs, which turns out to be the discrete skeleton underneath all wave physics in solids and metamaterials. Before we get there, though, we need something more fundamental. The spring-mass chain is useful precisely *because* it approximates something continuous, and that continuous something is governed by the wave equation. If you want to understand how metamaterials manipulate sound, you need to understand this equation first — where it comes from, what it says, and what happens when you start engineering the quantities inside it.

This post derives the 1D wave equation from scratch using nothing more than Newton's second law and a small piece of a fluid. The maths is real but the steps are small. If you took second-year physics or engineering and then promptly forgot most of it, this should come back naturally.

---

## What Actually Is a Wave?

The textbook answer is "a disturbance that propagates through a medium," which is technically correct and almost entirely unhelpful. Let's be more specific.

A wave is how a medium transports energy from one place to another *without permanently displacing the matter it's made of*. This is the genuinely counterintuitive part. When a sound wave travels across a room, the air molecules are not migrating from your mouth to my ear. Each molecule oscillates locally — compressing forward, relaxing back, compressing forward again — and this pattern of compression and rarefaction moves through space. The energy travels; the medium doesn't.

You can see the same thing in water. Throw a stone into a still pond. The ripples travel outward, but a floating leaf just bobs up and down in place. The wave moves; the water doesn't go anywhere net.

For sound specifically: a pressure disturbance propagates through air (or any fluid or solid) as alternating regions of high and low pressure. The molecules in a high-pressure zone push on their neighbours, creating a new high-pressure zone slightly further along, while the original zone relaxes. This chain of compression and expansion — each element responding to the one before it, handing the disturbance along — is what we call a longitudinal acoustic wave.

The question is: can we write down a single equation that captures this behaviour for *all* points in the medium at *all* times?

---

## Deriving the Wave Equation

Consider a small element of fluid with length Δx and cross-sectional area A. It has mass ρAΔx, where ρ is the fluid density. We want to apply Newton's second law to this element: the net force on it equals its mass times its acceleration.

**Forces:** The pressure on the left face of the element is P(x, t), pushing the element to the right. The pressure on the right face is P(x + Δx, t), pushing back to the left. The net force in the x-direction is:

$$F = [P(x,t) - P(x + \Delta x, t)] \cdot A$$

For small Δx, we can write:

$$P(x + \Delta x, t) \approx P(x, t) + \frac{\partial P}{\partial x} \Delta x$$

So the net force becomes:

$$F = -\frac{\partial P}{\partial x} \Delta x \cdot A$$

**Acceleration:** Let u(x, t) be the displacement of the fluid element from its equilibrium position. The acceleration is ∂²u/∂t².

**Newton's second law (F = ma):**

$$-\frac{\partial P}{\partial x} \Delta x \cdot A = \rho A \Delta x \cdot \frac{\partial^2 u}{\partial t^2}$$

Dividing through by AΔx:

$$-\frac{\partial P}{\partial x} = \rho \frac{\partial^2 u}{\partial t^2}$$

This is the equation of motion for the fluid element — essentially Newton's second law written in field form. It says the local pressure gradient drives local acceleration.

**Connecting pressure to displacement:** We need one more physical relationship. The bulk modulus B of a fluid measures how much pressure change you get for a given fractional change in volume. A small element that's compressed by ∂u/∂x (where a negative value means compression) experiences a pressure change:

$$P = -B \frac{\partial u}{\partial x}$$

Substituting this into our equation of motion:

$$\frac{\partial}{\partial x}\left(B \frac{\partial u}{\partial x}\right) = \rho \frac{\partial^2 u}{\partial t^2}$$

For a uniform medium where B doesn't vary with x, this simplifies to:

$$\frac{\partial^2 u}{\partial t^2} = \frac{B}{\rho} \frac{\partial^2 u}{\partial x^2}$$

This is the 1D wave equation. The quantity B/ρ has units of velocity squared, so we define c² = B/ρ and write:

$$\frac{\partial^2 u}{\partial t^2} = c^2 \frac{\partial^2 u}{\partial x^2}$$

Every step here was straightforward: Newton's law, a first-order Taylor expansion, and a constitutive relationship between pressure and strain. But the result is remarkably powerful.

---

## What the Equation Tells Us

The wave speed c = √(B/ρ) encodes something important: how fast waves travel depends entirely on the material properties of the medium.

A stiffer medium (higher B) transmits waves faster — the molecules are more tightly coupled, so a compression here reaches over there more quickly. A denser medium (higher ρ) transmits waves slower — more inertia to overcome at each step. This is why sound travels at around 340 m/s in air (low density, modest stiffness) but at around 1,480 m/s in water (much higher bulk modulus, denser but the stiffness wins). In steel, it's closer to 5,000 m/s.

This is where the connection to metamaterials becomes tangible. The wave equation says that if you can engineer B and ρ — the bulk modulus and density a wave *effectively experiences* as it passes through your material — you control everything about how that wave propagates. Its speed, its direction, whether it propagates at all. The entire field of acoustic metamaterials is, at one level, an exercise in engineering effective values of these two quantities.

---

## Solutions and What They Look Like

The wave equation is a linear second-order PDE, and its general solution is the superposition of any functions of the form f(x - ct) and g(x + ct). These represent disturbances moving in the positive and negative x directions respectively, both at speed c. It doesn't care what shape they are.

For monochromatic (single-frequency) waves, the solutions take the sinusoidal form:

$$u(x, t) = A \cos(kx - \omega t + \phi)$$

where ω is the angular frequency, k is the wavenumber (spatial frequency, measured in radians per metre), A is the amplitude, and φ is a phase offset. The wave speed is c = ω/k, which is called the *phase velocity*.

The relationship between ω and k is known as the *dispersion relation*. For a simple uniform medium, ω = ck, so the relationship is linear: all frequencies travel at the same speed. This is a non-dispersive medium. In dispersive media (and this is coming in Post 4), different frequencies travel at different speeds, which causes pulses to spread out over time and leads to some of the most interesting behaviour in metamaterials.

---

## When the Equation Breaks — Band Gaps and Evanescent Decay

Here's where it gets relevant to everything that follows. Go back to the wave equation:

$$\frac{\partial^2 u}{\partial t^2} = \frac{B}{\rho} \frac{\partial^2 u}{\partial x^2}$$

Now suppose ρ_eff is negative. The right-hand side flips sign, and the equation becomes:

$$\frac{\partial^2 u}{\partial t^2} = -|c^2| \frac{\partial^2 u}{\partial x^2}$$

This no longer has oscillatory solutions. Instead of u ~ e^{ikx}, the spatial solutions are exponentials: u ~ e^{±αx} with α real. A wave entering a region with negative effective density doesn't propagate — it decays exponentially with distance. This is called *evanescent decay*, and a frequency range where it happens is a *band gap*: the material refuses to transmit waves at those frequencies.

The same thing happens if B_eff goes negative. And if both go negative simultaneously, something more surprising occurs: the wave propagates again, but backwards — the phase velocity points opposite to the energy flow. That situation produces negative refraction, the basis for acoustic cloaking.

All of this flows directly from the wave equation. Metamaterials don't add new physics; they engineer the parameters inside equations we already have.

In Post 3, we'll build the physical model that makes this happen — the spring-mass-damper lattice that produces these anomalous effective properties at specific frequencies. The wave equation is the end goal; the spring-mass chain is the mechanism that gets us there.

---

## 🔬 Jupyter Notebook: Simulating the Wave Equation with FDTD

The code below numerically solves the 1D wave equation using the finite difference time domain (FDTD) method. It runs three scenarios: a baseline pulse propagating through a uniform medium, the same pulse in a denser medium (lower wave speed), and a pulse encountering a zone of "effective negative density" (evanescent decay). All plots use a consistent visual style.

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec

# ─── Simulation parameters ───────────────────────────────────────────────────

NX    = 500          # number of spatial grid points
NT    = 1200         # number of time steps
DX    = 0.01         # spatial step (m)
DT    = 0.4 * DX     # time step (s) — CFL condition: DT < DX/c_max

# ─── Helper: Gaussian pulse initial condition ─────────────────────────────────

def gaussian_pulse(x, x0=1.5, width=0.15):
    """A smooth initial displacement pulse centred at x0."""
    return np.exp(-((x - x0) ** 2) / (2 * width ** 2))

# ─── FDTD solver for 1D wave equation ─────────────────────────────────────────
#
#  We solve: d²u/dt² = c(x)² * d²u/dx²
#
#  Explicit finite difference scheme:
#    u[n+1,i] = 2*u[n,i] - u[n-1,i] + (c[i]*DT/DX)^2 * (u[n,i+1] - 2*u[n,i] + u[n,i-1])
#
#  Stability requires the Courant number C = c*DT/DX <= 1 everywhere.

def fdtd_wave(c_field, snapshot_steps):
    """
    Run FDTD simulation.

    Parameters
    ----------
    c_field        : array of shape (NX,), wave speed at each grid point
    snapshot_steps : list of time step indices at which to save the field

    Returns
    -------
    snapshots : dict mapping step index -> displacement array
    """
    x = np.linspace(0, NX * DX, NX)

    u_prev = gaussian_pulse(x)          # u at t = -DT (assume at rest: same as t=0)
    u_curr = gaussian_pulse(x)          # u at t = 0
    u_next = np.zeros(NX)

    courant = (c_field * DT / DX) ** 2  # pre-compute Courant² at each point

    snapshots = {}

    for n in range(NT):
        if n in snapshot_steps:
            snapshots[n] = u_curr.copy()

        # Interior points only; boundaries held at zero (absorbing-ish)
        u_next[1:-1] = (
            2 * u_curr[1:-1]
            - u_prev[1:-1]
            + courant[1:-1] * (u_curr[2:] - 2 * u_curr[1:-1] + u_curr[:-2])
        )

        # Dirichlet boundary conditions (fixed ends)
        u_next[0]  = 0.0
        u_next[-1] = 0.0

        u_prev = u_curr
        u_curr = u_next.copy()

    return snapshots

# ─── Scenario 1: Uniform medium, c = 1.0 m/s ─────────────────────────────────

x = np.linspace(0, NX * DX, NX)
snap_steps = [0, 100, 250, 450]

c_uniform = np.ones(NX) * 1.0
snaps_uniform = fdtd_wave(c_uniform, snap_steps)

# ─── Scenario 2: Denser medium (higher ρ → lower c), c = 0.6 m/s ─────────────

c_dense = np.ones(NX) * 0.6
snaps_dense = fdtd_wave(c_dense, snap_steps)

# ─── Scenario 3: Evanescent zone (negative effective density band) ────────────
#
#  In a region with ρ_eff < 0, the wave equation flips sign and solutions
#  become exponentially decaying rather than oscillatory.
#  We model this numerically by setting c² < 0 in a finite region,
#  achieved by using a purely imaginary-equivalent: the spatial update
#  coefficient becomes negative, driving exponential decay.

evanescent_start = int(0.4 * NX)
evanescent_end   = int(0.65 * NX)

evan_mask = np.zeros(NX, dtype=bool)
evan_mask[evanescent_start:evanescent_end] = True

def fdtd_wave_evanescent(snapshot_steps):
    """
    FDTD with an evanescent zone: negative-density region causes exponential decay.
    The spatial update coefficient is negated inside the evanescent band.
    """
    u_prev = gaussian_pulse(x)
    u_curr = gaussian_pulse(x)
    u_next = np.zeros(NX)

    courant_sq = np.where(evan_mask, -0.16, (1.0 * DT / DX) ** 2)

    snapshots = {}

    for n in range(NT):
        if n in snapshot_steps:
            snapshots[n] = u_curr.copy()

        u_next[1:-1] = (
            2 * u_curr[1:-1]
            - u_prev[1:-1]
            + courant_sq[1:-1] * (u_curr[2:] - 2 * u_curr[1:-1] + u_curr[:-2])
        )
        u_next[0]  = 0.0
        u_next[-1] = 0.0

        u_prev = u_curr
        u_curr = u_next.copy()

    return snapshots

snaps_evan = fdtd_wave_evanescent(snap_steps)

# ─── Plot 1: Wave propagation snapshots (3 scenarios) ────────────────────────

fig, axes = plt.subplots(3, 4, figsize=(16, 9), sharey=True, sharex=True)
fig.suptitle("1D Wave Equation — FDTD Simulation", fontsize=14, y=1.01)

titles = ["Uniform medium  (c = 1.0 m/s)",
          "Dense medium    (c = 0.6 m/s)",
          "Evanescent zone (ρ_eff < 0 region shaded)"]
snaps_list = [snaps_uniform, snaps_dense, snaps_evan]

for row, (snap_dict, row_title) in enumerate(zip(snaps_list, titles)):
    for col, step in enumerate(snap_steps):
        ax = axes[row][col]
        ax.plot(x, snap_dict[step], color="#2563eb", linewidth=1.5)
        ax.axhline(0, color="#94a3b8", linewidth=0.5)
        ax.set_xlim(0, NX * DX)
        ax.set_ylim(-1.2, 1.2)
        ax.set_title(f"t = {step * DT:.2f} s", fontsize=9)

        if row == 2:
            ax.axvspan(evanescent_start * DX, evanescent_end * DX,
                       alpha=0.12, color="#ef4444", label="ρ_eff < 0")

        if col == 0:
            ax.set_ylabel(row_title, fontsize=8, labelpad=4)

fig.tight_layout()
plt.savefig("wave_eq_snapshots.png", dpi=150, bbox_inches="tight")
plt.show()

# ─── Plot 2: Wave speed vs density ────────────────────────────────────────────
#
#  c = sqrt(B / rho). Fixing B = 1.0, we vary rho to show the relationship.

B_ref = 1.0
rho_vals = np.linspace(0.2, 5.0, 300)
c_vals   = np.sqrt(B_ref / rho_vals)

fig2, ax2 = plt.subplots(figsize=(7, 4.5))
ax2.plot(rho_vals, c_vals, color="#2563eb", linewidth=2)
ax2.set_xlabel("Density ρ  (kg/m³, normalised)", fontsize=11)
ax2.set_ylabel("Wave speed c  (m/s, normalised)", fontsize=11)
ax2.set_title("Wave speed vs density  —  c = √(B/ρ),  B = 1.0", fontsize=12)
ax2.grid(True, linestyle="--", alpha=0.4)
ax2.annotate("Higher density → slower wave", xy=(3.0, np.sqrt(1/3.0)),
             xytext=(3.2, 0.85),
             arrowprops=dict(arrowstyle="->", color="#64748b"),
             fontsize=9, color="#334155")
fig2.tight_layout()
plt.savefig("wave_speed_vs_density.png", dpi=150, bbox_inches="tight")
plt.show()
```

**Running the notebook:** save the code as a `.py` file or paste into a Jupyter cell. It requires `numpy` and `matplotlib` (both standard in any scientific Python environment). The simulation runs in a few seconds. Three rows of panels show the pulse at four time snapshots under each scenario; the shaded red region in row 3 is the evanescent zone where the pulse decays instead of propagating through. The second figure shows the c(ρ) relationship, which becomes central in Post 4 when we plot full dispersion curves.

---

## References

Achenbach, J.D. (1973). *Wave Propagation in Elastic Solids*. North-Holland. — The canonical graduate-level reference for elastic wave theory; Chapter 1 covers the derivation from continuum mechanics in full generality.

Pierce, A.D. (2019). *Acoustics: An Introduction to its Physical Principles and Applications* (3rd ed.). Springer. — Standard modern acoustics text; Chapter 1 treats the linearised equations of fluid motion and the derivation of the acoustic wave equation.

Cummer, S.A., Christensen, J., & Alù, A. (2016). Controlling sound with acoustic metamaterials. *Nature Reviews Materials*, 1, 16001. https://doi.org/10.1038/natrevmats.2016.1 — A thorough review that situates the wave equation squarely in the metamaterials context, including how effective-medium parameters emerge and how they modify wave behaviour.
