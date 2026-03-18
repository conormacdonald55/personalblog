---
title: "Band Gaps and Dispersion — Reading the Map"
subtitle: "How dispersion relations reveal where waves can and can't travel — and why band gaps are the heart of metamaterial design"
date: 2026-03-18
series: ["acoustic-metamaterials"]
tags: ["metamaterials", "acoustics", "band gaps", "dispersion", "Bloch-Floquet", "phononic crystals"]
categories: ["The Research"]
series_order: 4
draft: true
description: "The dispersion relation is the map of a metamaterial. Here's how to read it — and what band gaps actually mean."
---

At the end of Post 3, we arrived at a precise physical picture of how a material's effective parameters can go negative. Near a resonant frequency, the internal dynamics of a locally resonant inclusion reverse the macroscopic response: effective density goes negative above the resonant frequency of the internal mass, effective bulk modulus goes negative above the Helmholtz resonant frequency. When either parameter is negative, k² is negative, k becomes imaginary, and waves decay exponentially rather than propagate. We called the resulting frequency window a band gap.

But a single resonator gives you a single, narrow forbidden band. A real acoustic metamaterial is a *repeating array* of unit cells, and that spatial periodicity changes the picture entirely. Band gaps aren't just a side effect of a single resonance; they're a structural feature of the architecture, encoded in the dispersion relation of the periodic system. To understand them properly, you need to know how to read that map.

---

## Bloch-Floquet: What Periodicity Does to Waves

Suppose a medium repeats with spatial period *a*, the lattice constant. In a crystal, that might be the spacing between atoms; in a metamaterial, it's the spacing between unit cells. The Bloch-Floquet theorem is the fundamental mathematical statement of what this means for waves: the displacement field at position x + a is related to the field at x by a simple phase factor.

$$u(x + a) = e^{ika} \, u(x)$$

The wavenumber k characterises how much phase accumulates per unit cell. This is both elegant and practical: instead of solving a wave problem over an infinite periodic structure, you solve it over a single unit cell with periodic boundary conditions, then parameterise the solution by k. The wavenumber k lives in a compact domain called the first Brillouin zone, spanning from −π/a to π/a. Outside this range, solutions repeat; there is no new physical information beyond the zone boundary. Brillouin's 1953 text remains the clearest derivation of why that boundary exists and what happens at it.

The consequence is that for a periodic structure, the wave problem has a discrete family of solutions indexed by k, each with its own frequency. The resulting ω(k) relationship is the dispersion relation, and plotting it produces the band diagram: the complete map of which waves exist and at what frequencies.

---

## The Dispersion Relation: Slope, Curvature, and Speed

For a simple uniform medium, the dispersion relation is a straight line: ω = ck. All frequencies travel at the same speed c, and the medium is non-dispersive. In a periodic structure, this simplicity breaks.

Two velocities matter. The *phase velocity* is ω/k: the speed at which the wave's phase pattern moves through space. The *group velocity* is ∂ω/∂k: the slope of the dispersion curve at any point, and the speed at which energy and information travel. In a non-dispersive medium these are equal; in a periodic structure they generally differ. Where the dispersion curve is steep, energy moves fast. Where the slope flattens, energy slows. Where the slope reaches exactly zero, energy is not transported at all and the mode is localised in space. This last condition is the mathematical signature of a flat band, and it is central to understanding what band gaps look like on a diagram.

---

## Two Ways to Open a Gap

Band gaps in periodic structures come from two distinct physical mechanisms, and understanding both matters for design.

**Bragg scattering** is the older mechanism, familiar from X-ray crystallography. When the lattice period a is comparable to the acoustic wavelength λ, waves reflecting from successive unit cells interfere with the forward-propagating wave. At the Bragg condition (2a = nλ for integer n), this interference is destructive and propagation is forbidden. Kushwaha, Halevi, Dobrzynski, and Djafari-Rouhani demonstrated this for elastic composites in their 1993 Physical Review Letters paper, which introduced the term "phononic crystals" and established Bragg-based band structure as the foundational framework for periodic acoustic engineering. The Bragg gap frequency scales inversely with the lattice constant: pushing it to lower frequencies requires either a larger unit cell or a slower medium, both of which impose significant geometric constraints.

