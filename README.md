# Aether-Strings — Flapping Vibrations (v7.1 Arch-Wave Milestone)

**Live in-browser demo (WebGL2):** https://virtualorganics.github.io/Aether-Strings/

---

## New Direction (`v7.1`): Simplifying the Cosine-Wave into an Arch-Wave

Earlier versions (`v1`–`v5.1`) modeled each vibration as a 1.5-period `cos(πu)` flapping string where the center flipped opposite the wingtips on an oscillator clock. Starting with **`v7.1`**, we simplify the baseline unit into an **Arch-Wave**—a single tightly packed group of particles inside a mega-dense, stiff elastic medium that dents the medium deeply, hits maximum compression, and rebounds back and forth without a hardcoded frequency clock:

![Simplifying the previous Cosine-Wave into an Arch-Wave](assets/arch-wave-cycle.png)

**Simplifying the previous Cosine-Wave into an Arch-Wave:**

- **`t₀` – Forward Push (North):** Tightly packed particles move as a unit, denting and compressing the medium to the North.
- **`t₁` – Stagnation & Containment:** At max compression, the medium pushes back and curves around the sides like tree bark, squeezing the group tight as forward motion stalls.
- **`t₂` – Rebound:** Built-up tension pushes the group backward toward the South while the medium guides its edges.
- **`t'₀` – Opposite Compression (South):** The group pushes into the opposite side, compressing the medium to the South before the cycle repeats.

---

## Medium / Vibrations Model (`v7.1` Architecture)

`v7.1` returns to the conceptual foundation of **`v5.1`**—a **stiff elastic displacement lattice** representing a vast, mega-dense vibrating medium—while upgrading the scale, replacing the `1.5` cosine wave with the clock-free Arch-Wave, removing direct neighbor-perception crutches, and eliminating curve overlaps:

- **High-resolution stiff elastic displacement lattice (`480 × 300` over a `120 × 75` world, `H = 0.25`):**
  Evolves lattice displacement $\mathbf{s}(x,y)$ and velocity $\mathbf{v}(x,y)$ via staggered-face Cauchy stress with nonlinear cubic hard-packing compression ($d + 2.8 d^2 + 24 d^3$ for $\delta\rho \ge 0$, softened $0.82 d$ in tension), shear rigidity $\mu$, density-dependent heaviness $\rho_{\text{eff}}(\delta\rho)$, and live deformed lattice lines that visibly pinch ahead of each arch and curve around its flanks like tree bark.
  - **Orange** = compressed, stiffer medium pushing back ($\delta\rho > 0$)
  - **Blue / Cyan** = stretched-out tension wake ($\delta\rho < 0$)
  - **Deforming lattice grid** = elastic shear & tree-bark wrapping around active arches
- **Compact Arch-Wave scale (`256` continuous slots, `L = 0.85`):**
  Each arch-string spans `1.70` world units (`1.4%` of the `120`-unit arena width), preserving the sense of small localized coherent groups emerging inside a vast, fine-grained elastic medium (`180+` active arches with spontaneous emergence in `Pure Soup`).
- **Clock-free 4-phase compression–rebound (`t₀ → t₁ → t₂ → t'₀`):**
  Replaces the `cos(πu)` stroke and fixed `-ω₀² q` frequency clock with a single zero-mean arch dome $\phi_{\text{bow}}(u) = \cos(\frac{\pi}{2}u) - 0.6388$ whose curvature bends dynamically (`∩ ↔ ∪`) with motion and local lattice compression. Rebound timing emerges from the medium's bulk stiffness $K$ and nonlinear compression wall rather than a hardcoded frequency.
- **Exact `9 × 9` curve-to-curve exclusion:**
  Marker-to-marker hard-core repulsion and contact rebound prevent arch-strings from intersecting or ghost-overlapping at any collision angle (`Crossed 90°`, `Head-On`, `Closed Ring`, and `Pure Soup`).
- **Zero `leaderCue` / `rearFollower` / `fCruise` perception crutches:**
  Steering and multi-arch coupling act through local lattice pressure gradients, curl, and direct curve contact.

> **Work-in-progress note:** `v7.1` establishes the new **Arch-Wave** visual and conceptual baseline on top of the stiff displacement lattice. Deeper physical refinement—including strict continuum momentum/energy conservation, natural symmetry-breaking pair/chain gliding from pure two-way stress transfer, and lifting to 3D loops and knots—is left for the next research milestones.

---

## Development History: From `v5.1` to `v7.1`

Every intermediate experiment is preserved in the live version archive so behaviors can be compared directly in the browser:

