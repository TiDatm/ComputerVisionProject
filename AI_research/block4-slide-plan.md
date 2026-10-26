# Block 4 slide plan: From simulation to reality

AI-assisted plan (see `cs-research-2026-10-26/`). Agreed with Andrii on 2026-10-26: 4 main slides, about 2:20, plus backup slides for questions. The block file `blocks/04_thorenko.md` is written separately, in my own words.

Entry from block 3: "Swift learned to fly in simulation. But will it work in the real world?"

## Main slides

### 1. The gap between simulation and reality (~35 s)
- Picture: the same racing gate, as the simulator draws it and as the real camera sees it.
- Four kinds of gap: looks, sensors, physics, delays (callback to block 1: at 100 km/h, a 50 ms delay is about 1.3 m of blind flight).
- Measured example: GRaD-Nav, the same policy succeeds 7-10/10 in simulation and 0-2/10 in reality.

### 2. Two recipes, both beat human champions (~40 s)
- Swift (UZH, Nature 2023): the policy sees gate corners and the estimated position, not pixels. About 50 s of real flight corrected the simulator, then one fine-tuning pass. Won 15 of 25 races against champions.
- MonoRace (TU Delft, A2RL, April 2025): one camera, physics randomized by at least 40%, no real flight data for control. Best time 16.56 s, top speed 28.23 m/s, beat three world-champion-level pilots.
- Message: opposite recipes, same result. Swift is the 2023 baseline; MonoRace is the 2025 step forward (cheaper setup, no motion capture, no real data for control).
- Video: short clips of both (see below).

### 3. Don't draw the world, copy it (~35 s)
- Pipeline: 3-minute video, camera positions, Gaussian splats, training, real flight.
- FalconGym 2.0: zero-shot transfer, 69 of 70 real gates (98.6%) over 30 trials. SOUS VIDE: a twin from a 2-3 minute handheld video, 105 real flights.

### 4. Limits and the open question (~30 s, can merge into the team conclusion)
- Twin limits: no physics, frozen in time, one place. SOUS VIDE with objects moved: 0 of 5 fully successful.
- The industry debate: draw the world (NVIDIA Isaac, MonoRace), copy it (Wayve PRISM-1, DJI Terra), or learn it (Waymo World Model, Genie 3).
- World models still break physics: the best video model scored 29.5/100 on Physics-IQ.
- Exit: "No method closes the gap completely yet, and this is where the field is working now."

## Videos (candidates, links checked 2026-10-26)

| Use | Video | Channel | Link |
|---|---|---|---|
| Intro or slide 2, Swift | Champion-level Drone Racing using Deep Reinforcement Learning (Nature, 2023) | UZH Robotics and Perception Group | https://youtu.be/fBiataDpGIo |
| Slide 2, MonoRace | AI Grand Challenge: MAVLAB Takes the Win at A2RL | Abu Dhabi Autonomous Racing League | https://www.youtube.com/watch?v=AvyI58mnyo4 |
| Slide 2 alt, AI vs human | AI Beats Human in Autonomous Drone Race Championship Final | Abu Dhabi Autonomous Racing League | https://www.youtube.com/watch?v=iw79WXzigRA |
| Backup "why not LiDAR?" | SUPER - Safety-assured High-Speed Navigation for MAVs | MARS LAB HKU | https://www.youtube.com/watch?v=GPHuzG0ANmI |
| Slide 3, twin | not found yet: no official SOUS VIDE or FalconGym 2.0 video located | | |

Preference (2026-10-26): include at least one of the Swift or SUPER videos; decide placement with the team.

Rules for videos: muted 5-10 s loops that play while I talk (time limit), and a still frame plus link in `slides.pdf`, which cannot play video.

## Backup slides (not presented)
- NeRF vs Gaussian splatting (134 fps vs 0.06 fps).
- RCAN image translation (70% with zero real data).
- Why not LiDAR: weight, power, sunlight (SUPER video here).
- World models: DreamerV3, V-JEPA 2.
- Swift's full pipeline: camera, U-Net corners, VIO + Kalman filter, RL policy, motors.

## Sources for the numbers
- Swift: https://www.nature.com/articles/s41586-023-06419-4
- MonoRace: https://arxiv.org/abs/2601.15222
- FalconGym 2.0: https://arxiv.org/abs/2510.02248
- SOUS VIDE: https://arxiv.org/abs/2412.16346
- Full list with the other numbers: `cs-research-2026-10-26/03-papers.md`
