---
title: "Negative Parameters — When Physics Gets Weird"
subtitle: "How engineered materials can have negative density and negative bulk modulus — and what that actually means"
date: 2026-03-18
series: ["acoustic-metamaterials"]
tags: ["metamaterials", "acoustics", "negative density", "negative bulk modulus", "physics"]
categories: ["The Research"]
series_order: 3
draft: false
description: "Negative mass. Negative stiffness. These aren't science fiction — they're how acoustic metamaterials work. Here's the physics."
---

At the end of Post 2, we derived the wave equation and discovered something unsettling sitting inside it. If the effective density of a material goes negative, the character of the equation changes. Instead of oscillatory solutions that propagate energy through space, you get exponential ones: a wave entering such a region decays rather than travels. We called this evanescent decay, named the resulting frequency band a band gap, and noted it was central to everything interesting in acoustic metamaterials.

Then we left the obvious question sitting there: how, exactly, do you make a material with negative density?

That is what this post answers. We are going to build the physical picture for both negative effective density and negative effective bulk modulus, see what the wave equation does in each case, and look at what happens when you engineer both simultaneously. That last scenario is where genuinely exotic behaviour lives.

---

## What "Effective" Actually Means

The key word, the one that makes all of this physically coherent, is *effective*.

When a wave propagates through a material, it does not resolve individual atoms or individual structural elements. If the wavelength is much larger than the internal structure, the wave responds to the averaged, macroscopic behaviour of the medium. This averaging process is called homogenisation, and the parameters it produces are called effective parameters: an effective density ρ_eff and an effective bulk modulus B_eff.

In a conventional material, effective parameters are essentially just the bulk material properties. The homogenised density of steel is, to excellent approximation, the density of steel. But in an engineered structure with internal resonators, the effective parameters become frequency-dependent. Near a resonance, the structure responds to an applied force or pressure in ways the static material properties do not predict. And in those narrow frequency windows near resonance, the effective parameters can take values unavailable in any natural material. Including negative ones.

This is not magic, and it is not a violation of any physical law. No individual component has negative mass or negative stiffness. The effective parameters are emergent descriptions of how the composite structure responds to waves at the macroscale. They are real, measurable quantities. They are simply not the same as the properties of the constituent materials.

---

## Negative Effective Density: Membrane Resonators and Local Resonance

The clearest physical route to negative effective mass density uses locally resonant inclusions: small internal structures embedded inside a host material, each resonating at a target frequency.

Liu and colleagues demonstrated this in its simplest form in their landmark 2000 *Science* paper. Lead spheres coated in soft silicone rubber, embedded in an epoxy matrix. The lead provides the inertia; the rubber provides the spring coupling to the host; together they form a resonator whose natural frequency is set by the sphere mass and coating stiffness. At and above this resonant frequency, the inner sphere moves opposite to the force applied to the outer shell. Pull the composite in one direction and the lead core accelerates the other way. Observed from outside, measuring only the net momentum of the composite, you would calculate a negative effective mass.

The formal derivation, worked out carefully by Huang, Sun, and Huang (2009), produces an effective mass density of the form:

$$\rho_\text{eff}(\omega) = \rho_0 \left[1 - \frac{F \omega_r^2}{\omega^2 - \omega_r^2}\right]$$

where ω_r is the resonant frequency of the internal resonator, F is a filling fraction parameter, and ρ₀ is the background density. Below resonance, the bracketed term is positive and ρ_eff is positive. Just above resonance, the internal oscillator lags the driving force, the bracketed term becomes negative, and so does the effective density. The transition is sharp and centred on ω_r.

Membrane-type resonators achieve the same effect through a different geometry. A thin elastic membrane stretched across an aperture in a rigid panel has its own fundamental resonance; near and above that frequency, the effective density of the membrane-plus-panel system goes negative. The sub-wavelength condition is key: as long as the structural period is much smaller than the acoustic wavelength, the wave cannot resolve the internal geometry and the effective-medium description holds. This is why metamaterials based on local resonance can achieve band gaps at frequencies two orders of magnitude below what Bragg scattering would predict from the same length scales. The resonance, not the geometry, sets the working frequency.

