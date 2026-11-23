# Aether-Strings — Flapping Vibrations (v5.1)

**Live in-browser demo (WebGL2):** https://virtualorganics.github.io/Aether-Strings/

---

## Medium / Vibrations Model (Concept & Goal)

Basic symmetric vibrations (oscillations) living inside a flexible 2D ground-medium. On their own, the vibrations only rock in place, back & forth, with nowhere to go, and fade out. They only keep living and start gliding when they team up with a partner, breaking the symmetry as they provide for each other a ‘solid ground’ to push off from.

They have flexible wave bodies where the middle flips opposite the wingtips, and they can ripple side-to-side if hit unevenly.

- **Orange** = squeezed, stiffer medium pushing back
- **Blue** = stretched-out medium, slipstream wake
- **Wave body:** each vibration has a 2-mode wave body (`cos(πu)` flipping spine + `sin(πu)` antisymmetric twist mode)

In the current `v5.1` sandbox, waves carve twin Left & Right blue drafting pockets with a dark dead-zone down the middle, group into trains out of a random soup, and hold steady circular loops.

> **Work-in-progress note:** `v5.1` is an early exploratory 2D test bench. Several headline behaviors (drafting thrust, survival, swarm suppression, and phase lock) still rely on heuristic neighbor rules alongside the medium solver rather than purely emergent two-way physics. See the **[Honest Audit & v6.0 Roadmap](#honest-audit-current-v51-scaffolding-vs-v60-physics-roadmap)** below for a full breakdown.

---

## Why the `cos(πu)` Wave Body Matters

Instead of treating oscillators as point dipoles or rigid chevrons, each vibration in `v5.1` has a continuous 2-mode Fourier body parameterized along its transverse span $u \in [-1, +1]$:

$$y(u, t) = A \cdot \big(q_s(t)\cos(\pi u) + q_a(t)\sin(\pi u)\big)$$

with base stroke amplitude $A = 0.34$ (plus a small cruise camber $0.06 \cdot \text{cruiseAmt} \cdot (1 - u^2)$ during forward glide). Choosing `cos(πu)` and `sin(πu)` as the modal basis gives four useful geometric properties:

1. **Exact zero-mean stroke & fore–aft parity:**
   $$\int_{-1}^{1} \cos(\pi u)\,du = 0$$
   The curve crosses zero at two nodal points ($u = \pm 0.5$) and meets flat horizontal slopes at the wingtips ($\frac{d}{du}\cos(\pi u) = -\pi\sin(\pi u) = 0$ at $u = \pm 1$). With zero static camber, an isolated symmetric oscillator obeys exact parity + half-period inversion symmetry ($y \to -y,\; t \to t + T/2$), meaning its stroke carries zero net impulse even in an inertial, nonlinear medium.
2. **Twin `[L]`/`[R]` drafting pockets & central dead-zone spine (with caveat):**
   Visually, cruising chains produce two symmetric Left and Right stretched blue slipstream pockets ($\delta\rho < 0$) separated by a dark central dead-zone spine, with no explicit dead-zone repulsion rule. *Caveat:* in `v5.1`, the Particle-to-Grid cruise splat (`P2G_FS`) also applies a wing-weighted envelope (`wingEnv`: 35% center / 100% outer wings), a lateral parting push (`fPart ∝ u`), and a constant cruise term (`+0.16`). Isolating how much of the twin-pocket structure survives from the pure `cos(πu)` stroke alone requires an ablation test (`wingEnv = 1`, `fPart = 0`).
3. **Orthogonal antisymmetric twist mode & traveling-wave chirality:**
   The second mode $\phi_a(u) = \sin(\pi u)$ is passively damped ($\zeta = 0.16$). Under symmetric loading it stays silent; when a wake or neighbor loads the left and right wings unevenly, $q_a$ rings up. Combining $(q_s, q_a)$ and their normalized velocities yields a traveling-wave chirality invariant:
   $$L_{\text{wave}} = q_s \frac{\dot{q}_a}{\omega} - \frac{\dot{q}_s}{\omega} q_a$$
   allowing lateral ripples to couple into steering torque.
4. **Exact 7-point DFT quadrature:**
   Both P2G force splatting and G2P modal projection evaluate 7 uniformly spaced span points $u_k = \frac{2(k - 3)}{7}$ ($k = 0 \dots 6$). By discrete Fourier orthogonality, `cos(πu)` has zero discrete sum and zero cross-overlap with `sin(πu)` at these quadrature nodes.

---

## Key Architecture & Medium Solver (`v5.1`)

- **Nonlinear compressible 2D viscoelastic lattice (`320 × 200` grid over `80 × 50` world, $H = 0.25$):**
  Evolves lattice displacement $\mathbf{s}$ and velocity $\mathbf{v}$, deriving local compression $\delta\rho = -\nabla \cdot \mathbf{s}$ and vorticity $\omega_z = \nabla \times \mathbf{v}$.
  - *Asymmetric nonlinear elasticity:* Compression stiffens cubically ($d + 2.5 d^2 + 16 d^3$, orange walls), while extension softens linearly ($0.85 d$, blue tension wake).
  - *Density-dependent local inertia ("heaviness"):* $\rho_{\text{eff}}(\delta\rho) = \rho_0 \cdot \text{clamp}(1 + 1.8\,\delta\rho,\, 0.30,\, 3.0)$ ($\rho_0 = 2.0$), giving higher local wave speed $c_{\text{local}} = \sqrt{(K + \mu)/\rho_{\text{eff}}}$ ($c_0 \approx 5.48\text{ c/s}$ at rest) in stretched blue wakes and heavier, stiffer response in compressed orange zones.
  - *Split irrotational vs. rotational dissipation:* Separate bulk viscosity $\nabla(\nabla \cdot \mathbf{v})$ and higher bulk damping ($\gamma_B = 2.20$) suppress longitudinal ringing, while lower shear damping ($\gamma_S = 0.75$) and rotational diffusion $\nabla^2 \mathbf{v}$ preserve shear longer.
- **Continuous Lagrangian particle state:**
  Up to `256` continuous floating-point oscillator slots (`RGBA32F` ping-pong textures, avoiding discrete grid-cell overwrite artifacts) splatting additively into `forceTex` (`EXT_float_blend` with `RGBA16F` fallback).

---

## Honest Audit: Current `v5.1` Scaffolding vs. `v6.0` Physics Roadmap

A strict code audit of `v5.1.html` separates what is currently **heuristic scaffolding** from what must be earned from first principles next:

### Known Scaffolding & Conservation Leaks in `v5.1`
- **Direct O(N²) partner perception (`BIRD_UPDATE_FS`):** `leaderCue`, `rearFollower`, `closeBlock`, and `crowdMask` read neighboring particles' positions, headings, and speeds directly. These cues currently gate roughly 70% of forward thrust (`fwdThrust`), scripted `life` sustain/decay, and side-crowding penalties—the medium modulates them rather than generating them on its own.
- **Explicit Kuramoto phase sync:** At $c_0 \approx 5.48$ (Mach $\approx 0.36$), acoustic travel delay introduces a large Doppler phase lag across tandem spacing, so `v5.1` defaults to direct neighbor phase locking (`Wingtip Phase Sync = 1.20`), leaving `Elastic Wave → Mode` at `0.00` as an experimental slider.
- **Non-reciprocal, massless body dynamics:** Particles move kinematically along their heading (`vAxial`) with speed-matching/clamping and no lateral momentum or moment of inertia. Forces splatted in `P2G_FS` (including a net-forward monopole cruise push) are computed separately from forces read in `BIRD_UPDATE_FS`, so Newton's 3rd law and total momentum/energy are not conserved.
- **Anchored displacement lattice (no advection / free vortices):** The on-site spring ($-0.04\,K\,\mathbf{s}$) and frame damping ($-\gamma\,\mathbf{v}$, further reduced in stretched blue wakes) leak momentum to a fixed background frame and relax every disturbance back to rest—meaning the current medium cannot hold persistent free vortices.

### Next Steps: Falsification Tests & `v6.0` Honest Core
1. **Ablation tests on `v5.1` (`T1–T4`):**
   - `T1`: Set `wingEnv = 1`, `fPart = 0`, and drop the constant `+0.16` cruise term to test whether the twin `[L]`/`[R]` pockets survive from `cos(πu)` alone.
   - `T2`: Zero direct neighbor perception (`leaderCue`, `rearFollower`, `closeBlock`) to measure how much motion the medium alone carries.
   - `T3`: Disable scripted `life` / `crowdChoke` penalties to test whether random soup still prefers 1D trains over 2D clumps.
   - `T4`: Add live momentum and energy budget readouts.
2. **Two-way momentum-conserving immersed bodies (`v6.0`):**
   - Give every body finite mass $m$, moment of inertia $I$, and full 2D velocity $(v_x, v_y)$ with zero direct neighbor perception (only medium coupling + short-range steric contact).
   - Use a single partition-of-unity immersed-boundary kernel (`11`–`13` span markers) with semi-implicit equal-and-opposite impulse exchange ($\Delta \mathbf{p}_{\text{body}} = -\Delta \mathbf{p}_{\text{grid}}$) so linear and angular momentum are conserved to floating-point precision.
   - Remove the anchored substrate spring and frame drag (using only momentum-conserving internal viscous stress) so partners truly have to act as *each other's* solid ground.
3. **True symmetry breaking & radiative survival:**
   - Remove static cruise camber so solo stasis holds strictly by parity + half-period symmetry.
   - Note that two identical symmetric `cos(πu)` bodies flapping **in phase (`0°`) or anti-phase (`180°`)** still have zero net center-of-mass translation by mirror symmetry below the inertial buckling threshold—so genuine pair/chain gliding comes from either a **phase lag ($\Delta\phi \neq 0, \pi$, metachronal wave)** driven by medium load feedback, or **inertial spontaneous symmetry breaking** (Vandenberghe / Alben–Shelley flapping transition).
   - Replace scripted `life` with radiative energy balance (a weakly pumped/subcritical oscillator that loses energy to outgoing waves when alone and sustains its amplitude $|q_s|$ by capturing partner wave power $\sum F_{\text{med},k} \cdot \dot{y}_k$).
4. **Open-chain radial turning & 3D knotted loops:**
   - Hunt genuine **lateral buckling (pitchfork) instabilities** on straight chains (spontaneous left/right symmetry breaking without hardcoded chirality).
   - Move to a **3D Maxwell-viscoelastic flowing medium** (elastic on short flap timescales, advecting/flowing on long timescales) so inner-loop compression can vent along the Z-axis and real circulating vortex rings, closed 3D orbits, and knotted structures can be tested honestly.

---

## Running Locally

This is a dependency-free WebGL2 browser application. Serve the directory with any static HTTP server (such as Python's built-in server) and open it in a WebGL2-capable browser:

```bash
python3 -m http.server 8789
```

Then open `http://localhost:8789/`.

---

## Version Archive

All previous milestone snapshots are preserved alongside `v5.1` and accessible directly in the browser:

| Version | Live Browser Link | File |
| :--- | :--- | :--- |
| **v5.1 (Current)** | [Open v5.1](https://virtualorganics.github.io/Aether-Strings/) ([direct](https://virtualorganics.github.io/Aether-Strings/v5.1.html)) | [`index.html`](index.html) / [`v5.1.html`](v5.1.html) |
| **v5.0** | [Open v5.0](https://virtualorganics.github.io/Aether-Strings/v5.0.html) | [`v5.0.html`](v5.0.html) |
| **v4.4** | [Open v4.4](https://virtualorganics.github.io/Aether-Strings/v4.4.html) | [`v4.4.html`](v4.4.html) |
| **v4.3** | [Open v4.3](https://virtualorganics.github.io/Aether-Strings/v4.3.html) | [`v4.3.html`](v4.3.html) |
| **v4.2** | [Open v4.2](https://virtualorganics.github.io/Aether-Strings/v4.2.html) | [`v4.2.html`](v4.2.html) |
| **v4.1** | [Open v4.1](https://virtualorganics.github.io/Aether-Strings/v4.1.html) | [`v4.1.html`](v4.1.html) |
| **v3.0** | [Open v3.0](https://virtualorganics.github.io/Aether-Strings/v3.html) | [`v3.html`](v3.html) |
| **v2.1** | [Open v2.1](https://virtualorganics.github.io/Aether-Strings/v2.1.html) | [`v2.1.html`](v2.1.html) |
| **v2.0** | [Open v2.0](https://virtualorganics.github.io/Aether-Strings/v2.html) | [`v2.html`](v2.html) |
| **Stage 1** | [Open Stage 1](https://virtualorganics.github.io/Aether-Strings/stage1-milestone.html) | [`stage1-milestone.html`](stage1-milestone.html) |

---

## License & Attribution

Released under the [MIT License](LICENSE) — free to explore, fork, port (e.g., to 3D, WebGPU, or CUDA), and build upon with attribution.

If you build on this model, reference the `cos(πu)` wave-body formulation, or extend it toward 3D loops/knots, please credit:

> **Michel Van de gaer (VirtualOrganics)** — *Aether-Strings: Flapping Vibrations (v5.1)*, 2026.  
> https://github.com/VirtualOrganics/Aether-Strings