1. **`v5.1` (`v5.1.html`) — 1.5-Sine Cosine Sandbox:** Established the stiff elastic displacement lattice, twin `[L]`/`[R]` drafting wakes, chains, closed rings, and soup dynamics using a 2-mode Fourier body $y(u,t) = A(q_s\cos(\pi u) + q_a\sin(\pi u))$.
2. **`v5.1` Ablation Suite (`v5.1-ablations.html`) — Falsification Tests `T1–T4`:** Quantified which `v5.1` behaviors came from the medium versus heuristic scaffolding (`leaderCue`, `rearFollower`, `fCruise`, scripted `life`/`crowdMask`), proving that medium-reading steering works once wakes exist while `fCruise` generated most of `v5.1`'s wake.
3. **`v6.0` & `v6.1` (`v6.0.html`, `v6.1.html`) — Momentum-Conserving Viscoelastic Core:** Tested an unanchored finite-volume Jaumann–Maxwell continuum with exact Peskin $\Delta\mathbf{p}_{\text{body}} = -\Delta\mathbf{p}_{\text{med}}$ exchange (`|ΔP_tot| ~ 1e-5`), but the Eulerian fluid-like formulation softened the medium, allowed curves to overlap in `v6.1`, and still used the artificial `cos(πu)` clock flip-flop.
4. **`v7.0` (`v7.0.html`) — First Arch-Wave Prototype on the Fluid Core:** Replaced `cos(πu)` with a curved bow and `13 × 13` exclusion on top of `v6.1`, demonstrating clean curve separation and emergent stiffness-dependent turnaround, but exposed that the `v6` fluid grid damped isolated pushes dead and bloated the wave scale relative to the medium.
5. **`v7.1` (`index.html` / `v7.1.html`) — Current Arch-Wave Milestone:** Returned to `v5.1`'s stiff elastic displacement lattice at `480 × 300` (`120 × 75`), restored fine-grained scale, and integrated the clock-free 4-phase Arch-Wave cycle with `9 × 9` curve exclusion.

---

## Running Locally

This is a dependency-free WebGL2 browser application. Serve the directory with any static HTTP server (such as Python's built-in server) and open it in a WebGL2-capable browser:

```bash
python3 -m http.server 8791
```

Then open `http://localhost:8791/`.

---

## Version Archive

All milestone snapshots are preserved alongside `v7.1` and accessible directly in the browser (and via the sidebar version links inside the app):

| Version | Milestone Summary | Live Browser Link | File |
| :--- | :--- | :--- | :--- |
| **v7.1 (Current)** | Stiff elastic lattice (`480×300`) + 4-phase Arch-Wave baseline + `9×9` no-overlap | [Open v7.1](https://virtualorganics.github.io/Aether-Strings/) ([direct](https://virtualorganics.github.io/Aether-Strings/v7.1.html)) | [`index.html`](index.html) / [`v7.1.html`](v7.1.html) |
| **v7.0** | First Arch-Wave + `13×13` exclusion test on unanchored Jaumann–Maxwell core | [Open v7.0](https://virtualorganics.github.io/Aether-Strings/v7.0.html) | [`v7.0.html`](v7.0.html) |
| **v6.1** | Stiff Jaumann–Maxwell core + `∇·σ` stress-gradient modal sensing | [Open v6.1](https://virtualorganics.github.io/Aether-Strings/v6.1.html) | [`v6.1.html`](v6.1.html) |
| **v6.0** | First unanchored momentum-conserving viscoelastic core (`13`-marker Peskin) | [Open v6.0](https://virtualorganics.github.io/Aether-Strings/v6.0.html) | [`v6.0.html`](v6.0.html) |
| **v5.1 Ablations** | `T1–T4` falsification & conservation telemetry suite for `v5.1` | [Open v5.1 Ablations](https://virtualorganics.github.io/Aether-Strings/v5.1-ablations.html) | [`v5.1-ablations.html`](v5.1-ablations.html) |
| **v5.1** | Modal-wavepacket `1.5`-sine Fourier body + `320×200` stiff viscoelastic lattice | [Open v5.1](https://virtualorganics.github.io/Aether-Strings/v5.1.html) | [`v5.1.html`](v5.1.html) |
| **v5.0** | Early continuous `1.5`-sine Fourier modal body | [Open v5.0](https://virtualorganics.github.io/Aether-Strings/v5.0.html) | [`v5.0.html`](v5.0.html) |
| **v4.4** | Continuous lattice & drafting iterations | [Open v4.4](https://virtualorganics.github.io/Aether-Strings/v4.4.html) | [`v4.4.html`](v4.4.html) |
| **v4.3** | Lattice coupling iteration | [Open v4.3](https://virtualorganics.github.io/Aether-Strings/v4.3.html) | [`v4.3.html`](v4.3.html) |
| **v4.2** | Lattice coupling iteration | [Open v4.2](https://virtualorganics.github.io/Aether-Strings/v4.2.html) | [`v4.2.html`](v4.2.html) |
| **v4.1** | Lattice coupling iteration | [Open v4.1](https://virtualorganics.github.io/Aether-Strings/v4.1.html) | [`v4.1.html`](v4.1.html) |
| **v3.0** | Early flapping-wave sandbox | [Open v3.0](https://virtualorganics.github.io/Aether-Strings/v3.html) | [`v3.html`](v3.html) |
| **v2.1** | Early prototype iteration | [Open v2.1](https://virtualorganics.github.io/Aether-Strings/v2.1.html) | [`v2.1.html`](v2.1.html) |
| **v2.0** | Early prototype iteration | [Open v2.0](https://virtualorganics.github.io/Aether-Strings/v2.html) | [`v2.html`](v2.html) |
| **Stage 1** | Initial milestone prototype | [Open Stage 1](https://virtualorganics.github.io/Aether-Strings/stage1-milestone.html) | [`stage1-milestone.html`](stage1-milestone.html) |

---

## License & Attribution

Released under the [MIT License](LICENSE) — free to explore, fork, port (e.g., to 3D, WebGPU, or CUDA), and build upon with attribution.

If you build on this model, reference the Arch-Wave or `cos(πu)` formulations, or extend it toward 3D loops/knots, please credit:

> **Michel Van de gaer (VirtualOrganics)** — *Aether-Strings: Flapping Vibrations (v7.1 Arch-Wave Milestone)*, 2026.  
> https://github.com/VirtualOrganics/Aether-Strings