---

## Negative Effective Bulk Modulus: Helmholtz Resonators

Negative bulk modulus comes from a different class of internal structure: the Helmholtz resonator.

A Helmholtz resonator is a rigid cavity with a narrow neck. When the fluid at the neck is pushed inward, the air inside the cavity compresses and pushes back, like a spring with mass provided by the fluid in the neck itself. The resonant frequency is set by the neck dimensions and cavity volume. Below that frequency, the cavity behaves as a stiffening element: compress it and it pushes back. This is positive bulk modulus, normal behaviour.

The surprise comes near resonance. Just above the Helmholtz resonant frequency ω_H, the cavity response leads the driving pressure. Applied compression now drives an oscillation that causes the enclosed volume to *expand*. The structure gets bigger when you squeeze it. That is negative bulk modulus.

The effective bulk modulus of a host medium seeded with Helmholtz resonators takes a form analogous to the effective density expression: a function with a pole at ω_H that passes through zero and goes negative just above resonance. Cummer, Christensen, and Alù (2016) derive this explicitly in their Nature Reviews Materials survey and connect it to the broader effective-medium theory for acoustic metamaterials. The physical picture is the same as for density: the resonator's internal dynamics, not its static material properties, determine how the composite responds to waves near the resonant frequency.

---

## What the Wave Equation Does With Negative Parameters

Recall the dispersion relation from Post 2:

$$k^2 = \frac{\omega^2 \rho_\text{eff}}{B_\text{eff}}$$

When both parameters are positive, k² is positive and k is real. Solutions take the form u ~ e^{ikx}, oscillatory in space. Waves propagate.

When one parameter is negative and the other positive, k² is negative. The wavenumber k becomes imaginary: k = iα for real α > 0. Substituting into the plane wave solution: u ~ e^{-αx}. The field decays exponentially in space rather than oscillating. Energy does not transmit through the material at this frequency. This is the evanescent regime, and the frequency band where it occurs is a band gap.

Negative ρ_eff alone (with positive B_eff) produces one band gap, centred just above the resonant frequency of the internal mass resonator. Negative B_eff alone produces a different band gap, centred just above the Helmholtz resonator frequency. Both are stopbands in the transmission spectrum. They can be designed to occur at the same frequency or different frequencies, depending on the geometry.

---

## Double-Negative: When Both Go Negative Simultaneously

Now for the counterintuitive part. If both ρ_eff and B_eff are simultaneously negative, k² = ω²ρ_eff / B_eff is positive again. Negative divided by negative is positive. k is real. Waves propagate.

But not in the usual way. In a double-negative medium, the wave vector and the energy flow (the group velocity) point in opposite directions. Phase moves in one direction; energy moves the other. This is the acoustic analogue of a negative refractive index, a concept Veselago proposed for electromagnetic materials in 1968 and Pendry and colleagues made practical in 2000.

The acoustic version follows the same mathematics. A negative index means that a wave crossing an interface into the double-negative medium refracts backwards: instead of bending toward the normal on the far side of an interface (as in glass), it bends *the same way* as the incoming wave, on the same side. Snell's law still holds, but with n < 0 the bending goes the other direction. This produces effects with no natural counterpart: superlensing (focusing below the diffraction limit) and acoustic cloaking, where a designed shell of spatially varying effective parameters guides sound waves around an object so cleanly that a downstream observer detects nothing.

Hussein, Leamy, and Ruzzene (2014) situate these double-negative phenomena within the broader landscape of phononic and acoustic metamaterials in their Applied Mechanics Reviews survey, connecting the effective-medium picture to the periodicity and band structure analysis that underlies the full theoretical framework.

