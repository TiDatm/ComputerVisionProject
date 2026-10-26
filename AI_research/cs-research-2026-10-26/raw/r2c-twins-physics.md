# R2c: Camera-only digital twins and their physics gap (round 2)

Researched 2026-10-26. Block 4 of "How to teach a machine to see: from simulation to the real world (drones)".
Team rule: twins built from photos or video only, no LiDAR.

Grades: A = peer-reviewed or full-text primary with tables · B = arXiv full text · C = vendor or company · D = secondary.
Flags: -coi (author is the vendor) · -1run (single setup or small n) · -indirect (not drones, or not policy training) · -abstract/-snippet (only the abstract or a snippet was read).
In every quote, "..." marks text that was cut. Anything marked **[inference]** is my reasoning, not a source claim.

## Summary

1. **New drone results with camera-only twins (2025-2026).**
   - FalconGym 2.0 (UIUC + Toyota, arXiv 2510.02248, ICRA 2026) is the cleanest new example. A 3-minute video from the drone's own camera goes through COLMAP and Splatfacto to make the twin. The real drone passed 69 of 70 gates over 30 trials (98.6%).
   - SINGER (Stanford MSL, arXiv 2509.18610, ICRA 2026) trains a language-guided drone in five Nerfstudio splat scenes. On hardware it reached the goal in 23/30 trials (76.7%) against 16/30 for the baseline. The paper does not say how its scenes were captured.
   - GRaD-Nav++ (Stanford MSL, arXiv 2506.14009) reports 67% success on trained tasks and 50% on unseen tasks, with 3 trials per task. The capture method is not stated.
   - FalconGym 1 (arXiv 2503.02198, IROS 2025) is a NeRF twin built from about 1500 camera images, with poses from Vicon motion capture. Hardware success was 95.8% over 120 gates.
   - GaussFly (arXiv 2604.05062, 2026) only says "zero-shot real" in its abstract, with no numbers.
   - I found no UZH or TU Delft paper that trains a drone policy in a splat twin.
2. **Full-text tables from the three non-drone papers.**
   - SplatSim (manipulation): 86.25% success zero-shot from the splat sim, against 97.5% for a policy trained on real data (Table I, 40 trials per task).
   - VR-Robo (legged robot, RGB only): 100%, 93.33% and 100% success on easy, medium and hard, against 0-20% for a textured-mesh simulator (Table I, 15 trials per level). Its twin comes from iPhone or iPad photos.
   - GaussGym: confirmed to use VGGT, a feed-forward reconstruction model. VGGT gives the camera poses and point clouds; NKSR then builds the collision mesh. The paper reports no real-world numbers and only "partial transfer".
3. **Physics gap, quantified.** arXiv 2511.04665 (Columbia, SceniX and Google DeepMind) is verified.
   - Sim-versus-real Pearson r is 0.944 (toy), 0.901 (rope) and 0.915 (T-push).
   - Removing physics optimisation drops r to 0.694 on the toy task. IsaacLab gets r = 0.237 on rope.
   - PolaRiS (2512.16881) reports average r ≈ 0.9 and a worst case of 0.81.
   - ReVeal (2609.23910, phone video twins) links better geometry to better agreement: r rises from 0.773 (2DGS) to 0.960 (PGSR-D).
   - For mesh error, the 2DGS paper gives DTU mean Chamfer of 1.96 for plain 3DGS against 0.80 for 2DGS. VR-Robo says RGB-only meshes "require manual post-processing".
4. **Industry pipeline.**
   - NVIDIA documents a smartphone-photos → COLMAP → 3DGUT → Isaac Sim pipeline (blog of 2025-10-23). It states the splat scene has "no inherent collision properties" and reports no measured robot result (grade C).
   - DJI Terra 5.0 exports splats as PLY. I found nothing documented on loading them into a simulator.
   - The best public end-to-end pipeline that comes with measured flights is open-source academic: SOUS VIDE / FiGS (GPL-3.0). Its README still listed deployment code as "COMING SOON: Oct 2025".

