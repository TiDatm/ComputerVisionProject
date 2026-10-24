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

## Digital twins

## Beyond simulators

## Slide plan

## Sources

### Sim-to-real gap and Swift
- [Champion-level drone racing using deep reinforcement learning, Nature 2023](https://www.nature.com/articles/s41586-023-06419-4) (open access full text: [PMC10468397](https://pmc.ncbi.nlm.nih.gov/articles/PMC10468397/))
- [Deep Drone Acrobatics, RSS 2020](https://arxiv.org/abs/2006.05768): trained only in simulation, transfers by feeding the policy abstracted features instead of raw images (also in block 3's sources)