**Local resonance** works at a fundamentally different scale. The gap appears near the resonant frequency ω_r of the internal resonator embedded in each unit cell, regardless of the lattice constant, provided the acoustic wavelength is much larger than the unit cell. This is the sub-wavelength condition, and it is what Liu and colleagues exploited in their 2000 Science paper to produce band gaps at frequencies two orders of magnitude below what Bragg scattering would predict from the same physical dimensions. The mechanism is not geometric interference but inertial reversal: at and above ω_r, the internal mass moves against the applied force, and the composite appears, to an outside observer, to have negative effective mass. The gap position is set by the resonator physics, not the unit cell size, which is why locally resonant metamaterials can achieve low-frequency attenuation that would be geometrically impractical with Bragg-based designs.

---

## Reading a Band Diagram

A band diagram for a diatomic spring-mass chain plots ω on the vertical axis against k across the Brillouin zone, from k = 0 to k = π/a. Two branches appear. The *acoustic branch* occupies the lower half: at long wavelengths (small k), the two masses in each unit cell move together and the dispersion is approximately linear, recovering the uniform-medium result. As k approaches the zone boundary, the curve bends and flattens, slowing to zero group velocity at k = π/a. The *optical branch* occupies the upper half: the two masses move in opposition, and the branch has a non-zero frequency even at k = 0. Between the top of the acoustic branch and the bottom of the optical branch lies the band gap: a horizontal strip on the diagram that no real dispersion branch crosses.

Flat bands deserve special attention. A completely horizontal dispersion curve means ∂ω/∂k = 0 everywhere: the group velocity is zero and energy is not transported. In a locally resonant metamaterial, the optical branch near the resonant frequency approaches flat. This is the direct signature of the internal resonator absorbing energy locally rather than passing it along the chain. Phani, Woodhouse, and Fleck (2006) demonstrate this clearly in their treatment of two-dimensional periodic lattices: identifying a band gap reduces to finding the frequency intervals where no real-valued k exists across the full Brillouin zone. That criterion is universal and applies to systems far more complex than the diatomic chain.

The band diagram also tells you whether a gap comes from Bragg scattering or local resonance. A Bragg gap appears symmetrically at the zone boundary (k = π/a), where the acoustic and optical branches both flatten. A locally resonant gap appears as a horizontal strip that can sit anywhere in the frequency range, often well below the Bragg condition, and is associated with the flat optical branch pinned near ω_r. Hussein, Leamy, and Ruzzene's 2014 Applied Mechanics Reviews survey covers the full historical trajectory from Bragg-based phononic crystals to locally resonant metamaterials and maps the relationship between these two regimes with precision.

---

## Engineering the Gap

Everything discussed so far can be controlled. The resonant frequency of a locally resonant unit cell is approximately ω_r = √(k_internal / m_internal): reduce the internal spring stiffness or increase the resonator mass and the gap shifts to lower frequencies; do the reverse and it moves up. The gap *width* grows with the contrast between the resonator and the host: a heavy, softly coupled resonator produces a wider forbidden band than a light, stiff one. The filling fraction (how densely unit cells are packed) also matters; higher filling fraction generally broadens the gap until cell-to-cell interactions become significant.

Multiple gaps are achievable by designing unit cells with resonators at several distinct frequencies. Huang and Sun (2010) showed that multi-resonator acoustic metamaterials produce band gaps centred on each resonant frequency in the system. This is one practical route toward broadband attenuation: instead of one narrow forbidden band, you build a ladder of gaps covering a wider frequency range. The geometry need not be exotic; graded resonator masses and adjusted spring constants are often sufficient to position multiple gaps where an application demands them.

For Bragg gaps, the control parameter is impedance contrast: alternating layers of high and low acoustic impedance widen the gap; closely matched layers narrow it. This is the same principle underlying classical quarter-wave stacks in optics, and it transfers directly to periodic acoustic composites. Designing simultaneously for both Bragg and local resonance gaps is an active area of research, and it points toward metamaterials that are effective across a much broader frequency range than either mechanism alone can achieve.

---

## Where We're Going

Everything in this post assumes small amplitudes. The dispersion relation is a property of the linearised system, describing how the periodic structure responds when each unit cell behaves independently of drive level. In real nonlinear systems, that assumption fails. When vibration amplitudes grow large enough, the effective stiffness of the resonators changes, the resonant frequency shifts, and the band gap moves with it. Push further into the regime where the internal dynamics become sensitive to initial conditions, and something qualitatively different happens: the system enters chaos, and the wave suppression becomes broadband and amplitude-dependent.

