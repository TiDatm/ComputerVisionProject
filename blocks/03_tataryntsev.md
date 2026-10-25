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
