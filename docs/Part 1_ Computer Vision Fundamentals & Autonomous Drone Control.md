# Part 1: From Computer Vision Fundamentals to Autonomous Drone Control
**Presentation Narrative & Technical Roadmap**  
**Author:** Andrii Yablonskyi

---

## 1. Framing Standard Computer Vision Tasks
To understand how autonomous drones perceive their world, we must start with standard Computer Vision (CV). Traditional visual perception relies on three core capabilities: categorizing whole images, localizing targets with geometric bounds, and delineating fine spatial borders.

| Task | Core Objective | Output Type | Primary Example |
| :--- | :--- | :--- | :--- |
| **Classification** | What is in this image? | Single categorical label | Identifying an image as a "racing gate" |
| **Object Detection** | What is in the image and where is it? | Label + Bounding box coordinates $(x, y, w, h)$ | Locating multiple gates with bounding boxes |
| **Segmentation & Keypoints** | Which exact pixels belong to which object / feature? | Pixel-level mask / Keypoint heatmap | Outlining gate boundaries and pinpointing the 4 gate corners |

### Detailed Breakdown
- **Classification:** Evaluates the entire visual frame to assign a single category label (answering *"What is in this image?"*), but provides no spatial context regarding where the object sits.
- **Object Detection:** Combines classification with spatial localization. It fits rectangular bounding boxes around detected instances, giving a 2D spatial estimate within the camera frame.
- **Segmentation (Semantic & Keypoint Heatmaps):** Operates at the individual pixel level. Semantic segmentation classifies every pixel into a category, whereas keypoint heatmaps predict the exact sub-pixel coordinates of structural landmarks (such as the four corners of a racing gate), which are essential for downstream 3D pose estimation.

---

## 2. The Speed Barrier: Why High-Speed Drones Cannot Rely on Heavy CV Pipelines
While classic detection and heavy segmentation approaches work well for static imagery or slow robotics, autonomous racing platforms like the AI system **Swift** operate at speeds over $100\text{ km/h}$ ($\approx 28\text{ m/s}$) in complex 3D environments. Relying on traditional heavy CV pipelines creates critical bottlenecks that lead to flight instability and catastrophic crashes:

1. **High Processing Latency:** Heavy object detection and instance segmentation models require substantial computational time. At $100\text{ km/h}$, a delay of even $50\text{ ms}$ translates to over **$1.4\text{ meters}$ of blind, unguided flight**.
2. **Static Outputs vs. Dynamic Dynamics:** Classic CV yields static 2D spatial descriptions (e.g., *"Gate detected at bounding box $[x, y, w, h]$"*). Flight control requires continuous, dynamic physical state estimates such as 3D position, linear velocity, angular rates, and thrust vectors.
3. **Pipeline Cascading Errors:** Cascaded architectures that decouple perception from estimation require high-frequency, low-latency keypoints. If perception lags or misclassifies a frame, downstream path planning and attitude control fail immediately.

---

## 3. Technical Mechanics of Computer Vision for Beginners

### 3.1 Images as Numerical Tensors
To a computer, an image is not a visual picture but a structured matrix of numbers:
- **Grid & Resolution:** A standard digital image is a 2D grid of pixels (e.g., $640 \times 480$).
- **Pixel Intensity:** Each pixel holds numerical values ranging from 0 (pure black) to 255 (pure white), commonly normalized to the interval $[0.0, 1.0]$.
- **Color Channels (RGB):** Color images consist of three overlapping 2D matrices (Red, Green, Blue), forming a 3D Tensor of shape $(3 \times H \times W)$, or in batch form $[B, 3, H, W]$.

### 3.2 Kernels & Convolutions
Convolutions are the core visual operation used to extract spatial features from pixel grids:
- **Kernel (Filter):** A small matrix (e.g., $3 \times 3$ or $5 \times 5$) containing specific weight values designed to detect structural patterns.
- **Sliding Window:** The kernel slides across the image pixel-by-pixel. At each position, it performs element-wise multiplication with the underlying image pixels and sums the result into a single output number.
- **Feature Extraction:** Depending on its weights, a convolution kernel can highlight horizontal lines, vertical edges, color gradients, or sharp corners while filtering out irrelevant background noise.