Fang, Wen, Bonello, Yin, and Yu demonstrated in 2017 that nonlinear acoustic metamaterials built around Duffing-type resonators exhibit exactly this behaviour: amplitude-dependent band gap positions, widening of the forbidden zone with increasing drive, and eventually chaotic dynamics that suppress wave transmission across a wide frequency range through mechanisms that have no analogue in the linear theory. That is the territory Post 5 opens.

---

## 🔬 Jupyter Notebook: Post 4 | Band Diagrams for the 1D Diatomic Chain

**What this notebook explores:**
The dispersion relation of a 1D diatomic spring-mass chain, computed analytically and then parameterised to show how the band gap position and width depend on the resonator design.

**What the plots show:**
- The full band diagram ω(k) for the diatomic chain, with acoustic and optical branches clearly labelled
- The band gap shaded on the diagram
- How the gap position shifts with mass ratio (m₁/m₂) and spring constant ratio (k₁/k₂)
- A parameter sweep showing gap width as a function of mass contrast

```python
"""
Post 4 Companion: Band Diagrams for the 1D Diatomic Spring-Mass Chain
======================================================================
Computes and plots the dispersion relation ω(k) for a diatomic chain:
  m₁ and m₂ alternating, coupled by springs k₁ (between unlike masses)
  and k₂ (between like masses, or uniform spring for simplest case).

For the uniform-spring diatomic chain (k₁ = k₂ = k):
  ω²(k) = k(1/m₁ + 1/m₂) ± k·sqrt[(1/m₁ + 1/m₂)² - 4sin²(ka/2)/(m₁·m₂)]

Requirements: numpy, matplotlib
"""

import numpy as np
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec

# ─── Dispersion relation for uniform-spring diatomic chain ───────────────────
#
# Two masses m1, m2 per unit cell; lattice constant a = 1.
# All springs between adjacent masses have constant k.
#
# Eigenvalues of the dynamical matrix give:
#   ω²± = (k/μ) ± k · sqrt[(1/m1 - 1/m2)² + (4/m1m2)·sin²(ka/2)]
# where μ = m1·m2/(m1 + m2) is the reduced mass.
#
# Acoustic branch: ω²_- (lower sign)
# Optical branch:  ω²_+ (upper sign)

def diatomic_dispersion(k_vals, m1, m2, k_spring, a=1.0):
    """
    Returns (omega_acoustic, omega_optical) for the diatomic chain.

    Parameters
    ----------
    k_vals   : array of wavenumbers in [0, π/a]
    m1, m2   : masses of the two atoms per unit cell
    k_spring : spring constant (uniform)
    a        : lattice constant (default 1)
    """
    C = k_spring * (1/m1 + 1/m2)
    D = k_spring * np.sqrt((1/m1 - 1/m2)**2 + 4/(m1 * m2) * np.sin(k_vals * a / 2)**2)

    omega_sq_minus = C - D   # acoustic branch
    omega_sq_plus  = C + D   # optical branch

    # Numerical safety
    omega_acoustic = np.sqrt(np.maximum(omega_sq_minus, 0))
    omega_optical  = np.sqrt(np.maximum(omega_sq_plus, 0))

    return omega_acoustic, omega_optical


# ─── Reference values ────────────────────────────────────────────────────────

a        = 1.0      # lattice constant (normalised)
k_spring = 1.0      # spring constant (normalised)

# Brillouin zone: k in [0, π/a]
k_vals = np.linspace(0, np.pi / a, 300)

# ─── Figure 1: Band diagram for several mass ratios ─────────────────────────

mass_ratios = [1.0, 2.0, 4.0, 8.0]   # m2/m1
colours     = ["#2563eb", "#059669", "#d97706", "#dc2626"]

fig1, ax1 = plt.subplots(figsize=(8, 6))

for ratio, colour in zip(mass_ratios, colours):
    m1 = 1.0
    m2 = ratio * m1

    omega_ac, omega_op = diatomic_dispersion(k_vals, m1, m2, k_spring, a)

    # Band gap limits
    gap_bottom = omega_ac.max()
    gap_top    = omega_op.min()

    ax1.plot(k_vals, omega_ac, color=colour, linewidth=2.0,
             label=f"m₂/m₁ = {ratio:.0f}  (gap: {gap_bottom:.2f}–{gap_top:.2f})")
    ax1.plot(k_vals, omega_op, color=colour, linewidth=2.0, linestyle='--')

    # Shade band gap for the highest contrast case only (cleaner)
    if ratio == mass_ratios[-1]:
        ax1.axhspan(gap_bottom, gap_top, alpha=0.10, color=colour, label='Band gap (m₂/m₁ = 8)')

ax1.set_xlabel("Wavenumber k  (units of 1/a)", fontsize=11)
ax1.set_ylabel("Angular frequency ω  (normalised)", fontsize=11)
ax1.set_title("Diatomic Chain Dispersion — Effect of Mass Ratio\n"
              "(solid = acoustic branch, dashed = optical branch)", fontsize=12)
ax1.legend(fontsize=9, loc='upper left')
ax1.grid(True, linestyle='--', alpha=0.35)
ax1.set_xlim(0, np.pi / a)
ax1.set_ylim(0, None)

# Label zone boundary
ax1.axvline(np.pi / a, color='#94a3b8', linewidth=1.0, linestyle=':')
ax1.text(np.pi / a * 0.97, ax1.get_ylim()[1] * 0.02, "k = π/a\n(zone boundary)",
         ha='right', va='bottom', fontsize=8, color='#64748b')

fig1.tight_layout()
plt.savefig("band_diagram_mass_ratio.png", dpi=150, bbox_inches='tight')
plt.show()


# ─── Figure 2: Single detailed band diagram with annotations ─────────────────

m1 = 1.0
m2 = 4.0

omega_ac, omega_op = diatomic_dispersion(k_vals, m1, m2, k_spring, a)
gap_bottom = omega_ac.max()
gap_top    = omega_op.min()

fig2, ax2 = plt.subplots(figsize=(7, 6))

ax2.plot(k_vals, omega_ac, color='#2563eb', linewidth=2.5, label='Acoustic branch')
ax2.plot(k_vals, omega_op, color='#dc2626', linewidth=2.5, label='Optical branch')
ax2.axhspan(gap_bottom, gap_top, alpha=0.15, color='#ef4444', label=f'Band gap ({gap_bottom:.2f} – {gap_top:.2f})')

# Slope annotation (group velocity near k=0)
dk     = k_vals[5] - k_vals[0]
dw_ac  = omega_ac[5] - omega_ac[0]
v_g    = dw_ac / dk
k_ann  = k_vals[20]
ax2.annotate(f"Slope = group velocity\n∂ω/∂k ≈ {v_g:.2f}",
             xy=(k_ann, omega_ac[20]),
             xytext=(k_ann + 0.3, omega_ac[20] - 0.15),
             arrowprops=dict(arrowstyle='->', color='#334155'),
             fontsize=9, color='#334155')

# Flat band annotation
k_flat_idx = np.argmin(np.abs(k_vals - np.pi / a * 0.95))
ax2.annotate("Flat band → zero\ngroup velocity here",
             xy=(k_vals[k_flat_idx], omega_op[k_flat_idx]),
             xytext=(k_vals[k_flat_idx] - 0.6, omega_op[k_flat_idx] + 0.15),
             arrowprops=dict(arrowstyle='->', color='#334155'),
             fontsize=9, color='#334155')

ax2.axvline(np.pi / a, color='#94a3b8', linewidth=1.0, linestyle=':')
ax2.set_xlabel("Wavenumber k  (units of 1/a)", fontsize=11)
ax2.set_ylabel("Angular frequency ω  (normalised)", fontsize=11)
ax2.set_title(f"Band Diagram — Diatomic Chain  (m₂/m₁ = {m2/m1:.0f})", fontsize=12)
ax2.legend(fontsize=10)
ax2.grid(True, linestyle='--', alpha=0.35)
ax2.set_xlim(0, np.pi / a)
ax2.set_ylim(0, None)

fig2.tight_layout()
plt.savefig("band_diagram_annotated.png", dpi=150, bbox_inches='tight')
plt.show()


# ─── Figure 3: Gap width vs mass contrast ────────────────────────────────────

ratios    = np.linspace(1.01, 10.0, 200)
gap_widths = []

for ratio in ratios:
    oa, oo = diatomic_dispersion(k_vals, 1.0, ratio, k_spring, a)
    gap_widths.append(oo.min() - oa.max())

fig3, ax3 = plt.subplots(figsize=(7, 4.5))
ax3.plot(ratios, gap_widths, color='#2563eb', linewidth=2)
ax3.fill_between(ratios, gap_widths, alpha=0.15, color='#2563eb')
ax3.set_xlabel("Mass ratio  m₂/m₁", fontsize=11)
ax3.set_ylabel("Band gap width  Δω  (normalised)", fontsize=11)
ax3.set_title("Band Gap Width vs Mass Contrast\n"
              "(higher contrast = wider gap)", fontsize=12)
ax3.grid(True, linestyle='--', alpha=0.35)
ax3.annotate("Equal masses → no gap",
             xy=(1.01, gap_widths[0]),
             xytext=(2.5, gap_widths[0] + 0.05),
             arrowprops=dict(arrowstyle='->', color='#64748b'),
             fontsize=9, color='#334155')

fig3.tight_layout()
plt.savefig("gap_width_vs_mass_ratio.png", dpi=150, bbox_inches='tight')
plt.show()

print("Saved: band_diagram_mass_ratio.png, band_diagram_annotated.png, gap_width_vs_mass_ratio.png")
```

