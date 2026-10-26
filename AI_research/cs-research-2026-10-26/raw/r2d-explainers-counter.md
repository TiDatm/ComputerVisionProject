# R2d: Explainers + world-model counter-evidence (block 4)

Researched 2026-10-26. YouTube metadata (title, channel, publish date, length) was taken from each watch page's own HTML (`publishDate` and `lengthSeconds` fields) with curl on 2026-10-26, so every YouTube URL below resolved that day. Text pages were checked with an HTTP 200 and read with WebFetch.

Grades: A = primary source, read directly. B = primary source, but partial or read through a summarizer. C = secondary source. D = unverified.
Flags: -coi (the creator has a stake), -1run, -indirect, -abstract, -snippet.

Language: unless marked UA, every item is in English. Note: YouTube shows auto-translated titles to Ukrainian-locale viewers (for example, DataMListic HZgv6B9Sq1E shows a Ukrainian title), but the audio is English. Do not count those as Ukrainian sources.

## A. Explainers

### 1. Swift drone race (UZH, Nature 2023)

| Title | Creator | URL | Date | Length | Level / accuracy note | Grade |
|---|---|---|---|---|---|---|
| Champion-level Drone Racing using Deep Reinforcement Learning (Nature, 2023) | UZH Robotics and Perception Group (official) | https://www.youtube.com/watch?v=fBiataDpGIo | 2023-08-30 | 4:50 | The official paper video, also embedded on UZH News. It shows the races and the sim-training idea. Easiest primary-source video to show in class. | A -coi |
| Zurich Drone Racing: AI vs Human | IEEE Spectrum | https://www.youtube.com/watch?v=EtRXay2kqtc | 2023-09-21 | 0:49 | Short independent press clip. Good as a hook. Contains no technical content. | C |
| Human-Level Performance with Autonomous Vision-based Drones (talk) | Davide Scaramuzza, UZH RPG channel | https://www.youtube.com/watch?v=vVataTRomsg | 2023-11-13 | 1:26:22 | Full lecture by the PI. Too long for students; useful only for team members who want to pick clips (sim-to-real residual models, limitations). | A -coi |
| UA: "ШІ-дрон Swift вперше переміг людину в гонці безпілотників" | Mezha.ua (Yevhenia Hubina), based on Ars Technica | https://mezha.ua/2023/09/01/shi-dron-swift/ | 2023-09-01 | ~300 words | A short Ukrainian news item. It does not mention Nature, simulation training or the 15/25 figure, so do not cite it for numbers. | C -indirect |

