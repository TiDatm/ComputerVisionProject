# Block 3 slides: outline and script

3 slides, about 2 minutes.

## Slide 1. Learning from answers: Swift's eyes

On the slide:
- A camera frame with the four gate corners marked, next to a loop of four steps:
  1. Show an image.
  2. The network guesses where the corners are.
  3. Compare with the correct answer (the *label*). The *loss* is one number for how wrong the guess was.
  4. Change the *weights* (the network's adjustable numbers) a little, in the direction that lowers the loss. This is *gradient descent*. Repeat.
- A confident wrong answer costs a lot: saying "1 %" for a real corner gives loss 4.6, saying "99 %" gives 0.01.
- Swift uses a *U-Net* (a network that gives an answer for every pixel) to mark the 4 gate corners.
- Fine-tuning: take a trained network and keep training it on a small new set.
- Tag: "Labeled images: block 2".
- Sources: Nature, 2023; AlphaPilot, arXiv 2005.12813.

Script:
Part of Swift is ordinary supervised learning. We show the network an image together with the correct answer, called a label. Here the label is where the four gate corners are. The network guesses, and the loss, one number for how wrong it was, is large when it is confidently wrong. Saying one percent for a real corner costs about 4.6. Saying ninety-nine percent costs almost nothing. Then gradient descent changes the weights, the network's adjustable numbers, by a small step. After many repeats the network, a U-Net, can find gates. Where the labeled images come from is block 2. But finding gates does not tell the drone how to fly.

## Slide 2. Learning from a score: Swift's pilot

On the slide:
- A loop: drone state, then *policy* (the network that picks the action), then action (thrust and 3 rotation rates), then simulator physics, then *reward* (a score after each step), then back to the state.
- Reward: plus for getting closer to the next gate, plus for keeping the gate in view, minus for jerky commands, minus for a crash (the flight ends).
- *PPO*: improve in small steps, never change the policy much in one update.
- 31 numbers in, 4 out. Two layers of 128 nodes. 100 simulated drones at once. 10^8 steps, about 50 minutes, about a month of flying.
- Then 2×10^7 more steps in a simulator corrected with real data (fine-tuning). This leads to block 4.
- Sources: Nature, 2023; PPO, arXiv 1707.06347; UZH press release.

Script:
Nobody can label the right motor command at every moment, but a lap can be scored. So Swift learns from a reward, a score after every step. The policy, the network that chooses actions, gains points for getting closer to the next gate and for keeping it in view. It loses points for jerky commands and for crashing. It flies simulated laps, a hundred drones at once, and improves with PPO, which only allows small updates so one bad step cannot ruin what it has learned. After a hundred million steps, about fifty minutes, which is roughly a month of flying, it is a pilot. Block 4 shows how real data corrects it.

## Slide 3. Learning by copying, and Swift's recipe

On the slide:
- Left, *imitation*: a student copies a teacher. The teacher sees the exact state (possible only in simulation), the student sees only the camera and the IMU (a sensor for acceleration and rotation). Small mistakes put the student where the teacher never showed. *DAgger* fixes this: the student flies and the teacher labels where it went.
- Example: Deep Drone Acrobatics, same lab, loops and rolls up to 3g, trained only in simulation.
- Right, Swift's pipeline, colour-coded: camera + IMU, then VIO (classical: estimates motion from camera and IMU), then gate detector (supervised), then Kalman filter (classical: merges noisy estimates into one), then policy (RL), then motors.
- Swift does not copy pilots. They were its opponents.
- Question for the room: should a machine invent its own strategy or copy a human?
- Closing line: "It flew perfectly in simulation. Will it work in the real world?"
- Sources: Nature, 2023; Deep Drone Acrobatics, arXiv 2006.05768; DAgger, arXiv 1011.0686.

Script:
There is a third way: copy. In imitation learning a student copies a teacher that knows more, for example the exact position, which exists only in simulation. Small mistakes can push the student into places the teacher never showed, so DAgger lets the student fly and the teacher label where it ended up. The same lab used this for acrobatic flips. Swift does not copy pilots, though. It combines methods: supervised learning for seeing, reinforcement learning for flying, and classical filters in between. So should a machine invent its own strategy, or copy a human? And will it work outside simulation?