## Claims table

| # | Claim | Number / quote | Source (date) | Grade |
|---|---|---|---|---|
| 1 | FalconGym 2.0 twin captured with drone's onboard camera, video only | §III-A: "a human operator uses the onboard camera to capture a 3-minute video across the flying arena from diverse viewpoints"; COLMAP + ArUco alignment + NeRFStudio Splatfacto | arXiv 2510.02248 v1, 2025-10-02, Miao, Yuceel, Fainekos, Hoxha, Okamoto, Mitra (UIUC, Toyota) | B |
| 2 | FalconGym 2.0 real flights | Abstract: "98.6% success rate (69 / 70 gates) over 30 trials spanning two three-gate tracks and a moving-gate track". Table II real: Left-Turn 96.7%, Random 100%, Moving 100% (moving track lateral only); mean gate error 12.7-17.7 cm against 5.9-11.6 cm for the state-based expert | same | B, -1run (one arena) |
| 3 | FalconGym 1: NeRF twin from about 1500 images; poses from Vicon, not COLMAP | §III-A: about 1500 images "with an ArduCam RGB camera"; the Vicon system "eliminates the need for Structure from Motion"; no LiDAR | arXiv 2503.02198 v2, 2025-08-01 (v1 2025-03-04), IROS 2025 | B |
| 4 | FalconGym 1 real results | "95.8% success rate and an average error of just 10 cm" over 30 flights and 120 gates. Table III: Circle 100%, U-turn 87.5%, Figure-8 100% | same | B |
| 5 | SINGER: splat sim (Nerfstudio), 5 environments, about 1650 expert trajectories | "approximately 1650 trajectories across 15 different semantic queries in 5 different 3DGS environments"; capture method **not stated** | arXiv 2509.18610 v1, 2025-09-23, Adang, Low, Shorinwa, Schwager (Stanford) | B |
| 6 | SINGER hardware, unseen office mock-up, 30 trials per policy | Within 1 m of goal: 23/30 (76.67%) against baseline 16/30 (53.33%); collisions 5/30 against 8/30; fully onboard variant 20/30 | same, §VI-D | B, -1run |
| 7 | GRaD-Nav++ hardware | Abstract: "67% success on trained tasks and 50% on unseen ones" (Table III: 16/24, 6/12; 3 trials per task); capture not stated | arXiv 2506.14009 v1, 2025-06-16 (Stanford MSL) | B, -1run (small n) |
| 8 | GaussFly claims zero-shot real transfer, no numbers | "robust and zero-shot policy transfer to unseen real-world environments with complex textures" | arXiv 2604.05062, 2026-04-06 | B-abstract |
| 9 | Splat-Nav (planning in a splat map, not a learned policy): 126 hardware flights | Abstract-level summary | arXiv 2403.02751, IEEE T-RO 2025 | B-snippet, -indirect |
| 10 | SplatSim: real scene from RGB video, zero-shot to real | Abstract: "average success rate of 86.25%, compared to 97.5% for policies trained on real-world data". Table I, 40 trials per task: T-Push 90%, Apple 95%, Orange-on-plate 90%, Assembly 70% | arXiv 2409.10161 v3, 2024-10-07, CMU | B, -indirect (manipulation) |
| 11 | VR-Robo: twin from iPhone or iPad photos, COLMAP, planar 3DGS; collision mesh by TSDF fusion of rendered depth | §V-A: "iPads or iPhones to take photos"; §VI-A: "meshes reconstructed using only RGB input often contain structural imperfections and require manual post-processing" | arXiv 2502.01536 v3, 2025-06-03, RA-L (Tsinghua group, Hang Zhao) | B, -indirect (legged) |
| 12 | VR-Robo real results (RGB goal-reaching, 15 trials per level) | Table I success rate, easy / medium / hard: Ours 100 / 93.33 / 100%; textured-mesh simulator 20 / 6.67 / 0%; without domain randomisation 53.33 / 6.67 / 0%; imitation learning 0% | same | B, -indirect |
| 13 | GaussGym uses VGGT (feed-forward reconstruction) for every input, including phone scans | §3: "All inputs are standardized via the ... (VGGT)"; splats are initialised from VGGT point clouds; meshes come from NKSR on VGGT points and normals | arXiv 2510.15352 v1, 2025-10-17, Escontrela, Kerr, ..., Abbeel (Berkeley, ETH, Amazon FAR) | B |
| 14 | GaussGym real results only qualitative | Conclusion: "show partial transfer to real-world scenarios"; A1 stair climbing zero-shot; §5 notes "a decline in the precise foot placement" on the real robot; **no numbers, no mesh-error metric** | same | B, -indirect |
| 15 | Splat twin predicts real policy ranking (manipulation, soft bodies) | Table I, MMRV / r: toy 0.076 / 0.944, rope 0.174 / 0.901, T-push 0.108 / 0.915. Without physics optimisation: r = 0.694 / 0.832 / 0.905. IsaacLab: rope 0.237, T-push 0.649 | arXiv 2511.04665 v2, 2025-11-10 (v1 2025-11-06), Zhang, Sha, ..., Yunzhu Li (Columbia, SceniX, Google DeepMind) | B, -indirect. Note: training cameras are RealSense RGB-D; the splat comes from an iPhone app (Scaniverse) |
| 16 | PolaRiS: twin from a 2-5 min monocular video, 2DGS, TSDF mesh into IsaacSim | Average r ≈ 0.9; worst environment r = 0.81; 4 policies × 6 environments × 20 real rollouts. Appendix D.1: sim visuals with real dynamics cost about 25% performance; sim dynamics with real visuals cost none | arXiv 2512.16881 v1, 2025-12-18 (UW, Berkeley, Stanford, TRI, Physical Intelligence and others) | B, -indirect. The r values are read from figures |
| 17 | Better twin geometry gives better sim-real agreement | Table II, Pearson r / MMRV: 2DGS 0.773 / 0.235, PGSR 0.850 / 0.205, PGSR-D 0.960 / 0.102. Capture: "short RGB video (1-3 minutes) ... using a mobile phone" | arXiv 2609.23910, 2026-09-20, ReVeal (Purdue and co-authors) | B, -indirect, -1run (8 tasks) |
| 18 | Mesh accuracy of splats: plain 3DGS geometry is poor | DTU mean Chamfer (Table 1, mm per DTU convention): 3DGS 1.96, SuGaR 1.33, 2DGS 0.80. Tanks and Temples F1 mean: 3DGS 0.09, 2DGS 0.32 | arXiv 2403.17888 v3 (2DGS, SIGGRAPH 2024) | B, -indirect (object scale) |
| 19 | NVIDIA smartphone → Isaac Sim pipeline has no physics by default, no measured result | "Your reconstructed scene is simply visual geometry ... with no inherent collision properties"; adds a ground plane and a proxy mesh by hand | NVIDIA developer blog, 2025-10-23, Han and Deutsch | C, -coi |
| 20 | NuRec input | "reconstructs camera or lidar data ... and packages the result as a USD scene"; no drone or policy result on the page | developer.nvidia.com/omniverse/nurec (undated) | C, -coi |
| 21 | DJI Terra 5.0 exports splats as PLY or 3DTiles | No simulator import documented | dronedj.com 2025-07-21; radiancefields.com | D-snippet |
| 22 | SOUS VIDE code is public (GPL-3.0); hardware deployment not released when read | README: "COMING SOON: Oct 2025" for deployment | github.com/StanfordMSL/SousVide (read 2026-10-26) | B (repo) |

