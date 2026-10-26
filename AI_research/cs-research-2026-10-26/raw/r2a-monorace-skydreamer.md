# R2a: MonoRace and SkyDreamer (full-text read), plus A2RL results

Date of research: 2026-10-26. Both papers were read as arXiv HTML full text (the main text and tables; the reference list past about 100k characters was not read). Quotes are verbatim from the HTML rendering. The papers use unnumbered (MonoRace) or numbered (SkyDreamer) headings, as noted.

Grades: A = peer-reviewed or primary full text with numbers; B = primary preprint full text; C = official or organiser news; D = secondary press. Flags: -coi (the author or organiser has a stake), -1run (single event or run), -indirect, -abstract/-snippet.

## Summary

- **MonoRace** (arXiv 2601.15222v1, 21 Jan 2026, TU Delft). Title: "MonoRace: Winning Champion-Level Drone Racing with Robust Monocular AI". Hardware: one rolling-shutter IMX219 camera (155°×115° FOV, 820×616 px at 90 Hz, cropped to 384×384) plus the flight-controller IMU. Compute: a Jetson Orin NX for vision and an STM32H743 for the 500 Hz control net.
  - **Perception:** GateNet (U-Net segmentation) → QuAdGate corners → PnP → EKF. GateNet is trained on a "3500:500 synthetic to real split". The synthetic images are gate faces with randomized scale, rotation and perspective, pasted onto background photos, plus HSV jitter and noise. The 500 real images are manually labeled.
  - **Control:** PPO (Stable-Baselines3) in a custom Gym simulator. The policy outputs motor commands directly from 24 state observations. **It does not see images.**
  - **Randomization:** applies to the *dynamics-model parameters* (motor constants, rotor speed limits, drag and moment coefficients), as a % around nominal. Quote: "most parameters are randomized ≥40%".
  - **Race:** won the A2RL x DCL 2025 Grand Challenge, AI vs Human and Drag Race, and placed 3rd in multi-drone. Best time 16.56 s; top speed 28.23 m/s.
- **SkyDreamer** (arXiv 2510.14783v1, 16 Oct 2025, TU Delft). Title confirmed: "SkyDreamer: Interpretable End-to-End Vision-Based Drone Racing with Model-Based Reinforcement Learning".
  - **Policy:** pixel-level gate *segmentation masks* (64×64) plus IMU body rates and rotor speeds go straight to motor commands, via Informed DreamerV3.
  - **Sim-to-real:** a CycleGAN-variant (StochGAN) translates "from synthetic segmentation masks to more realistic masks and vice versa". It is trained on ~4,000 unpaired real and rendered images.
  - **Results:** up to 21 m/s and 6 g; 75/75 laps on the small tracks; 5/6 flights on the big track.
  - **No with-vs-without-GAN ablation was found.**
- **A2RL:**
  - **2025:** organiser-side Abu Dhabi Media Office release (17 Apr 2025) confirms MAVLab won AI vs Human, the Grand Challenge and the drag race.
  - **Season 2 (January 2026, UMEX, ADNEC):** a human, Minchan Kim, beat TII Racing's AI in a best-of-nine final. TII Racing won the AI Speed Challenge; MAVLab reportedly won multi-drone. Sources are TII and press, not an official results page.

## Claims table

