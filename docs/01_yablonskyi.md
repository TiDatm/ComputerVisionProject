# Block 1: Computer Vision Fundamentals & Perception Pipeline

> **Presenter:** Andrii Yablonskyi  
> **Presentation Section:** Block 1 (Slides 2–4)  
> **Role & Purpose:** Establish the foundational mechanics of Computer Vision (CV) in autonomous drone navigation, explain how deep convolutional networks interpret visual scenes, debunk the common "pixels-to-motors end-to-end" misconception using the Nature 2023 *Swift* architecture, and bridge the gap between vision, state estimation (VIO/Kalman filter), and flight control.

---

## 1. Slide-by-Slide Presentation Structure

### Slide 2: Computer Vision Tasks on a Racing Gate
- **Core Objective:** Demystify how a computer parses a visual scene into actionable geometric data using a racing gate as the unifying example.
- **Three Fundamental Tasks:**
  1. **Classification (Categorical Label):**
     - *Question:* "What is in this image?"
     - *Output:* Single categorical class (e.g., `Gate` vs. `Background`).
     - *Limitation:* Zero spatial localization; the system knows a gate exists in the field of view, but cannot steer toward it.
  2. **Object Detection (Bounding Box Localization):**
     - *Question:* "What is in the image, and where is it located?"
     - *Output:* Class label + 2D bounding box coordinates $[x, y, w, h]$.
     - *Limitation:* Rectangles approximate bounding volume but fail to capture gate tilt, camera roll, exact 3D orientation, or precise entry boundaries.
  3. **Segmentation & Keypoint Heatmap (Pixel-Level Geometry):**
     - *Question:* "Which exact pixels belong to the target object / keypoints?"
     - *Output:* Pixel-level binary/multi-class mask or keypoint heatmap.
     - *Drone Application:* Predicting the 4 precise corners of the racing gate. This sub-pixel/pixel-level accuracy is necessary to estimate the drone's relative 3D pose and fly through the gate at over 100 km/h.

---

### Slide 3: How a Convolutional Neural Network (CNN) Sees the World
- **Core Objective:** Trace the journey of visual data from raw numerical inputs to hierarchical feature representations.
- **1. Images as Multi-Dimensional Tensors:**
  - A digital image is not an abstract visual scene to a processor, but a 3D tensor of shape:
    $$\text{Input Tensor} \in \mathbb{R}^{B \times C \times H \times W}$$
    where $B$ is batch size, $C = 3$ (RGB color channels), $H$ is image height, and $W$ is image width.
  - Pixel intensities are represented numerically in range $[0, 255]$ (or normalized to $[0.0, 1.0]$).
- **2. Convolution Kernels (Spatial Filtering):**
  - Small learnable matrices (e.g., $3 \times 3$ or $5 \times 5$) slide across spatial dimensions with stride and padding.
  - At each spatial window, the kernel computes element-wise products and sums them:
    $$(I * K)(i, j) = \sum_{m} \sum_{n} I(i - m, j - n) K(m, n)$$
  - Convolutions preserve 2D spatial locality and translation equivariance while extracting features.
- **3. Hierarchical Feature Extraction Across Layers:**
  - **Early Layers (Low-Level Features):** Sharp edges, horizontal/vertical gradients, color contrasts, texture transitions.
  - **Middle Layers (Mid-Level Features):** Corners, geometric primitives, curves, gate arch borders.
  - **Deep Layers (High-Level Context):** Holistic gate shapes, spatial context, track boundaries, and relative depth cues.

---

### Slide 4: The Speed Barrier & Debunking the "End-to-End" Myth
- **Core Objective:** Dispel popular media myths claiming autonomous racing drones operate as pure "pixels-to-motors" black boxes, and present the actual cascaded pipeline deployed on *Swift* (Nature 2023).

#### The High-Speed Latency Barrier
- At racing velocities exceeding $100\text{ km/h}$ ($\approx 28\text{ m/s}$), latency is fatal:
  - A perceptual processing delay of $50\text{ ms}$ results in **$1.4\text{ meters}$ of blind flight**.
  - Traditional heavy object detectors (e.g., YOLOv8, Mask R-CNN) or complex two-stage pipelines introduce excessive compute latency on resource-constrained embedded drone hardware.
  - Cascading latency causes catastrophic overshoot and gate collisions.

