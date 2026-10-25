# Block 3: Training methods

## Supervised learning

### The idea

In supervised learning the model learns from examples whose correct answers are already known. We show it many pairs *(input, correct answer)*, measure how far its output is from the answer, and adjust it to be less wrong. The labeled dataset plays the role of a teacher.

### Vocabulary

| Term | Meaning | Drone example |
| --- | --- | --- |
| Input $x$ | What the model sees | A grayscale camera image |
| Label $y$ | The correct answer for that input | For every pixel: 1 if it is a gate corner, 0 if not |
| Model $f_\theta$ | A function with adjustable numbers | A neural network |
| Weights $\theta$ | The adjustable numbers inside the model (millions of them) | The network's filters |
| Prediction | What the model outputs for $x$ | For every pixel, a probability $p$ of being a corner |
| Loss $\ell$ | One number saying how wrong a prediction is (0 = perfect) | See [What training means](#what-training-means) |
| Training set | Examples the weights are adjusted on | Most of the labeled images |
| Validation set | Labeled examples kept aside to check the model on unseen data | A few thousand held-out images |
| Overfitting | The model memorizes the training set and fails on new data | Perfect on training images, misses corners on new ones |
| Learning rate $\eta$ | How big a step each update takes | A small number, such as 0.001 |

### What training means

With a dataset of $N$ pairs $(x_i, y_i)$, training looks for weights that make the average loss small. This is called empirical risk minimization:

$$\min_\theta \; \frac{1}{N}\sum_{i=1}^{N} \ell\big(f_\theta(x_i),\, y_i\big)$$

Read it as: "among all possible weights, pick the ones with the smallest average mistake on our examples". The task decides the loss: a number (such as a speed) uses squared error, a category uses cross-entropy, and a label for every pixel (segmentation) is a classification per pixel.

### Binary cross-entropy loss (BCE)

Our task: for every pixel $i$ the network outputs a probability $p_i$ that it is a corner. The label $y_i$ is 0 or 1. Why is the loss shaped the way it is? It follows from one question: *which weights make the labels we actually observed the most probable?*

**Step 1. One pixel.** The label is 0 or 1, so it follows a *Bernoulli distribution*: a coin that comes up 1 ("corner") with probability $p_i$ and 0 with probability $1-p_i$. So the probability the model gives to the true label is

$$P(y_i \mid x,\theta) = p_i^{\,y_i}\,(1-p_i)^{\,1-y_i}$$

If $y_i = 1$ only $p_i$ is left. If $y_i = 0$ only $1-p_i$ is left.

**Step 2. All pixels.** If pixels are treated as independent, probabilities multiply:

$$P(y \mid x,\theta) = \prod_{i=1}^{N} p_i^{\,y_i}(1-p_i)^{\,1-y_i}$$

**Step 3. Maximum likelihood.** Choose the weights that maximize this product.

**Step 4. Take the log.** The log is an increasing function, so the best weights do not change. It turns the product into a sum, and a product of thousands of numbers below 1 would round to zero on a computer:

$$\theta^{*} = \arg\max_\theta \sum_i \big[\, y_i \ln p_i + (1-y_i)\ln(1-p_i) \,\big]$$

**Step 5. Flip the sign.** Training minimizes a loss, and maximizing a quantity is the same as minimizing its negative. Averaging over $N$ pixels gives the final formula:

$$L_{BCE} = -\frac{1}{N}\sum_{i=1}^{N}\Big[\, y_i \ln p_i + (1-y_i)\ln(1-p_i) \,\Big]$$

**Reading it.** If $y_i=1$ the term is $-\ln p_i$: zero when $p_i=1$, and growing without limit as $p_i \to 0$. If $y_i=0$ the term is $-\ln(1-p_i)$, the mirror image. So a confident mistake is punished the most.

**Worked example.** A corner pixel ($y=1$): with $p=0.69$ the loss is $-\ln 0.69 = 0.37$; with $p=0.10$ it is $-\ln 0.10 = 2.30$. Four pixels with $p=(0.9, 0.8, 0.2, 0.1)$ and $y=(1,1,0,0)$ give per-pixel losses $0.105,\,0.223,\,0.223,\,0.105$, mean $0.164$.

**Why the gradient is clean.** The network's raw output $z$ (the *logit*) becomes a probability through the sigmoid $p=\sigma(z)=\frac{1}{1+e^{-z}}$, whose derivative is $p(1-p)$. For one pixel:

$$\frac{\partial \ell}{\partial p} = -\frac{y}{p} + \frac{1-y}{1-p}, \qquad \frac{\partial \ell}{\partial z} = \frac{\partial \ell}{\partial p}\cdot p(1-p) = -y(1-p) + (1-y)p = p-y$$

The error signal is simply "prediction minus label". Squared error would add an extra factor $p(1-p)$ that is close to zero when the network is confidently wrong, so it would learn slowly exactly when it should learn fast. This is the usual reason BCE is preferred for classification.

**Side note: the same recipe gives other losses.** If the answer is a number $y$ with Gaussian noise around the prediction $\mu$, then $-\ln \mathcal N(y;\mu,\sigma^2) = \frac{(y-\mu)^2}{2\sigma^2} + \text{const}$, so maximum likelihood becomes minimizing mean squared error.

## Fine-tuning

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
