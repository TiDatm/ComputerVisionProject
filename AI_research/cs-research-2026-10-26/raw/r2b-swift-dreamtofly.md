# R2b: Swift (Nature 2023) and Dream to Fly (ICRA 2026): full-text extraction

Date of research: 2026-10-26. Block 4 of the team presentation, "from simulation to reality", with camera-based vision as the base.

Grades: A = peer-reviewed primary full text. B = primary preprint, or the authors' institution's own release. C = secondary press. D = social media or unverified. Flags: [Q] exact quote, [P] paraphrase of the primary source, [INF] my inference, [NF] not found in the source.

Quotation note: my fetch tool returns only short verbatim fragments (under about 125 characters). Longer passages below are paraphrases marked [P], with their location given.

## Sources (IDs verified)

| ID | Source | Date | Grade |
|---|---|---|---|
| S1 | Kaufmann, Bauersfeld, Loquercio, Müller, Koltun, Scaramuzza. "Champion-level drone racing using deep reinforcement learning". Nature 620(7976):982-987. DOI 10.1038/s41586-023-06419-4. Open copy PMC10468397: https://pmc.ncbi.nlm.nih.gov/articles/PMC10468397/ | Received 2023-01-05, accepted 2023-07-10, published 2023-08-30 | A |
| S2 | UZH news release "Drone race": https://news.uzh.ch/en/articles/media/2023/Drone-race.html | 2023-08-30 | B (the authors' institution) |
| S3 | Romero, Shenai, Geles, Aljalbout, Scaramuzza. "Dream to Fly: Model-Based Reinforcement Learning for Vision-Based Drone Flight". arXiv 2501.14377. v1 2025-01-24, v2 2026-04-10. Comments field: "accepted to IEEE ICRA 2026". RPG, University of Zurich. https://arxiv.org/abs/2501.14377 | 2025-01 / 2026-04 | B (accepted paper; I read the preprint, not the proceedings version) |
| S4 | Geles, Bauersfeld, Romero, Xing, Scaramuzza. "Demonstrating Agile Flight from Pixels without State Estimation". RSS 2024. arXiv 2406.12505 | 2024-06-18 | A- (peer-reviewed conference) |
| S5 | Xing, Bauersfeld, Song, Xing, Scaramuzza. "Contrastive Learning for Enhancing Robust Scene Transfer in Vision-based Agile Flight". ICRA 2024. arXiv 2309.09865 (v3, 2024-02-29) | 2023-09 / 2024-02 | A- |
| S6 | RPG publications list: https://rpg.ifi.uzh.ch/publications.html (2024-2026 titles) | Read 2026-10-26 | B (index only) |
| S7 | Press reports of Swift's 17.47 s lap, e.g. https://yourstory.com/2023/09/ai-drone-swift-outpaces-human-fpv-champions | 2023-09 | C |

## Summary

- **Real data used by Swift.** Swift used very little real data: "three full rollouts in the real world, corresponding to approximately 50 s of flight time" (S1, Methods, Residual model identification). Each rollout was recorded with the onboard sensors plus a motion-capture system that gave the "ground truth".
  - The "800 and 1,000 samples" are the number of data points that fit the residual dynamics model (k-NN, k = 5) for the track used. They come from those roughly 50 s of flight. They are not a separate dataset.
  - Only the policy (the controller network) was fine-tuned. The gate detector's weights were kept fixed.
- **Sensing during the races.** In the races Swift used only onboard sensing, with no external infrastructure (S1, Fig. 1 caption).
  - Onboard hardware: NVIDIA Jetson TX2, Intel RealSense T265 (visual-inertial odometry at 100 Hz, greyscale images for gate detection), IMU, and a Betaflight flight controller.
  - Motion capture served only for data collection, model fitting and ground truth.
- **Measured effect of the real-data step.**
  - In simulation with realistic dynamics and noise, the zero-shot baselines (with and without domain randomization) "are able to complete" not even "a single lap". Swift's approach, with its empirical residual models, completes the track in every setting "with small increases in lap time" (S1, Simulation results; Extended Data Table 1b,c).
  - Extra fine-tuning iterations bring almost nothing: lap-time change 0.02 ± 0.02 s, gate-centre distance change 0.09 ± 0.08 m (S1, Fine-tuning for several iterations; Extended Data Fig. 2).
  - S1 does not report a real-world before/after-fine-tuning success rate. [NF]
- **Gate detector.** It is a six-level U-Net that segments gate corners on 384×384 greyscale images, using FP16 TensorRT on the TX2, at about 40 ms per pass. S1 does not say what data it was trained on (real or simulated, how many images). [NF]
  - Same-lab context: the RSS 2024 detector (S4) used 80k labelled images, mixing real and rendered ones, none from the deployment environment.