## Plain descriptions (first-year level)

**1. FalconGym 2.0: "film the room, then practise in the copy" (drone racing gates).**
Someone holds the drone and walks around the flight room for 3 minutes, filming with the drone's own camera. Software (COLMAP) works out where the camera was for each frame. A second program (Splatfacto) turns the frames into millions of small coloured blobs, called Gaussians, that look like the room from any angle. Inside this copy the team can move or add racing gates by code in milliseconds, so the drone practises on many tracks it never saw for real. The trained network then flew the real drone through 69 of 70 gates over 30 runs. It never saw a real flight during training. Caveats: one indoor arena, a gentle task, and the robot's state likely comes from motion capture.
Source: arXiv 2510.02248, Table II.

**2. VR-Robo: why the twin needs a solid floor as well as a pretty picture (robot dog).**
The team took iPhone and iPad photos of rooms and built two things from them. The first is a splat that gives the camera realistic images. The second is a mesh, a surface the physics engine can stand on. A robot dog learned to walk to a coloured cone using only its RGB camera. Trained this way it reached the cone 93-100% of the time in the real world. Trained in a normal textured-mesh simulator it reached it 0-20% of the time. The catch: the authors say meshes made from photos alone "require manual post-processing", so the physics half still needs human clean-up.
Source: arXiv 2502.01536, Table I, §VI-A.

