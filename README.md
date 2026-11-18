# PaddyScout Mission Simulator

Interactive simulator for the PaddyScout research question: **when does an onboard AI accelerator (Coral, Hailo-8, KV260) improve UAV crop-disease scouting per unit of mission energy?**

It follows the chain from the research blueprint:
lesion size → GSD → tiles per frame → processor throughput → allowable survey speed → dropped frames → lesion recall → Wh/ha.

> **Provenance:** every output is **SIMULATION**. All default inputs are placeholders until measured. The header badge shows how many inputs have been marked as measured.

## Run it

- **Online:** open the GitHub Pages link for this repo (see below). `index.html` is the project landing page with the simulator embedded; `simulator.html` is the simulator on its own.
- **Edit the landing page:** your names, affiliation and links go in the `SITE` block at the bottom of `index.html`. Update `measuredCount` and each input's `status` as you measure.
- **Offline (for presentations):** download Three.js r128 from
  `https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js`
  and save it as `vendor/three.min.js`. The page uses the CDN copy when online and falls back to the local copy offline. Fonts fall back to system fonts.

## Publish on GitHub Pages

1. Create a new repository on GitHub, e.g. `paddyscout-sim`.
2. Upload the contents of this folder (`index.html`, `simulator.html`, `README.md`, `inputs/`, `vendor/`).
3. Go to **Settings → Pages**, set **Source** to *Deploy from a branch*, branch `main`, folder `/ (root)`, and save.
4. After a minute the simulator is live at `https://<your-username>.github.io/paddyscout-sim/`.

## What it models

| Part | Model |
|---|---|
| Geometry | GSD = h·p/f per photo, using the altitude actually held; footprint = sensor pixels × GSD |
| Motion blur | ground smear = speed × exposure, combined with GSD along track |
| Workload | tiles per frame from eq. 3 of the blueprint; *native* or *target-resolution* tiling |
| Compute | time per frame = overhead + tile work ÷ tiles/s, lognormal jitter from p95/p50; 3-frame buffer, drops when full |
| Flight | trapezoidal acceleration on lanes and lane changes; altitude-hold error as a correlated random walk |
| Detection | measured recall-vs-pixels curve if provided, else model recall × logistic curve |
| Power | rotor momentum theory + parasite drag ÷ motor efficiency, airspeed including wind; calibrate with one measured hover power |
| Uncertainty | Monte Carlo over disease fields, altitude-error traces and latency jitter → mean ± 95% CI |

The discrete-event simulation and the closed-form model agree to within about 2 points of recall and 1% of energy across all four processors.

## Inputs

`inputs/defaults.json` holds the placeholder inputs. To use measured values:

1. Edit the tables at the bottom of the page (or edit the JSON), and tick **measured** for each value you replace.
2. Click **Show current inputs**, copy the JSON, and save it as e.g. `inputs/pi5-coral-2026-12.json`.
3. Commit it, so every figure can be regenerated from a named inputs file.

## Not modelled (yet)

Terrain relief, camera tilt, lighting variation, gusts, plant occlusion, and pipelining of overhead with inference. These are the targets of the planned Gazebo + PX4 software-in-the-loop validation.

## Related

- Research blueprint: *PaddyScout — Hardware-Aware, Accuracy-Constrained UAV Crop Scouting*, v2.0 (16 Nov 2026).
- Measurement checklist: what to measure to replace each placeholder.