**Running the notebook:** Copy into a `.py` file or Jupyter notebook. Only `numpy` and `matplotlib` are required. Three figures are produced. The first overlays dispersion curves for four mass ratios, showing how increasing mass contrast widens and deepens the band gap. The second is an annotated single-ratio diagram pointing out where group velocity is steep (fast energy transport), where it flattens at the zone boundary, and where it reaches zero on the nearly flat optical branch. The third plots band gap width as a function of mass ratio, making the engineering tradeoff explicit: more contrast, more suppression, but also a heavier resonator.

**What to explore:** Set `m2 = m1` (ratio = 1) and observe that the gap closes entirely — a uniform chain has no band gap, only a cutoff frequency. Increase the ratio progressively and watch the gap open from zero and widen. Adjust `k_spring` to shift the overall frequency scale without changing the gap structure. These are the same design levers available in physical metamaterials.

---

## References

1. **Brillouin, L. (1953).** *Wave Propagation in Periodic Structures: Electric Filters and Crystal Lattices* (2nd ed.). Dover Publications. *(The classical reference for Bloch-Floquet theory and Brillouin zones; the clearest mathematical treatment of why periodicity creates forbidden frequency bands.)*

2. **Kushwaha, M. S., Halevi, P., Dobrzynski, L., & Djafari-Rouhani, B. (1993).** Acoustic band structure of periodic elastic composites. *Physical Review Letters*, 71(13), 2022–2025. https://doi.org/10.1103/PhysRevLett.71.2022 *(Introduced the term "phononic crystals" and established the Bragg-scattering band gap framework for acoustic composites from first principles.)*

