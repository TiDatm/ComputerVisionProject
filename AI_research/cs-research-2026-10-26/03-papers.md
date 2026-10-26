# 03. Papers: from foundations to 2026

Ordered from foundational to recent. Each entry has the citation and link, the technology it introduces, a plain explanation, the key measured number, and **why it matters for block 4**. Read `02-primer.md` first.

**Grades:**
- **A**: replicated by independent groups.
- **B**: one paper with measured numbers.
- **C**: vendor or demo claim.
- **D**: blog.

**Flags:**
- `-1run`: small number of trials.
- `-coi`: the authors report their own system or product.
- `-abstract`: only the abstract was read.

**★ = recommended for block 4 slides.** The sections in "Verification" at the end record which numbers a separate verifier re-checked in the source.

---

## A. Foundations: the ideas everything else uses

### A1. Jakobi, Husbands, Harvey (1995): "Noise and the Reality Gap"
- **Citation:** ECAL 1995, LNCS 929, pp. 704-720. https://doi.org/10.1007/3-540-59496-5_337
- **Technology:** the name "reality gap"; adding noise to a simulator so evolved robot controllers survive on a real robot.
- **Plain:** the first paper to say "the simulator lies a little, so train with noise to cope".
- **Why for us:** a one-line history hook: the problem is 30 years old. Grade B, abstract only.

### A2. ★ Aljalbout et al. (2025): "The Reality Gap in Robotics: Challenges, Solutions, and Best Practices"
- **Citation:** arXiv 2510.20808 (to appear in Annual Review of Control, Robotics, and Autonomous Systems 2026). Authors include D. Fox and D. Scaramuzza. https://arxiv.org/abs/2510.20808
- **Technology:** the current reference definition and map of the field.
  - Kinds of gap: dynamics, perception/sensing, actuation, system design (latency).
  - The *reality gap* (how different the worlds are) is separate from the *performance gap* (how much the score drops).
  - Two families of solutions: *reduce* the gap or *overcome* it.
- **Plain:** "there are no perfect simulators, and therefore there is always a difference from the real world, which we call reality gap" (§1).
- **Why for us:** the definition on slide 1 of block 4, from a 2025 source by Swift's lab head. Grade B (survey).

### A3. Tobin et al. (2017): "Domain Randomization for Transferring Deep Neural Networks from Simulation to the Real World"
- **Citation:** IROS 2017. arXiv 1703.06907. https://arxiv.org/abs/1703.06907
- **Technology:** domain randomization: train on wildly varied, unrealistic textures and lights so reality looks like one more variation.
- **Number:** an object detector trained with no real images located real objects to about 1.5 cm (abstract).
- **Why for us:** block 2 owns this. We cite it in one line as "fix #1". Grade B -abstract.

### A4. Peng et al. (2018): "Sim-to-Real Transfer of Robotic Control with Dynamics Randomization"
- **Citation:** ICRA 2018. arXiv 1710.06537. https://arxiv.org/abs/1710.06537
- **Technology:** randomizing *physics* (mass, friction, delays), not just looks.
- **Number:** real pushing task, 89% success with randomization vs 0% without (Tables II-III).
- **Why for us:** a contrast with Swift, which explicitly did **not** randomize the drone's physics and corrected the simulator instead. Grade B.