- **Race results.** Head-to-head races of 3 laps over a 75 m, 7-gate track. Swift won 5/9 vs Vanover, 4/7 vs Bitmatta and 6/9 vs Schaepper, which is 15 of 25 (my sum). It set the fastest race time, "a lead of half a second" over the best human.
  - The absolute lap time of about 17.47 s comes only from press reports (S7, grade C). The paper's text gives no absolute seconds. The full numbers sit in Extended Data Table 1d, an image I could not read.
- **Limitations.** The authors list: no crash recovery; the perception system assumes the environment's appearance matches training ("If this assumption fails, the system can fail."), and changes in lighting are named; the 30 Hz camera; and the code is not released.
- **Dream to Fly (S3).** DreamerV3, a world-model method, learns from RGB pixels only (64×64, no state, no IMU in the observation), trained in a Flightmare plus Habitat-rendering simulator.
  - PPO and SAC "are [not] able to achieve any considerable training in 20 million environment interactions" (v2, Fig. 3 caption). DreamerV3 converges on Circle, Kidney and Figure-8 tracks. Training takes about 240 hours.
  - No success-rate numbers are reported.
  - In the real-world flights the drone and its dynamics are real, but the images are rendered by Habitat and fed to the policy, which sends commands over RF to the real drone. Speed was up to 9 m/s (abstract).
- **UZH follow-ups on appearance.** I found no 2024-2026 UZH paper that directly fixes Swift's lighting limitation with measured real-world lighting tests. The closest work is S5 (ICRA 2024, contrastive scene transfer, simulation numbers only) and S4 (RSS 2024, detector trained on mixed data, not tested under lighting changes).

## Claims table

