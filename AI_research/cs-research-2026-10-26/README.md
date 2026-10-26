# CS101 project research: block 4 "From simulation to reality" (2026-10-26)

AI-assisted preparation for Andrii's part of the group project "Як навчити машину бачити: від симуляції до реального світу". It is research only: the block file `blocks/04_thorenko.md` and the slides are written separately, in your own words (see `00-requirements.md`, I1-I2).

Method: 3 research rounds with parallel agents.
1. Round 1: 5 directions.
2. Round 2: 4 gap-filling deep reads.
3. Round 3: 2 independent verifiers re-checked 20 slide-critical claims; 13 were exact, 7 had small corrections, which are applied, and none was wrong.

## Files

| File | What |
|---|---|
| `00-requirements.md` | Shortlist: dates, grading (20 = 12 group + 8 yours), format, git rules, integrity; ends with 5 lines to keep in mind |
| `01-our-portion.md` | Block 4 is ours; what it covers, its boundaries with blocks 2/3, contradictions resolved |
| `02-primer.md` | Plain-language concepts, a Ukrainian term table, and 6 likely defence questions with answers |
| `03-papers.md` | ~35 papers from foundations (1995) to June 2026: citation, technology, plain explanation, key number, why it matters; counter-evidence; explainer videos; corrections to the 10-24 notes; verification log |
| `04-companies-products.md` | Who does this in industry (Wayve, Waymo, NVIDIA, DJI, Skydio, TU Delft...), with grades: mostly company claims |
| `raw/` | The agents' full reports and claims tables (r1-r5 = round 1, r2a-r2d = round 2, v1-v2 = verification) |

## Key findings in 6 lines

1. **Swift crossed the gap with very little real data.** Its policy never sees pixels (gate corners + VIO). The team flew 3 real runs, about 50 s, corrected the simulator with what they measured, and fine-tuned once. They did not randomize the physics. (Nature 2023, verified)
2. **MonoRace (TU Delft) won the A2RL race in April 2025 with the opposite recipe:** one camera, heavy physics randomization ("≥40%"), and no real flight data for control. It beat 3 champion pilots at up to 28 m/s. This independent result pairs well with Swift.
3. **Digital twins from video work for drones now.**
   - SOUS VIDE: a twin from a 2-3 min handheld video, 105 real flights.
   - FalconGym 2.0: the drone films a 3-min video, then trains in the copy; 98.6% of real gates passed.
   - Limits: no physics, frozen lighting, one place only. Removing objects from the room broke SOUS VIDE (0/5 fully successful).
4. **Gaussian splatting is why this is possible:** 134 fps vs 0.06 fps for a top NeRF.
5. **World models are the frontier but thin.** DreamerV3 and V-JEPA 2 are real results. The drone versions either use no camera (SkyJEPA) or rendered images (Dream to Fly). Industry (Genie 3, GAIA, Cosmos, Waymo) shows demos with no published real-world benefit, and video models score 29.5/100 on a physics test.
6. **Corrections to our 10-24 notes:** two arXiv entries were misdescribed, and AirSim is not archived. See `03-papers.md`.

## Options for the owner

What could go into block 4 (3 slides, ~2.5 min). You decide.

1. **"Two ways across the gap" (my recommendation).**
   - Slide 1: what the gap is, with a measured example (GRaD-Nav: sim 7-10/10 vs real 0-2/10) and Swift's own weakness (lighting).
   - Slide 2: Swift (abstraction + 50 s of real data to correct the simulator) vs MonoRace (heavy randomization, no real data for control). Both beat champions.
   - Slide 3: rebuild the real place from video (FalconGym 2.0 / SOUS VIDE) + limits, then one closing line on world models.
   - *Trade-off:* the strongest "актуально й по суті" (4 points), and an independent 2025 result rather than one lab. Slightly denser than the posted plan; image translation shrinks to one line.
2. **The posted plan as is:** gap → adaptation (RCAN image translation + fine-tuning on a little real data) → digital twins → how Swift did it.
   - *Trade-off:* exactly what the team agreed on 10-19, so no re-negotiation. RCAN is a robot arm, not a drone, and there's no 2025-26 race result, so it's less current.
3. **Your 10-24 notes plan (gap → closing it → world models).**
   - *Trade-off:* the freshest-sounding. But the world-model slide has the weakest evidence (demos, no camera, rendered images), which is risky in Q&A, and it overlaps the team's conclusion on risks.
4. **Twin-centred:** "the drone films the place and learns in the copy" (FalconGym 2.0, SOUS VIDE, NeRF → splats), with Swift as the before-picture.
   - *Trade-off:* closest to your original interest (3D environments); very visual; camera-only by construction. It drifts toward block 2's simulator topic, so the boundary line in the README matters more.

Optional extras for the joint intro and conclusion (team step 3):
- **Intro, where it works:** Wayve (camera-only street twins), DJI Terra (drone photos → 3D twin), Figure (humanoid walking learned in sim).
- **Conclusion, risks:** a twin is one place; world models hallucinate physics; who checks a policy that was never tested in that exact reality.

## Questions for the owner

1. **Block deadline.**
   - On 10-24 Yablonskyi wrote "у нас 4 дні", which points to **~Wed 10-28** for your block in the repo. The guidelines say about a week before the defence.
   - This decides how much time is left for the block file + 3 slides + gradual commits.
   - Recommendation: treat 10-28 as the deadline and start committing the block skeleton today, because "гіт має показувати роботу".
2. **Defence slot.**
   - The defences are the CS101 [P] slots on Nov 2, 5, 6, 9, 11 and 12, which you marked "don't attend".
   - It decides whether you need to be in class (the teacher asks random members about their own block).
   - Recommendation: ask in the team chat which slot is ours. I couldn't read the chat after 10-24 because the Telegram connector was down.
3. **Which option** (1-4 above)?
   - It decides what goes in the block file and slides.
   - Recommendation: option 1, and post the one-line block 2/4 boundary to the team so block 2 keeps domain randomization.
4. **Should block 2 get the AirSim correction?**
   - The 10-19 planning session called AirSim archived; it isn't (Microsoft announced retirement in 2022; IAMAI now maintains Project AirSim).
   - It decides whether a teammate repeats a wrong fact on a slide.
   - Recommendation: yes, one line to whoever owns block 2 (probably Misha). You send it; I don't message anyone.