### 3.3 The Hierarchical Layer Pipeline
Deep Convolutional Networks organize features in a progressive hierarchy across layers:
- **Early Layers (Low-Level Features):** Detect primitive visual elements such as simple lines, high-contrast edges, gradient changes, and basic color transitions.
- **Middle Layers (Mid-Level Features):** Combine low-level edges to recognize geometric primitives, textures, corners, curves, and basic shapes.
- **Deep Layers (High-Level Context):** Combine geometric shapes into semantic patterns, recognizing full objects, spatial orientation, track structures, and environmental context.

---

## 4. Debunking the Myth: Cascaded Architecture vs. Pure End-to-End
A widespread myth in popular media claims that champion racing drones like *Swift* operate via a pure "pixels-to-motors" end-to-end black box, feeding raw camera pixels directly into motors without intermediate perception or state estimation.

In reality, as published in *Nature* (Kaufmann et al., 2023), pure end-to-end controllers suffer from severe sample inefficiency, vulnerability to visual domain shifts, and unverifiable safety. Instead, the state-of-the-art *Swift* system employs a disciplined **cascaded hybrid architecture**:

```
[ Camera Frame ] ──> [ U-Net (Corner Heatmap) ] ──> [ VIO + Kalman Filter ] ──> [ RL Policy (PPO) ] ──> [ Motor Commands ]
```

1. **Lightweight U-Net Corner Detector:**  
   Runs onboard at high frame rate to detect the 2D pixel coordinates of the 4 gate corners.
2. **Visual-Inertial Odometry (VIO) & Extended Kalman Filter (EKF):**  
   Fuses the visual corner detections with $1000\text{ Hz}$ IMU readings (accelerometers and gyroscopes) to estimate the drone's true 3D position, velocity, and orientation.
3. **Reinforcement Learning (RL) Policy:**  
   Trained in simulation with Proximal Policy Optimization (PPO), mapping the estimated drone state relative to the track directly to body rates and collective thrust.
4. **Motor Actuation:**  
   Standard low-level PID controller translates control commands into individual motor RPMs.

---

## 5. Technical Deep Dive: U-Net Architecture & Skip Connections

### U-Net vs. Classic Autoencoder
While both architectures use an encoder-decoder framework with a bottleneck, their mechanics and objectives are fundamentally different:

| Feature | Classic Autoencoder | U-Net |
| :--- | :--- | :--- |
| **Primary Goal** | Dimensionality reduction, data compression, latent representation | Dense pixel-level prediction (keypoint heatmaps / segmentation) |
| **Bottleneck** | Severe spatial compression into 1D vector; destroys fine spatial coordinates | Downsampled spatial tensor holding global scene semantics |
| **Information Flow** | Strict information bottleneck; decoder has no access to early features | **Skip Connections** copy feature maps directly from encoder to decoder |
| **Suitability for Racing** | Unusable: output coordinates are blurry and imprecise | Ideal: sub-pixel accuracy for gate corner localization |

### Why Skip Connections Are Critical for Autonomous Drones
- In the encoder, successive convolutions and pooling operations (`MaxPool2d`) increase the receptive field to capture global context ("this is a racing gate"). However, downsampling sacrifices exact spatial resolution.
- For high-speed navigation at $100\text{ km/h}$, spatial precision is paramount: an error of even 5 pixels in corner detection leads to significant 3D pose misestimation.
- **Skip Connections (`torch.cat`):** The high-resolution feature maps from early encoder layers are cached. When the decoder upsamples the deep feature maps, the cached high-resolution maps are concatenated along the channel axis. This allows the decoder to combine deep semantic understanding with crisp, exact edge and corner coordinates.