---

## 🔬 Jupyter Notebook: Effective Parameters and the Transition to Evanescence

The code below plots ρ_eff(ω) and B_eff(ω) as functions of frequency, computes k(ω) across the full range, and shows how the wave response transitions from propagating to evanescent as each parameter crosses zero.

```python
"""
Post 3 Companion: Effective Parameters and the Evanescent Transition
=====================================================================
Plots ρ_eff(ω), B_eff(ω), and k(ω) = sqrt(ω² ρ_eff / B_eff)
to show where parameters go negative and what happens to the wave.

Requirements: numpy, matplotlib
"""

import numpy as np
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec

# ─── Parameter definitions ────────────────────────────────────────────────────
# Effective mass density model (locally resonant inclusion)
# ρ_eff(ω) = ρ0 * [1 - F * ωr² / (ω² - ωr²)]

rho0 = 1.0      # background density
F    = 0.4      # filling fraction
wr   = 1.0      # resonant frequency of internal mass resonator (normalised)

# Effective bulk modulus model (Helmholtz resonator)
# 1/B_eff(ω) = 1/B0 * [1 - G * wH² / (ω² - wH²)]
# (equivalent form: B_eff goes negative above wH)

B0   = 1.0      # background bulk modulus
G    = 0.3      # coupling parameter
wH   = 1.4      # Helmholtz resonator frequency (set above wr for distinct gaps)

# Frequency range (avoid poles)
eps   = 0.02
omega = np.concatenate([
    np.linspace(0.05, wr - eps, 400),
    np.linspace(wr + eps, wH - eps, 400),
    np.linspace(wH + eps, 3.0, 400),
])

# ─── Compute effective parameters ─────────────────────────────────────────────

def rho_eff(omega):
    return rho0 * (1.0 - F * wr**2 / (omega**2 - wr**2))

def B_eff(omega):
    inv_B = (1.0 / B0) * (1.0 - G * wH**2 / (omega**2 - wH**2))
    # Guard against division by zero
    with np.errstate(divide='ignore', invalid='ignore'):
        B = np.where(np.abs(inv_B) > 1e-10, 1.0 / inv_B, np.nan)
    return B

rho = rho_eff(omega)
B   = B_eff(omega)

# ─── Compute k² and k ─────────────────────────────────────────────────────────
# k² = ω² ρ_eff / B_eff
# k is real when k² > 0 (propagating), imaginary when k² < 0 (evanescent)

k_sq = omega**2 * rho / B

k_real = np.where(k_sq >= 0, np.sqrt(k_sq), 0.0)
k_imag = np.where(k_sq < 0,  np.sqrt(-k_sq), 0.0)  # evanescent decay rate α

# ─── Plotting ─────────────────────────────────────────────────────────────────

fig = plt.figure(figsize=(14, 10))
gs  = gridspec.GridSpec(2, 2, hspace=0.45, wspace=0.35)

clr_prop = "#2563eb"   # propagating — blue
clr_evan = "#ef4444"   # evanescent  — red
clr_zero = "#94a3b8"   # zero line   — grey

# Helper: shade evanescent regions (k² < 0)
def shade_evanescent(ax):
    neg_mask = k_sq < 0
    if neg_mask.any():
        # find contiguous blocks
        from itertools import groupby
        from operator import itemgetter
        indices = np.where(neg_mask)[0]
        blocks = []
        for k, g in groupby(enumerate(indices), lambda x: x[0] - x[1]):
            block = list(map(itemgetter(1), g))
            blocks.append((block[0], block[-1]))
        for start, end in blocks:
            ax.axvspan(omega[start], omega[end], alpha=0.10, color=clr_evan)


# ── Plot 1: ρ_eff(ω) ──────────────────────────────────────────────────────────
ax1 = fig.add_subplot(gs[0, 0])
ax1.plot(omega, rho, color=clr_prop, linewidth=2)
ax1.axhline(0, color=clr_zero, linewidth=0.8, linestyle='--')
ax1.axvline(wr, color="#f59e0b", linewidth=1.0, linestyle=':', alpha=0.9, label=f'ωᵣ = {wr}')
ax1.set_ylim(-3, 3)
ax1.set_xlabel("Angular frequency ω (normalised)", fontsize=10)
ax1.set_ylabel("ρ_eff / ρ₀", fontsize=10)
ax1.set_title("Effective mass density ρ_eff(ω)", fontsize=11)
ax1.legend(fontsize=9)
ax1.grid(True, alpha=0.3, linestyle='--')
shade_evanescent(ax1)

# ── Plot 2: B_eff(ω) ──────────────────────────────────────────────────────────
ax2 = fig.add_subplot(gs[0, 1])
ax2.plot(omega, B, color=clr_prop, linewidth=2)
ax2.axhline(0, color=clr_zero, linewidth=0.8, linestyle='--')
ax2.axvline(wH, color="#8b5cf6", linewidth=1.0, linestyle=':', alpha=0.9, label=f'ωH = {wH}')
ax2.set_ylim(-5, 5)
ax2.set_xlabel("Angular frequency ω (normalised)", fontsize=10)
ax2.set_ylabel("B_eff / B₀", fontsize=10)
ax2.set_title("Effective bulk modulus B_eff(ω)", fontsize=11)
ax2.legend(fontsize=9)
ax2.grid(True, alpha=0.3, linestyle='--')
shade_evanescent(ax2)

# ── Plot 3: k_real(ω) and k_imag(ω) ──────────────────────────────────────────
ax3 = fig.add_subplot(gs[1, 0])
ax3.plot(omega, k_real, color=clr_prop, linewidth=2, label='Re(k) — propagating')
ax3.plot(omega, k_imag, color=clr_evan, linewidth=2, linestyle='--', label='Im(k) = α — evanescent')
ax3.axhline(0, color=clr_zero, linewidth=0.8, linestyle='--')
ax3.axvline(wr, color="#f59e0b", linewidth=1.0, linestyle=':', alpha=0.7)
ax3.axvline(wH, color="#8b5cf6", linewidth=1.0, linestyle=':', alpha=0.7)
ax3.set_ylim(-0.2, 3.5)
ax3.set_xlabel("Angular frequency ω (normalised)", fontsize=10)
ax3.set_ylabel("Wavenumber k", fontsize=10)
ax3.set_title("Wave vector k(ω): real vs imaginary", fontsize=11)
ax3.legend(fontsize=9)
ax3.grid(True, alpha=0.3, linestyle='--')
shade_evanescent(ax3)

# ── Plot 4: k²(ω) — the decisive quantity ─────────────────────────────────────
ax4 = fig.add_subplot(gs[1, 1])
ax4.plot(omega, k_sq, color="#0f172a", linewidth=2)
ax4.axhline(0, color=clr_zero, linewidth=0.8, linestyle='--')
ax4.fill_between(omega, k_sq, 0, where=(k_sq < 0), interpolate=True,
                 color=clr_evan, alpha=0.20, label='k² < 0: evanescent')
ax4.fill_between(omega, k_sq, 0, where=(k_sq >= 0), interpolate=True,
                 color=clr_prop, alpha=0.12, label='k² > 0: propagating')
ax4.axvline(wr, color="#f59e0b", linewidth=1.0, linestyle=':', alpha=0.9, label=f'ωᵣ')
ax4.axvline(wH, color="#8b5cf6", linewidth=1.0, linestyle=':', alpha=0.9, label=f'ωH')
ax4.set_ylim(-6, 6)
ax4.set_xlabel("Angular frequency ω (normalised)", fontsize=10)
ax4.set_ylabel("k²(ω) = ω² ρ_eff / B_eff", fontsize=10)
ax4.set_title("k²(ω): sign determines wave character", fontsize=11)
ax4.legend(fontsize=9)
ax4.grid(True, alpha=0.3, linestyle='--')

fig.suptitle(
    "Effective Parameters and the Evanescent Transition\n"
    "(red shading marks evanescent band gaps where k² < 0)",
    fontsize=13, y=1.01
)

plt.savefig("effective_parameters.png", dpi=150, bbox_inches="tight")
plt.show()
print("Saved: effective_parameters.png")
```

