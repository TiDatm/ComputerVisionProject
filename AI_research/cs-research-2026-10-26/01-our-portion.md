# 01. Our portion

## Pick

**Block 4, "Від симуляції до реальності" (From simulation to reality)**: the 4th of four blocks, owned by Andrii Tkhorenko.

What it covers, from the team plan posted in the chat (TG "CS project", 2026-10-19, msg 101951):
- **sim-to-real gap:** why a model that is perfect in simulation gets confused in the real world (light, textures, noise);
- **adaptation:** fine-tuning on a little real data, and "repainting" synthetic frames to look real (domain adaptation);
- **an environment built from the real world:** digital twins, i.e. 3D copies of real places made from photos and video (photogrammetry, Gaussian splatting);
- **how Swift did it:** it added data from real flights to the simulation;
- **discussion:** build the virtual world by hand, or rebuild the real one from photos?

Boundary: block 2 covers how the simulator generates data. Block 4 covers how to bring the simulation close to reality and move the model there (same message).

What we deliver as "ours":
1. `blocks/04_thorenko.md`, about one page in your own words. Worth 6 + 2 personal points.
2. Three slides for block 4 (~2.5 min).
3. Q&A readiness on the block.
4. Shared with the team: a line or two of input to the joint intro and conclusion, plus the 3 → 4 and 4 → conclusion transitions.

## Why this pick

- The chat's final plan names block 4 and its boundary. On 10-19 you asked the planning session to make block 4 "more about adaptation from simulation to reality plus building the environment itself", and the posted message matches that.
- The teammates' branches line up: Hlib has block 3 (`blocks/03_tataryntsev.md`, training methods), and Yablonskyi has Part 1, CV fundamentals (Preplan + docx). Misha Dvali's branch is empty, so block 2 (simulation) is presumably his. That is an inference: the chat never names him for block 2.
- On 10-21 the team turned down a 5th member: "тему ми вже розписали" (msgs 102252–102256). The split stays at 4.

## Contradictions and how I resolved them

| Issue | Pick | Alternative |
|---|---|---|
| Earlier drafts (10-19, before the final message) had block 4 = SLAM, visual odometry, navigation without GPS, Ingenuity, Skydio. That is your original interest: "3D environments and navigation" | The **final posted plan** (sim-to-real + digital twins). SLAM is out | One intro line on Ingenuity or Skydio as a "where it works" example (the planning session suggested this) |
| Your 10-24 notes add a 3rd slide on **world models** (V-JEPA 2, SkyJEPA), which the posted plan doesn't have | Keep it as an **option**: one slide or one closing line. See README options | Drop it and spend slide 3 on digital twins only |
| Domain randomization: the plan puts it in block 2, but it is also a sim-to-real fix | **One line** in block 4 that points to block 2 | — |

## Not ours

- How the simulator generates data, simulators and sensor models (block 2).
- Supervised learning, RL and imitation learning (block 3). We only say "Swift's policy was trained with RL in the simulator; the gate detector was trained with labelled images".
- CNN/ViT basics, loss functions, metrics (block 1). We use the terms block 1 explains and don't re-explain them.

## Open (for the owner)

- Confirm Misha owns block 2, so the boundary line in the README names the right person.
- Read the chat after 10-24 (the connector was down) for any change to the split or the dates.
