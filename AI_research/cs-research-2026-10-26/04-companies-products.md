# 04. Companies and products that actually do it

Who uses sim-to-real, image-built digital twins and world models in real products, as of 2026-10-26.
Grades:
- **B**: a measured result with an independent check, or a peer-reviewed paper.
- **C**: the company's own claim (`-coi`).
- **D**: press or second-hand.

Most industry material is C: companies show features and demos, rarely measurements. Say "the company says" on slides.

## Top 5 for slides

| # | Who | Why it fits block 4 | Grade |
|---|---|---|---|
| 1 | **TU Delft MAVLab, "MonoRace"**, winner of A2RL 2025 (Abu Dhabi), a university team | The cleanest drone sim-to-real story after Swift, and independent of Swift's lab:<br>• one camera;<br>• control learned in a simple simulator with randomized physics, then moved to the real drone with no extra tuning;<br>• gate detector trained on "3500:500 synthetic to real" images;<br>• beat 3 champion pilots in an official race, top speed 28.23 m/s (~100 km/h). | win B; method C (own preprint) |
| 2 | **Wayve** (UK, self-driving): PRISM-1 → GAIA-2 → GAIA-3 | One company, two block-4 techniques:<br>• PRISM-1 rebuilds street scenes in 4D from **camera video only**;<br>• GAIA-2 and GAIA-3 are generative world models used to test the driving AI ("what if" re-drives, rare scenarios). | C-coi |
| 3 | **Waymo World Model** (Feb 2026, built on DeepMind's Genie 3) | A generative simulator for rare events (tornado, elephant on the road), and it can turn a dashcam or phone video into a simulation. Waymo also uses lidar, so use it only as the world-model example. | C-coi, no benchmarks |
| 4 | **DJI Terra 5.0 / Pix4D** with Gaussian splatting | **The drone films a place, the software builds a photoreal 3D twin.** This is the most direct "photos → twin" product link to drones. | C-coi |
| 5 | **NVIDIA Isaac Sim + Omniverse NuRec / Cosmos** | The "photos → Gaussian splats → robot simulator" pipeline as a product. NVIDIA's tutorial uses about 100 overlapping photos (or a smartphone video) → COLMAP → 3DGUT → Isaac Sim, and says the scene has "no inherent collision properties". | C-coi |

Consumer example everyone knows: **Google Maps Immersive View** (indoor views of restaurants built from photos with NeRF, Google Research blog 2023-06-14) and phone apps such as Luma, Polycam and Scaniverse that turn a phone video into a splat.

## Full table

| Company | Product | What it does | Technique | Evidence | Date | Grade | Figure |
|---|---|---|---|---|---|---|---|
| TU Delft MAVLab | MonoRace | Autonomous racing drone, one rolling-shutter camera (IMX219) + IMU, all onboard | RL (PPO) in a custom simulator with **dynamics randomization** ("most parameters are randomized ≥40%"); gate detector (GateNet U-Net) trained on synthetic + hand-labelled real images; the policy sees 24 state numbers, **not images** | [arXiv 2601.15222](https://arxiv.org/abs/2601.15222); [MAVLab post](https://mavlab.tudelft.nl/dronerace-defeating-champions/); [ESA story](https://www.esa.int/Enabling_Support/Space_Engineering_Technology/From_ESA_lab_to_a_historic_drone_race_win) | race April 2025; paper 2026-01-21 | win B; training C | best time 16.56 s; 28.23 m/s; 88.4% success over 43 flights (final model); real lap times "closely match" sim (figure only) |
| UZH Robotics & Perception Group | Swift | The racing drone that beat 3 champions (2022 race) | RL in sim + residual models from ~50 s of real flight | [Nature 2023](https://www.nature.com/articles/s41586-023-06419-4) | 2023-08-30 | B (peer-reviewed) | 15 of 25 races won |
| Skydio | X10 / Skydio Autonomy | Vision-based obstacle avoidance and GPS-free navigation | Its published learned pilot (2020) was trained by imitation on **~3 h of logged real flights**. No primary statement found on simulation for X10 | [Skydio Autonomy](https://www.skydio.com/autonomy); [IEEE Spectrum 2020](https://spectrum.ieee.org/deep-neural-pilot-skydio-2-2650279616) | 2020 / current | C-coi | — |
| Zipline | Platform 2 delivery drone | Home delivery by drone | Heavy simulation and software-in-the-loop testing (exec interview). **Detect-and-avoid is acoustic (microphones), not camera**: a counter-example to "vision everywhere" | [interview](https://insideunmannedsystems.com/five-good-questions-juan-albanell-head-of-system-test-engineering-at-zipline/); [New Atlas](https://newatlas.com/drones/zipline-acoustic-sense-avoid-uav/) | 2022–2025 | C-coi | — |
| Wayve | PRISM-1 | 4D reconstruction of street scenes from camera video, no lidar | Digital twin from images | [wayve.ai/thinking/prism-1](https://wayve.ai/thinking/prism-1/) | 2024-06-17 | C-coi | — |
| Wayve | GAIA-2 / GAIA-3 | Generative multi-camera video world model used to test the driver | World model | [GAIA-2](https://wayve.ai/thinking/gaia-2/); [GAIA-3](https://wayve.ai/thinking/gaia-3/); [GAIA-2 paper arXiv 2503.20523](https://arxiv.org/abs/2503.20523) | 2025-03-26; 2025-12-02 | C-coi | GAIA-3: 15B parameters, ~10x GAIA-2's data, 9 countries. Says it predicts relative policy performance; no number given |
| Waymo | Waymo World Model | Generative driving simulator on Genie 3; camera + lidar output; dashcam/phone video → simulation | World model | [Waymo blog](https://waymo.com/blog/2026/02/the-waymo-world-model-a-new-frontier-for-autonomous-driving-simulation) | 2026-02-06 | C-coi | ~200M real autonomous miles vs "billions" virtual; no benchmarks published |
| Tesla | Neural world simulator | Generates multi-camera video that reacts to the driving AI; also shown for Optimus | World model | Secondary only ([Humanoids Daily](https://www.humanoidsdaily.com/news/tesla-ai-chief-details-unified-world-simulator-for-fsd-and-optimus)) | 2025-10 | D | "500 years of driving per day" is **unverified, don't use** |
| Applied Intuition | Neural Sim | Turns fleet drive logs into photoreal 3D scenes for closed-loop testing | Digital twin from images (radiance fields, splats) | [blog](https://appliedintuition.com/blog/neural-sim-end-to-end-sds-validation) | 2025-10-14 | C-coi | "thousands" of neural sims a day |
| Parallel Domain | PD Replica | Video/photo scans → simulation-ready 3D scene | Digital twin from images + synthetic data | [interview](https://radiancefields.com/parallel-domain-interview) | c. 2024 | C-coi | — |
| NVIDIA | Isaac Sim 5.0 + Omniverse NuRec | Photos/video → 3D Gaussian splats → robot simulator | Digital twin from images + sim-to-real RL (Isaac Lab) | [NuRec](https://developer.nvidia.com/omniverse/nurec); [Isaac Sim 5.0 GA](https://developer.nvidia.com/blog/announcing-general-availability-for-nvidia-isaac-sim-5-0-and-nvidia-isaac-lab-2-2) | 2025-08 | C-coi | ~100 photos per capture (tutorial) |
| NVIDIA | Cosmos (Predict / Transfer / Reason) | World foundation models; Transfer turns simulator depth/segmentation into photoreal video | World model + synthetic data | [Cosmos paper arXiv 2501.03575](https://arxiv.org/abs/2501.03575); [Cosmos-Transfer2.5 page](https://research.nvidia.com/labs/cosmos-lab/cosmos-transfer2.5) | 2025-01 → 2025-10 | B for model facts / C for benefit | Paper: ~20M h of raw video. Transfer2.5 augmentation: real robot 1/30 → 24/30 in new scenes (vendor, and real-to-real, not sim-to-real) |
| Figure AI | Figure 02 walking | Humanoid walking learned fully in simulation | RL + domain randomization, zero-shot | [figure.ai news](https://www.figure.ai/news/reinforcement-learning-walking) | 2025-03-25 | C-coi | same network on 10 real robots |
| RAI Institute + Boston Dynamics | Spot RL locomotion | First fully learned control policy on Spot | Sim-to-real RL, sim tuned to real data | [arXiv 2504.17857](https://arxiv.org/abs/2504.17857) | 2025-04-24 | C (authors' preprint) | >5.2 m/s, 3x Spot's default max |
| 1X | 1X World Model | Video world model that predicts what the NEO humanoid sees; used to rank policies instead of slow real tests | World model | [1x.tech](https://www.1x.tech/discover/redwood-ai-world-model) | 2025-06-16 | C-coi | "strong correlation" with real tests, no coefficient |
| DJI | DJI Terra 5.0 | Drone photos → photogrammetry mesh and Gaussian splats (PLY / 3D Tiles export) | Digital twin from images | [DroneXL](https://dronexl.co/2025/07/17/dji-terra-update-photorealistic-3d-gaussian-splatting) | 2025-07-17 | C | — |
| Pix4D | PIX4Dmatic / catch / cloud | Drone/ground photos → georeferenced models + Gaussian splats | Digital twin from images | [pix4d blog](https://pix4d.com/blog/gaussian-splatting-pix4dmatic/) | 2025-02 → 2026-05 | C-coi | — |
| Google (+ Cesium) | Photorealistic 3D Tiles | Photogrammetry city models streamed into game engines and simulators; researchers fly simulated drones over real cities | Digital twin from images | [Cesium blog](https://cesium.com/blog/2023/05/10/cesium-partners-with-google-maps-platform/); drone-sim use [arXiv 2401.06407](https://arxiv.org/abs/2401.06407) | 2023-05 | C | 2,500+ cities. A user report says tiles are too coarse at drone altitude ([forum](https://community.cesium.com/t/cesium-isaac-sim-terrain-goes-black-at-drone-scale-10-500ft-google-photorealistic-3d-tiles-not-loading-close-up/45997)) |
| Google | Maps Immersive View (indoor) | Indoor views of venues built from photos | NeRF | [Google Research blog](https://research.google/blog/reconstructing-indoor-spaces-with-nerf/) | 2023-06-14 | C-coi | — |
| Luma AI / Polycam / Niantic Scaniverse | phone apps | Phone video → Gaussian splat scene | Gaussian splatting | [Luma](https://radiancefields.com/luma-interactive-scenes-announced); [Scaniverse](https://radiancefields.com/niantic-spatial-bets-big-on-scaniverse-and-gaussian-splatting) | 2023–2025 | C | — |
| Microsoft → IAMAI | AirSim → Project AirSim | Drone/car simulator on Unreal Engine | Simulation / synthetic data | [AirSim](https://microsoft.github.io/AirSim/); [ProjectAirSim](https://github.com/iamaisim/ProjectAirSim) | 2017; retirement announced 2022; successor 2025 | B (GitHub facts) | **Correction:** the repo is not archived (checked 2026-10-26). Microsoft announced retirement in 2022; IAMAI maintains Project AirSim |
| Duality AI | Falcon | Digital-twin simulator for synthetic camera/sensor data | Synthetic data + digital twin | [Army Technology](https://www.army-technology.com/news/duality-us-army-ai-anti-drone/) | 2024 | C-coi | vendor deck: 96% → 99.98% accuracy with synthetic data (no method) |
| Physical Intelligence | π0 | Generalist robot policy | **Counter-example:** trained on real robot data, no simulation | [pi.website](https://www.pi.website/blog/pi0) | 2024-10-31 | C-coi | — |

## Defence companies (one line at most)

Shield AI (Hivemind; "trained in simulation"), Anduril (synthetic imagery per job posts), and the Ukrainian firms The Fourth Law (TFL-1 onboard autonomy module) and Swarmer.
- Their technique claims are press or job-post level (C/D), and the team's rule is to stay non-operational.
- Use at most "autonomy and simulation are used in defence too", with no specifics.

## What's not public

- No company publishes a measured "trained in our world model → better in reality" number. Waymo, Wayve, Tesla and 1X all show demos or relative claims.
- No public company pipeline "drone photos → splat twin → train → real flight" with a measured result.
  - The best public end-to-end ones are academic: SOUS VIDE (Stanford, 2-3 min handheld video, 105 real flights) and FalconGym 2.0 (UIUC, twin from a 3-min video by the drone's own camera, 69 of 70 real gates per its abstract). See `03-papers.md`.