#### Debunking the End-to-End Myth
- **The Common Myth:** The drone feeds raw camera pixels directly into a monolithic neural network policy that directly commands motor voltages/thrusts ($\text{Pixels} \to \text{Motors}$).
- **Why Pure End-to-End Fails in Practice:**
  1. *Extreme Sample Inefficiency:* Learning 3D spatial dynamics and visual features simultaneously from raw pixels requires billions of unfeasible environment steps.
  2. *Sim-to-Real Reality Gap:* Minor visual variations (lighting, lens flare, background spectators) disrupt motor control if trained end-to-end.
  3. *Unverifiable Safety:* Zero modularity makes debugging state estimation failures impossible.
- **The Actual Nature 2023 Pipeline (*Swift*):**
  Instead of an end-to-end black box, *Swift* uses a decoupled, hybrid cascaded architecture combining fast deep learning perception with classical state estimation and reinforcement learning control:

```
[ Onboard Fisheye Camera ]
            │
            ▼
┌────────────────────────────────────────────────────────┐
│  1. Lightweight U-Net Neural Network                   │
│     Predicts 2D pixel coordinates of 4 gate corners    │
└────────────────────────────────────────────────────────┘
            │  (Corner Heatmaps)
            ▼
┌────────────────────────────────────────────────────────┐
│  2. Visual-Inertial Odometry (VIO) + Kalman Filter    │
│     Fuses gate corner pixels with 1000 Hz IMU data     │
│     Estimates full 3D state: [p, v, q, ω]              │
└────────────────────────────────────────────────────────┘
            │  (Filtered 3D Drone State)
            ▼
┌────────────────────────────────────────────────────────┐
│  3. Reinforcement Learning (RL) Policy                │
│     Trained via PPO in high-fidelity simulation        │
│     Outputs collective thrust & body rates (c, ω)      │
└────────────────────────────────────────────────────────┘
            │  (Control Commands)
            ▼
[ 4. Low-Level Betaflight PID & Motors ]
```

---

## 2. Technical Deep Dive: U-Net Architecture & Skip Connections

### Why U-Net Instead of a Classic Autoencoder?
Both architectures feature an encoder-decoder topology with a bottleneck, but their operational objectives and mathematical behaviors differ fundamentally:

| Dimension | Classic Autoencoder | U-Net (Autonomous Drone Perception) |
| :--- | :--- | :--- |
| **Primary Objective** | Data compression (dimensionality reduction) and global image reconstruction | Dense pixel prediction (gate corner detection and keypoint heatmaps) |
| **Bottleneck Representation** | Compresses the whole input into a compact 1D latent vector $z \in \mathbb{R}^D$ | Downsampled 2D spatial feature map retaining global semantic context |
| **Spatial Resolution** | Spatial coordinates are irrevocably collapsed in the latent vector | Spatial dimensions are systematically recovered via symmetric upsampling |
| **Inter-path Connections** | None (decoder reconstructs solely from the compressed latent vector) | **Skip Connections:** Direct tensor concatenation across corresponding resolution levels |
| **Critical Flaw for Drones** | High spatial blurriness; loses boundary sharpness (fatal error at 100 km/h) | Sub-pixel corner precision; zero spatial loss thanks to skip connections |

### The Critical Role of Skip Connections for Drone Flight
- In an encoder, pooling layers (`MaxPool2d`) reduce spatial dimensions $(H, W)$ to aggregate large receptive fields. While deep layers understand **what** is in the scene (a gate), they lose exact **where** coordinates.
- If a decoder reconstructs predictions solely from the bottleneck, output masks are coarse and blurred. At $100\text{ km/h}$, an error of just 5 pixels in corner estimation shifts 3D gate pose calculations by dozens of centimeters, causing gate clipping.
- **The Skip Connection Mechanism:**
  - Before each downsampling operation in the encoder, the high-resolution feature tensor is preserved in memory.
  - In the decoder, after upsampling the deep semantic features, the preserved encoder tensor is concatenated along the channel dimension (`torch.cat([upsampled_feat, skip_feat], dim=1)`).
  - A subsequent $3 \times 3$ convolution merges the combined channels, fusing high-level semantics ("this is a racing gate") with micro-level edge geometry ("the corner vertex is exactly at pixel $(u, v)$").