| # | Claim | Source | Date | Grade | Quote / location |
|---|---|---|---|---|---|
| M1 | The ID resolves to the MonoRace paper by Bahnam, Ferede, Blaha, Lang, Lucassen, Missinne, Verraest, De Wagter, de Croon (TU Delft) | arxiv.org/html/2601.15222 | 2026-01-21 (v1) | B -coi (team reports its own win) | Title page |
| M2 | Sensors are a monocular rolling-shutter wide-FOV camera plus IMU; no external motion capture | same | 2026-01-21 | B | Materials and Methods > Overview: "a monocular rolling-shutter camera with a wide field of view (155° horizontal × 115° vertical)" |
| M3 | Camera: IMX219, 820×616 at 90 Hz, undistorted and cropped to 384×384 | same | 2026-01-21 | B | Fig. 5 caption: "An IMX219 camera image is grabbed and timestamped on the Orin." Resolution and fps from Overview (paraphrased by the extractor, not quoted) |
| M4 | IMU data comes from the flight controller: accelerometer at 1000 Hz, gyroscope at 2000 Hz | same | 2026-01-21 | B | Overview (paraphrase) |
| M5 | Compute: Jetson Orin NX for vision; STM32H743 (480 MHz) runs the 500 Hz control net; total mass 966 g | same | 2026-01-21 | B | Overview (paraphrase) |
| M6 | The drone hardware was supplied by the organisers | same | 2026-01-21 | B | "The drone uses a custom carbon fiber frame designed by the competition organizers"; Fig. 1 "supplied externally made competition robot" |
| M7 | Perception chain: GateNet (U-Net segmentation), then QuAdGate corners, RANSAC, PnP, and an EKF | same | 2026-01-21 | B | Section GateNet / vision pipeline (paraphrase) |
| M8 | GateNet training mix is 3,500 synthetic : 500 real | same | 2026-01-21 | B | GateNet: "The synthetic and real-world datasets are merged and fed through a unified dataloader (in a 3500:500 synthetic to real split)". Note: this is stated as a *split*. A total of 4,000 is implied, not stated. |
| M9 | The real images were manually labeled | same | 2026-01-21 | B | "in addition to manually labeled real-world images" |
| M10 | Synthetic images are made by warping gate faces (random scale, rotation, perspective), compositing them onto backgrounds (warehouses, parking lots, halls), then adding HSV jitter and Gaussian noise | same | 2026-01-21 | B | GateNet: "each gate face is subjected to a series of randomized transformations" (other details paraphrased) |
| M11 | The control policy is trained with PPO (Stable-Baselines3) in a custom Gym quadrotor model (quadratic drag, advance-ratio effects, Euler at dt=0.01 s). It is a 3×64 MLP that maps 24 observations to 4 motor commands | same | 2026-01-21 | B | Materials and Methods > Control (paraphrase); "The policy takes in 24 observations, including the quadcopter's state" |
| M12 | The policy does not see images. It acts on the state estimated by the vision pipeline | same | 2026-01-21 | B | "use G&CNets to directly compute low-level motor commands from the predicted states provided by the vision pipeline" |
| M13 | The "40–50%" is per-episode randomization of each **dynamics parameter** around its nominal value, and it differs per policy | same | 2026-01-21 | B | "we randomize each parameter by a specified percentage around its nominal value." "However, most parameters are randomized ≥40%, and evaluation with 30% randomization still achieves a 94% success rate." Table 1: kω 15% (M16) → 40% (M17, M18) → 50% (M19, M22, M23); ωmax 15/33/30/30/30/40% |
| M14 | Sim vs real: real completion times match the simulated averages, but simulated crash rates were "less predictive" because the sim lacks vision-estimation errors | same | 2026-01-21 | B | Results > Performance, Fig. 2 "G&CNet completion times: real flights versus simulation"; "real-world completion times closely match the average simulated times". **No numeric table; the numbers are only in the figure.** |
| M15 | Reliability: M23 had an 88.4% success rate over 43 flights; M17 had 55/77 | same | 2026-01-21 | B | "completed 43 flights with an 88.4% success rate"; the M17 figure is paraphrased |
| M16 | Ablation on IMU saturation: IMU-only prediction gave 22 successes and 22 crashes; with model-based correction, 3/3 later flights succeeded | same | 2026-01-21 | B -1run (n=3) | Results (paraphrase) |
| M17 | Ablation on delay compensation (in SITL): RMS error 0.103 m vs 0.289 m for the naive version | same | 2026-01-21 | B | Results (paraphrase) |
| M18 | Ablation on cropping (3 fastest flights): adaptive crop found 26,516 corners vs 19,168 for resizing; IoU 0.50 vs 0.47 (center crop 0.36) | same | 2026-01-21 | B | Results (paraphrase) |
| M19 | Calibration: precise extrinsic calibration was not possible at the event, so it was optimized after flights. Errors in sim were 0.14°/0.49°/0.71° (pitch/roll/yaw) | same | 2026-01-21 | B | Methods / Discussion (paraphrase). **No real-flight with-vs-without-calibration result was found.** |
| M20 | Camera interference: 8–75% of images were corrupted across 10 flights; 2 crashes resulted | same | 2026-01-21 | B | Results (paraphrase); "The drone designed by the organizers originally featured long MIPI cables for the camera connection." |
| M21 | Race: "A2RL x DCL", 11–12 April 2025, Abu Dhabi. 11 gates, 2 laps, split-S. MAVLab won the Grand Challenge, AI vs Human and Drag Race, and was 3rd in Multi-Drone | same | 2026-01-21 | B -coi | Results > Competition Format. **The date conflicts with the MAVLab blog ("April 14"); see Gaps.** |
| M22 | AI vs Human: a knockout of three rounds; fastest time 16.56 s; beat three world-champion-level FPV pilots. Only one opponent is named (Killian Rousseau, Fig. 7C) | same | 2026-01-21 | B -coi | "AI vs Human — three knockout rounds in a head-to-head race (AI teams and FPV pilots)."; "Our controller achieved the fastest completion time of 16.56 s, outperforming three world champion-level FPV pilots." Which round Rousseau was in was not established. |
| M23 | Max speed 28.23 m/s (~100 km/h) | same | 2026-01-21 | B -coi | "reached a maximum speed of 28.23 m/s, which, to our knowledge, is the fastest fully onboard" |
| M24 | The paper names what it did not solve: decoupled perception and control (the policy assumes near-perfect state); dependence on rectangular gates; no detection or avoidance of other drones (a crash cost the multi-drone race); simulated crash rates do not transfer | same | 2026-01-21 | B | Discussion (paraphrase) |
| S1 | The ID resolves to "SkyDreamer: Interpretable End-to-End Vision-Based Drone Racing with Model-Based Reinforcement Learning" by Verraest, Bahnam, Ferede, de Croon, De Wagter | arxiv.org/html/2510.14783 | 2025-10-16 (v1) | B | Title page |
| S2 | The policy goes from pixels to motors, end to end | same | 2025-10-16 | B | Abstract: "the first end-to-end vision-based ADR policy that maps directly from pixel-level representations to motor commands" |
| S3 | The "pixels" are binary gate segmentation masks from GateNet, resized to 64×64, plus IMU body rates and ESC rotor speeds (no accelerometer) and a flight-plan vector | same | 2025-10-16 | B | Sec. II (paraphrase); Appendix B "images of size 64×64, the same resolution as those rendered and used by SkyDreamer during training" |
| S4 | Camera: Arducam IMX219 wide angle, 175° FOV, 90 Hz. Compute: Jetson Orin NX 16 GB (policy 1.3 ms, segmentation 3 ms) | same | 2025-10-16 | B | "a low-cost rolling shutter Arducam IMX219 Wide Angle Camera" |
| S5 | Image translation: CycleGAN/StochGAN between synthetic and realistic masks, trained on ~4,000 unpaired images that need no labels | same | 2025-10-16 | B | Sec. II-F: "from synthetic segmentation masks to more realistic masks and vice versa"; "The GAN is trained on approximately 4000 unpaired real and rendered images, which require no manual labeling" |
| S6 | Other mask augmentations: random 1-pixel erosion (50% of the time) and a rolling-shutter warp | same | 2025-10-16 | B | "we randomly erode the masks by 1 pixel … 50% of the time at a mask resolution of 64×64" (the HTML renders this as "11 pixel"; check the PDF) |
| S7 | **No with-vs-without-GAN ablation**; the paper only gives a qualitative attribution | same | 2025-10-16 | B | "Thanks to the use of StochGAN and additional data augmentations, SkyDreamer still completes 25 consecutive laps on the inverted loop track" |
| S8 | GateNet data: 200 real labeled images (orange gates); 700 real + 8,500 synthetic (MAVLab gates) | same | 2025-10-16 | B | Appendix A (paraphrase) |
| S9 | Randomization (train / eval): camera roll and yaw ±5°, pitch 45–55°; ωmin/ωmax ±20%; other dynamics ±30% (train) / ±20% (eval); force, moment and action disturbances; gate size | same | 2025-10-16 | B | Table III |
| S10 | Speed up to 21 m/s (estimated) and acceleration up to 6 g (measured) | same | 2025-10-16 | B | "reaching speeds of up to 21 m/s and accelerations of up to 6 g"; "SkyDreamer reached measured accelerations of up to 6 g" |
| S11 | Real flights: 3 small tracks × 5 flights × 5 laps, all successful (75 laps); big track 5 of 6 two-lap flights | same | 2025-10-16 | B | "five out of six flights consisting of two laps each were successful". The failure involved camera extrinsics outside the training range (paraphrase) |
| S12 | Training: ~17M environment steps, ~50 h on an A100 (big track ~35M) | same | 2025-10-16 | B | Sec. II/III (the HTML shows "1717 million" and "5050 hours", which are rendering artifacts; verify in the PDF) |
| S13 | Limitations: parameter estimates drift; decoded states jump; vulnerable to segmentation false positives; requires a known track layout; heavy compute | same | 2025-10-16 | B | Sec. IV: "still vulnerable to false positives in segmentation masks" |
| R1 | A2RL 2025: MAVLab won AI vs Human, the AI Grand Challenge (2 laps / 22 gates / 170 m in 17 s) and the drag race. TII Racing won multi-drone | mediaoffice.abudhabi (A2RL x DCL release) | 2025-04-17 | C -coi (Abu Dhabi government is the organiser side) | "Team MAVLab's AI drone outpaced a world-leading human pilot to win the AI vs Human Challenge." No event date is given. |
| R2 | MAVLab blog: race on "Saturday, April 14, 2025"; beat three former DCL world champions; 95.8 km/h | mavlab.tudelft.nl/?p=3518 | 2025-04-16 | C -coi | "flight speeds up to 95.8 km/h on the very winding track". **14 Apr 2025 was a Monday.** The paper says 11–12 Apr. |
| R3 | A2RL Season 2 (UMEX, ADNEC, ~21–22 Jan 2026): human pilot Minchan Kim beat TII Racing's AI in a best-of-nine final that reached 4–4 before the last heat | tii.ae/insights (republished from Khaleej Times) | 2026-01-26 | C -coi (TII is affiliated with the organiser side) -1run | Paraphrase: Kim won the deciding heat after 4–4 |
| R4 | Season 2: TII Racing won the AI Speed Challenge; MAVLab won multi-drone | search snippet of the BusinessWire release 20260126339209 (the page returned 403) | 2026-01-26 | D -snippet | Not read in full |

