# 02. Primer: what you need to know before reading the papers

Plain-language background for block 4, "From simulation to reality". Read it once in order; the papers in `03-papers.md` assume these ideas.
Numbers here are only for intuition. The exact, sourced figures are in `03-papers.md`.

## 0. The whole story in six sentences

1. Robots and drones learn their skills mostly in a **simulator**, a video game for robots, because practice there is fast, cheap and crash-free.
2. The game is never an exact copy of the world. The difference is the **sim-to-real gap**, and a skill that is perfect in the game can fail in reality.
3. There are two ways to fight it: **make the game closer to reality**, or **make the robot not care** about the differences that remain.
4. One way to make the game closer to reality is to **build it from photos and video of the real place** (a digital twin).
5. The newest idea is to **let a neural network learn the game itself** from real video (a world model), so nobody has to program it.
6. Swift, our through-example, used a little of both strategies. It practised in a simple game, flew about 50 seconds for real, corrected the game with what it measured, and practised again.

## 1. Words you will meet everywhere

| Term | Plain meaning | Українською (for slides) |
|---|---|---|
| Simulator | A program that imitates physics and cameras so a robot can practise virtually | симулятор |
| Policy | The robot's "brain": the function that turns what it senses into what it does (motor commands) | політика (керування), стратегія |
| Training / learning | Adjusting the network's numbers so its outputs get better. Block 1 and block 3 explain how | навчання |
| Sim-to-real transfer | Moving a policy trained in the simulator onto the real robot | перенесення з симуляції в реальність |
| Sim-to-real gap / reality gap | How different the simulator is from the real world | розрив між симуляцією й реальністю |
| Performance gap | How much worse the robot does in reality than in the simulator | розрив у якості / результатах |
| Zero-shot | Works on the real robot the first time, with no real practice | «з першої спроби», без донавчання |
| Fine-tuning | Training a bit more, starting from an already trained model | донавчання |
| Domain | A "world" the data comes from: sim is one domain, reality another | домен |
| Domain randomization | Making the simulator randomly varied so reality looks like "one more variation" (block 2 owns this) | рандомізація домену |
| Domain adaptation | Methods that make one domain look or behave like the other | адаптація домену |
| Digital twin | A virtual copy of a real place or machine | цифровий двійник |
| World model | A neural network that predicts "what happens next if I do X", learned from experience or video | модель світу |

## 2. The sim-to-real gap: where it comes from

