# Block 3: Training methods

## Contents

- [Supervised learning](#supervised-learning)
  - [The idea](#the-idea)
  - [Vocabulary](#vocabulary)
  - [What training means](#what-training-means)
  - [Binary cross-entropy loss (BCE)](#binary-cross-entropy-loss-bce)
  - [Dice loss](#dice-loss)
  - [Gradient descent](#gradient-descent)
  - [Backpropagation and Adam](#backpropagation-and-adam)
  - [Generalization](#generalization)
  - [Where labels come from](#where-labels-come-from)
  - [Supervised learning in Swift](#supervised-learning-in-swift)
  - [Limits](#limits)
- [Fine-tuning](#fine-tuning)
  - [The idea](#the-idea-1)
  - [Vocabulary](#vocabulary-1)
  - [Why it works](#why-it-works)
  - [The math: a small step from a good start](#the-math-a-small-step-from-a-good-start)
  - [Strategies](#strategies)
  - [Catastrophic forgetting](#catastrophic-forgetting)
  - [Domain shift](#domain-shift)
  - [Fine-tuning in robotics](#fine-tuning-in-robotics)
  - [Fine-tuning in Swift](#fine-tuning-in-swift)
  - [Limits](#limits-1)
- [Reinforcement learning](#reinforcement-learning)
- [Imitation learning](#imitation-learning)
- [Sources](#sources)

## Supervised learning

### The idea

In supervised learning the model learns from examples whose correct answers are already known. We show it many pairs *(input, correct answer)*, measure how far its output is from the answer, and adjust it to be less wrong. The labeled dataset plays the role of a teacher.

### Vocabulary

| Term | Meaning | Drone example |
| --- | --- | --- |
| Input $`x`$ | What the model sees | A grayscale camera image |
| Label $`y`$ | The correct answer for that input | For every pixel: 1 if it is a gate corner, 0 if not |
| Model $`f_\theta`$ | A function with adjustable numbers | A neural network |
| Weights $`\theta`$ | The adjustable numbers inside the model (millions of them) | The network's filters |
| Prediction | What the model outputs for $`x`$ | For every pixel, a probability $`p`$ of being a corner |
| Loss $`\ell`$ | One number saying how wrong a prediction is (0 = perfect) | See [What training means](#what-training-means) |
| Training set | Examples the weights are adjusted on | Most of the labeled images |
| Validation set | Labeled examples kept aside to check the model on unseen data | A few thousand held-out images |
| Overfitting | The model memorizes the training set and fails on new data | Perfect on training images, misses corners on new ones |
| Learning rate $`\eta`$ | How big a step each update takes | A small number, such as 0.001 |

### What training means

With a dataset of $`N`$ pairs $`(x_i, y_i)`$, training looks for weights that make the average loss small. This is called empirical risk minimization:

```math
\min_\theta \; \frac{1}{N}\sum_{i=1}^{N} \ell\big(f_\theta(x_i),\, y_i\big)
```

Read it as: "among all possible weights, pick the ones with the smallest average mistake on our examples". The task decides the loss: a number (such as a speed) uses squared error, a category uses cross-entropy, and a label for every pixel (segmentation) is a classification per pixel.

### Binary cross-entropy loss (BCE)

Our task: for every pixel $`i`$ the network outputs a probability $`p_i`$ that it is a corner. The label $`y_i`$ is 0 or 1. Why is the loss shaped the way it is? It follows from one question: *which weights make the labels we actually observed the most probable?*

**Step 1. One pixel.** The label is 0 or 1, so it follows a *Bernoulli distribution*: a coin that comes up 1 ("corner") with probability $`p_i`$ and 0 with probability $`1-p_i`$. So the probability the model gives to the true label is

```math
P(y_i \mid x,\theta) = p_i^{\,y_i}\,(1-p_i)^{\,1-y_i}
```

If $`y_i = 1`$ only $`p_i`$ is left. If $`y_i = 0`$ only $`1-p_i`$ is left.

**Step 2. All pixels.** If pixels are treated as independent, probabilities multiply:

```math
P(y \mid x,\theta) = \prod_{i=1}^{N} p_i^{\,y_i}(1-p_i)^{\,1-y_i}
```

**Step 3. Maximum likelihood.** Choose the weights that maximize this product.

**Step 4. Take the log.** The log is an increasing function, so the best weights do not change. It turns the product into a sum, and a product of thousands of numbers below 1 would round to zero on a computer:

```math
\theta^{*} = \arg\max_\theta \sum_i \big[\, y_i \ln p_i + (1-y_i)\ln(1-p_i) \,\big]
```

**Step 5. Flip the sign.** Training minimizes a loss, and maximizing a quantity is the same as minimizing its negative. Averaging over $`N`$ pixels gives the final formula:

```math
L_{BCE} = -\frac{1}{N}\sum_{i=1}^{N}\Big[\, y_i \ln p_i + (1-y_i)\ln(1-p_i) \,\Big]
```

**Reading it.** If $`y_i=1`$ the term is $`-\ln p_i`$: zero when $`p_i=1`$, and growing without limit as $`p_i \to 0`$. If $`y_i=0`$ the term is $`-\ln(1-p_i)`$, the mirror image. So a confident mistake is punished the most.

**Worked example.** A corner pixel ($`y=1`$): with $`p=0.69`$ the loss is $`-\ln 0.69 = 0.37`$; with $`p=0.10`$ it is $`-\ln 0.10 = 2.30`$. Four pixels with $`p=(0.9, 0.8, 0.2, 0.1)`$ and $`y=(1,1,0,0)`$ give per-pixel losses $`0.105,\,0.223,\,0.223,\,0.105`$, mean $`0.164`$.

**Why the gradient is clean.** The network's raw output $`z`$ (the *logit*) becomes a probability through the sigmoid $`p=\sigma(z)=\frac{1}{1+e^{-z}}`$, whose derivative is $`p(1-p)`$. For one pixel:

```math
\frac{\partial \ell}{\partial p} = -\frac{y}{p} + \frac{1-y}{1-p}, \qquad \frac{\partial \ell}{\partial z} = \frac{\partial \ell}{\partial p}\cdot p(1-p) = -y(1-p) + (1-y)p = p-y
```

The error signal is simply "prediction minus label". Squared error would add an extra factor $`p(1-p)`$ that is close to zero when the network is confidently wrong, so it would learn slowly exactly when it should learn fast. This is the usual reason BCE is preferred for classification.

**Side note: the same recipe gives other losses.** If the answer is a number $`y`$ with Gaussian noise around the prediction $`\mu`$, then $`-\ln \mathcal N(y;\mu,\sigma^2) = \frac{(y-\mu)^2}{2\sigma^2} + \text{const}`$, so maximum likelihood becomes minimizing mean squared error.

### Dice loss

**The problem BCE does not solve.** Corner pixels are a tiny share of the image. Take an image with 10,000 pixels, of which 20 are corners. A model that says $`p=0.002`$ for every pixel, with no idea where the corners are, gets a BCE of only $`\approx 0.014`$. That looks excellent, because 9,980 pixels are background and it is "almost right" about all of them. BCE averages over every pixel, so the huge background drowns out the few pixels that matter.

**The idea.** Ignore the background and ask a different question: *how well do the pixels we call "corner" overlap the pixels that really are corners?* Overlap is counted relative to the sizes of both groups.

**The Dice coefficient for sets.** Let $`A`$ be the set of pixels the model calls corners and $`B`$ the set of true corner pixels. Then

```math
\mathrm{Dice}(A,B) = \frac{2\,|A\cap B|}{|A|+|B|}
```

where $`|A|`$ is the number of pixels in $`A`$ and $`A\cap B`$ is the pixels in both sets. The factor 2 makes a perfect match give exactly 1: if $`A=B`$ then $`2|A|/(|A|+|A|)=1`$. If the sets share nothing, the score is 0.

**A tiny example.** A row of 10 pixels where the true corners are pixels 3 and 4, so $`|B|=2`$.

| The model calls corners | $`\lvert A\rvert`$ | Overlap $`\lvert A\cap B\rvert`$ | Dice |
| --- | --- | --- | --- |
| pixels 3, 4 (perfect) | 2 | 2 | $`2\cdot2/(2+2)=1.0`$ |
| pixels 4, 5 (half right) | 2 | 1 | $`2\cdot1/(2+2)=0.5`$ |
| nothing at all | 0 | 0 | $`0`$ |
| all 10 pixels | 10 | 2 | $`2\cdot2/(10+2)=0.33`$ |

Saying nothing scores 0, and shouting "everything is a corner" is also punished, because $`|A|`$ grows. Background pixels that the model correctly leaves out appear in neither set, so they do not count at all. That is why the large background cannot hide a failure.

**From sets to a loss.** The network does not output yes/no. It outputs probabilities $`p_i\in(0,1)`$, and counting set members cannot be differentiated. So each count is replaced by a sum, which can be differentiated:

| Set quantity | Soft version | Meaning |
| --- | --- | --- |
| $`\lvert A\cap B\rvert`$ | $`\sum_i p_i y_i`$ | Each true corner ($`y_i=1`$) adds how confident the model was there |
| $`\lvert A\rvert`$ | $`\sum_i p_i`$ | Total "corner mass" the model predicted |
| $`\lvert B\rvert`$ | $`\sum_i y_i`$ | Number of true corner pixels |

Training minimizes a loss, and a good score should be a small loss, so we use 1 minus the score:

```math
L_{Dice} = 1 - \frac{2\sum_i p_i y_i}{\sum_i p_i + \sum_i y_i}
```

In practice a tiny constant is added to the top and bottom so that an image without any corners does not divide 0 by 0.

**Worked example 1.** $`p=(0.9,0.8,0.2,0.1)`$, $`y=(1,1,0,0)`$: $`\sum p_i y_i = 0.9+0.8 = 1.7`$, $`\sum p_i = 2.0`$, $`\sum y_i = 2`$. So $`\mathrm{Dice}=2\cdot1.7/(2.0+2)=0.85`$ and $`L_{Dice}=0.15`$.

**Worked example 2: the know-nothing model from above.** $`p=0.002`$ on all 10,000 pixels, 20 true corners. Then $`\sum p_i y_i = 0.002\cdot20=0.04`$ and $`\sum p_i=\sum y_i=20`$, so $`\mathrm{Dice}=2\cdot0.04/40=0.002`$ and $`L_{Dice}=0.998`$, almost the worst possible.

| Model | BCE | Dice loss |
| --- | --- | --- |
| Know-nothing ($`p=0.002`$ everywhere) | 0.014 (looks great) | 0.998 (terrible) |
| Perfect (1 on corners, 0 elsewhere) | 0 | 0 |

Dice sees the failure that BCE hides.

**Why use both.** Dice handles the imbalance but its gradients are less smooth, especially early in training when predictions are poor. BCE gives smooth, clean gradients. So the two are often added together. This is the standard choice for such tasks. I could not confirm which loss Swift itself uses.

### Gradient descent

We now have a loss $`L(\theta)`$. How do we change the weights to make it smaller?

**Step 1. Look locally.** Near the current weights $`\theta`$, a small change $`\Delta`$ changes the loss by about

```math
L(\theta+\Delta) \approx L(\theta) + \mathrm{grad}_\theta L(\theta)^{\top}\Delta
```

where the gradient, written $`\mathrm{grad}_\theta L`$ (in books often $`\nabla_\theta L`$), is the list of slopes of the loss with respect to every weight. This is the first-order Taylor expansion.

**Step 2. Pick the best direction.** For a fixed step length $`\|\Delta\|`$, the Cauchy–Schwarz inequality gives $`\mathrm{grad}\,L^{\top}\Delta \ge -\|\mathrm{grad}\,L\|\,\|\Delta\|`$, with equality when $`\Delta`$ points exactly opposite to the gradient. So the steepest local decrease is

```math
\Delta = -\eta\,\mathrm{grad}_\theta L \quad\Longrightarrow\quad \theta \leftarrow \theta - \eta\,\mathrm{grad}_\theta L
```

**Worked example.** Take one weight $`w`$ and the loss $`L(w)=(w-3)^2`$, whose slope is $`2(w-3)`$. Start at $`w=0`$ with $`\eta=0.1`$. Slope $`=-6`$, so $`w \leftarrow 0 + 0.6 = 0.6`$. Next slope $`=-4.8`$, so $`w=1.08`$. It moves toward the minimum at $`w=3`$ in shrinking steps. With $`\eta=1.1`$ the steps overshoot more each time ($`0 \to 6.6 \to -1.32 \to \dots`$) and the loss grows. So $`\eta`$ must be small, because the Taylor approximation only holds close to $`\theta`$.

**Mini-batches.** Computing the gradient over all $`N`$ images every step is slow. Using a random handful (a *mini-batch*) gives a noisy but cheap estimate of the same direction. This is stochastic gradient descent (SGD).

**Honest caveat.** For a neural network the loss is not a simple bowl, and nothing guarantees that gradient descent finds the global minimum. That it works so well in practice is partly empirical.

### Backpropagation and Adam

**Backpropagation** computes the gradient for every weight in one backward pass using the chain rule. For one layer $`a=f(Wx+b)`$ inside a network with loss $`L`$:

```math
\frac{\partial L}{\partial W} = \Big(\frac{\partial L}{\partial a}\odot f'(Wx+b)\Big)\,x^{\top}
```

Each layer reuses the gradient of the layer above it, so the cost of all gradients is about that of one forward pass.

**Adam** is a popular improvement on plain gradient descent. It keeps running averages of the gradient $`g_t`$ and of its square, corrects their startup bias, and scales the step for each weight:

```math
m_t=\beta_1 m_{t-1}+(1-\beta_1)g_t,\quad v_t=\beta_2 v_{t-1}+(1-\beta_2)g_t^2,\quad \hat m_t=\frac{m_t}{1-\beta_1^t},\quad \hat v_t=\frac{v_t}{1-\beta_2^t},\quad \theta_t=\theta_{t-1}-\eta\,\frac{\hat m_t}{\sqrt{\hat v_t}+\epsilon}
```

**Worked first step.** With $`g=0.5`$, $`\beta_1=0.9`$, $`\beta_2=0.999`$: $`m=0.05`$, $`v=0.00025`$, $`\hat m=0.5`$, $`\hat v=0.25`$, so the step is $`\eta\cdot 0.5/0.5=\eta`$ (ignoring the tiny $`\epsilon`$). The first step has size $`\eta`$ whatever the gradient's scale, which makes Adam forgiving about loss scaling.

### Generalization

A low training loss is not enough. The model must work on images it has never seen, such as a different room or different light. Three habits protect against overfitting:
- **Validation set:** check the loss on held-out data; if training loss keeps falling while validation loss rises, the model is memorizing.
- **Augmentation:** randomly alter training images (rotate, change brightness, hue, contrast) so that the model sees a wider world.
- **More and more varied data.**

A model only knows the world its data showed it. That is exactly the gap block 4 deals with, when simulated images meet a real camera.

### Where labels come from

Labels are the bottleneck of supervised learning. They come from hand annotation (slow and costly), semi-automatic annotation (label some frames and track the labels through the video), or a simulator that produces labels for free (block 2). The model can only be as good and as broad as its labels.

### Supervised learning in Swift

Swift's gate detector is a U-Net, a network that outputs a prediction for every pixel. Here it marks the gate corners in a grayscale camera image ([Nature, 2023](https://www.nature.com/articles/s41586-023-06419-4)). The detected corners are then combined with the known track map to compute the drone's position and are fused with other sensors. Supervised learning fits because the question "where are the gate corners?" has a checkable answer.

How such a detector is trained is described in the same lab's earlier [AlphaPilot paper](https://arxiv.org/abs/2005.12813): about 28,000 real images with gate-corner labels (hand and tracked), plus rotation and brightness augmentation. That was an earlier, smaller network, so these numbers illustrate the method, not Swift's exact data.

### Limits

Supervised learning needs a correct answer for every example. "Which motor command is best right now?" has no ground-truth label, so Swift's controller is trained with reinforcement learning instead. A supervised model also fails when the inputs differ from its training data (distribution shift) and when labels are expensive.

## Fine-tuning

### The idea

Training a network from scratch needs a lot of data. Often a network that was already trained on a related task exists. Fine-tuning takes it as the starting point and keeps training it on the new task, usually with a small learning rate, instead of starting from random weights. The general name for reusing knowledge from one task in another is *transfer learning*; fine-tuning is its most common form.

It pays off when data for the new task is scarce, expensive or risky to collect, which describes real robot flight.

### Vocabulary

| Term | Meaning | Drone example |
| --- | --- | --- |
| Pretrained model | A model already trained on some task | A policy trained in a simulator |
| Source task / target task | What it was trained on / what we want to use it for | Simulated flight / real flight |
| Backbone | The early layers that extract general features | The first layers of a vision network |
| Head | The last layers that produce the task-specific output | The last layers of the detector, which output the corner map |
| Frozen weights | Weights that are not updated during training | A detector kept fixed while the policy trains |
| Domain shift (domain gap, distribution shift) | The target data differs systematically from the source data | Simulated images versus real camera images |
| Catastrophic forgetting | Training on the new task overwrites what the model knew about the old one | A detector that gets worse on the old track after training on a new one |
| Replay | Mixing some old data into the new training | Old images kept in every batch |
| Fisher information $`F_i`$ | A number saying how much the loss reacts when weight $`i`$ changes, so how important that weight is | See [Catastrophic forgetting](#catastrophic-forgetting) |

### Why it works

Networks trained on images learn in layers: early layers pick up general features such as edge-like filters and colour patches, and later layers become specific to the task. General features are useful for many tasks, so they are worth keeping, and only the specific part needs to be adapted.

[Yosinski et al. (2014)](https://arxiv.org/abs/1411.1792) tested this on ImageNet. They split its classes into two halves, A and B, trained a network on each, and copied the first $`n`$ layers of the A-network into a new B-network, either frozen or fine-tuned. Their findings:
- The first layers transfer almost perfectly (they learn edge and colour filters), while higher layers transfer worse, especially between dissimilar tasks.
- Copying and then fine-tuning ended 1.6 % *better* than training on B directly, even after long fine-tuning.

### The math: a small step from a good start

Fine-tuning is ordinary training (see [Gradient descent](#gradient-descent)) with two changes: the starting weights are the pretrained ones, $`\theta_0=\theta_{\text{pre}}`$, and the loss $`L_{\text{target}}`$ is computed on the target data. The update rule is the same:

```math
\theta_{t+1}=\theta_t-\eta\,\mathrm{grad}_\theta L_{\text{target}}(\theta_t)
```

**Why a small learning rate matters, derived.** The following holds for plain gradient descent.

**Step 1. Total movement.** Adding up the first $`T`$ updates, everything in between cancels:

```math
\theta_T-\theta_0=-\eta\sum_{t=0}^{T-1}\mathrm{grad}_\theta L_{\text{target}}(\theta_t)
```

**Step 2. Bound its size.** The length of a sum is at most the sum of the lengths (the triangle inequality), so

```math
\lVert\theta_T-\theta_0\rVert\le\eta\sum_{t=0}^{T-1}\lVert\mathrm{grad}_\theta L_{\text{target}}(\theta_t)\rVert
```

The weights cannot move farther from the pretrained solution than $`\eta`$ times the total size of the gradients.

**Worked example.** Suppose each gradient has length about 2 and we train for $`T=100`$ steps. With $`\eta=0.001`$ the weights move by at most $`0.001\cdot100\cdot2=0.2`$. With $`\eta=0.1`$ the bound is $`20`$, a hundred times larger. This is only an upper bound, but it shows how the learning rate sets the size of the region where the weights can end up.

A small $`\eta`$ therefore keeps the weights near $`\theta_0`$, where the pretrained features still work. A large $`\eta`$ lets the first noisy gradients throw them far away, and the benefit of pretraining is lost.

**Freezing.** Split the weights into frozen ones $`\theta_{\text{fix}}`$ and trainable ones $`\theta_{\text{tr}}`$, and update only the second group:

```math
\theta_{\text{tr}}\leftarrow\theta_{\text{tr}}-\eta\,\mathrm{grad}_{\theta_{\text{tr}}}L_{\text{target}},\qquad\theta_{\text{fix}}\ \text{unchanged}
```

Example: a backbone with $`1{,}000{,}000`$ weights and a head with $`1{,}000`$. Training only the head updates $`0.1\,\%`$ of the weights. These sizes are an illustration. Fewer trainable weights also means less to overfit on a small dataset.

### Strategies

From the least to the most change:

| Strategy | What is trained | Fits when | Main risk |
| --- | --- | --- | --- |
| Feature extraction | A new head only; the backbone is frozen | Very little target data, similar domain | Limited adaptation |
| Partial fine-tuning | Later layers; early layers are frozen | Small data, moderate domain gap | Choosing which layers |
| Full fine-tuning | All weights, with a small learning rate | Enough data, or a large domain gap | Overfitting, forgetting |
| Parameter-efficient (adapters, LoRA) | A small set of added weights; the original ones are frozen | Very large models | Extra structure to manage |

**LoRA** (low-rank adaptation, [Hu et al., 2021](https://arxiv.org/abs/2106.09685)) freezes a pretrained weight matrix $`W_0`$ of size $`d\times k`$ and learns a small correction $`\Delta W`$ as the product of two thin matrices. For an input $`x`$ the layer computes

```math
h=W_0x+\Delta Wx=W_0x+BAx,\qquad B\in\mathbb{R}^{d\times r},\; A\in\mathbb{R}^{r\times k},\; r\ll\min(d,k)
```

Only $`A`$ and $`B`$ are trained. The paper starts $`A`$ from random values and $`B`$ from zero, so $`\Delta W=BA=0`$ at the beginning and training starts exactly at the pretrained model. It also scales the correction by $`\alpha/r`$, where $`\alpha`$ is a constant.

**Why this saves parameters, derived.** A full update of $`W_0`$ has $`d\cdot k`$ numbers. The pair $`B,A`$ has

```math
d\cdot r+r\cdot k=r\,(d+k)
```

**Worked example.** For $`d=k=1000`$ and $`r=8`$ a full update has $`1{,}000{,}000`$ numbers and LoRA has $`8\cdot2000=16{,}000`$, which is $`1.6\,\%`$. These sizes are an illustration. The paper reports $`10{,}000`$ times fewer trainable parameters for GPT-3 175B (rank $`r=4`$, applied to the attention matrices only), which shrinks the saved checkpoint from 350 GB to 35 MB.

### Catastrophic forgetting

Plain fine-tuning optimizes only the new loss, so nothing prevents it from overwriting weights that the old task needed. Common mitigations are a low learning rate, freezing layers, replay of old data, and regularizers.

**Elastic weight consolidation (EWC)** ([Kirkpatrick et al.](https://arxiv.org/abs/1612.00796)) adds a penalty for moving weights that were important for the old task A while learning the new task B:

```math
L(\theta)=L_B(\theta)+\sum_i\frac{\lambda}{2}\,F_i\,\big(\theta_i-\theta^{*}_{A,i}\big)^2
```

This is Eq. 3 of the paper. Here $`L_B`$ is the loss on task B alone, $`\theta^{*}_{A,i}`$ is weight $`i`$ after learning task A, $`F_i`$ is the diagonal of the Fisher information matrix for that weight, and $`\lambda`$ sets how important the old task is compared to the new one. The paper notes that $`F`$ is equivalent to the second derivative of the loss near a minimum and can be computed from first derivatives alone. So $`F_i`$ is large when changing weight $`i`$ quickly raises the old loss.

The penalty acts like a spring pulling each weight back to its old value, stiff for important weights and loose for unimportant ones. A uniform penalty with the same coefficient for every weight would be too severe: it protects all weights equally and leaves little room to learn task B.

**Worked example.** Two weights with importance $`F=(10,\;0.1)`$ and $`\lambda=1`$.
- Moving only the important weight by $`0.5`$ costs $`\tfrac12\cdot10\cdot0.25=1.25`$.
- Moving only the unimportant weight by $`0.5`$ costs $`\tfrac12\cdot0.1\cdot0.25=0.0125`$, which is 100 times cheaper.
- Moving both by $`0.5`$ costs $`1.25+0.0125\approx1.26`$.

So training is pushed to change the unimportant weight and leave the important one alone.

### Domain shift

A second danger is fitting the target data too narrowly. A model fine-tuned on a few images from one room may work there and nowhere else. The cure is the same as for any supervised model: held-out validation data and varied data (see [Generalization](#generalization)).

### Fine-tuning in robotics

Three typical forms (my grouping, not a standard classification):
1. Fine-tune a perception network on a small set of real labeled images after training it on simulated ones (supervised).
2. Fine-tune a policy directly on the real robot with reinforcement learning. Data is scarce and crashes damage hardware, so this is rare.
3. Fine-tune a policy in a simulator that has been corrected with real data. This is the form Swift uses.

### Fine-tuning in Swift

Swift's control policy is first trained with $`1\times10^{8}`$ simulated interactions in a plain simulator, which takes about 50 minutes. It is then trained for $`2\times10^{7}`$ more interactions in a simulator that was corrected with real flight data: three rollouts, about 50 seconds of flight ([Nature, 2023](https://www.nature.com/articles/s41586-023-06419-4)). Only the control policy is updated, and the weights of the gate-detection network are kept constant. So this is *reinforcement-learning* fine-tuning, not "retrain a classifier on a few labeled images".

The real data does not train the policy directly. It is used to fit two small residual models that are added to the simulator: one for the drift of the drone's state estimate from visual-inertial odometry (Gaussian processes), and one for the acceleration that the simulator's physics misses (k-nearest-neighbour regression with $`k=5`$). The policy then keeps learning inside this corrected simulator. How those models are fitted belongs to block 4. This block covers how the policy learns, which is the reinforcement learning section below.

Swift is not a zero-shot system. According to my reading of the paper, the baselines trained only in the idealized simulator and deployed without adaptation failed to complete even a single lap, which is why the fine-tuning step matters.

Swift does not use LoRA or forgetting regularizers as far as I found. I did not find its fine-tuning learning rate or whether the optimizer state was reset, so I don't state them.

### Limits

Fine-tuning needs a good pretrained model whose task is related enough to the new one. It can forget the old task, it can overfit a small target set, and it does not fix a large mismatch between source and target by itself.

## Reinforcement learning

### The idea

Think of learning to ride a bike or training a dog. Nobody gives you the correct move for every moment. You try something, see how it turns out, and slowly do more of what worked. Reinforcement learning (RL) is this idea for machines: an *agent* acts in an *environment* and gets a number called the *reward* that says how well it is going. The reward can arrive much later than the action that earned it.

The loop is always the same: look at the situation, choose an action, the environment moves on and gives a reward, repeat. For a racing drone, the reward is high for getting closer to the next gate and very low for a crash.

The difference from supervised learning: there, every example comes with the correct answer. Here, the agent only gets a score and has to work out for itself which of its actions caused it.

### Vocabulary

| Term | Meaning | Drone example |
| --- | --- | --- |
| State $`s`$ | The current situation | Position, speed, orientation, where the gate is |
| Action $`a`$ | What the agent chooses | Thrust and rotation rates |
| Reward $`r`$ | A score after each step | Positive for approaching the gate, negative for a crash |
| Policy $`\pi_\theta(a\mid s)`$ | A function: give it a state $`s`$ and an action $`a`$, and it returns the probability of choosing $`a`$ in $`s`$. It has adjustable weights $`\theta`$ | A small neural network |
| Return $`G_t`$ | All the rewards from step $`t`$ onward, added up | See [Return](#return) |
| Discount $`\gamma`$ | How much less a later reward counts, $`0<\gamma\le1`$ | 0.99: a reward 100 steps away counts about 37 % |
| Value $`V(s)`$ | The return the agent expects from state $`s`$ | "From here it usually goes well" |
| Advantage $`A`$ | How much better an action was than expected | A sharp turn that paid off |
| On-policy | Learning only from data made by the current policy | Fresh simulated flights |

### Return

The agent should not chase the next reward but the whole future. So we add up all the future rewards, counting each step further away a little less, by a factor $`\gamma`$ each time:

```math
G_t=r_t+\gamma\,r_{t+1}+\gamma^{2}\,r_{t+2}+\dots
```

Why discount? A reward now is more certain than one far away, and it keeps the sum finite when episodes are long.

**A useful rewriting.** Take $`\gamma`$ out of every term after the first:

```math
G_t=r_t+\gamma\big(r_{t+1}+\gamma\,r_{t+2}+\dots\big)=r_t+\gamma\,G_{t+1}
```

So the return is "the reward now plus the discounted return from the next step". This is how it is computed in practice, from the end backwards. **Worked example.** Three rewards of 1 with $`\gamma=0.9`$: $`G_2=1`$, $`G_1=1+0.9\cdot1=1.9`$, $`G_0=1+0.9\cdot1.9=2.71`$.

The goal is to find policy weights $`\theta`$ for which the *average* return $`J(\theta)`$ is as large as possible.

### Policy gradient

**The intuition.** Try an action. If things went well, make that action more likely next time. If they went badly, make it less likely. The formula below says exactly this, and the derivation shows why it works.

**Step 1. The simplest case.** Take one decision with no future. The policy picks action $`a`$ with probability $`\pi_\theta(a)`$ and the reward is $`R(a)`$. The average reward is

```math
J(\theta)=\sum_a\pi_\theta(a)\,R(a)
```

and its slope with respect to the weights is $`\mathrm{grad}_\theta J=\sum_a\mathrm{grad}_\theta\pi_\theta(a)\,R(a)`$. We cannot compute this sum for a real drone, because there are far too many possible actions and outcomes. We want something we can estimate by *trying* actions.

**Step 2. A rewriting trick.** For a fixed action $`a`$, the probability $`\pi_\theta(a)`$ is a function of the weights $`\theta`$. The chain rule says that the slope of the logarithm of a function is the slope of the function divided by the function itself:

```math
\mathrm{grad}_\theta\ln\pi_\theta(a)=\frac{\mathrm{grad}_\theta\,\pi_\theta(a)}{\pi_\theta(a)}
```

Multiply both sides by $`\pi_\theta(a)`$:

```math
\mathrm{grad}_\theta\,\pi_\theta(a)=\pi_\theta(a)\;\mathrm{grad}_\theta\ln\pi_\theta(a)
```

*Check.* For the policy $`\pi_\theta(1)=\sigma(\theta)`$ at $`\theta=0`$: the left side is $`\sigma'(0)=0.25`$, and the right side is $`0.5\cdot(1-0.5)=0.25`$.

Now put this into the sum from Step 1:

```math
\mathrm{grad}_\theta J=\sum_a\mathrm{grad}_\theta\,\pi_\theta(a)\,R(a)=\sum_a\pi_\theta(a)\;\mathrm{grad}_\theta\ln\pi_\theta(a)\;R(a)
```

Each term is now multiplied by $`\pi_\theta(a)`$, the probability of picking $`a`$. A sum weighted by probabilities is an average, so

```math
\mathrm{grad}_\theta J=\mathbb{E}_{a\sim\pi}\Big[\mathrm{grad}_\theta\ln\pi_\theta(a)\;R(a)\Big]
```

An average can be estimated by sampling: try an action, see its reward, and move the weights along $`\mathrm{grad}_\theta\ln\pi_\theta(a)\cdot R(a)`$. The direction $`\mathrm{grad}_\theta\ln\pi_\theta(a)`$ is the one that makes that action more likely, and $`R(a)`$ says how strongly to push: a high reward pushes toward the action, a negative one pushes away.

**Worked example.** A policy picks action 1 with probability $`p=\sigma(\theta)`$ (the sigmoid of its single weight) and action 0 otherwise. The reward is 1 for action 1 and 0 for action 0, so $`J=p`$ and the true slope is $`p(1-p)`$, which is $`0.25`$ at $`\theta=0`$ ($`p=0.5`$). The slopes of the log-probabilities are $`\mathrm{grad}\ln\pi(1)=1-p=0.5`$ and $`\mathrm{grad}\ln\pi(0)=-p=-0.5`$. The formula gives $`0.5\cdot0.5\cdot1+0.5\cdot(-0.5)\cdot0=0.25`$, the same number.

**Step 3. Many steps.** A flight is a long sequence of decisions, and the environment's physics sits in between. The same argument works for the whole sequence. The probability of a whole trajectory is a *product* of policy terms $`\pi_\theta(a_t\mid s_t)`$ and environment terms. The log turns the product into a sum, and the environment terms do not depend on $`\theta`$, so their slope is zero. Only the policy terms remain:

```math
\mathrm{grad}_\theta J=\mathbb{E}\Big[\sum_t\mathrm{grad}_\theta\ln\pi_\theta(a_t\mid s_t)\;G_t\Big]
```

Here $`G_t`$ replaces the single reward, because an action only affects rewards *after* it. We never need a model of the physics, only samples. This is why the method is called *model-free*.

**Step 4. Compare with what was expected.** If every flight scores around 100, all actions look "good" and the push is noisy. Better to subtract a typical value $`b(s)`$ first. This does not change the average direction, because the average of the log-slope is zero:

```math
\mathbb{E}_{a\sim\pi}\big[\mathrm{grad}\ln\pi(a\mid s)\big]=\sum_a\mathrm{grad}\,\pi(a\mid s)=\mathrm{grad}\sum_a\pi(a\mid s)=\mathrm{grad}\,1=0
```

so subtracting $`b(s)`$ only removes noise. The natural choice is the value $`V(s)`$. Then $`G_t-V(s_t)`$ is the advantage $`\hat A_t`$, meaning "how much better than expected":

```math
\mathrm{grad}_\theta J=\mathbb{E}\Big[\sum_t\mathrm{grad}_\theta\ln\pi_\theta(a_t\mid s_t)\;\hat A_t\Big]
```

### Advantage

To subtract the value we need to know it. A second network, the *critic*, learns $`V(s)`$ by ordinary supervised regression onto the returns, so part of RL is supervised learning. The critic gives a quick estimate of how good the last action was:

```math
\delta_t=r_t+\gamma V(s_{t+1})-V(s_t)
```

Read it as: "what I got now plus what I expect next, minus what I expected before". If it is positive, things went better than expected.

One step is noisy in a different way, because the critic can be wrong. *Generalized advantage estimation* (GAE, [Schulman et al.](https://arxiv.org/abs/1506.02438)) mixes the one-step estimate with longer-looking ones:

```math
\hat A_t=\sum_{l\ge0}(\gamma\lambda)^{l}\,\delta_{t+l}=\delta_t+\gamma\lambda\,\hat A_{t+1}
```

With $`\lambda=0`$ it is just $`\delta_t`$: smooth but dependent on the critic being right. With $`\lambda=1`$ it is the full return minus $`V(s_t)`$: honest but noisy. A value in between, such as 0.95, balances the two.

**Worked example.** Rewards $`r=(1,2)`$, values $`V=(0.5,0.6)`$, value $`0`$ after the end, $`\gamma=0.99`$, $`\lambda=0.95`$. Backwards: $`\delta_1=2-0.6=1.4`$, so $`\hat A_1=1.4`$. $`\delta_0=1+0.99\cdot0.6-0.5=1.094`$, so $`\hat A_0=1.094+0.9405\cdot1.4=2.411`$.

### PPO

Proximal policy optimization (PPO, [Schulman et al., 2017](https://arxiv.org/abs/1707.06347)) is the algorithm Swift uses.

**The problem.** The data was collected by the *old* policy, and the update formulas are only trustworthy close to it. If one update changes the policy a lot, the data no longer describes it and training can collapse. We want to improve the policy, but only in small steps.

**Step 1. Measure the change.** Compare how likely the new policy finds an action to how likely the old one did:

```math
r_t(\theta)=\frac{\pi_\theta(a_t\mid s_t)}{\pi_{\theta_{\text{old}}}(a_t\mid s_t)}
```

If $`r_t=1`$ nothing changed, and $`r_t=1.2`$ means the action became 20 % more likely. The objective we would like to increase is $`r_t\,\hat A_t`$: make good actions more likely ($`\hat A>0`$) and bad ones less likely ($`\hat A<0`$).

**Step 2. Stop rewarding big changes.** Once a good action is already, say, 20 % more likely, there is no need to push further. PPO simply cuts the benefit off outside a range $`[1-\epsilon,\,1+\epsilon]`$, with a small $`\epsilon`$ such as 0.2, and takes the smaller of the two values so it never overestimates:

```math
L^{CLIP}(\theta)=\mathbb{E}_t\Big[\min\big(r_t\hat A_t,\ \mathrm{clip}(r_t,\,1-\epsilon,\,1+\epsilon)\,\hat A_t\big)\Big]
```

**Worked example** with $`\epsilon=0.2`$, so the range is $`[0.8,\,1.2]`$ and $`\mathrm{clip}(1.4)=1.2`$:
- **Good action** ($`\hat A=+2`$) that became 40 % more likely ($`r=1.4`$): $`\min(1.4\cdot2,\;1.2\cdot2)=\min(2.8,\,2.4)=2.4`$. The clipped value is used and has no slope, so there is no more push upward.
- **Bad action** ($`\hat A=-2`$) that also became 40 % more likely ($`r=1.4`$): $`\min(1.4\cdot(-2),\;1.2\cdot(-2))=\min(-2.8,\,-2.4)=-2.8`$. The unclipped value is used and still has a slope, so the action is pushed back down.

So the clip ignores a change only when it would make the objective look better, and a harmful change is still counted in full.

**Step 3. Train both networks.** The paper combines the policy objective with a loss for the critic and a bonus $`S`$ for keeping the policy a bit random, so it keeps exploring (Eq. 9 of the paper):

```math
L=\mathbb{E}_t\Big[L^{CLIP}_t(\theta)-c_1\big(V_\theta(s_t)-V^{\text{target}}_t\big)^2+c_2\,S[\pi_\theta](s_t)\Big]
```

**The loop.** Several parallel simulators run the current policy for a number of steps. Then the objective is optimized for a few passes over that data, and the new policy becomes the "old" one. In the paper's tests on seven simulated robot-control tasks, $`\epsilon=0.2`$ worked best (average normalized score 0.82, versus 0.76 for 0.1 and 0.70 for 0.3). Those are not Swift's settings.

**Other algorithm families.** PPO belongs to the *policy-optimization* family: it improves the policy directly and learns from fresh data, which makes it stable but data-hungry. *Q-learning* methods (such as DQN) learn the value of each action and can reuse old data, but are less stable. *Model-based* methods learn a model of the environment and plan with it, which can save data but exploits the model's errors ([OpenAI Spinning Up overview](https://spinningup.openai.com/en/latest/spinningup/rl_intro2.html)).

### Reward design

The reward is the only teacher, so designing it is much of the work. A *sparse* reward, for example only the final lap time, is true to the goal but gives almost no feedback along the way. A *shaped* reward adds hints such as progress toward the next gate, so the agent gets feedback at every step. The risk: the agent optimizes what you *wrote*, not what you *meant*. [Ng, Harada and Russell (1999)](https://andrewng.org/publications/policy-invariance-under-reward-transformations-theory-and-application-to-reward-shaping) describe a simulated bicycle rewarded for making progress toward a goal but not punished for moving away. It learned to ride in small circles near the start, collecting the reward again and again.

**A safe kind of hint.** Their Theorem 1 says that adding to the reward a term of the form

```math
F(s,s')=\gamma\,\Phi(s')-\Phi(s)
```

for any function $`\Phi`$ of the state (a "potential", for example minus the distance to the goal) does not change which policy is best. The paper also shows that, without more knowledge of the environment, this form is the *only* safe one.

**Why it is safe, for $`\gamma=1`$.** Suppose the agent goes round a loop $`s_1\to s_2\to s_1`$. The hints add up to $`F(s_1,s_2)+F(s_2,s_1)=\big(\Phi(s_2)-\Phi(s_1)\big)+\big(\Phi(s_1)-\Phi(s_2)\big)=0`$. Whatever is gained going one way is lost coming back, so going in circles earns nothing. Over any path the hints add up to just $`\Phi(\text{end})-\Phi(\text{start})`$, which does not depend on the route. The bicycle's "progress" reward was not of this form, which is why it could be exploited.

### Reinforcement learning in Swift

Swift's control policy is trained with PPO in simulation ([Nature, 2023](https://www.nature.com/articles/s41586-023-06419-4)).

**What it sees and does**
- **Observation, 31 numbers:** 15 for the drone's state estimate (position, velocity, orientation), 12 for the relative positions of the next gate's four corners, and 4 for its previous action.
- **Action, 4 numbers:** the total thrust and three rotation rates.

**The networks.** PPO uses two networks: the policy (the *actor*) and the value network (the *critic*). Both are small: two layers of 128 nodes. Both are trained with Adam at a learning rate of $`3\times10^{-4}`$, and only the policy is deployed on the drone. During training the critic also receives exact position, orientation and velocity, which the policy never gets. That helps the critic judge situations better without the policy depending on information it will not have in flight.

**Scale.** 100 simulated drones fly in parallel, in episodes of 1,500 steps, each reset starting at a random gate. Training takes $`1\times10^{8}`$ interactions, about 50 minutes on a workstation. The [UZH press release](https://news.uzh.ch/en/articles/media/2023/Drone-race.html) describes this as a month of simulated flight in under an hour on a desktop PC.

**The reward** is a shaped reward with four parts:

```math
r_t=r_t^{\text{prog}}+r_t^{\text{perc}}+r_t^{\text{cmd}}-r_t^{\text{crash}}
```

- **Progress:** $`\lambda_1\,(d_{t-1}-d_t)`$, a reward for getting closer to the next gate ($`d`$ is the distance to its centre).
- **Perception:** a reward for pointing the camera at the next gate. The paper's reason: seeing the gate makes the pose estimate more accurate.
- **Command:** a penalty for large rotation-rate commands and for abrupt changes between consecutive actions.
- **Crash:** a penalty of 5.0, and the flight ends, if the drone hits a gate or the ground.

I could not read the weights $`\lambda_1,\dots,\lambda_5`$ or PPO's $`\epsilon`$, $`\gamma`$, $`\lambda`$ and batch settings. They are in a table of the paper that appears as an image, so they are not given here.

**An observation of mine, not from the paper.** The progress term $`d_{t-1}-d_t`$ has the safe form above, with the potential $`\Phi=-d`$ and $`\gamma=1`$, so on its own it would not change the best policy. Two things weaken this: the potential jumps when the target switches to the next gate, and the other three terms are not of that form, so they do change what is best. Treat it as a discussion point.

**Link to block 4.** Swift does not randomize the drone's dynamics during training, as most earlier sim-to-real work does. It fine-tunes on real data instead (see [Fine-tuning in Swift](#fine-tuning-in-swift)).

### Limits

RL needs a fast simulator and a very large number of trials. A badly designed reward gets exploited, the agent exploits simulator errors as readily as real physics, and training can be unstable. When a good expert exists, imitation learning needs no reward at all (next section).

## Imitation learning

## Sources

### Swift (overview)
- [Champion-level drone racing, Nature 2023](https://www.nature.com/articles/s41586-023-06419-4)
- [UZH press release](https://news.uzh.ch/en/articles/media/2023/Drone-race.html)
- [Video: Swift, UZH](https://youtu.be/fBiataDpGIo)

### Supervised learning
- [AlphaPilot: Autonomous Drone Racing](https://arxiv.org/abs/2005.12813)
- [Video: 3Blue1Brown, gradient descent](https://www.3blue1brown.com/lessons/gradient-descent)

### Fine-tuning
- [How transferable are features in deep neural networks? (Yosinski et al., 2014)](https://arxiv.org/abs/1411.1792)
- [LoRA: Low-Rank Adaptation of Large Language Models](https://arxiv.org/abs/2106.09685)
- [Overcoming catastrophic forgetting in neural networks](https://arxiv.org/abs/1612.00796)

### Reinforcement learning
- [Proximal Policy Optimization](https://arxiv.org/abs/1707.06347)
- [OpenAI Spinning Up: kinds of RL algorithms](https://spinningup.openai.com/en/latest/spinningup/rl_intro2.html)

### Imitation learning
- [DAgger](https://arxiv.org/abs/1011.0686)
- [Deep Drone Acrobatics](https://arxiv.org/abs/2006.05768)
- [Video: Deep Drone Acrobatics](https://youtu.be/2N_wKXQ6MXA)
