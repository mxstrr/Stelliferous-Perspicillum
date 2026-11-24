# 🌌 Stelliferous Perspicillum: A Browser-Resident Galactic Rendering Apparatus ✨

> 🔭 *An exegesis upon the procedural conjuration, gravitational choreography, and phosphorescent rasterization of a spiral nebulous agglomeration, rendered within the ephemeral confines of a hypertext viewport.*

---

## 📜 Preamble Concerning Teleological Aspirations

The present treatise adumbrates the architectural scaffolding of a **galaxy renderer** whose ontological habitat is the browser. 🌐 Its raison d'être is the ostentation of several hundred thousand luminous point-sources, orchestrated in a logarithmic-spiral configuration, at an interactive cadence on commodity silicon. 💎

The perspicuous (if prosaic) summary: it draws a lot of dots that look like a galaxy. 🪐

---

## 🧭 Taxonomy of Constituent Strata

| 🏛️ Stratum | 🔮 Responsibility | 🛠️ Mechanism |
|---|---|---|
| Genesis Locus | Stochastic stellar parturition | Seeded pseudorandom generator in a Web Worker |
| Kinematic Locus | Orbital perambulation | Vertex shader evaluating angular velocity per radius |
| Luminary Locus | Chromatic and photometric assignment | Planckian locus approximation sampled from a lookup texture |
| Compositing Locus | Incandescent bloom | Additive blending, then separable Gaussian convolution |
| Contemplative Locus | Observer perambulation | Orbit camera with inertial damping |

---

## 🌀 Morphogenetic Prescription of the Stellar Population

### 🧬 Tripartite Anatomical Decomposition

A galaxy is apportioned into three structural constituents, each sampled from a distinct probability density:

1. **🟡 The Bulge**: a spheroidal, centrally condensed agglomeration. Radii are drawn from a Gaussian kernel, then flattened along the polar axis by a scalar coefficient below unity.
2. **🔵 The Disc**: radial distance follows an exponential law, `r = -h * ln(1 - u)` with `u` uniform on `[0, 1)`, where `h` is the characteristic scale length. Vertical displacement follows a Laplacian distribution so that the disc thins toward its periphery.
3. **⚪ The Halo**: a tenuous, quasi-spherical diaspora of ancient stars, sparsely populated and tinted toward amber.

### 🌪️ Spiral Arm Imposition

Each disc star is first assigned a radius, then an azimuth consistent with a logarithmic spiral:

```
theta = (1 / tan(pitch)) * ln(r / r0) + (2 * pi * k / N) + sigma(r) * gaussian()
```

- `pitch` is the pitch angle of the arms.
- `k` indexes one of `N` arms.
- `sigma(r)` is a dispersion term that widens the arms with distance, producing the diffuse, feathered margins that distinguish a convincing spiral from a rigid pinwheel. 🎡

---

## ⚙️ Kinematic Perambulation Without Per-Frame Mutation

Stellar positions are never rewritten on the CPU after genesis. 🚫🧮 Instead, every star retains its immutable `(r, theta0, z, seed)` tuple in a static vertex buffer, and the vertex shader computes the instantaneous azimuth:

```
theta(t) = theta0 + omega(r) * t
```

where `omega(r)` decays with radius, approximating a flat-ish rotation curve. 🎠 Differential rotation then winds the arms over time, and a gentle counter-modulation prevents the pattern from tightening into an illegible coil. The expense is one `sin` and one `cos` per vertex, and zero bus traffic per frame. 🚀

---

## 🎨 Chromatic Provenance and Photometric Valuation

- 🌡️ Each star receives a temperature, drawn from a skewed distribution biased toward cool, dim dwarfs, with a rare hot tail.
- 🖌️ Temperature indexes a 256-texel one-dimensional lookup texture encoding blackbody colour from deep ember through white to pale cerulean.
- 💡 Point size and intensity scale with a pseudo-luminosity term, so that scarce hot giants dominate the glimmer while the multitudinous dwarfs contribute a cumulative haze.
- 🌫️ Dust lanes are rendered as a second, subtractive pass: translucent dark quads aligned with the inner edge of each arm, modulated by a tileable noise texture.

---

## 🖼️ Rasterization Pipeline

```
 ┌────────────┐   ┌──────────────┐   ┌───────────────┐   ┌──────────────┐
 │ 🧪 Worker  │──▶│ 📦 Vertex    │──▶│ 🌟 HDR Float  │──▶│ 💫 Bloom     │
 │ Genesis    │   │ Buffer (GPU) │   │ Framebuffer   │   │ + Tonemap    │
 └────────────┘   └──────────────┘   └───────────────┘   └──────────────┘
```

1. **Instanced billboards or `gl.POINTS`**: points are the economical default; quads are reserved for stars exceeding a size threshold, so that diffraction spikes can be painted upon the brightest few. ✴️
2. **Additive blending** (`gl.ONE, gl.ONE`) into a half-float render target, permitting overlapping stars to accumulate beyond unity without clipping.
3. **Bloom**: downsample a mip chain, apply a separable Gaussian blur at each level, and recombine. 🌈
4. **Tonemapping**: an ACES-flavoured filmic curve compresses the high-dynamic-range accumulation into displayable range.

WebGL2 is the baseline target. 🎯 A WebGPU backend behind a common interface is an anticipated refinement, chiefly so that generation can migrate into a compute shader and obviate the worker altogether.

---

## 🔭 Observer Instrumentation

- 🖱️ Orbit, dolly, and pan, each smoothed by exponential damping so that release of the pointer produces a graceful deceleration rather than an abrupt cessation.
- 🎚️ A small control panel exposes star count, arm count, pitch angle, bulge radius, and bloom strength.
- 📱 Star count defaults adaptively: a short calibration burst at startup measures frame duration and selects a population tier accordingly.

---

## 🧱 Anticipated Directory Topology

```
/src
  /gen        🧬 stellar density sampling, seeded RNG, worker entry
  /gl         🖥️ context, program, buffer, framebuffer wrappers
  /shaders    🎭 star.vert, star.frag, dust.frag, bloom.frag, tonemap.frag
  /camera     🎥 orbit controller with inertia
  /ui         🎛️ parameter panel
  main.ts     🚪 composition root
```

---

## 🧪 Verification Stratagems

- ✅ Seeded generation must produce byte-identical buffers for identical seeds, asserted in a unit test.
- 📸 Reference screenshots at fixed seeds and fixed time are compared against golden images within a perceptual tolerance.
- ⏱️ Frame time is recorded through `EXT_disjoint_timer_query_webgl2` where available, and reported per pass.

---

## 🗺️ Prospective Elaborations

- 🪐 Level-of-detail instancing that resolves a selected region into individual star systems upon close approach.
- 🌠 Satellite dwarf galaxies on eccentric orbits.
- 🕳️ A central supermassive attractor with gravitational lensing, implemented as a screen-space radial distortion.
- 🎶 Sonification of rotation velocity, for those who prefer their cosmos audible.

---

*🌌 Finis. May your fragment shaders compile on the first attempt.* 🙏