## Plain-language descriptions (for first-year students)

**MonoRace.** A Delft team raced a drone that saw the world through a single cheap camera, like one eye, and felt motion with a small motion sensor. A neural network looks at each camera frame and colours in where the racing gates are. Simple geometry then works out where the drone must be, given where the gate corners appear. A second, tiny network decides how hard to spin each of the four motors, and it learned that entirely in a physics simulator by trial and error (reinforcement learning). To make sure this "simulation brain" would not be fooled by a real drone that behaves slightly differently, the team kept changing the simulated drone's motor strength and air drag by ±40–50% during training. The vision network learned from 3,500 fake (pasted-together) images and 500 real photos. In April 2025 in Abu Dhabi it beat three champion human pilots and flew at up to about 100 km/h. It still crashed sometimes, could not see other drones, and only works with rectangular gates.

**SkyDreamer.** This is the same lab's next step: one network goes straight from what the camera sees to motor commands, with no separate "where am I?" step. It does not use raw photos. It uses a black-and-white "gate mask", a 64×64 picture where gates are white and everything else is black, so the simulator only has to draw simple masks rather than realistic scenes. Real masks are messier than simulated ones, so the team trained an image-translation network (a CycleGAN variant) on about 4,000 unlabelled real and simulated masks to make the simulated masks look realistic. The policy learns with a "world model" (Dreamer) that imagines what will happen next. On real tracks it flew up to 21 m/s with up to 6 g, finished all 75 laps on small tracks, and finished 5 of 6 flights on a bigger one. The paper does not measure how much the image translation alone helped.

