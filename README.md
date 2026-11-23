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