| # | Claim | Quote / location | Source | Grade | Flag |
|---|---|---|---|---|---|
| 1 | Real data for fine-tuning = 3 rollouts, about 50 s | "we collect three full rollouts in the real world, corresponding to approximately 50 s of flight time" (Methods, Residual model identification) | S1 | A | [Q] |
| 2 | The real data is labelled by motion capture | "highly accurate pose estimates from a motion-capture system" (The Swift system); residuals "obtained from an external motion-tracking system" (Methods) | S1 | A | [Q] |
| 3 | 800-1,000 samples per track = the k-NN dataset for the residual dynamics | "ranges between 800 and 1,000 samples for the track layout used in this work" (Methods, Residual dynamics model); k-NN with k = 5 | S1 | A | [Q]+[P] |
| 4 | The 800-1,000 samples come from the 3 rollouts / about 50 s | The sample count is stated in the same subsection that names the 3 rollouts as the real data. Arithmetic: 50 s at a recording rate around 20 Hz gives about 1,000 samples. The paper states no recording rate. | S1 | - | [INF] |
| 5 | Only the policy is fine-tuned; the detector is frozen | "the weights of the gate-detection network are kept constant" (Methods) | S1 | A | [Q] |
| 6 | The residual observation model = 9 one-dimensional Gaussian processes (position, velocity, attitude drift); the residual dynamics model = k-NN on state plus thrust | Methods, Residual observation model / Residual dynamics model | S1 | A | [P] |
| 7 | No external infrastructure in the races | "rely only on onboard sensory measurements, with no support from external infrastructure" (Fig. 1 caption) | S1 | A | [Q] |
| 8 | Onboard: Jetson TX2, RealSense T265 VIO at 100 Hz, IMU; policy inference 8 ms on the CPU | "An NVIDIA Jetson TX2 ... provides the main compute resource"; "Intel RealSense Tracking Camera T265 that provides VIO estimates"; "one inference pass taking 8 ms" (Methods, Drone hardware configuration) | S1 | A | [Q] |
| 9 | Camera rate 30 Hz | "the limited refresh rate of the camera used by Swift (30 Hz)" (Discussion) | S1 | A | [Q] |
| 10 | A Kalman filter fuses gate detections to correct VIO drift (position only) | Methods, Gate detection / state estimation | S1 | A | [P] |
| 11 | Drone 870 g, about 35 N static thrust, Betaflight | Methods, Drone hardware configuration | S1 | A | [P] |
| 12 | Gate detector = six-level U-Net, segments corners, greyscale 384×384, FP16, about 40 ms | "The architecture of the segmentation network is a six-level U-Net" (Methods, Gate detection) | S1 | A | [Q]+[P] |
| 13 | The detector's training data (real or simulated, count) is not described | Methods and Extended Data searched | S1 | - | [NF] |
| 14 | In simulation with realistic dynamics and noise, zero-shot baselines fail and Swift's method succeeds | "none of the three baselines are able to complete even a single lap"; "succeeds in all deployment settings, with small increases in lap time" (Simulation results; Ext. Data Table 1b,c) | S1 | A | [Q] |
| 15 | Further fine-tuning iterations have negligible effect | "subsequent fine-tuning operations result in negligible enhancements"; lap-time difference "0.02 ± 0.02 s"; gate-centre distance "0.09 ± 0.08 m" (Fine-tuning for several iterations; Ext. Data Fig. 2) | S1 | A | [Q] |
| 16 | Race format: head-to-head, first to finish 3 laps; 7 gates, 30×30×8 m, 75 m lap | "The first vehicle that completes three full laps through the track"; "seven square gates arranged in a volume of 30 × 30 × 8 m, forming a lap of 75 m in length" | S1 | A | [Q] |
| 17 | Wins 5/9, 4/7, 6/9 (15/25 in total); 40% of the 10 losses were collisions with the opponent | Results | S1 | A | [Q]; the 15/25 total is my sum |
| 18 | Fastest race time, about 0.5 s ahead of the best human | "with a lead of half a second over the best time clocked by a human pilot" (Results) | S1, S2 | A | [Q] |
| 19 | Swift's best lap about 17.47 s | Press reports; not in the paper's text (only in Ext. Data Table 1d, an image I could not read) | S7 | C | press only, unverified |
| 20 | Races held 5-13 June 2022; humans had one week of practice | "The races took place between 5 and 13 June 2022" (S2); "one week of practice on the race track" (S1) | S1, S2 | A/B | [Q] |
| 21 | Limitation: appearance and lighting | "Swift's perception system assumes that the appearance of the environment is consistent"; "If this assumption fails, the system can fail."; humans are robust to "illumination" changes (Discussion) | S1 | A | [Q] |
| 22 | Remedy the authors propose (untested) | "Robustness to appearance changes can be provided by training the gate detector and the residual observation model in a diverse set of conditions." (Discussion) | S1 | A | [Q] |
| 23 | Other limitations: no crash recovery; unaware of the opponent; not fastest on every segment; code not released | Discussion; Results; Code availability | S1 | A | [Q] |
| 24 | UZH press: failed when conditions differed, e.g. too much light | Release text | S2 | B | [P] |
| 25 | Dream to Fly: RGB pixels only, 64×64 | "The observation space consists of RGB images captured at each time step" (Sec. III-B1); "resized to 64×64 RGB pixels" (Sec. IV) | S3 | B | [Q] |
| 26 | DreamerV3 "Large" config (768-unit MLPs, 2048 recurrent units), imagination horizon 16; reward = progress − body-rate penalty, +10 per gate, −4 on collision; 5 seeds; one Quadro RTX 8000 | Sec. III-C/D, IV | S3 | B | [P] |
| 27 | PPO and SAC fail within 20M interactions | "none PPO nor SAC are able to achieve any considerable training in 20 million environment interactions" (v2 Fig. 3). v1 said "PPO ... in 10 million". | S3 | B | [Q]; the number changed between versions |
| 28 | DreamerV3 convergence: about 10M steps (v2 Fig. 4 label) / "20 million steps" (v1 Sec. VI); about 240 h | v2 Conclusion: "requiring approximately 240 hours to converge" | S3 | B | [Q]; the step counts are inconsistent across versions |
| 29 | Dream to Fly reports no success rates, in simulation or real | Whole paper searched (v1, v2) | S3 | - | [NF] |
| 30 | Emergent perception-aware behaviour (camera steered toward gate texture) | "a perception-aware behaviour of actively steering the camera toward texture-rich gate regions emerges" (Abstract) | S3 | B | [Q] |
| 31 | Real-world flight = hardware-in-the-loop: real drone and dynamics, rendered images | "the images are rendered using the habitat simulator and fed into the network" (v2 Sec. V-A); v1: "the commands produced are sent directly to the real drone platform"; "we have the real world dynamics in the loop" | S3 | B | [Q] |
| 32 | Real-world speed up to 9 m/s (v2 abstract); Figure-8 track; thrust-to-weight ratio limited to 2.7 (v1) | Abstract; Sec. V | S3 | B | [Q]/[P] |
| 33 | How the real drone's pose was obtained to render the images | Not stated in S3. In the same lab's S4 hardware-in-the-loop setup, "a motion-capture system gives us access to the pose of the drone in real time". So mocap is almost certainly used in S3 too. | S3, S4 | - | [INF] |
| 34 | Dream to Fly's stated limitation: real camera pixels still to come | "additional challenges remain to transfer to real camera pixel observations" (Sec. VI) | S3 | B | [Q] |
| 35 | RSS 2024 pixels-to-commands racing: detector trained on 80k labelled images (25k real from a different camera, 25k photorealistic renders, 25k gates on ImageNet backgrounds, 5k real FPV), "not trained on real images from the deployment environment"; 100% success over 6 runs; up to 40 km/h | Sec. III-C, IV-B, Table II | S4 | A- | [Q]/[P] |
| 36 | S4 does not measure robustness to lighting | Lighting is named only as a design goal | S4 | - | [NF] |
| 37 | Contrastive scene transfer (ICRA 2024): unseen-scene success 64.1% (simple) and 43.8% (realistic) vs 0.0% for all baselines; 64 trials per setting; brightness and blur changes in the test set; no real closed-loop flights (too slow on the TX2) | Table II; Sec. IV-D; limitations | S5 | A- | [P] |

