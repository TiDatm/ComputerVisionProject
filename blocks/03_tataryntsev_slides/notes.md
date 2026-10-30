# Block 3 slides: outline and script

Final version of block 3 (training methods): 4 slides, about 3 minutes, no formulas on the slides, plus 4 appendix slides with the derivations. The slides live in the team's Google Slides deck, final version: https://docs.google.com/presentation/d/122Me1PN4XW-5lF2SaP3Aab39bJobhIVjEQTUdVfTbSg/edit (header "by Hlib Tataryntsev / Training methods"). The deck is the source of truth, this file holds the speaker script and the text behind the slides. Appendix derivations are in `appendix.md`, images and credits in `visuals/`, the full research text in `../03_tataryntsev.md`. `core.pdf` is a PDF of the four main slides of the final deck (slide images, the text is not selectable).

Order in the talk: 1 Supervised learning, 2 Reinforcement learning, 3 Imitation learning, 4 Learning in Swift. Fine-tuning is covered in one sentence on slide 4 and in the research text. If time is short, cut slide 3 first.

## Slide 1. Supervised learning

On the slide:
- Picture: Nature 2023, Fig. 2 (CC BY 4.0), the real camera image next to the gate detector's output. One line: photo, guess, compare with label, nudge.
- *Loss: cross-entropy*: one number for how wrong a guess is. A confident wrong guess costs a lot.
- *Optimization: gradient descent*: nudge the weights (the network's adjustable numbers) so the mistake shrinks.
- Swift's U-Net (answers for every pixel) gives each pixel a corner probability. The corners are the peaks of that map, so the output is not exactly 4 pixels.
- Hand-off line: labeled images come from the simulator (next part).
- Sources: Nature, 2023; AlphaPilot, arXiv 2005.12813.

Script:
Part of Swift is ordinary supervised learning. We show the network a camera image and the correct answer, the label: where the gate corners are. The network, a U-Net, answers for every pixel with a probability that this pixel is a corner. The loss, cross-entropy, turns the guess into one number. If the network is sure about a wrong answer, the number is huge. Then gradient descent nudges the weights, the adjustable numbers, a little so that the mistake shrinks, and we repeat this many times. Where the labeled images come from is the next block.

## Slide 2. Reinforcement learning

On the slide:
- Picture: Nature 2023, Fig. 2b (CC BY 4.0), simulated flights scored by the reward. One line: fly, score, do more of what worked.
- *Reward*: a score after each step, progress is good, crashes are bad.
- *Policy gradient*: the policy is the network that picks actions, make well-scored actions likelier.
- PPO (Proximal Policy Optimization) in one sentence: it keeps every update small, so one bad step cannot ruin the policy.
- Sources: Nature, 2023; PPO, arXiv 1707.06347.

Script:
Nobody can label the right motor command at every moment, but a lap can be scored. So Swift learns from a score, which is reinforcement learning. It gets points for progress to the next gate and for keeping it in view, and loses points for jerky commands and crashes. The policy is the network that picks the action, and the policy gradient makes well-scored actions likelier. The method, PPO, keeps each update small, so one bad step cannot ruin what is already learned. Swift trained with a hundred simulated drones at once, a hundred million steps, about fifty minutes.

## Slide 3. Imitation learning

On the slide:
- Three steps with arrows: the teacher is the expert and sees the exact state (simulation only), the student sees only the camera and copies the teacher's actions, DAgger: the student flies and the teacher corrects where it drifted.
- Text: learn by copying an expert. No score (that is reinforcement learning) and no labelled photo (that is supervised learning), the example is what the expert did.
- Line under the steps: training data = what the expert does in each situation.
- Photos: barrel roll, power loop, Matty flip, trained only in simulation.
- Question for the room: should a machine invent its own strategy, or copy a human?
- Sources: Nature, 2023; Deep Drone Acrobatics, RSS 2020 (arXiv 2006.05768); DAgger (arXiv 1011.0686).

Script:
There is a third way: copy. In imitation learning a student copies a teacher that knows more, for example the exact position, which exists only in simulation. This is close to supervised learning, but the labels are the expert's actions, and there is no score like in reinforcement learning. The catch: small mistakes push the student into situations the teacher never showed. DAgger fixes this: the student flies, and the teacher corrects it where it really drifted. The same lab used this for acrobatic flips. So should a machine invent its own strategy, or copy a human?

Questions this slide may get:
- Is imitation a mix of supervised and reinforcement learning? No. It is supervised learning where the labels are an expert's actions, with no reward. DAgger keeps adding labels on the states the student really reaches.
- Why can imitation not beat the expert? The student only learns to match the teacher. Reinforcement learning needs only a score, so it can go beyond a human, as Swift did.

## Slide 4. Learning in Swift

On the slide:
- Pipeline: camera + IMU (motion sensor), gate detector (supervised learning), state estimate (classical filter, no learning), flying policy (reinforcement learning, fine-tuned on real data), motor commands.
- Imitation learning is not part of Swift. The same lab used it for acrobatic flips, with a teacher that sees the exact state in simulation.
- Sources: Nature, 2023; Deep Drone Acrobatics, RSS 2020 (arXiv 2006.05768).

Script:
Here is where each method sits in Swift. The gate detector learns from labeled images, that is supervised learning. The state estimate uses classical filters, nothing is learned there. The flying policy learns from a score, reinforcement learning, and is then fine-tuned with real flight data: fine-tuning means training an already trained network a little more, with small steps, so it keeps what it knew. Imitation learning is not in Swift, but the same lab used it for acrobatic flips. How real data corrects the simulator is the last block.

## Appendix

Four slides for questions, one per derivation (cross-entropy, Dice, return, compounding error). Each has the steps with formulas, an intuition line and a small example. The steps and the LaTeX sources are in `appendix.md`.
