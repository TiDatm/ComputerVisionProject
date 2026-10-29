# Block 3 slides: outline and script

5 slides, about 4 minutes. Built in Canva ("Block 3 - Training methods (5 slides)", on the layouts of the team deck so the style matches). `core.pdf` is the export. Images and credits are in `visuals/`. If time is short, cut slide 2 or slide 4 first.

## Slide 1. Learning from answers

On the slide:
- Formula image: loss `L = -[y ln p + (1-y) ln(1-p)]` with the examples (y = 1, p = 0.99 gives 0.01; p = 0.01 gives 4.6), and the step `w <- w - eta * grad L`.
- *Loss*: one number for how wrong a guess is. *Gradient step*: weights, the network's adjustable numbers, move against the gradient.
- In Swift, a U-Net (a network that answers for every pixel) marks the 4 gate corners.
- Sources: Nature, 2023; AlphaPilot, arXiv 2005.12813.

Script:
Part of Swift is ordinary supervised learning. We show the network an image and the correct answer, the label: where the four gate corners are. The network guesses a probability p. The loss turns the guess into one number. If the corner is real and the network says ninety-nine percent, the loss is 0.01. If it says one percent, the loss is 4.6. A confident wrong answer costs a lot. Then the gradient tells us in which direction the loss grows, and we change the weights a small step the other way, eta being the step size. Repeat many times and the network, a U-Net, finds the gates. Where the labeled images come from is block 2.

## Slide 2. Reusing what is learned (fine-tuning)

On the slide:
- Three cards: *Start trained* (copy the weights of a network that already works, then keep training on new data), *Small steps* (a small step size keeps the weights near the start), *Swift* (10^8 steps in simulation, then 2x10^7 steps with real flight data).
- Under the cards: after T steps of size eta, with gradients of size at most G, the weights moved at most eta*T*G. Too big a step erases what the network knew.
- Sources: Nature, 2023; Yosinski et al., arXiv 1411.1792; EWC, arXiv 1612.00796; LoRA, arXiv 2106.09685.

Script:
Training from nothing is expensive, so we reuse. Fine-tuning means taking a network that already works and training it a little more on new data. The step size matters. After T steps of size eta, with gradients no bigger than G, the weights moved at most eta times T times G. Keep eta small and the network stays close to what it knew. Take big steps and it forgets. Swift does exactly this: first a hundred million steps in simulation, then twenty million steps with real flight data. How real data corrects the simulator is block 4.

## Slide 3. Learning from a score

On the slide:
- Formula image: return `G = r0 + gamma r1 + gamma^2 r2 + ...` with the example (rewards 1, 1, 1 and gamma 0.9 give 2.71), and the policy gradient `grad J = E[ grad ln pi(a|s) * A ]`.
- *Return*: all future rewards added up, near ones count more because gamma is below 1. *Policy gradient*: the policy is the network that picks actions, so make actions with a positive advantage A (better than average) likelier.
- Swift's reward: progress to the next gate and the gate in view, minus jerky commands and crashes.
- Sources: Nature, 2023; PPO, arXiv 1707.06347; GAE, arXiv 1506.02438.

Script:
Nobody can label the right motor command at every moment, but a lap can be scored. So Swift learns from a reward. The return adds up all future rewards, and near rewards count more, because gamma is below one. With rewards of one, one, one and gamma of 0.9 the return is 2.71. The policy is the network that picks the action. The policy gradient says: look at each action, ask how much better than average it was, that is the advantage A, and make good actions likelier. Swift gets points for progress to the next gate and for keeping it in view, and loses points for jerky commands and crashes.

## Slide 4. PPO: small, safe updates

On the slide:
- Formula image: ratio `r = pi_new(a|s) / pi_old(a|s)`, clipped objective `L = E[min(r*A, clip(r, 1-eps, 1+eps)*A)]` with eps = 0.2, and the paper's plot for A > 0 and A < 0.
- Cards: *Ratio r* (new policy over old policy for the same action, 1 means no change), *Clip* (gains from r outside 1 +- eps are cut off), *Why* (one bad step cannot wreck what the policy has learned).
- Sources: PPO, arXiv 1707.06347 (Fig. 1, Eq. 7); Nature, 2023.

Script:
A big policy update can ruin everything the network learned. PPO prevents that. The ratio r compares the new policy with the old one for the same action. If r is one, nothing changed. The objective takes the smaller of two numbers: the plain gain, and the gain with r clipped to the range one minus epsilon to one plus epsilon, epsilon being 0.2. Look at the plot on the left: once r goes past one plus epsilon, the line is flat, so there is no reward for pushing further. One bad step cannot wreck the policy. Swift trained this way with a hundred drones at once, a hundred million steps, about fifty minutes.

## Slide 5. Learning by copying

On the slide:
- Imitation: a student copies a teacher that sees the exact state (only in simulation). DAgger: the teacher labels where the student flew.
- Photos: barrel roll, power loop and Matty flip, trained only in simulation.
- Swift's pipeline, top to bottom: camera + IMU (motion sensor), gate detector (supervised learning), position estimate (classical filters), flying policy (reinforcement learning), motor commands. Black means learned, gray means sensors and classical methods.
- Question for the room: should a machine invent its own strategy, or copy a human?
- Sources: Nature, 2023; Deep Drone Acrobatics, RSS 2020 (arXiv 2006.05768); DAgger, arXiv 1011.0686.

Script:
There is a third way: copy. In imitation learning a student copies a teacher that knows more, for example the exact position, which exists only in simulation. Small mistakes can push the student into places the teacher never showed, so DAgger lets the student fly and the teacher label where it ended up. The same lab used this for acrobatic flips. Swift does not copy pilots, though. It combines methods: supervised learning for the gate detector, reinforcement learning for the flying policy, and classical filters in between. So should a machine invent its own strategy, or copy a human? And will it work outside simulation?