The "reality gap" has had a name since 1995 (Jakobi). A 2025 survey (Aljalbout et al., with Scaramuzza, Swift's lab head) sorts it into four kinds. For a camera drone they look like this:

- **Looks (perception gap):** lighting, glare, textures, reflections, motion blur, lens distortion. Simple renderers leave out effects like lens flare and the "rolling shutter" smear of a moving camera.
- **Sensors:** real cameras and the motion sensor (IMU) are noisy; the camera may sit at a slightly different angle than in the simulator.
- **Physics (dynamics gap):** air drag, how strong the motors really are, the battery getting weaker during the flight.
- **Timing (system design):** the image arrives late and commands take time to reach the motors. At 100 km/h a drone moves almost 1 m in 33 ms (simple arithmetic), so a small delay matters.

Two different things get measured: the **reality gap** (how different the worlds are) and the **performance gap** (how much the score drops).
Measured example: without its fix, GRaD-Nav's drone succeeded 7-10 out of 10 times in the simulator, but only 0-2 out of 10 in reality.

## 3. Strategy A: make the robot not care (robustness)

- **Domain randomization (one line, block 2's topic).** Train under random colours, lights, textures, weights and delays, so the real world is just one more variation. Tobin 2017 and Peng 2018 are the classics, and OpenAI's Rubik's-cube hand is the famous example.
- **Abstraction (the key idea behind Swift).** Don't show the policy raw pixels. Show it a simplified "sketch" that looks the same in the simulator and in reality: gate corner points, a segmentation mask (gate pixels coloured white, everything else black), depth, or "feature tracks" (dots that follow corners in the image).
  - Grass colour and lighting are not in the sketch, so they can't confuse the policy.
  - In Deep Drone Acrobatics (2020), tested in simulation on new backgrounds, a raw-image policy failed completely (0%), while the sketch-based one kept 100%. The sketch-based policy then flew the real drone with no extra training.
- **Pretrained vision models (DINOv2, CLIP).** These are big networks already trained on internet images, used as the robot's "eyes" in the hope that they describe sim and real pictures the same way. The evidence is mixed: they help in some tasks and fail in others.

## 4. Strategy B: make the simulator closer to reality

- **System identification.** Measure the real machine (mass, motor strength, drag) and set the simulator's numbers to match, like tuning a racing game's car settings to your real car.
- **Residual models (Swift's trick).**
  - Fly the real drone briefly and record where reality differs from the simulator's prediction.
  - That leftover error is the "residual". A small model learns to predict it and is added into the simulator.
  - Then the policy practises again in the corrected simulator.
  - Swift used two simple learners for this. **k-nearest neighbours** means "look up the 5 most similar recorded moments and average their errors". A **Gaussian process** draws a smooth curve through the recorded errors and also says how unsure it is.
- **Image translation.** A neural network learns to "repaint" simulator pictures so they look like real camera photos, like a photo filter.
  - **GAN (generative adversarial network):** two networks compete. One paints fakes; the other tries to tell fakes from real photos. The painter gets better until the critic can't tell.
  - **CycleGAN:** a GAN that learns the restyling without matched pairs of photos. Its trick: repaint sim → real → sim again and you must get the original back, so the content can't change.
  - **RCAN** does the reverse: it turns any messy picture (randomized sim or real) into one clean "canonical" sim picture. Analogy: retype any handwriting in one standard font, then read only that font.
  - Catch: these repainters usually learn from many **unlabelled** real photos, so "no real data" often means "no real labels".
- **A little real data mixed in (co-training, fine-tuning).** Learn mostly in sim, then practise a little in reality, or mix sim and real examples in each lesson. A 2025 study found the best mix was about 99% sim; 100% sim was worse.
- **Real-to-sim-to-real.** Scan the real place, train in that copy, bring the skill back. This leads to section 6.

## 5. To understand Swift: how a drone knows where it is

- **IMU (inertial measurement unit):** a chip with accelerometers and gyroscopes, the drone's inner ear. It measures acceleration and rotation, but its errors add up quickly.
- **VIO (visual-inertial odometry):** works out "where am I?" from how the camera view shifts plus the IMU, like dead reckoning. It is accurate over short times but slowly drifts.
- **Kalman filter:** a method for combining two imperfect estimates, trusting each as much as it deserves. Swift combines VIO with "I can see gate 3 right there" to stop the drift.
- **U-Net (gate detector):** a network that colours the image pixel by pixel. Swift's U-Net marks the gate corners in the camera image.
- **Swift in one line:** camera → U-Net finds gate corners → corners + VIO + Kalman filter → a compact state (where am I, where's the next gate) → the policy (trained with RL) → motor commands. **The policy never sees pixels.** Say exactly this on the slide; "Swift learned to see through RL" is wrong.

## 6. Digital twins built from photos and video

The pipeline: **photos or a phone video → camera positions → 3D scene → render new views → train the policy inside it.**

- **Photogrammetry / structure-from-motion (SfM).**
  - Take many overlapping photos. The software finds the same corners in several photos and works backwards to compute where each photo was taken and where those points sit in 3D.
  - The output is a "dot cloud" plus all camera positions. **COLMAP** is the standard free tool.
- **NeRF (neural radiance field, 2020).**
  - A neural network learns one question: "at this 3D point, looking from this direction, what colour is it and how solid is it?" To draw a picture, you ask it millions of times along every pixel's ray.
  - The pictures are beautiful but slow, about 30 s per frame in the original paper. A drone simulator needs hundreds of frames per second.
- **3D Gaussian splatting (3DGS, 2023).**
  - The scene is millions of tiny, soft, coloured, semi-transparent blobs ("splats"), like a 3D pointillist painting. The computer moves and recolours them until rendered views match the photos.
  - Graphics cards draw blobs very fast: about 134 fps against 0.06 fps for a top NeRF on the same benchmark. **That speed is why splats became robot training gyms.**
- **Feed-forward reconstruction (DUSt3R, MASt3R, VGGT, 2024-2025).** Networks that have seen millions of scenes and "just know" the 3D shape from a few photos, in under a second instead of minutes or hours of SfM.
- **What a splat twin cannot do (say this on the slide):**
  - It is only paint: there is **no physics**, so a hidden collision shape must be added.
  - It is **frozen in time**: nothing moves.
  - **Lighting is baked in**: a sunny scan stays sunny.
  - Views far from where the video was shot look blurry or broken.
  - It is **one place**: a drone trained in one room may fail when furniture moves.
- **Team rule check:** some drone splat papers (e.g. the 2026 forest paper) capture the map with LiDAR (a laser rangefinder). Our rule is camera-only, so prefer SOUS VIDE (from a 2-3 minute handheld video) as the example.

## 7. World models: learning the simulator itself

- **World model:** a network that has watched lots of experience or video and learned "if I do X, what will I see next?". It is a game engine nobody programmed.
- **Training "in a dream" (Ha & Schmidhuber 2018; Dreamer):**
  - The agent first learns a mini-simulator from experience, then practises inside it, like a pilot rehearsing a flight in their head.
  - DreamerV3 (Nature 2025) learned more than 150 tasks this way, including collecting diamonds in Minecraft from scratch.
  - Catch: if the dream has bugs, the agent learns to exploit the bugs.
- **JEPA (LeCun; V-JEPA 2, Meta 2025):** predict a compact summary of the future (an "embedding", a list of numbers that captures the meaning) instead of painting every pixel. It is like predicting "the ball will be on the floor" without drawing every leaf behind it.
- **Generative interactive worlds** (DeepMind Genie 3, Wayve GAIA, NVIDIA Cosmos, Waymo World Model): AI video generators you can steer live. Companies want them as an infinite test track for rare events.
- **Honest limits:**
  - These models can make objects vanish or ignore gravity, and their own authors report this.
  - Small errors snowball over long predictions.
  - Most industry claims are demos, without a published measurement that the robot works better in reality.
  - The drone world-model papers either use flight numbers instead of a camera (SkyJEPA) or tested with rendered images (Dream to Fly).

## 8. Likely questions at the defence (and short answers)

1. **"What is the sim-to-real gap?"** The difference between the simulator and reality (looks, sensors, physics, timing), which makes a policy that is perfect in the simulator fail on the real drone.
2. **"How did Swift cross it?"**
   - The policy saw only gate corners plus its estimated position, not pixels.
   - The team flew 3 real runs (~50 s) and learned correction models for perception and dynamics errors.
   - They added these to the simulator and fine-tuned the policy once there.
   - They did not randomize the drone's physics.
3. **"Why not just train on real data?"** Real flights are slow, expensive and dangerous: crashes break drones. In the simulator Swift practised about a month of flying in under an hour.
4. **"What's the difference between NeRF and Gaussian splatting?"** Both rebuild a scene from photos. NeRF is a neural network queried per pixel (slow). Splatting draws millions of blobs (fast enough for training).
5. **"Does a digital twin solve everything?"** No: it fixes looks for one place, but it has no physics, frozen lighting and objects, and it breaks when the scene changes.
6. **"Is this computer vision?"** Yes. Every step is about camera images: what the drone sees, how simulated images differ from real ones, and how real images build the simulator.
