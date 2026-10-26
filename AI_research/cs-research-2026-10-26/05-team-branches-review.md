# 05. Team branches review (2026-10-26)

Fetched `origin` on 2026-10-26 (read-only; no checkout or merge). The Telegram connector was still down, so chat changes after 10-24 are unknown.

## State per branch

| Block | Branch | What is there | Ready for slides? |
|---|---|---|---|
| 1 CV fundamentals (Yablonskyi) | `Andrii-Yablonskyi` @42ee610 (10-24) | `docs/Preplan.pdf` (scan) + `docs/Part 1_ ... .docx` (~8.5k chars, English): CV tasks table, "speed barrier", tensors, convolutions, layer hierarchy, "end-to-end pixels-to-control", loss functions, roadmap for parts 2-4 | Content yes; **format no** (docx in `docs/`, not `blocks/01_<surname>.md`); **no sources at all** |
| 2 Simulation (Misha?) | `Misha-Dvali` = initial commit | Nothing | **No** |
| 3 Training methods (Tataryntsev) | `Hlib-Tataryntsev` @dafe2e4 (10-25, 15 commits) | `blocks/03_tataryntsev.md`: supervised learning in depth (BCE and Dice derivations, gradient descent, backprop, Adam, generalization, labels, Swift's U-Net, limits); **Fine-tuning, RL, Imitation learning headings empty** (sources listed) | Supervised part yes (very detailed, far over "about one page"); the other 3 sections not yet |
| 4 Sim-to-real (you) | `Andrii-Thorenko` @7569175 (10-24) | README only; research in `AI_research/` | **No block file yet** (`blocks/04_thorenko.md`) |

## Collisions

1. **Block 1 says Swift is end-to-end "raw pixels → network → motors" and "bypasses explicit object detection entirely" (sections 2 and 4).** This is wrong and contradicts both block 3 (Swift's U-Net gate-corner detector, correct) and block 4. Swift = camera → U-Net corners → VIO + Kalman filter → state → RL policy → motor commands; the policy never sees pixels (Nature 2023). Must be fixed before slides, or the teacher will catch the contradiction between blocks 1 and 3.
2. **Block 1's roadmap for part 4 says "visual domain randomization and sensor noise adaptation allow policies trained entirely in simulation to fly flawlessly".** Swift used ~50 s of real flight data to correct its simulator, did no dynamics randomization, and won 15 of 25 races. "Flawlessly" and "entirely in simulation" are both wrong for Swift (MonoRace is the randomization example).
3. **Block 1's roadmap for part 2 promises "high-fidelity photorealistic simulations".** Swift's policy did not need photorealism (it saw no images). Block 2 should decide its own framing.
4. **Loss functions appear twice:** block 1 section 3.4 (analogy) and block 3 (derivations). One of them should drop it; block 3 owns it. Block 1's "exponentially larger loss" is also inaccurate (BCE grows like -log, MSE quadratically).
5. **Fine-tuning appears in block 3 (heading) and block 4 (adaptation).** Proposed split: block 3 = what fine-tuning is and how it works; block 4 = using it to cross the gap (Swift fine-tunes once in the corrected simulator; co-training ~99% sim).
6. **Deep Drone Acrobatics** is in block 3's imitation-learning sources and in block 4's abstraction point. Fine if block 3 uses it for "learning from an expert" and block 4 for "feature tracks transfer, raw pixels don't".
7. **Domain randomization** sits in block 2 by plan, block 1's roadmap gives it to part 4, and block 4 has MonoRace. Keep the 10-19 boundary: block 2 explains it; block 4 shows the real result (MonoRace) in one line.

## Missing for the whole presentation

1. **Block 2 has no content at all**, about 2 days before the inferred ~10-28 block deadline. Block 4's opening depends on it.
2. **State estimation (VIO + Kalman filter)** is in nobody's block, but it is the bridge from "see corners" to "know where I am". Cheapest fix: one line in block 4's Swift recap, or in block 1 next to U-Net.
3. **Block 1 lacks U-Net and metrics (IoU, accuracy),** which the plan gave it, and U-Net is the network Swift actually uses. It also has no ViT (planned "CNN/ViT basics").
4. **The RL section (block 3) is empty,** yet RL is how Swift's controller learned. Block 4 needs it explained first.
5. **No sources in block 1** (rules F5, G6: inline sources and working links).
6. **Each block's "live industry debate"** (F4) is missing in blocks 1 and 3 so far.
7. **Joint intro, conclusion, transitions, `slides.pdf`** (R5): not started anywhere (expected at step 3).
8. **Language:** both drafts are in English; check whether slides and blocks are expected in Ukrainian (the task PDF and example are Ukrainian).
