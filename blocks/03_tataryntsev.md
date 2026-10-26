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

## Reinforcement learning

## Imitation learning

## Sources

### Swift (overview)
- [Champion-level drone racing, Nature 2023](https://www.nature.com/articles/s41586-023-06419-4)
- [UZH press release](https://news.uzh.ch/en/articles/media/2023/Drone-race.html)
- [Video: Swift, UZH](https://youtu.be/fBiataDpGIo)

### Supervised learning
- [AlphaPilot: Autonomous Drone Racing](https://arxiv.org/abs/2005.12813)
- [Video: 3Blue1Brown, gradient descent](https://www.3blue1brown.com/lessons/gradient-descent)

### Reinforcement learning
- [Proximal Policy Optimization](https://arxiv.org/abs/1707.06347)
- [OpenAI Spinning Up: kinds of RL algorithms](https://spinningup.openai.com/en/latest/spinningup/rl_intro2.html)

### Imitation learning
- [DAgger](https://arxiv.org/abs/1011.0686)
- [Deep Drone Acrobatics](https://arxiv.org/abs/2006.05768)
- [Video: Deep Drone Acrobatics](https://youtu.be/2N_wKXQ6MXA)