## Swift vs MonoRace

| Row | Swift (UZH, Nature 2023) | MonoRace (TU Delft, 2026 preprint) |
|---|---|---|
| Sensors | From round 1: one camera plus IMU (not re-read) | One rolling-shutter IMX219 camera (155°×115°, 90 Hz) plus flight-controller IMU [M2–M4] |
| What the policy sees | From round 1: an estimated state (VIO plus gate detections), not images (not re-read) | The estimated state (24 observations) from the vision pipeline and EKF; no images [M11, M12] |
| How sim-to-real was handled | From round 1: residual models of perception and dynamics fitted from real data (not re-read) | Large dynamics-parameter randomization (mostly ≥40%) in a custom simulator; synthetic gate images plus real images for GateNet; engineering fixes (delay compensation, IMU-saturation model, adaptive crop) [M10, M13, M16–M18] |
| Real data used | From round 1: real flight data to fit the residuals (not re-read) | 500 manually labeled real images for GateNet; real flights to pick and tune policies (M16–M23 iterations) [M8, M9, M15] |
| Result | From round 1: beat human champions in a controlled head-to-head (not re-read) | Won A2RL 2025 AI vs Human against three champion-level pilots in a live knockout; 16.56 s; 28.23 m/s; organiser-supplied drone [M21–M23, R1] |