**3. Can we trust a test inside the twin? (Columbia, 2511.04665, plus ReVeal).**
Before flying, you would like to test many versions of a policy inside the copy and keep only the best. These papers check whether the ranking in the copy matches the ranking in reality. When images and physics are both tuned, the match is strong (r ≈ 0.90-0.94, where 1.0 is perfect). Without physics tuning it can fall to 0.69, and a standard simulator scored 0.24 on rope. ReVeal shows the same effect for geometry: a flatter, more accurate reconstruction lifted agreement from 0.77 to 0.96. Lesson for drones **[inference]**: a splat twin is a good camera simulator, but its collision geometry and physics are the weak half, and they need checking.

## Not found / gaps

- **UZH or TU Delft** drone policy trained in a splat twin with real flights: not found (two searches). A UZH student-project listing about RL on splat race tracks exists (sirop.org), but it is not a paper.
- **"Gaussian splatting drone racing" with real flights:** only the FalconGym line (UIUC) turned up.
- **Capture method for SINGER and GRaD-Nav++:** not stated in the HTML I read, so I cannot confirm they are camera-only. Round 1 found SOUS VIDE / GRaD-Nav used handheld video.
- **GaussGym real numbers and mesh error:** none in the paper.
- **A drone-specific sim-versus-real correlation study** for splat twins: none found. All correlation studies are manipulation (-indirect).
- **Collision-mesh error measured inside a robot twin (cm-level):** none found. The closest are object-scale DTU Chamfer (2DGS paper) and ReVeal's unitless planarity metric.
- **Industry end-to-end pipeline with a measured flight result:** not found. NVIDIA's pipeline is documented without results, and its NuRec page also accepts LiDAR. DJI Terra stops at PLY export. Parallel Domain mentions drones only in an interview (not read in full).
- **SOUS VIDE successor named "SplatNav":** Splat-Nav (2403.02751) is a 2024 planning-and-localisation paper, not a successor. SINGER and GRaD-Nav++ are the actual 2025 MSL follow-ups.

## Sources (accessed 2026-10-26)

- https://arxiv.org/html/2510.02248v1
- https://arxiv.org/html/2503.02198v2
- https://arxiv.org/html/2509.18610v1
- https://arxiv.org/html/2506.14009v1
- https://arxiv.org/abs/2604.05062
- https://arxiv.org/abs/2602.11575 (ReaDy-Go: platform not specified, no numbers in abstract)
- https://arxiv.org/html/2403.02751v2
- https://arxiv.org/html/2409.10161
- https://arxiv.org/html/2502.01536v3
- https://arxiv.org/html/2510.15352v1
- https://arxiv.org/html/2511.04665v2
- https://arxiv.org/html/2512.16881v1
- https://arxiv.org/html/2609.23910
- https://arxiv.org/html/2403.17888v3
- https://developer.nvidia.com/blog/reconstruct-a-scene-in-nvidia-isaac-sim-using-only-a-smartphone/
- https://developer.nvidia.com/omniverse/nurec
- https://github.com/StanfordMSL/SousVide
- https://dronedj.com/2025/07/21/dji-terra-drone-gaussian-splatting/