### A5. Zhu et al. (2017): CycleGAN, "Unpaired Image-to-Image Translation using Cycle-Consistent Adversarial Networks"
- **Citation:** ICCV 2017. arXiv 1703.10593. https://arxiv.org/abs/1703.10593. Project page: https://junyanz.github.io/CycleGAN/
- **Technology:** repaint images from one style to another (horse ↔ zebra, sim ↔ real) **without matched pairs**. "Cycle consistency": repaint and repaint back, and you must get the original.
- **Plain:** a photo filter learned from two piles of unrelated photos.
- **Why for us:** the base of "repaint sim frames to look real" (fix #2). Explainer: Computerphile "Zebras, Horses & CycleGAN" (2019). Grade B.

### A6. Schönberger & Frahm (2016): "Structure-from-Motion Revisited" (COLMAP)
- **Citation:** CVPR 2016. Author PDF: https://demuc.de/papers/schoenberger2016sfm.pdf. Tool: https://colmap.github.io/
- **Technology:** structure-from-motion. Given a pile of photos, it computes where each was taken and a 3D point cloud.
- **Number:** registered 20,918 of 74,394 internet photos of Rome in about 3 h (Table 1).
- **Why for us:** step 1 of every photo-built digital twin (SOUS VIDE, FalconGym 2.0, NVIDIA's pipeline). Grade B.

### A7. Mildenhall et al. (2020): "NeRF: Representing Scenes as Neural Radiance Fields for View Synthesis"
- **Citation:** ECCV 2020. arXiv 2003.08934. https://arxiv.org/abs/2003.08934
- **Technology:** a neural network that stores a scene as "colour and density at every 3D point and viewing direction". It renders new views by asking that network along each pixel's ray.
- **Numbers:** real scenes from 20-62 photos; about 30 s to render one frame (Appendix D); 1-2 days of training per scene on one V100 GPU (§5.3).
- **Why for us:** the breakthrough in photo-realistic reconstruction, but too slow to train a drone in. That motivates A8. Grade B.

### A8. ★ Kerbl et al. (2023): "3D Gaussian Splatting for Real-Time Radiance Field Rendering"
- **Citation:** ACM TOG 42(4), SIGGRAPH 2023. arXiv 2308.04079. https://arxiv.org/abs/2308.04079
- **Technology:** represent the scene as millions of small coloured, semi-transparent 3D blobs and draw them with the graphics card ("splatting").
- **Number:** on the Mip-NeRF360 benchmark, 134 fps after ~41 min of training, vs 0.06 fps after 48 h for Mip-NeRF360 (Table 1). Cost: ~734 MB per scene.
- **Own limits:** artifacts in poorly photographed areas, "popping", GPU memory over 20 GB (§7).
- **Why for us:** the speed that turned photo reconstructions into robot training gyms. Grade B.

### A9. Ha & Schmidhuber (2018): "World Models"
- **Citation:** arXiv 1803.10122. https://arxiv.org/abs/1803.10122. Interactive version: https://worldmodels.github.io/
- **Technology:** an agent learns a compressed model of its game from pixels, then trains *inside its own dream*. The controller transferred back to the real VizDoom game.
- **Caveat:** the agent learned to exploit bugs in its dream (monsters that never fire).
- **Why for us:** the origin of "learn the simulator itself" for the "what's next" slide. Grade B.

---

## B. Closing the gap with vision: the classic results

### B1. Sadeghi & Levine (2017): CAD2RL, "Real Single-Image Flight without a Single Real Image"
- **Citation:** RSS 2017. arXiv 1611.04201. https://arxiv.org/abs/1611.04201
- **Technology:** a drone policy trained only on heavily randomized renderings of 3D building models.
- **Number:** in real hallways, about half the collisions of the baseline (6 vs 13 in 164 m; Table I).
- **Why for us:** the first drone to fly real corridors without a single real image. Grade B -1run.

### B2. Bousmalis et al. (2018): GraspGAN, "Using Simulation and Domain Adaptation to Improve Efficiency of Deep Robotic Grasping"
- **Citation:** ICRA 2018. arXiv 1709.07857. https://arxiv.org/abs/1709.07857
- **Technology:** a GAN repaints simulated images to look real, plus feature-level adaptation.
- **Numbers:** with no real labels, 63.4% grasp success, matching a model trained on 939,777 labelled real grasps. But the GAN learned from ~9 million unlabelled real images.
- **Why for us:** "no real data" often means "no real *labels*". Grade B.

### B3. ★ James et al. (2019): RCAN, "Sim-to-Real via Sim-to-Sim"
- **Citation:** CVPR 2019. arXiv 1812.07252. https://arxiv.org/abs/1812.07252
- **Technology:** instead of making sim look real, translate any messy image (randomized sim *or* real) into one clean "canonical" simulator image. The policy only ever sees the clean version.
- **Numbers (Table 1):**
  - With zero real grasps, 70% success on unseen objects, vs 21% for plain sim and 33-37% for randomization alone.
  - With 5,000 real grasps, 91%, beating QT-Opt trained on 580,000 real grasps (87%), with under 1% of the data.
- **Why for us:** the best numeric example of "translate images, not the model". Not a drone, so say "robot arm". Grade B. The qualitative claim (pixel translation helps zero-shot) is A across Google, Berkeley and MIT.

### B4. Loquercio et al. (2019): "Deep Drone Racing: From Simulation to Reality with Domain Randomization"
- **Citation:** IEEE T-RO 2019. arXiv 1905.09727. https://arxiv.org/abs/1905.09727
- **Technology:** a CNN trained only in randomized simulation (textures, lights, gate shapes), deployed with no fine-tuning.
- **Numbers:** real track completed ~95% of the time, but best lap 12.1 s vs a pro pilot's 5.0 s (Table II). Illumination randomization mattered most.
- **Why for us:** "Swift's grandfather". It shows how far the field came in 4 years, from 2.4x slower than a pilot to beating champions. Grade B -coi.

### B5. ★ Kaufmann et al. (2020): "Deep Drone Acrobatics"
- **Citation:** RSS 2020. arXiv 2006.05768. https://arxiv.org/abs/2006.05768
- **Technology:** **abstraction**. The policy gets "feature tracks" (dots following corners in the image), not raw pixels. Feature tracks depend on geometry, not surface appearance.
- **Numbers:**
  - In sim on unseen backgrounds, raw-image policy 0% vs feature-track policy 100% (Table II).
  - Real loops, rolls and flips 100% over 10 runs each, trained only in simulation (Table III).
- **Why for us:** the clearest evidence that hiding raw pixels from the policy closes the gap. It is the same idea Swift uses. Grade B -1run -coi.

### B6. OpenAI et al. (2019): "Solving Rubik's Cube with a Robot Hand"
- **Citation:** arXiv 1910.07113. https://arxiv.org/abs/1910.07113. Video: https://www.youtube.com/watch?v=x4O8pojMF0w
- **Technology:** Automatic Domain Randomization, where the randomization ranges grow as the policy improves. About 13,000 years of simulated experience.
- **Numbers:** success 60% (15-move scrambles) and 20% (26-move), N=10 trials (§8.4.1).
- **Why for us:** the famous video everyone remembers. Use it only if block 2 doesn't. Grade B -1run -coi.

### B7. Loquercio et al. (2021): "Learning High-Speed Flight in the Wild"
- **Citation:** Science Robotics 6(59), 2021. arXiv 2110.05113. https://arxiv.org/abs/2110.05113
- **Technology:** imitation of a privileged expert in sim with simulated sensor noise. The input is depth from stereo **cameras**, used as an abstraction.
- **Numbers:** zero-shot real flights in forests, 100% at 3 and 5 m/s, 8/10 at 7 m/s, 60% at 10 m/s.
- **Why for us:** a camera-based abstraction that survives real forests. Grade B -1run.

---

## C. ★★ The through-example

### C1. Kaufmann, Bauersfeld, Loquercio, Müller, Koltun, Scaramuzza (2023): "Champion-level drone racing using deep reinforcement learning" (Swift)
- **Citation:** Nature 620, 982-987 (30 Aug 2023). doi:10.1038/s41586-023-06419-4. Open copy: https://pmc.ncbi.nlm.nih.gov/articles/PMC10468397/. Video: https://www.youtube.com/watch?v=fBiataDpGIo (4:50)
- **Technology:**
  1. **Perception:** a six-level U-Net finds gate corners in greyscale images (Intel RealSense T265, 30 Hz). Gate pose + VIO (visual-inertial odometry) are fused by a Kalman filter.
  2. **The policy never sees pixels.** It sees a 15-D state estimate, the next gate's 4 corners, and its previous action. It outputs thrust and body rates.
  3. **Training:** RL in simulation, 100 parallel agents, about 50 min on a workstation.
  4. **Closing the gap:**
     - "We do not perform randomization of the platform dynamics."
     - Instead: **3 real rollouts, about 50 s of flight**, labelled with motion capture.
     - Perception errors (random) are modelled with a Gaussian process. Dynamics errors (mostly repeatable) are modelled with k-nearest-neighbours (k=5, 800-1,000 samples).
     - Both are added to the simulator, and the policy is fine-tuned once. A second round changed lap time by only 0.02 s.
     - The gate detector stayed frozen.
  5. **Races:** onboard only, "no support from external infrastructure". Motion capture was used only to collect the correction data.
- **Numbers:**
  - Won 15 of 25 races: 5/9 vs A. Vanover, 4/7 vs T. Bitmatta, 6/9 vs M. Schaepper.
  - Fastest race time, about 0.5 s ahead of the best human.
  - 40% of its 10 losses were collisions with the opponent.
  - In realistic simulation, none of three baselines finished even a lap, while Swift's approach did (Ext. Data Table 1).
- **Own limits:** perception assumes the scene looks as in training: "If this assumption fails, the system can fail." Lighting is named. No crash recovery.
- **Not in the paper's text:** absolute lap times (a 17.47 s figure circulates in press only), and what images the gate detector was trained on. **Don't put these on slides.**
- **Why for us:** the spine of block 4. Use the exact sequence: abstract the image → train in sim → measure reality for 50 s → correct sim → fine-tune → race. Grade B (peer-reviewed, -coi Intel co-authors).

---

## D. After Swift: drone racing with one camera (2024-2026)

### D1. Geles et al. (2024): "Demonstrating Agile Flight from Pixels without State Estimation"
- **Citation:** RSS 2024. arXiv 2406.12505. https://arxiv.org/abs/2406.12505
- **Technology:** the policy sees an 84×84 gate *mask* plus its last actions, with no state estimate. Trained with dynamics randomization and corrupted gate edges.
- **Numbers:** real figure-8 track, 100% over 20 laps, up to 40 km/h.
- **Why for us:** shows the move from "state" to "pixels-ish" input. Grade B -1run -coi.

### D2. ★ Bahnam, Ferede, …, de Croon (TU Delft, 2026): "MonoRace: Winning Champion-Level Drone Racing with Robust Monocular AI"
- **Citation:** arXiv 2601.15222 (21 Jan 2026). https://arxiv.org/abs/2601.15222. Lab post: https://mavlab.tudelft.nl/dronerace-defeating-champions/
- **Technology:**
  - **Hardware:** one rolling-shutter camera plus IMU, all onboard.
  - **Perception:** GateNet (U-Net) → corners → pose → EKF. The detector was trained on a "3500:500 synthetic to real split": synthetic images are gate faces pasted onto background photos.
  - **Control:** PPO in a simple simulator with **dynamics randomization** ("most parameters are randomized ≥40%"). The policy outputs motor commands at 500 Hz from 24 state numbers, **not images**.
- **Numbers:**
  - Won A2RL 2025 (Abu Dhabi, April 2025) AI-vs-human against 3 champion-level pilots, plus the Grand Challenge and the drag race.
  - Best time 16.56 s; top speed 28.23 m/s (~100 km/h).
  - 88.4% success over 43 flights (final model).
- **Gaps:** real vs sim lap times "closely match" only in a figure, and simulated crash rates did not predict real ones.
- **Why for us:** an **independent** (non-Zurich) result with the **opposite** recipe to Swift. Swift corrected the simulator with real data; MonoRace randomized physics heavily and needed no real flight data for control. A perfect "two ways to cross the gap" pair. Grade B for the win; method -coi.

### D3. Verraest et al. (TU Delft, 2025): "SkyDreamer: Interpretable End-to-End Vision-Based Drone Racing with Model-Based Reinforcement Learning"
- **Citation:** arXiv 2510.14783. https://arxiv.org/abs/2510.14783
- **Technology:**
  - Input: a 64×64 gate mask → motor commands (DreamerV3-based).
  - A **CycleGAN** translates simulated masks into realistic-looking ones and back, trained on ~4,000 unlabelled real and rendered images.
- **Numbers:** ~21 m/s, 6 g; all 75 laps on small tracks; 5 of 6 flights on a big track. No with-vs-without-GAN ablation.
- **Why for us:** a drone example of image translation (fix #2) in 2025. Grade B -1run.

---

## E. Digital twins built from photos and video, for drones

### E1. Quach, Chahine, Amini, Hasani, Rus (MIT, 2024): "Gaussian Splatting to Real World Flight Navigation Transfer with Liquid Networks"
- **Citation:** arXiv 2406.15149. https://arxiv.org/abs/2406.15149. *(Our 10-24 notes had the wrong title for this ID.)*
- **Technology:** a splat twin from ~430 iPhone photos (COLMAP, ~15 min of splat training), then a "liquid" neural network trained in it.
- **Numbers:** real indoor 75% (N=40); outdoor lawn 50% vs 0/40 for an LSTM.
- **Why for us:** photo-only twin → real drone. Grade B -1run.

### E2. ★ Low, Adang, Yu, Nagami, Schwager (Stanford, 2024/25): "SOUS VIDE: Cooking Visual Drone Navigation Policies in a Gaussian Splatting Vacuum"
- **Citation:** arXiv 2412.16346. https://arxiv.org/abs/2412.16346
- **Technology:** "FiGS" simulator = a Gaussian splat of the room from a **2-3 minute handheld video** + simple drone physics, rendering up to ~130 fps. The policy (RGB + optical flow + IMU) is trained in it and flown zero-shot.
- **Numbers:**
  - 105 real flights. Lighting change 4/5, people walking through 5/5, wind and mass changes 5/5.
  - **Objects removed: 0/5 fully successful.** The authors call the policy "highly optimized for a single real-life environment".
- **Why for us:** the best camera-only "rebuild the real place and train there" example, honest limits included. Grade B -1run.

### E3. Chen, …, Schwager (Stanford, 2025): "GRaD-Nav"
- **Citation:** arXiv 2503.03984. https://arxiv.org/abs/2503.03984
- **Technology:** splat environment + differentiable drone physics + RL.
- **Numbers (Table V):** real gate flights 7/10, 7/10, 6/10. Without its context-estimator module, real 0/10, 2/10, 1/10, while simulation still gets 9/10, 10/10, 7/10.
- **Why for us:** a **measured** sim-to-real gap in one table. Great for the "what the gap looks like" slide. Grade B -1run.

### E4. ★ FalconGym 2.0 (UIUC + Toyota, 2025; ICRA 2026)
- **Citation:** arXiv 2510.02248. https://arxiv.org/abs/2510.02248 (predecessor FalconGym 1: arXiv 2503.02198, IROS 2025, NeRF-based with motion-capture camera poses)
- **Technology:** the twin is built from a **~3-minute video filmed with the drone's own camera** (COLMAP + Splatfacto), then the policy is trained inside it.
- **Numbers:** the abstract reports 69 of 70 gates over 30 trials (98.6%). Table II gives real success per track: 96.7% (left turn), 100% (random), 100%* (moving gate; the * means lateral tracking only, without crossing the gate, for safety).
- **Why for us:** the purest "the drone films the place, then learns in the copy" story, and it fits the camera-only rule. Grade B -1run.

### E5. Lv et al. (SJTU, 2026): "Zero-Shot UAV Navigation in Forests via Relightable 3D Gaussian Splatting"
- **Citation:** arXiv 2602.07101. https://arxiv.org/abs/2602.07101
- **Technology:** a "relightable" splat that separates lighting from geometry, so one scan can be trained under sun, overcast and twilight.
- **Numbers:** "up to 10 m/s" (not ">10"). Real twilight success 3/10 → 8/10 with relighting (Table IV).
- **⚠ Caveat:** the map was captured with **LiDAR + RTK** plus cameras. The drone flies with one camera, but the twin is not photo-only. Our 10-24 notes need both corrections.
- **Why for us:** the measured answer to Swift's lighting weakness. Use with the caveat. Grade B -1run.

### E6. Xie et al. (2025): "Vid2Sim: Realistic and Interactive Simulation from Video for Urban Navigation"
- **Citation:** CVPR 2025. arXiv 2501.06693. https://arxiv.org/abs/2501.06693
- **Technology:** twins from **15-second web walking videos** (splats for looks + a hidden mesh for physics).
- **Numbers:** zero-shot real success 85/65/55% on three tasks vs 0% for a mesh-simulator baseline.
- **Why for us:** a twin even from internet video. Ground robot, not a drone. Grade B -1run.

### E7. Wang et al. (Oxford + Meta, 2025): "VGGT: Visual Geometry Grounded Transformer"
- **Citation:** CVPR 2025. arXiv 2503.11651. https://arxiv.org/abs/2503.11651
- **Technology:** one network pass predicts camera positions and 3D points from a handful of photos ("feed-forward reconstruction"). GaussGym (arXiv 2510.15352) uses it to build robot twins.
- **Number:** ~0.2 s for camera poses of ~10 images vs ~15 s for a COLMAP-based pipeline (Table 1).
- **Why for us:** the "photos → 3D in under a second" future of twin building. Grade B.

### E8. Limits of splat twins (measured)
- **Extrapolated views:** 3DGS image quality (PSNR) drops 23-29% away from the capture path. EUVS benchmark, ICCV 2025, arXiv 2412.05256 (driving scenes).
- **No physics:** splats need a separate collision mesh. NVIDIA's own tutorial says the scene has "no inherent collision properties".
- **Correlation:** when splat twins are used to *evaluate* policies, sim and real success correlate strongly (r ≈ 0.90-0.94 on manipulation; arXiv 2511.04665). Without physics tuning it drops to 0.69 on one task. No such study exists for drones.

---

## F. A little real data

### F1. Maddukuri et al. (NVIDIA + UT Austin, 2025): "Sim-and-Real Co-Training: A Simple Recipe for Vision-Based Robotic Manipulation"
- **Citation:** arXiv 2503.24361. https://arxiv.org/abs/2503.24361
- **Technology:** mix simulated and real examples in every training batch.
- **Numbers:**
  - Average real success 45.3% (real only, 20-50 demos per task) → 83.2% when co-trained with task-matched sim ("digital cousin") and prior sim data (Table I).
  - On one task (CupPnP), the best mix was ~99% sim per batch; 99.9% sim fell from 95% to 60% (§V-D).
  - Matching the sim camera to the real camera mattered (67% → 56% when misaligned).
- **Why for us:** "how much real data is enough?" has a measured answer: a little real is worth a lot. Grade B -coi.

### F2. Torne et al. (MIT, 2024): RialTo, "Reconciling Reality through Simulation: A Real-to-Sim-to-Real Approach"
- **Citation:** RSS 2024. arXiv 2403.03949. https://arxiv.org/abs/2403.03949
- **Technology:** scan the real scene with a phone (~15 min), build a twin, train with RL there, bring the skill back.
- **Numbers:** 91/77/75% real success vs 10/0/0% for plain imitation (book-on-shelf, 15 demos).
- **⚠ Caveat:** its real policies use depth-camera point clouds, not RGB. Grade B.

---

## G. What's next: learned world models

### G1. Hafner, Pasukonis, Ba, Lillicrap (2025): DreamerV3, "Mastering diverse control tasks through world models"
- **Citation:** Nature 640, 647-653 (2 Apr 2025). doi:10.1038/s41586-025-08744-2. Open copy: https://pmc.ncbi.nlm.nih.gov/articles/PMC12003158/ (bot-gated; preprint: https://arxiv.org/abs/2301.04104)
- **Technology:** one world-model recipe (learn the world, practise in imagination) that works on more than 150 tasks with the same settings.
- **Number:** "the first algorithm to collect diamonds in Minecraft from scratch without human data or curricula". The input is a 64×64 image.
- **Why for us:** proof that "practise in a learned dream" works at scale. Grade B.

### G2. Assran et al. (Meta, 2025): "V-JEPA 2: Self-Supervised Video Models Enable Understanding, Prediction and Planning"
- **Citation:** arXiv 2506.09985 (11 Jun 2025). https://arxiv.org/abs/2506.09985. Blog: https://ai.meta.com/blog/v-jepa-2-world-model-benchmarks/
- **Technology:** JEPA: predict a compact summary of the next moment, not pixels. A ~1B-parameter encoder pretrained on more than 1M hours of video; the robot version was post-trained on **under 62 h** of unlabelled robot video.
- **Numbers:** zero-shot pick-and-place in two new labs, 80% (cup) / 65% (box) vs 15% / 10% for the Octo baseline. 10 trials per condition.
- **Own limits:** sensitive to camera position; errors accumulate over long horizons.
- **Why for us:** LeCun's "world model" line made concrete. Note it is a robot arm, not a drone. Grade B -1run -coi.

### G3. Romero, Shenai, Geles, Aljalbout, Scaramuzza (UZH, 2025): "Dream to Fly: Model-Based Reinforcement Learning for Vision-Based Drone Flight"
- **Citation:** arXiv 2501.14377 (ICRA 2026). https://arxiv.org/abs/2501.14377
- **Technology:** DreamerV3 learns to fly through gates from **64×64 camera pixels only**. Model-free PPO and SAC made no real progress in the same setting.
- **Numbers:**
  - Up to 9 m/s on a real drone, but the **images were rendered** (Habitat simulator) while the real drone flew. A real camera is named as an open challenge.
  - No success rates reported; ~240 h of training.
- **Why for us:** Swift's own lab moving from "hide the pixels" to "learn from pixels with a world model". The honest bridge to "what's next". Grade B -indirect.

### G4. Rao, Zhang, Balestriero, LeCun, Loianno (2026): "SkyJEPA: Learning Long-Horizon World Models for Zero-Shot Sim-to-Real Control of Quadrotors"
- **Citation:** arXiv 2606.23444 (22 Jun 2026). https://arxiv.org/abs/2606.23444
- **Technology:** a JEPA-style world model of the drone's flight. **Inputs are numbers (position, speed, tilt, motor forces), not camera images.** Trained only in a randomized physics simulator (20k trajectories), ~99K parameters, runs onboard.
- **Numbers:** real outdoor circle, position error 0.24 m vs 0.39 m for the baseline (5 flights per condition).
- **Why for us:** "JEPA reached drones" (fresh, June 2026), but **not vision**. Say so if used. Grade B -1run.

### G5. Industry world models (demos)
- **DeepMind Genie 2** (Dec 2024): stays consistent up to ~1 min. https://deepmind.google/discover/blog/genie-2-a-large-scale-foundation-world-model/
- **Genie 3** (5 Aug 2025): 720p, 24 fps, real time, consistent for "a few minutes". https://deepmind.google/blog/genie-3-a-new-frontier-for-world-models/ ; video https://www.youtube.com/watch?v=PDKhUknuQDg
- **NVIDIA Cosmos** (Jan 2025): ~20M h of video. The paper itself reports "object impermanence" and "violation of gravity". arXiv 2501.03575.
- **Wayve GAIA-2/3, Waymo World Model** (built on Genie 3, Feb 2026): see `04-companies-products.md`.
- All are grade C: no published measurement that a robot trained in them works better in reality.

---

## H. Counter-evidence: say this to sound credible

| Finding | Number | Source | Grade |
|---|---|---|---|
| Video generators don't understand physics | Physics-IQ, real videos = 100: best model (VideoPoet multiframe) 29.5, Sora 10.0. "Severely limited, and unrelated to visual realism" | Motamed et al. 2025, arXiv 2501.09038, Table 1 | B |
| Intuitive physics gap | IntPhys 2: humans 96.4%, best model (V-JEPA 2) 57.5%, others 49-56% (≈ chance) | Meta, arXiv 2506.09849, Table 2 | B -coi |
| Video models copy, not generalize | Out of distribution they imitate the closest training case (colour > size > velocity > shape) | Kang et al., ICML 2025, arXiv 2411.02385 | B -abstract |
| Image translation closes only part of the gap | CyCADA: game → real city segmentation 17.9 → 35.4 mIoU, vs 60.3 trained on real | Hoffman et al., ICML 2018, arXiv 1711.03213, Table 4 | B |
| Pretrained "foundation" encoders don't fix transfer | Navigation 90% vs 10%, but most real manipulation tasks "drop to near zero" | Silwal et al. 2023/24, arXiv 2310.02219 | B |
| Splat twin = one place | SOUS VIDE: objects removed, 0/5 fully successful | arXiv 2412.16346 | B -1run |
| Swift = one look | "If this assumption fails, the system can fail" (appearance, lighting) | Swift, Nature 2023, Discussion | B |

---

## I. Explainers (watch or read first)

| Topic | Pick | Date | Length |
|---|---|---|---|
| Swift | UZH official video https://www.youtube.com/watch?v=fBiataDpGIo | 2023-08-30 | 4:50 |
| Swift, short hook | IEEE Spectrum https://www.youtube.com/watch?v=EtRXay2kqtc | 2023-09-21 | 0:49 |
| Domain randomization | Lilian Weng blog https://lilianweng.github.io/posts/2019-05-05-domain-randomization/ | 2019-05-05 | ~14 min read |
| Rubik's cube | OpenAI https://www.youtube.com/watch?v=x4O8pojMF0w | 2019-10-15 | 2:51 |
| CycleGAN | Computerphile https://www.youtube.com/watch?v=T-lBMrjZ3_0 | 2019-08-01 | 13:10 |
| Game → photoreal (sim-to-real look) | Two Minute Papers on Intel's GTA work https://www.youtube.com/watch?v=22Sojtv4gbg | 2021-06-05 | 8:03 |
| NeRF | Computerphile https://www.youtube.com/watch?v=wKsoGiENBHU | 2023-12-21 | 13:35 |
| Gaussian splatting | Computerphile https://www.youtube.com/watch?v=VkIJbpdTujE | 2024-03-14 | 17:39 |
| Gaussian splatting (text) | Hugging Face https://huggingface.co/blog/gaussian-splatting | 2023-09-18 | ~7 min read |
| World models | Computerphile "World Foundation Models" https://www.youtube.com/watch?v=pdV9eaLy--E | 2025-07-04 | 12:47 |
| Genie 3 | DeepMind https://www.youtube.com/watch?v=PDKhUknuQDg | 2025-08-05 | 2:23 |
| "Learning in a dream" | Two Minute Papers #247 https://www.youtube.com/watch?v=gvjCu7zszbQ | 2018-05-06 | 2:51 |
| Kalman filter | MATLAB Part 1 https://www.youtube.com/watch?v=mwn8xhgNpFY | 2017-01-31 | 6:47 |
| VIO | ModalAI https://www.youtube.com/watch?v=lIdmbrRahk8 | 2025-05-09 | 7:11 |

Ukrainian-language explainers: none of quality found. The only item is a short Mezha.ua news piece on Swift (2023-09-01) without technical detail.

---

## Corrections to our earlier notes (`AI_research/04_thorenko_research.md`, 10-24)

1. arXiv 2406.15149 is the MIT "Liquid Networks" paper, not a "dynamics-augmented Gaussian splatting simulator".
2. arXiv 2602.07101 says "up to 10 m/s", not "above 10 m/s", and its map used LiDAR + RTK.
3. Swift's "800-1,000 samples per track" is the k-NN training set. The real data was 3 rollouts / ~50 s.
4. AirSim is not archived. Microsoft announced its retirement in 2022; IAMAI maintains Project AirSim. This matters to block 2, not us.
5. SkyJEPA uses no camera: present it as "world models reached drone control", not as the vision path.

## Verification (round 3, 2026-10-26)

Two separate verifier agents re-opened the primary sources for 20 slide-critical claims, given only the claim and the URL. All URLs were live; the Nature/PMC pages were read through PMC, ar5iv or arXiv HTML.

| Claim | Verdict |
|---|---|
| Swift: 3 rollouts / ~50 s; no dynamics randomization; GP + k-NN residuals | confirmed |
| Swift: 5/9, 4/7, 6/9 wins; "a lead of half a second" | confirmed |
| Swift: "If this assumption fails, the system can fail"; policy sees no images; onboard only | confirmed |
| MonoRace: one camera, "3500:500", "≥40%" (training; evaluation 30%), 16.56 s, 28.23 m/s, April 2025 | confirmed. PPO confirmed in the round-2 full read, not by the verifier (page cut off) |
| Deep Drone Acrobatics: raw image 0% vs feature tracks 100% | partly: the table is **in simulation**, on unseen backgrounds (image policy 80% on the training background). Real transfer "with no fine-tuning" is confirmed separately |
| RCAN: 70% zero real; 91% @ 5,000 vs QT-Opt 87% @ 580,000 | confirmed |
| Deep Drone Racing: 95%, 12.1 s vs 5.0 s | confirmed (static track; dynamic track 15.0 s vs 6.5 s; pilot completed 90%/80%) |
| Aljalbout: "which we call reality gap" (§1); reality vs performance gap (§2.3) | confirmed |
| 3DGS: 134 fps / 41m33s vs 0.06 fps / 48 h | confirmed |
| NeRF: ~30 s per frame; 1-2 days on V100 | confirmed (§5.3, App. D) |
| SOUS VIDE: 2-3 min video, up to 130 fps (peak), 105 flights, objects removed 0/5 fully successful | confirmed |
| FalconGym 2.0: 3-min drone-camera video, COLMAP + Splatfacto, 98.6% | partly: 98.6% is from the abstract; Table II shows 96.7/100/100* (fixed above) |
| GRaD-Nav: real 7,7,6/10; without the module 0,2,1/10 | partly: sim without the module is 9, 10, 7/10 (fixed above) |
| V-JEPA 2: <62 h; 80%/65% vs 15%/10% | confirmed (Table 3 average) |
| DreamerV3: first Minecraft diamonds from scratch | confirmed |
| Physics-IQ: 100 / 29.5 / 10.0 + quote | confirmed |
| SkyJEPA: state inputs, sim-only training, 0.24 vs 0.39 m | confirmed (baseline = MPPI with a standard predictive model; state from onboard GPS-based localization) |
| Co-training: 45.3% → 83.2%; 99% best | partly: 83.2% includes digital-cousin + prior sim; 99% is for one task (fixed above) |

Caveat: the verifiers read pages through a summarising fetch tool. Quotes match in meaning, but check exact wording against the PDF before putting a quote on a slide.