## How Swift crossed the gap in 5 steps (for first-year students)

1. **Learn in a video game first.** A neural-network pilot practised millions of laps in a simulator, learning by trial and error (reinforcement learning, PPO). In the simulator crashes cost nothing and time runs faster than real life (S1, Methods).
2. **Don't let the pilot look at raw pixels.** A separate small network (the "gate detector") turns the camera image into something simple: where the corners of the gates are. A filter combines those corners with the drone's motion sensors (camera-based odometry plus IMU) into "where am I, how fast am I going". The pilot only ever sees this short summary, so the messy look of the real world never reaches it directly (S1, Methods).
3. **Fly for real, briefly, and measure the mistakes.** The simulator-trained pilot flew the real track three times (about 50 seconds in total). Cameras around the room (motion capture) recorded where the drone really was. Engineers then compared this to two things: what the drone thought its position was, and how the simulator predicted it would move (S1, Methods).
4. **Fix the simulator, not the pilot.** The measured mistakes became two small "correction" models. One adds realistic sensor errors; the other adds the real air and motor effects the simulator was missing. The pilot then trained again in this more honest simulator. One round was enough: a second round changed the lap time by only 0.02 s (S1, Methods; Extended Data Fig. 2).
5. **Race with only what the drone carries.** In the races the room cameras were not used. Everything ran on the drone's own computer, camera and motion sensors. Swift won 15 of 25 races against three champions and set the fastest race time. It still has a weak spot: if the lighting or the look of the track changes from what it trained on, it can fail (S1, Results, Discussion).

## Dream to Fly in plain words

Swift's pilot never sees the camera image; it sees a short summary of positions. Dream to Fly (UZH, same lab, accepted to ICRA 2026) asks whether a pilot can learn straight from small 64×64 colour pictures, with no position and no motion sensors in its input.

It uses DreamerV3, which builds a "world model": a small internal simulator that learns to predict what the next pictures will look like. The pilot then practises inside that imagined world. This learns far faster than PPO and SAC, which barely learned anything in 20 million tries (S3, Fig. 3). Without being told to, the pilot learned to keep the camera pointed at the next gate.

The real-world test was half real. The drone, its motors and its physics were real and flew the Figure-8 track at up to 9 m/s. The pictures it "saw", though, were rendered by a computer from the drone's measured position and sent over radio. A real onboard camera was not used yet; the authors themselves say real camera pixels remain an open challenge (S3, Sec. V-A, VI). Measuring the drone's position for the rendering most likely relied on motion capture, as in the same lab's earlier setup (S4). That point is my inference; S3 does not say.

The paper reports no success-rate numbers, and training takes about 240 GPU-hours.

## Not found / open

- **Swift gate detector training data** (real or simulated, number of images, how they were labelled). Not in the S1 main text, Methods or Extended Data captions. It may be in the Nature Supplementary Information, which I did not read.
- **Swift absolute lap times in seconds.** Not in the S1 text. They are presumably in Extended Data Table 1d (an image, not readable to me). The 17.47 s figure comes from press only (C).
- **Swift real-world success rate before vs after fine-tuning.** Not reported. The before/after evidence is from simulation only (Ext. Data Table 1b,c) and the iteration-2 comparison (Ext. Data Fig. 2).
- **Dream to Fly.** No numeric success rates, no number of real flights or laps, and no explicit statement of the real-world pose source.
- **UZH 2024-2026 work that directly tests Swift's lighting/appearance limitation with real-world measurements.** None found.
  - S5 measures scene and brightness transfer in simulation only.
  - S4 does not test lighting.
  - The RPG publication list (S6) shows titles such as "Superhuman Safe and Agile Racing through Multi-Agent RL" (arXiv 2026) and "Learning Agile Quadrotor Flight in the Real World" (RSS 2026). I did not read them, so I cannot say whether they address lighting.
  - A WorldMinds2025 theatre demo under "poor lighting conditions" exists only as an X post by Scaramuzza (grade D, no measurements).
- **Not UZH (excluded from task 3, but useful counterpoints):**
  - MonoRace (TU Delft, arXiv 2601.15222, Science Robotics 2026): won A2RL 2025 against AI teams and humans with a monocular rolling-shutter camera plus IMU.
  - SkyDreamer (TU Delft, arXiv 2510.14783): flown in a hall with poorer lighting.
  - Qiao et al., IJCNN 2024 (arXiv 2405.01054): continual-learning gate detection under dynamic lighting; its abstract gives no numbers.