Supporting text sources: the UZH News release (https://news.uzh.ch/en/articles/media/2023/Drone-race.html, 2023-08-30) and the paper (Kaufmann et al., Nature 620:982-987, DOI 10.1038/s41586-023-06419-4, HTTP 200). No full documentary about Swift was found (see Not found).

### 2. Sim-to-real gap and domain randomization

| Title | Creator | URL | Date | Length | Level / accuracy note | Grade |
|---|---|---|---|---|---|---|
| Solving Rubik's Cube with a Robot Hand | OpenAI (official) | https://www.youtube.com/watch?v=x4O8pojMF0w | 2019-10-15 | 2:51 | Classic demo of training only in simulation with Automatic Domain Randomization. Promotional framing: press at the time reported only about 20% success on full scrambles, so state that caveat. | A -coi |
| OpenAI's Robot Hand Won't Stop Rotating The Rubik's Cube | Two Minute Papers | https://www.youtube.com/watch?v=9b2dxc1QalM | 2019-11-22 | 5:00 | Plain-language retelling of the same work, at first-year level. Enthusiastic tone. | C |
| Domain Randomization for Sim2Real Transfer (blog) | Lilian Weng | https://lilianweng.github.io/posts/2019-05-05-domain-randomization/ | 2019-05-05 | ~14 min read | The best written overview: uniform and guided DR, and why DR works. Mid-level math; students can skip the equations. | B (survey) |
| Bridging the Sim-to-Real Gap for Accelerated Robot Training | NVIDIA | https://www.youtube.com/watch?v=tudMMCq04SA | 2025-08-11 | 2:19 | Short, current industry framing (Isaac). Marketing material. | C -coi |
| (optional, deep) Lecture 22 Sim2Real and Domain Randomization, CS287 | Pieter Abbeel, UC Berkeley | https://www.youtube.com/watch?v=ac_W9IgKX2c | 2020-02-16 | 1:23:20 | University lecture, for presenters only. | A |

### 3. CycleGAN and image-to-image translation

| Title | Creator | URL | Date | Length | Level / accuracy note | Grade |
|---|---|---|---|---|---|---|
| Zebras, Horses & CycleGAN | Computerphile | https://www.youtube.com/watch?v=T-lBMrjZ3_0 | 2019-08-01 | 13:10 | The best plain-language explanation of cycle-consistency, by an academic (Univ. of Nottingham). | B |
| AI Learns to Synthesize Pictures of Animals (Two Minute Papers #152) | Two Minute Papers | https://www.youtube.com/watch?v=D4C1dB9UheQ | 2017-05-10 | 3:54 | The video description confirms it covers the CycleGAN paper (Zhu et al. 2017). Very short. | C |
| Intel's Video Game Looks Like Reality! | Two Minute Papers | https://www.youtube.com/watch?v=22Sojtv4gbg | 2021-06-05 | 8:03 | Covers "Enhancing Photorealism Enhancement" (Intel ISL): it turns GTA V frames into realistic-looking images, the sim-to-real image-translation idea in action. Strong visual for the slide. | C |

Project page: https://junyanz.github.io/CycleGAN/ (HTTP 200).

### 4. NeRF and 3D Gaussian splatting

| Title | Creator | URL | Date | Length | Level / accuracy note | Grade |
|---|---|---|---|---|---|---|
| NERFs (No, not that kind) - Neural Radiance Fields | Computerphile | https://www.youtube.com/watch?v=wKsoGiENBHU | 2023-12-21 | 13:35 | Plain-language explanation of NeRF from an academic. | B |
| 3D Gaussian Splatting! | Computerphile | https://www.youtube.com/watch?v=VkIJbpdTujE | 2024-03-14 | 17:39 | Companion video. Explains splats compared with NeRF. | B |
| This Neural Network Learned To Look Around In Real Scenes! (NERF) | Two Minute Papers | https://www.youtube.com/watch?v=nCpGStnayHk | 2020-04-11 | 4:34 | Short and visual. | C |
| Wow, NVIDIA's Rendering, But 10X Faster! (3D Gaussian Splatting) | Two Minute Papers | https://www.youtube.com/watch?v=TLK3TDDcJFU | 2023-09-02 | 6:53 | Short and visual. The title credits NVIDIA, but the 3DGS paper is from Inria (Kerbl et al. 2023), so the title is misleading. | C |
| Introduction to 3D Gaussian Splatting (blog) | Dylan Ebert, Hugging Face | https://huggingface.co/blog/gaussian-splatting | 2023-09-18 | ~6-8 min read | Written walkthrough (SfM, then Gaussians, then rasterization). Somewhat technical. | B |
| 3D Gaussian Splatting - Explained! | Bilawal Sidhu | https://www.youtube.com/watch?v=sQcrZHvrEnU | 2023-11-05 | 8:28 | Practitioner-oriented. A good alternative to Two Minute Papers. | C |

UA: one Ukrainian academic article on 3DGS (Vdovychenko, KNU repository, https://ir.library.knu.ua/bitstreams/76df7b0c-4392-46ab-a51e-1d9601c75d73/download) showed up in search, but the page returned a bot-protection "Access Denied" page. Not verified (grade D).

### 5. World models (Genie 3, V-JEPA 2, Cosmos, Dreamer)

| Title | Creator | URL | Date | Length | Level / accuracy note | Grade |
|---|---|---|---|---|---|---|
| Genie 3: Creating dynamic worlds that you can navigate in real-time | Google DeepMind (official) | https://www.youtube.com/watch?v=PDKhUknuQDg | 2025-08-05 | 2:23 | Official demo. Pair it with the blog's own limits: "a few minutes" of consistency, 720p at 24 fps, limited action space, no accurate real-world locations (https://deepmind.google/discover/blog/genie-3-a-new-frontier-for-world-models/, 2025-08-05). | A -coi |
| DeepMind Just Made The Most Powerful Game AI Engine! | Two Minute Papers | https://www.youtube.com/watch?v=YvuEKrJhjos | 2025-08-17 | 6:42 | The description links the Genie 3 blog, so this is the Genie 3 episode. Hype-y but accessible. | C |
| World Foundation Models | Computerphile | https://www.youtube.com/watch?v=pdV9eaLy--E | 2025-07-04 | 12:47 | Neutral explainer of "world foundation models" (the Cosmos family). | B |
| NVIDIA Cosmos: A World Foundation Model Platform for Physical AI | NVIDIA (official) | https://www.youtube.com/watch?v=9Uch931cDx8 | 2025-01-06 | 2:22 | Official launch video (CES 2025). Marketing. | A -coi |
| Introducing the V-JEPA 2 world model and new benchmarks for physical reasoning (blog) | Meta AI (official) | https://ai.meta.com/blog/v-jepa-2-world-model-benchmarks/ | 2025-06-11 | blog | Official post. Its numbers: 1M+ hours of video, 62 h of robot data, 65-80% pick-and-place success. It also admits humans score 85-95% on the new benchmarks while models fall "notably short". No embedded video. | A -coi |
| This AI Learns From Its Dreams (Two Minute Papers #247) | Two Minute Papers | https://www.youtube.com/watch?v=gvjCu7zszbQ | 2018-05-06 | 2:51 | The original "World Models" idea (Ha & Schmidhuber 2018), which Dreamer builds on. Good intuition for "learning inside a dream". | C |
| But what exactly are world models? | Julia Turc | https://www.youtube.com/watch?v=MqjvfJTCuqw | 2026-05-23 | 28:50 | Longer, more careful overview, for presenters. I have not reviewed its content in detail. | C |

### 6. Visual-inertial odometry and Kalman filter intuition

| Title | Creator | URL | Date | Length | Level / accuracy note | Grade |
|---|---|---|---|---|---|---|
| Why Use Kalman Filters? (Understanding Kalman Filters, Part 1) | MATLAB | https://www.youtube.com/watch?v=mwn8xhgNpFY | 2017-01-31 | 6:47 | The standard beginner intuition (fusing a noisy sensor with a model). | B -coi (vendor) |
| Kalman Filter - 5 Minutes with Cyrill | Cyrill Stachniss (Univ. Bonn) | https://www.youtube.com/watch?v=o_HW6GnLqvg | 2020-04-14 | 4:01 | Short and accurate, from a robotics professor. | B |
| What is Visual Inertial Odometry (VIO)? | ModalAI | https://www.youtube.com/watch?v=lIdmbrRahk8 | 2025-05-09 | 7:11 | Camera + IMU fusion on drones, from a drone-autopilot vendor. Relevant to how Swift senses its state (camera + IMU). | C -coi |
| UA: "Фільтр Калмана" (Ukrainian Wikipedia) | Wikipedia | https://uk.wikipedia.org/wiki/Фільтр_Калмана | live (HTTP 200) | article | The only Ukrainian-language text found. Encyclopedic and math-heavy. | C |

## B. World-model counter-evidence

| # | Claim (measured) | Number | Location | Source | Grade |
|---|---|---|---|---|---|
| B1 | Physics-IQ: no tested video model comes close to the physical-variance ceiling. | Pairs of real videos that differ only by physical randomness define the ceiling, 100. Best model: VideoPoet (multiframe) 29.5. Then Lumiere (multiframe) 23.0, Runway Gen 3 (i2v) 22.8, VideoPoet (i2v) 20.3, Lumiere (i2v) 19.0, Stable Video Diffusion 14.8, Pika 1.0 13.0, Sora 10.0. | Table 1; Fig. 4 (left) and its caption | Motamed, Culp, Swersky, Jaini, Geirhos, "Do generative video models understand physical principles?", arXiv:2501.09038 v3 (2025-02-27; v1 2025-01-14). https://arxiv.org/abs/2501.09038 | B (Google DeepMind authors, so -coi for the Google models tested; HTML read through a summarizer) |
| B2 | Physics-IQ: visual realism does not imply physical understanding. | Abstract: physical understanding is "severely limited, and unrelated to visual realism". Dataset: 66 scenarios, 396 videos (3 views x 2 takes). | Abstract; Dataset section | same | A -abstract |
| B3 | Kang et al.: video diffusion models trained on a 2D physics simulator generalize perfectly in-distribution, show scaling on combinatorial generalization, but fail out-of-distribution. They act "case-based": they copy the closest training example, prioritizing color > size > velocity > shape. Conclusion: scaling alone is not enough to discover physical laws. | Qualitative in the abstract. Per-figure error numbers were not extracted. | Abstract; main experiments Sec. 3-5 (not read) | Kang, Yue, Lu, Lin, Zhao, Wang, Huang, Feng, "How Far is Video Generation from World Model: A Physical Law Perspective", ICML 2025, arXiv:2411.02385 (v1 2024-11-04, v2 2025-06-22). https://arxiv.org/abs/2411.02385 | A -abstract |
| B4 | IntPhys 2: humans are near-perfect, while every model tested is near chance (50%). | Humans 96.44% overall on the Main set (Easy 96.17 / Medium 97.8 / Hard 95.5) and 92.44% on Held-Out. Best model V-JEPA 2-h 57.51%. Gemini-2.5 Flash 55.63, GPT-4o 53.75, VideoMAEv2-g 53.75, Qwen-VL 2.5 52.27, Gemini-1.5 Pro 52.27, Cosmos-4B 49.41. Dataset: 1,416 videos (Debug 60, Main 1,012, Held-Out 344). | Table 2 (accuracy); Table 1 and App. A.3 (set sizes) | Bordes et al. (FAIR, Meta), "IntPhys 2: Benchmarking Intuitive Physics Understanding In Complex Synthetic Environments", arXiv:2506.09849 v1 (June 2025). https://arxiv.org/abs/2506.09849 | B -coi (Meta benchmarks its own V-JEPA 2; Table 2's last column is unlabelled in the HTML and is inferred to be the original IntPhys) |
| B4b | Even Meta's own announcement concedes the gap. | Humans score 85-95% on IntPhys 2, MVPBench and CausalVQA. Models, V-JEPA 2 included, fall "notably short". | Blog text | Meta AI blog, 2025-06-11, https://ai.meta.com/blog/v-jepa-2-world-model-benchmarks/ | A -coi |
| B5 | WorldGym: the pass rate inside the world model correlates with real-robot success. The brief's figures are verified: Pearson r = 0.78 on 17 tasks. | r = 0.78, per task, over 17 Bridge (WidowX) tasks x 3 policies (RT-1-X, Octo, OpenVLA), about 51 points. | Sec. 4.1, Fig. 4(a); task list in Table 5 (App. E.3) | Quevedo et al., "WorldGym: World Model as An Environment for Policy Evaluation", arXiv:2506.00613 v3 (2025-09-30), ICLR 2026. https://arxiv.org/abs/2506.00613 | B -indirect: the real-world success rates were NOT re-measured. The Table 5 caption says they are "taken directly from" the OpenVLA paper (Kim et al.). Only one robot is in the correlation. The authors admit not all interactions are "fully realistic". |
| B6 | WorldEval reports a higher correlation, but on a narrower set. | Average Pearson r 0.942 and MMRV 0.044 across 3 tasks (Place Cup 0.958, Strike Block 0.887, Handover Block 0.980). Unseen backgrounds: r 0.927, MMRV 0.047. 4 policies (Diffusion Policy, OpenVLA, DexVLA, pi0), 5 tasks, 40 rollouts per task, >1,000 real trials. | Table 1, Sec. 4.2 (the text calls it "Table 2", a labelling error); App. B | Li, Zhu, Wen, Shen, Xu, "WorldEval: World Model as Real-World Robot Policies Evaluator", arXiv:2505.19017 v1 (2025-05-25). https://arxiv.org/abs/2505.19017 | B (small number of tasks; the authors' own policies are among those evaluated) |

How to use this on a slide: B1-B4 show that today's generative video and world models do not reliably follow physics. Humans score about 96% on IntPhys 2 against models at about 50-58%, and the best Physics-IQ score is 29.5 out of 100. B5-B6 are the honest counterweight: as evaluators of robot policies, world models already rank policies roughly correctly (r 0.78-0.94), but on few tasks, and in WorldGym's case against real-world numbers reused from another paper. This framing is my inference; the numbers above are measured.

## Not found / not verified

- A documentary about Swift: none found. There are only the official 4:50 video, press clips and the 1.5 h PI lecture.
- An official Meta YouTube video for V-JEPA 2: not found. The blog has no embedded video. Use the blog.
- A Two Minute Papers episode on V-JEPA 2 or DreamerV3: not found by search. The nearest are #247 (World Models, 2018) and the Genie 3 episode.
- An official explainer video for DreamerV3: not found. Danijar Hafner's channel has "Dreamer 4 | Diamonds from Offline Experience" (oDlBtTcX0g0), whose metadata was not verified.
- Genuine Ukrainian-language video explainers for any of the 6 topics: none found. Only the Mezha news item (topic 1) and Ukrainian Wikipedia (topic 6) were found. The KNU 3DGS paper was blocked (D). Search mostly returned Russian-language Habr and YouTube results, which are excluded.
- Kang et al. per-experiment error numbers (OOD velocity error etc.): not extracted. Only the abstract-level findings above.
- An independent replication of WorldGym's r = 0.78 on newly measured real-robot trials: not found.