### PyTorch Implementation Structure
```python
# Conceptual U-Net Flow in PyTorch
class GateCornerUNet(nn.Module):
    def __init__(self):
        super().__init__()
        # Encoder
        self.enc1 = ConvBlock(3, 64)
        self.pool1 = nn.MaxPool2d(2)
        self.enc2 = ConvBlock(64, 128)
        self.pool2 = nn.MaxPool2d(2)
        
        # Bottleneck
        self.bottleneck = ConvBlock(128, 256)
        
        # Decoder
        self.up2 = nn.ConvTranspose2d(256, 128, kernel_size=2, stride=2)
        self.dec2 = ConvBlock(256, 128) # 128 (up) + 128 (skip) = 256 channels
        self.up1 = nn.ConvTranspose2d(128, 64, kernel_size=2, stride=2)
        self.dec1 = ConvBlock(128, 64)   # 64 (up) + 64 (skip) = 128 channels
        
        # Output Heatmap (4 gate corners)
        self.final_conv = nn.Conv2d(64, 4, kernel_size=1)

    def forward(self, x):
        # Encoder with skip caching
        s1 = self.enc1(x)
        p1 = self.pool1(s1)
        s2 = self.enc2(p1)
        p2 = self.pool2(s2)
        
        # Bottleneck
        b = self.bottleneck(p2)
        
        # Decoder with Skip Connections (torch.cat)
        d2 = self.up2(b)
        d2 = torch.cat([d2, s2], dim=1) # Skip connection from enc2
        d2 = self.dec2(d2)
        
        d1 = self.up1(d2)
        d1 = torch.cat([d1, s1], dim=1) # Skip connection from enc1
        d1 = self.dec1(d1)
        
        return self.final_conv(d1)
```

---

## 6. How the Drone Knows Its Position: VIO & Kalman Filtering
A fundamental question in autonomous racing is how the drone continuously localizes itself in 3D space:
- **Visual-Inertial Odometry (VIO):**  
  Merges visual landmarks (gate corners from U-Net) with high-speed IMU measurements ($1000\text{ Hz}$). IMU eliminates camera latency and fills in between visual frames, while visual detections eliminate IMU integration drift.
- **Extended Kalman Filter (EKF):**  
  Fuses these asynchronous sensor streams into an optimal state estimate: 3D position $(x, y, z)$, velocity $(\dot{x}, \dot{y}, \dot{z})$, and orientation quaternion $(q_w, q_x, q_y, q_z)$. This smooth state vector is fed into the RL flight controller.

---

## 7. Presentation Roadmap & Team Bridges
- **Block 2 (Simulation - Misha Dvali):**  
  How synthetic simulation environments generate thousands of photorealistic gate images with automated ground-truth masks and 3D corner coordinates, bypassing the prohibitive cost and crash risks of real-world data collection.
- **Block 3 (Training & RL - Hlib Tataryntsev):**  
  Supervised training of the U-Net perception model (using Dice/BCE loss) and reinforcement learning (PPO) of the control policy.
- **Block 4 (Sim-to-Real Transfer - Andrii Thorenko):**  
  Overcoming the reality gap through visual and physical domain randomization to deploy simulation-trained policies directly onto physical drones.

---

## 8. Sources & References
- **Swift Nature Paper:**  
  Kaufmann, E., Loquercio, A., Bauersfeld, L., et al. *"Champion-level drone racing using deep reinforcement learning."* Nature 620, 982–987 (2023). [https://doi.org/10.1038/s41586-023-06419-4](https://www.nature.com/articles/s41586-023-06419-4)
- **U-Net Architecture:**  
  Ronneberger, O., Fischer, P., Brox, T. *"U-Net: Convolutional Networks for Biomedical Image Segmentation."* MICCAI (2015). [https://arxiv.org/abs/1505.04597](https://arxiv.org/abs/1505.04597)
- **Visual-Inertial Odometry:**  
  Scaramuzza, D., Zhang, Z. *"Visual-Inertial Odometry of Aerial Robots: A Tutorial."* IEEE RAM (2020).
- **Kalman Filter Foundation:**  
  Kalman, R. E. *"A New Approach to Linear Filtering and Prediction Problems."* Journal of Basic Engineering (1960).