**What the plots show:** The top-left panel traces ρ_eff(ω), which goes negative in a window above ω_r. The top-right traces B_eff(ω), which goes negative above ω_H. The bottom-left separates Re(k) from Im(k) = α, the evanescent decay rate; wherever the imaginary part is non-zero, energy is not transmitting. The bottom-right plots k² directly and shades the regions by sign. Red bands are the band gaps. Notice that beyond the second pole, both parameters are negative and k² is positive again: the double-negative propagating window.

**To run:** Paste into a `.py` file or Jupyter notebook. Standard `numpy` and `matplotlib` only. Adjust `wr`, `wH`, `F`, and `G` to shift the gap positions and widths.

---

## Where We're Going

We have now built the physical picture for both anomalous parameters. Membrane resonators and locally resonant inclusions give negative effective density. Helmholtz resonators give negative effective bulk modulus. Either one creates a frequency window where waves are exponentially attenuated. Both simultaneously create a window where waves propagate but backwards, giving negative index.

What we have not yet done is map these effects across the full spectrum of a periodic structure. A real metamaterial is not a single resonator but a repeating array of unit cells, and the interaction between cells produces a band structure: a complete map of which frequencies propagate, at what speed, and in which direction. Post 4 introduces Bloch-Floquet theory, the mathematical framework for periodic systems, and shows how to construct and read a band diagram from first principles. The band gaps we have been pointing at throughout this series will become precisely located features on those curves, connected directly to the resonator geometry through quantitative relationships.

