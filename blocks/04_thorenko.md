# Block 4: From simulation to reality

## Scope

How to make a model trained in simulation work on a real drone.

- Block 2 covers how the simulator generates data (simulators, sensor models, auto-labelling, domain randomization).
- Block 3 covers how the model is trained (supervised, RL, imitation).
- Block 4 starts where both end: the model works in simulation, so why does it fail in the real world, and how do we close that gap?

## Research questions

- [ ] What exactly is the sim-to-real gap for a vision-based drone?
- [ ] How did Swift cross it? Check in the Nature paper, not in retellings.
- [ ] Which adaptation methods use a little real data, and how much do they need?
- [ ] How are digital twins of real places built from photos and video?
- [ ] Is there a way around a hand-built simulator altogether?
- [ ] What fits in 3 slides, and what do we leave out?

## Sim-to-real gap

A policy that is perfect in simulation gets confused in the real world, because reality differs from the simulator:
- **Looks:** different lighting, textures, reflections, motion blur, lens distortion.
- **Sensors:** real camera and IMU noise, drift of the position estimate.
- **Physics:** aerodynamics, battery sag and motor response that the simulator models only roughly.

Small errors add up quickly. At racing speed, a wrong estimate of a few centimetres per frame turns into a missed gate.

## How Swift crossed it

Checked in the paper (Kaufmann et al., Nature 2023):
- **The control policy never sees raw pixels.** A small U-Net network finds the corners of the next gate in the camera image. A visual-inertial estimator (VIO) tracks the drone's position from the camera and IMU. The two are fused with a Kalman filter, and the policy gets only this compact state. Hiding raw pixels from the policy is already a way to make simulation and reality look the same to it.
- **Correction ("residual") models learned from real flights.** The team recorded a few runs on the real track and measured where reality differs from the simulator:
  - perception errors are random, so they are modelled with Gaussian processes;
  - dynamics errors are mostly repeatable, so they are modelled with k-nearest-neighbour regression (k = 5, 800-1,000 samples per track).
- **Fine-tuning instead of randomizing physics.** The paper says it does not randomize the drone's dynamics in training. The policy is fine-tuned in the simulator corrected with real data.
- **Result:** Swift won several races against each of three human champions and set the fastest race time of the event.

Note for the team: Swift is not an end-to-end "pixels to motors" network. Block 1 should describe it as gate detection + state estimation + policy.

## Adaptation

Three ways to close the gap, from cheapest to most real data:
- **Domain randomization:** randomize weather, textures and light so that reality looks like one more variation. Covered in block 2; here only one line as a reminder.
- **Translate images, not the model (domain adaptation):** a network converts real camera frames into the clean "canonical" look of the simulator, so the policy always sees what it was trained on. RCAN (CVPR 2019, robot grasping): 70% success on new objects with zero real training data; with 5,000 real grasps 91%, on par with a system trained on 580,000 real grasps.
- **Fine-tune on a little real data:** like Swift's residual models above. A few real runs correct the simulator, then training continues in the corrected simulator.

Discussion point: how much real data is "enough", and who collects it safely?

## Digital twins

Instead of building the virtual world by hand, rebuild a real place from photos and video:
- **Photogrammetry:** a 3D mesh from many overlapping photos.
- **Gaussian splatting (3DGS):** the scene is stored as millions of small coloured blobs. Renders photo-realistic views fast enough for training.

Drone examples:
- **SOUS VIDE (2025):** a simulator made of a Gaussian splat scene plus a light drone dynamics model, rendering up to ~130 fps. A small onboard network flew the real drone with no real-world fine-tuning.
- **GRaD-Nav (2025):** trains visual navigation with RL directly inside Gaussian radiance fields.
- **Dynamics-augmented Gaussian splatting (2024):** trained only on indoor photo-realistic data, without scene randomization, then flew multi-step outdoor routes on a real quadrotor.
- **Relightable 3DGS (2026):** separates lighting from geometry, so one scan can be trained under sun and overcast; reports agile flight above 10 m/s with one RGB camera and no real-world fine-tuning.

Keeps the team's rule: the world is built from camera images, not from laser scans.

Numbers above come from the papers' abstracts; check the tables before putting them on a slide.

## Beyond simulators

## Slide plan

## Sources

### Sim-to-real gap and Swift
- [Champion-level drone racing using deep reinforcement learning, Nature 2023](https://www.nature.com/articles/s41586-023-06419-4) (open access full text: [PMC10468397](https://pmc.ncbi.nlm.nih.gov/articles/PMC10468397/))
- [Deep Drone Acrobatics, RSS 2020](https://arxiv.org/abs/2006.05768): trained only in simulation, transfers by feeding the policy abstracted features instead of raw images (also in block 3's sources)

### Adaptation
- [Domain randomization (Tobin et al., 2017)](https://arxiv.org/abs/1703.06907) (block 2 owns this topic)
- [RCAN: Sim-to-Real via Sim-to-Sim, CVPR 2019](https://arxiv.org/abs/1812.07252)

### Digital twins
- [SOUS VIDE: visual drone navigation policies in a Gaussian splatting simulator](https://arxiv.org/abs/2412.16346)
- [GRaD-Nav: visual drone navigation with Gaussian radiance fields](https://arxiv.org/abs/2503.03984)
- [Dynamics-augmented Gaussian splatting simulator for drones](https://arxiv.org/abs/2406.15149)
- [Relightable 3DGS for agile drone flight (2026)](https://arxiv.org/abs/2602.07101)