---

### PyTorch Layer-by-Layer Flow

```
Input Frame [B, 3, H, W]
   │
   ├──> [Conv2d + ReLU] ─────────────── Skip 1 ──────────────┐
   │         │                                               │
   │    [MaxPool2d] (↓ 2x)                                   │
   │         │                                               │
   │   [Conv2d + ReLU] ──────── Skip 2 ────────┐             │
   │         │                                 │             │
   │    [MaxPool2d] (↓ 2x)                     │             │
   │         │                                 │             │
   │   [Conv2d + ReLU] ── Skip 3 ──┐           │             │
   │         │                     │           │             │
   │    [MaxPool2d] (↓ 2x)         │           │             │
   │         │                     │           │             │
   │    [BOTTLENECK]               │           │             │
   │    (Deep context)             │           │             │
   │         │                     │           │             │
   │   [Upsample/Transpose] (↑ 2x) │           │             │
   │         │                     │           │             │
   │   [torch.cat(skip_3)] ◄───────┘           │             │
   │   [Conv2d + ReLU]                         │             │
   │         │                                 │             │
   │   [Upsample/Transpose] (↑ 2x)             │             │
   │         │                                 │             │
   │   [torch.cat(skip_2)] ◄───────────────────┘             │
   │   [Conv2d + ReLU]                                       │
   │         │                                               │
   │   [Upsample/Transpose] (↑ 2x)                           │
   │         │                                               │
   │   [torch.cat(skip_1)] ◄─────────────────────────────────┘
   │   [Conv2d + ReLU]
   │         │
   │   [Final Conv2d 1x1]
   ▼
Output Heatmap [B, 1, H, W] (Gate Corner Keypoints)
```

#### Tensor Shapes & Operations
1. **Input:** Raw frame from camera $[B, 3, H, W]$.
2. **Encoder Stage:**
   - `nn.Conv2d(in_channels, out_channels, kernel_size=3, padding=1)` expands channel depth ($3 \to 64 \to 128 \to 256$), detecting local primitives without losing spatial resolution.
   - `nn.MaxPool2d(kernel_size=2, stride=2)` halves spatial dimensions ($H \to H/2, W \to W/2$). Feature maps prior to pooling are cached into a skip list (`encoder_features.append(x)`).
3. **Bottleneck:** Lowest spatial resolution, highest channel depth (e.g., $512$ channels), capturing global scene structure and full context.
4. **Decoder Stage:**
   - `nn.ConvTranspose2d` or `nn.Upsample(scale_factor=2)` doubles spatial resolution.
   - `torch.cat([x, skip], dim=1)` concatenates the cached encoder feature map along the channel axis.
   - `nn.Conv2d` processes the concatenated tensor, refining spatial boundaries and reducing channel count.
5. **Output Stage:**
   - `nn.Conv2d(current_channels, out_channels=1, kernel_size=1)` yields a single-channel confidence heatmap $[B, 1, H, W]$, where peak values identify the 4 corner coordinates of the gate.

---

## 3. State Estimation: How the Drone Knows Its Position

A critical question often overlooked in high-level discussions is: **"How does the drone know where it is in 3D space?"**

### Visual-Inertial Odometry (VIO)
Visual data and inertial data provide complementary strengths:
- **IMU (Inertial Measurement Unit):** Measures angular velocity (gyroscopes) and linear acceleration (accelerometers) at high frequency ($1000\text{ Hz}$).
  - *Advantage:* Zero latency, robust to lighting changes.
  - *Weakness:* Sensor bias causes quadratic drift over time (dead-reckoning drift).
- **Vision (U-Net Detections):** Provides absolute geometric landmarks (gate corners) relative to the drone.
  - *Advantage:* Bounded global error, corrects IMU drift.
  - *Weakness:* Lower frequency ($\sim 30\text{–}60\text{ Hz}$), vulnerable to motion blur and lighting.