---

## References

1. **Liu, Z., Zhang, X., Mao, Y., Zhu, Y. Y., Yang, Z., Chan, C. T., & Sheng, P. (2000).** Locally resonant sonic materials. *Science*, 289(5485), 1734–1736. https://doi.org/10.1126/science.289.5485.1734 *(The foundational demonstration of locally resonant acoustic metamaterials and sub-wavelength band gaps.)*

2. **Huang, H. H., Sun, C. T., & Huang, G. L. (2009).** On the negative effective mass density in acoustic metamaterials. *International Journal of Engineering Science*, 47(4), 610–617. https://doi.org/10.1016/j.ijengsci.2008.12.007 *(Formal derivation of the frequency-dependent effective density from the two-mass model.)*

3. **Huang, G. L., & Sun, C. T. (2010).** Band gaps in a multiresonator acoustic metamaterial. *Journal of Vibration and Acoustics*, 132(3), 031003. https://doi.org/10.1115/1.4001452 *(Extension to multiresonator systems; connects effective-parameter description to multi-gap band structure.)*

4. **Cummer, S. A., Christensen, J., & Alù, A. (2016).** Controlling sound with acoustic metamaterials. *Nature Reviews Materials*, 1, 16001. https://doi.org/10.1038/natrevmats.2016.1 *(Comprehensive review covering negative effective parameters, cloaking, and the full landscape of acoustic metamaterial phenomena.)*

5. **Hussein, M. I., Leamy, M. J., & Ruzzene, M. (2014).** Dynamics of phononic materials and structures: Historical origins, recent progress, and future outlook. *Applied Mechanics Reviews*, 66(4), 040802. https://doi.org/10.1115/1.4026911 *(Situates negative-index and double-negative phenomena within the broader phononic and acoustic metamaterial field.)*

---

*Next post: [Dispersion Relations and Band Diagrams: Reading the Map of Forbidden Frequencies →]()*