## Not found

- The exact real-flight sim vs real lap times for MonoRace: they are only in Fig. 2, and no numbers are in the text.
- A real-flight ablation in MonoRace with vs without randomization, or with vs without calibration. The only evidence is the 30%-randomization sim evaluation (94%) and the per-model randomization history in Table 1.
- In SkyDreamer, a with-vs-without StochGAN ablation, and the exact direction in which the GAN is used at training time ("and vice versa").
- The raw-camera resolution for SkyDreamer.
- The official a2rl.io results page for the 2025 drone race. The a2rl.io page fetched covered only the car series.
- The full Season 2 BusinessWire release (HTTP 403).

## Remaining gaps

1. The 2025 race date is inconsistent: the paper says 11–12 Apr, the MAVLab blog says "Saturday April 14" (14 Apr 2025 was a Monday), and the organiser release gives no date. Use "April 2025" on slides.
2. Which of the three AI vs Human rounds involved Killian Rousseau, and the names of the other two pilots.
3. Whether "3500:500" refers to dataset counts or the per-epoch sampling ratio. The wording "split" in a "unified dataloader" is ambiguous. My inference is that it means 3,500 synthetic + 500 real images, but the totals are not stated.
4. SkyDreamer numbers were read through HTML that duplicates digits ("1717 million", "5050 hours", "11 pixel"). Check them against the PDF.
5. Season 2 (Jan 2026) results rest on TII- or organiser-affiliated and press sources (grade C/D). No neutral official results page was found.
6. The Swift column is from round 1 and was not re-read.

Sources:
- https://arxiv.org/html/2601.15222 (MonoRace, v1 2026-01-21)
- https://arxiv.org/html/2510.14783 (SkyDreamer, v1 2025-10-16)
- https://www.mediaoffice.abudhabi/en/technology/a2rl-x-dcl-autonomous-drone-championship-advances-global-expertise-in-ai-drone-technology/ (2025-04-17)
- https://mavlab.tudelft.nl/?p=3518 (2025-04-16)
- https://tii.ae/insights/watch-human-edges-ai-dramatic-drone-racing-finale-abu-dhabi (2026-01-26)
