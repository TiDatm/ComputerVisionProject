# Block 3 slides: outline and script

Final version of block 3 (training methods): 4 slides, about 3 minutes, no formulas on the slides, plus 4 appendix slides with the derivations. The slides live in the team's Google Slides deck, final version: https://docs.google.com/presentation/d/122Me1PN4XW-5lF2SaP3Aab39bJobhIVjEQTUdVfTbSg/edit (header "by Hlib Tataryntsev / Training methods"). The deck is the source of truth, this file holds the speaker script and the text behind the slides. Appendix derivations are in `appendix.md`, images and credits in `visuals/`, the full research text in `../03_tataryntsev.md`. `core.pdf` is an old Canva export of an earlier five-slide version and is not used any more.

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
