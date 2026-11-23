# Aether-Strings — Flapping Vibrations (v5.1)

**Live in-browser demo (WebGL2):** https://virtualorganics.github.io/Aether-Strings/

---

## Medium / Vibrations Model

Basic symmetric vibrations (oscillations) living inside a flexible 2D ground-medium. On their own, the vibrations only rock in place, back & forth, with nowhere to go, and fade out. They only keep living and start gliding when they team up with a partner, breaking the symmetry as they provide for each other a ‘solid ground’ to push off from.

They have flexible wave bodies where the middle flips opposite the wingtips, and they can ripple side-to-side if hit unevenly.

- **Orange** = squeezed, stiffer medium pushing back
- **Blue** = stretched-out medium, slipstream wake
- **Wave body:** each vibration has a real 2-mode wave body (`cos(πu)` flipping spine + `sin(πu)` antisymmetric twist mode)

The waves naturally carve out twin Left & Right blue drafting pockets with a dark dead-zone right down the middle, can group themselves into trains out of a random soup of vibrations, and hold steady circular loops.

*Still WIP.*

---

## Why the `cos(πu)` Wave Body Matters

Instead of treating swimmers/oscillators as point dipoles or rigid chevrons, each vibration in `v5.1` has a continuous 2-mode Fourier body parameterized along its transverse span $u \in [-1, +1]$:

$$y(u, t) = A \cdot \big(q_s(t)\cos(\pi u) + q_a(t)\sin(\pi u)\big)$$

with base stroke amplitude $A = 0.34$ (plus a small cruise camber $0.06 \cdot \text{cruiseAmt} \cdot (1 - u^2)$ during forward glide). Choosing `cos(πu)` and `sin(πu)` as the modal basis gives four useful physical properties directly from the geometry:

1. **Exact zero-mean self-cancellation:**
   $$\int_{-1}^{1} \cos(\pi u)\,du = 0$$
   The curve crosses zero at two nodal points ($u = \pm 0.5$) and meets flat horizontal slopes at the wingtips ($\frac{d}{du}\cos(\pi u) = -\pi\sin(\pi u) = 0$ at $u = \pm 1$). Because the central spine moves strictly anti-phase to the outer wings, an isolated symmetric oscillator pumps zero net impulse into the lattice, experiences zero self-propulsion, and fades away in place.
2. **Emergent twin `[L]`/`[R]` drafting pockets & central dead-zone spine:**
   As a coupled oscillator glides and pumps the ground-medium with its anti-phase center and outer lobes, it organically carves **two symmetric Left and Right stretched blue slipstream pockets ($\delta\rho < 0$) separated by a sharp dark dead-zone spine** down the centerline—with no hardcoded dead-zone forces.
3. **Orthogonal antisymmetric twist mode & traveling-wave chirality:**
   The second mode $\phi_a(u) = \sin(\pi u)$ is passively damped ($\zeta = 0.16$). Under symmetric loading it stays silent; when a wake or neighbor loads the left and right wings unevenly, $q_a$ rings up. Combining $(q_s, q_a)$ and their normalized velocities produces a traveling-wave angular momentum / chirality invariant:
   $$L_{\text{wave}} = q_s \frac{\dot{q}_a}{\omega} - \frac{\dot{q}_s}{\omega} q_a$$
   allowing lateral ripples to feed directly back into steering torque.
4. **Exact 7-point DFT quadrature:**
   Both Particle-to-Grid (P2G) force splatting and Grid-to-Particle (G2P) Galerkin projection evaluate 7 uniformly spaced span points $u_k = \frac{2(k - 3)}{7}$ ($k = 0 \dots 6$). By discrete Fourier orthogonality, `cos(πu)` has zero discrete sum and zero cross-overlap with `sin(πu)` at these quadrature nodes—eliminating spurious DC drift from discrete sampling on the GPU.

---

## Key Architecture & Physics Ideas (`v5.1`)

- **Nonlinear compressible 2D viscoelastic ground-medium (`320 × 200` grid over `80 × 50` world, $H = 0.25$):**
  Evolves lattice displacement $\mathbf{s}$ and velocity $\mathbf{v}$, computing local compression $\delta\rho = -\nabla \cdot \mathbf{s}$ and vorticity $\omega_z = \nabla \times \mathbf{v}$.
  - *Asymmetric nonlinear elasticity:* Compression stiffens cubically ($d + 2.5 d^2 + 16 d^3$, rendered orange), while extension softens linearly ($0.85 d$, rendered blue).
  - *Density-dependent local inertia ("heaviness"):* $\rho_{\text{eff}}(\delta\rho) = \rho_0 \cdot \text{clamp}(1 + 1.8\,\delta\rho,\, 0.30,\, 3.0)$ (with rest $\rho_0 = 2.0$), making stretched blue slipstream regions lighter with faster local wave speed $c_{\text{local}} = \sqrt{(K + \mu)/\rho_{\text{eff}}}$ ($c_0 \approx 5.48\text{ c/s}$ at rest) and compressed orange regions heavier and stiffer.
  - *Split irrotational vs. rotational dissipation:* Separate bulk viscosity $\nabla(\nabla \cdot \mathbf{v})$ and higher bulk damping ($\gamma_B = 2.20$) damp high-frequency acoustic ringing, while lower shear damping ($\gamma_S = 0.75$) and rotational diffusion $\nabla^2 \mathbf{v}$ let wake shear persist longer.
- **Continuous Lagrangian particle state + additive P2G / APIC coupling:**
  Up to `256` continuous floating-point oscillator slots (`RGBA32F` ping-pong textures, eliminating grid-cell overwrite artifacts) couple to the Eulerian lattice via instanced additive Gaussian force splatting (`EXT_float_blend` with `RGBA16F` fallback) and APIC-style linear-velocity transfer $v_{\text{local}}(u) = v_{\text{axial}} - \omega_{\text{body}}\,u\,L$.
- **Symmetry breaking & energy budget:**
  $q_s$ is driven by a Van der Pol limit-cycle oscillator ($f = 2.4\text{ Hz}$). Isolated vibrations remain symmetric with zero net drift and decay; partner blockage and wake asymmetry sustain both life and propulsion.

### Current Caveats & Open Frontiers (WIP)

- **Doppler phase lag vs. wingtip sync:** At cruise speeds relative to the default elastic wave speed ($c_0 \approx 5.48$, Mach $\approx 0.36$), finite acoustic travel lag introduces a substantial Doppler phase delay across tandem spacing—so `v5.1` defaults to symmetric `Wingtip Phase Sync` (`1.20`) while keeping `Elastic Wave → Mode` (`0.00` default) exposed as a live slider for sub-wavelength experiments (lower frequency / stiffer $K$).
- **Next research targets:**
  1. **Sustained radial turning on open chains:** keeping followers latched onto one `[L]` or `[R]` slipstream pocket to carve self-sustaining curved arcs via wingtip shear vorticity and traveling-wave chirality rather than straightening back onto the leader's centerline.
  2. **Pure wave-mediated phase locking** in the sub-wavelength regime.
  3. **3D viscoelastic ground-medium & knots:** lifting the lattice to 3D so compression trapped inside a curved loop can vent along the Z-axis, opening the door to self-sustaining 3D toroidal vortices and knotted loops.

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
