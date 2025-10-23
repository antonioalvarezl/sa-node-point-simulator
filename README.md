# Semi-autonomous Neural ODE Point Flow Simulator

Interactive demo to explore the evolution of **point clouds** under a **multi-neuron semi-autonomous ReLU Neural ODE**, with **time-dependent hyperplanes**, **free / interpolation / classification** modes, and simple **separability** and **cross-entropy** metrics.

**Live:** https://antonioalvarezl.github.io/relu-point-simulator/semi-autonomous
**Code:** `index_semi.html`
**License:** MIT

## Inspiration

This simulator is inspired by recent work on Neural ODEs viewed through control and architectural complexity:

- **Ziqian Li, Kang Liu, Lorenzo Liverani & Enrique Zuazua (2025)**. *Universal Approximation of Dynamical Systems by Semi-Autonomous Neural ODEs and Applications*. **Preprint**. https://arxiv.org/abs/2407.17092
- **Álvarez-López, Hadj Slimane & Zuazua (2024)**. *Interplay between depth and width for interpolation in neural ODEs*. **Neural Networks**, 180, 106640. https://doi.org/10.1016/j.neunet.2024.106640  
- **Ruiz-Balet & Zuazua (2023)**. *Neural ODE Control for Classification, Approximation, and Transport*. **SIAM Review**, 65(3), 735–773. https://doi.org/10.1137/21M1411433

## Overview

We simulate the continuous-time model
\[
\dot x(t) = \sum_{i=1}^{p} w_i \, \big(a_i^\top x(t) + b_i t + c_i\big)_+,
\]
with \(x\in\mathbb{R}^2\) and **p neurons** acting simultaneously. Each neuron contributes a flow with:
- Direction vector \(w_i\) (canonical)
- Hyperplane normal \(a_i\) (canonical)
- Hyperplane velocity \(b_i\) (the hyperplane moves with time)
- Initial offset \(c_i\)

The hyperplane for neuron \(i\) is given by \(a_i^\top x + b_i t + c_i = 0\), which **moves linearly** in time. Integration uses explicit Euler with 100 sub-steps.

**Modes**
- **Free:** inspect the vector field and trajectories with moving hyperplanes.
- **Interpolation:** track average point-to-target distance.
- **Classification:** drive classes into **horizontal bands** (separability) or minimize a simple **cross-entropy** built from band-center logits.

## Quick start

- **Online:** open the *Live* link when available.  
- **Local:** clone/download and open `index_semi.html` in your browser. No build needed.

## Controls & Options

- **Mode:** Free / Interpolation / Classification.  
- **Population:** number of points (1–60); uniform or per-class Gaussian sampling.  
- **Neurons (p):** add/remove neurons; each neuron has sliders/inputs for \(w_1, w_2, a_1, a_2, b_i, c_i\).  
- **Global time:** slider \([0, 10]\) seconds with **Play/Pause** and **Reset**.  
- **Visualization:** point size; vector-field density/opacity; activation hyperplanes \(a_i^\top x + b_i t + c_i = 0\) (active neuron shown darker/thicker).  
- **Interaction:** mouse wheel to zoom; drag to pan; **Shift+drag** to move points; save **PNG** or record a **WebM** of the evolution.

The **metrics** panel shows:
- **Interpolation:** mean distance to targets.  
- **Classification:**  
  - *Separability*: share of points inside their class band \(y\in[\frac{k}{M},\frac{k+1}{M})\).  
  - *Cross-entropy*: softmax loss using logits \(-|y-y_c|\) with band centers \(y_c=(k+\tfrac12)/M\).

## Mathematical background

Each neuron defines a **moving half-space** \(H_i^+(t)=\{x:\ a_i^\top x + b_i t + c_i > 0\}\) and \(H_i^-(t)=\{x:\ a_i^\top x + b_i t + c_i \le 0\}\).

- In \(H_i^-(t)\): neuron \(i\) contributes **zero** to the velocity field.  
- In \(H_i^+(t)\): neuron \(i\) contributes \(w_i \cdot \max(0, a_i^\top x + b_i t + c_i)\) to the velocity.

The **total velocity** at position \(x\) and time \(t\) is the **sum** of all neuron contributions:
\[
\dot x = \sum_{i=1}^{p} w_i \, (a_i^\top x + b_i t + c_i)_+
\]

As time evolves, the hyperplanes move (if \(b_i \neq 0\)), creating a **semi-autonomous** system where the active/inactive regions change dynamically.

In **classification by separability**, the final \(y\) coordinate is compared with equispaced horizontal bands. In **cross-entropy**, logits come from vertical proximity to band centers.

## UI notes

- **Canvas:** scatter plot (color by class), multiple shaded inactive regions (one per neuron where \(a_i^\top x + b_i t + c_i < 0\)), and vector-field arrows showing the **summed** contribution of all neurons.  
- **Neurons panel:** tabbed *Neuron i* sections with synchronized sliders and numeric inputs. The active neuron's hyperplane is drawn darker and thicker.  
- **Recording:** exports **WebM** (the UI shows an `ffmpeg` command to convert to MP4).  
- **Performance:** high point counts or many neurons can reduce FPS; tune density/opacity or reduce neuron count.

## Key differences from piecewise-constant simulator

- **No sequential steps:** all \(p\) neurons act **simultaneously** at every time instant.
- **Moving hyperplanes:** each hyperplane \(a_i^\top x + b_i t + c_i = 0\) evolves linearly in time with velocity \(b_i\).
- **Parameter \(c_i\):** initial offset for each hyperplane, in addition to the time-dependent term \(b_i t\).
- **Longer time horizon:** evolution from 0 to 10 seconds (instead of normalized [0,1] split across steps).
- **Fewer particles:** maximum 60 points (instead of 500) to maintain performance with multiple simultaneous neurons.

## Educational uses

- **Students:** understand how **multiple ReLU units** combine and how **time-varying** boundaries affect trajectories.  
- **Researchers:** explore the role of neuron count (\(p\)) and moving hyperplanes in interpolation/classification tasks.  
- **Instructors:** demonstrate semi-autonomous control systems and the difference between sequential composition (depth) and simultaneous action (width).

## Code (single file)

Everything lives in one HTML file (UI + canvas + logic), using the 2D Canvas API, `MediaRecorder` for video, and a simple Euler integrator. Use the `index_semi.html` provided in this repository and open it directly in a browser.

## Citation

If this demo supports your teaching or research, please cite:
```bibtex