3. **Liu, Z., Zhang, X., Mao, Y., Zhu, Y. Y., Yang, Z., Chan, C. T., & Sheng, P. (2000).** Locally resonant sonic materials. *Science*, 289(5485), 1734–1736. https://doi.org/10.1126/science.289.5485.1734 *(The foundational demonstration of locally resonant band gaps at sub-wavelength scales; the result that distinguishes metamaterial band gaps from Bragg gaps.)*

4. **Phani, A. S., Woodhouse, J., & Fleck, N. A. (2006).** Wave propagation in two-dimensional periodic lattices. *Journal of the Acoustical Society of America*, 119(4), 1995–2005. https://doi.org/10.1121/1.2179748 *(Systematic treatment of dispersion in 2D periodic lattices; useful model for reading band diagrams and identifying gap criteria.)*

5. **Hussein, M. I., Leamy, M. J., & Ruzzene, M. (2014).** Dynamics of phononic materials and structures: Historical origins, recent progress, and future outlook. *Applied Mechanics Reviews*, 66(4), 040802. https://doi.org/10.1115/1.4026911 *(Comprehensive review mapping the full trajectory from Bragg phononic crystals to locally resonant metamaterials and beyond; the reference for band structure analysis in the field.)*

6. **Huang, G. L., & Sun, C. T. (2010).** Band gaps in a multiresonator acoustic metamaterial. *Journal of Vibration and Acoustics*, 132(3), 031003. https://doi.org/10.1115/1.4001452 *(Extension to multi-resonator unit cells; demonstrates how stacking resonators at different frequencies produces multiple band gaps for broader attenuation.)*

7. **Fang, X., Wen, J., Bonello, B., Yin, J., & Yu, D. (2017).** Ultra-low and ultra-broad-band nonlinear acoustic metamaterials. *Nature Communications*, 8, 1288. https://doi.org/10.1038/ncomms11461 *(Landmark paper demonstrating amplitude-dependent band gap behaviour in nonlinear acoustic metamaterials using Duffing resonators; direct bridge to Post 5.)*

---

*Next post: [Beyond Small Amplitudes — Nonlinearity and the Moving Band Gap →]()*