### Extended Kalman Filter (EKF)
The Kalman Filter acts as the mathematically optimal state estimator:
1. **Prediction Step (IMU-driven):** Continuously projects the drone's high-rate kinematic state:
   $$\mathbf{x} = [p_x, p_y, p_z, v_x, v_y, v_z, q_w, q_x, q_y, q_z, \omega_x, \omega_y, \omega_z]^T$$
2. **Correction Step (Vision-driven):** When U-Net produces gate corner measurements, the filter computes the measurement residual (innovation) and updates the state estimate:
   $$\mathbf{x}_{k} = \mathbf{x}_{k}^- + \mathbf{K}_k (\mathbf{z}_k - h(\mathbf{x}_k^-))$$
3. **Output:** A smooth, drift-free 3D state vector fed directly into the RL control policy.

---

## 4. Bridge to Subsequent Blocks

- **Block 2 (Simulation - Misha Dvali):**  
  Training the U-Net corner detector and flight controller on physical drones would lead to immediate crashes and prohibitive hardware costs. In Block 2, Misha will explain how high-fidelity simulation generates unlimited labeled training data (perfect 3D corner coordinates, depth, masks) with zero human labeling effort.
- **Block 3 (Training & RL - Hlib Tataryntsev):**  
  Supervised training of U-Net using synthetic ground truth, and reinforcement learning (PPO) of the state-to-action flight policy.
- **Block 4 (Sim-to-Real Transfer - Andrii Thorenko):**  
  Bridging the sim-to-real gap through domain randomization, noise injection, and real-flight fine-tuning.

---

## 5. Sources & References

### Autonomous Drone Racing & Swift Architecture
- **Nature 2023 Paper:**  
  Kaufmann, E., Loquercio, A., Bauersfeld, L., Romero, A., Scaramuzza, D., et al. (2023). *"Champion-level drone racing using deep reinforcement learning."* **Nature**, 620, 982–987.  
  [https://doi.org/10.1038/s41586-023-06419-4](https://www.nature.com/articles/s41586-023-06419-4)
- **University of Zurich Press Release:**  
  *"Autonomous Drone Beats Human Champions"*  
  [https://news.uzh.ch/en/articles/media/2023/Drone-race.html](https://news.uzh.ch/en/articles/media/2023/Drone-race.html)
- **UZH Robotics & Perception Group Video:**  
  [Swift: Autonomous Drone Racing Champion (YouTube)](https://youtu.be/fBiataDpGIo)

### Computer Vision & U-Net Architecture
- **U-Net Original Paper:**  
  Ronneberger, O., Fischer, P., & Brox, T. (2015). *"U-Net: Convolutional Networks for Biomedical Image Segmentation."* Medical Image Computing and Computer-Assisted Intervention (MICCAI), Springer, LNCS, Vol. 9351, pp. 234–241.  
  [https://arxiv.org/abs/1505.04597](https://arxiv.org/abs/1505.04597)
- **Deep Learning Fundamentals:**  
  LeCun, Y., Bengio, Y., & Hinton, G. (2015). *"Deep learning."* **Nature**, 521, 436–444.  
  [https://doi.org/10.1038/nature14539](https://doi.org/10.1038/nature14539)
- **PyTorch Documentation:**  
  PyTorch `nn.Conv2d`, `nn.ConvTranspose2d`, and `torch.cat` operational specifications.  
  [https://pytorch.org/docs/stable/nn.html](https://pytorch.org/docs/stable/nn.html)

### Visual-Inertial Odometry & Kalman Filtering
- **VIO for Aerial Robotics:**  
  Scaramuzza, D., & Zhang, Z. (2020). *"Visual-Inertial Odometry of Aerial Robots: A Tutorial."* IEEE Robotics & Automation Magazine.
- **Benchmark of Monocular VIO:**  
  Delmerico, J., & Scaramuzza, D. (2018). *"A Benchmark Comparison of Monocular Visual-Inertial Odometry Algorithms for Flying Robots."* ICRA 2018.  
  [https://arxiv.org/abs/1804.04366](https://arxiv.org/abs/1804.04366)
- **Kalman Filtering:**  
  Kalman, R. E. (1960). *"A New Approach to Linear Filtering and Prediction Problems."* Journal of Basic Engineering, 82(1), 35–45.
