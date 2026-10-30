# Block 4: From simulation to reality

**Main thesis.** To bring an already trained model from the simulated space to reality, you need to somehow solve the difference between the real and the simulated world (the sim-to-real gap). There are a couple of options, which could be used for different situations.

## The gap

We start from the point where we have a drone able to navigate in a virtual world. We apply our development, test it, and train the model to see through the drone's cameras and navigate itself in the world. It crushes the simulation, scoring a near-perfect score on various flying tests, navigating simply by vision. But when we try to bring it into the real world, it crashes immediately, unable to perform properly 80–100% of the time [2].

You are confused and reasonably question yourself: why did that happen? You review the recording, and what you see is that your realistic simulation was not realistic enough. Some small bit of mud on the camera caused the drone to falsely detect obstacles along the way and crash immediately, trying to avoid them. That's basically what Aljalbout et al. (2025) describe [1]. Small bits of dirt, motion blur, unpredictable lighting, some improperly textured object in the simulation, camera noise or a different angle, and a minuscule mismatch in physics, along with speeds of dozens of meters per second, combined cause enough interference to crash your properly trained model.

## Two ways across

One group takes it in one direction. They try to improve the simulation: they add different dirt variations, grass types, textures and slight deviations to physics, creating a multitude of worlds where the model is expected to perform (this is called domain randomization). Unsurprisingly, machine learning does its thing and adapts to all of those hundreds or even thousands of variations, now being able to fly in a world where all the grass is pink, gravity is 2.5 times higher, and the entire scene is filled with light. When it enters the real world, it all seems like another test made during the training, so it adapts to actual real conditions.

Another group takes it in a different direction. They assume that the real world is way too complicated for a model to understand, and try to simplify it. The physics problem persists, so they still have to adapt to that. But the images themselves could be improved. Let's say the amount of greens in the grass is way too much, compared to the simulation, where the colour is consistent. So you limit the colour palette of the grass to a single colour. Take this idea to a larger scale and you have a model trained to convert any kind of real image into consistent style used during the simulation.

Turns out both options work [3][4]. Drones take off in the real world and perform their flight exams. However, both groups realised that the issue did not change. It's just that the responsibility to navigate in the actual world switched from the main machine brain to a module responsible for standardising and trying to understand the real world.

## Swift

If you look at our example, a group of scientists working on Swift (a racing-drone AI, University of Zurich, 2023) came up with a smart idea [5]. What if, instead of trying to predict an entire world to navigate, we give ourselves a much easier goal to detect? Here we have those gates that we need to pass. An additional smaller model is trained to recognise this specific square in all of its combinations. As a result, a model could navigate simply by detecting the next gate, following it, and auto-correcting its own path to fit into it. Those researchers have defeated three world champions in drone flying, winning 15 of 25 races [5]. The model demonstrated surprising speed and capability of navigating.

But when researchers at Swift managed to cross the navigation gap, they ran into another issue. Their system was able to navigate near perfectly, but not its own prediction of where the actual drone was supposed to be. A small misalignment between learned and real physics values gave a slight calculation error. However, those errors quickly compounded and caused the drone to misalign its position. As a fine-tune and trick, Swift used residual models: three short flights, about 50 seconds in total, combined with the actual drone inputs [5]. This allowed them to compare the results to their simulated model and align their simulation so it matched the recorded position, which was basically physics fine-tuning for their simulation. The model was then fine-tuned in this corrected simulation; fine-tuning itself is covered in block 3.

## Swift vs MonoRace

The thing that stands out when comparing Swift and one of its successors, MonoRace, is the approach they have taken. Both have the same base: instead of images, the control model gets a set of numbers, like position, speed, angle and the next gate's position [4][5]. To get those numbers, both combine the camera with the drone's motion sensor through a Kalman filter (a way to combine a prediction with each new noisy measurement) [10].

The difference is in how they crossed the gap. Swift used a separate unit that calculated the position, plus an additional 50 seconds of real data. That unit is visual-inertial odometry (VIO), a technology that allows a machine to detect its own movement without using expensive sensors or pre-set reference points [9]. It detects movement by connecting points between two different images, along with their delta time, allowing it to calculate the camera's change in position.

Compared to that, MonoRace didn't use any real flight data in training its control model [4]. Instead, they used environment randomization via randomized changes in physics, with most physics values changed by 40% or more [4].

As an example, they needed to use a cheaper rolling-shutter camera, which reads the image line by line instead of a single print, so fast movement stretches and blurs it; they added this blur to their training images so the model would not be confused by it in real flight [4].

But now we have to look not only at those successful attempts, but at why they actually failed. It turned out that in 4 of the 10 races Swift lost, it collided with its opponent, because it could not recognise other drones [5].

## Limits of cameras, and LiDAR

However, when considering issues that are still difficult to resolve with an image-based navigation system, you would name extra lighting [5] or, in turn, complete absence of light, imprecise position detection, and overall camera-based detection inaccuracies. Those specific issues of this method were attempted to be solved with a LiDAR system, which has its own issues. A LiDAR (Light Detection and Ranging) system is basically a laser shooting small blinks of light at the speed of light, thousands or even hundreds of thousands of times per second. It creates an approximation of the surrounding space as a point cloud: a set of dots, made by shooting many beams of light and measuring how they bounce back, accurate up to several centimetres across dozens of metres [6][11]. One team of scientists working on a project called SUPER was able to create a drone which was able to manoeuvre in the forest at a speed of over 20 metres per second, which had both the safety and the speed allowed by the reliability of a LiDAR sensor compared to image-based detection [7].

However, another application of LiDAR sensors in drone training is at the simulation level, covered in block 2 [8].

## Link to the rest of the presentation

This combines the entire pipeline, from creating (block 2) to training (block 3) to adapting the model (this block), giving us a machine able to navigate in the real world and able to see in the real world.

## Sources

[1] Aljalbout et al. (2025), "The Reality Gap in Robotics: Challenges, Solutions, and Best Practices": https://arxiv.org/abs/2510.20808
[2] Chen et al. (2025), "GRaD-Nav: Efficiently Learning Visual Drone Navigation with Gaussian Radiance Fields and Differentiable Dynamics": https://arxiv.org/abs/2503.03984
[3] James et al. (2019), "Sim-to-Real via Sim-to-Sim" (RCAN), CVPR 2019: https://arxiv.org/abs/1812.07252
[4] Bahnam et al. (2026), "MonoRace: Winning Champion-Level Drone Racing with Robust Monocular AI": https://arxiv.org/abs/2601.15222
[5] Kaufmann et al. (2023), "Champion-level drone racing using deep reinforcement learning", Nature: https://www.nature.com/articles/s41586-023-06419-4
[6] Lidar, Wikipedia: https://en.wikipedia.org/wiki/Lidar
[7] Ren et al. (2025), "Safety-assured high-speed navigation for MAVs" (SUPER), Science Robotics: https://doi.org/10.1126/scirobotics.ado6187
[8] Lv et al. (2026), "Zero-Shot UAV Navigation in Forests via Relightable 3D Gaussian Splatting": https://arxiv.org/abs/2602.07101
[9] Visual odometry, Wikipedia: https://en.wikipedia.org/wiki/Visual_odometry
[10] Kalman filter, Wikipedia: https://en.wikipedia.org/wiki/Kalman_filter
[11] Livox Mid-360 specifications (example drone LiDAR: 200,000 points/s, ≤2 cm precision at 10 m, 40–70 m range): https://www.livoxtech.com/mid-360/specs
