# Block 3 appendix: derivations behind the slides

The four appendix slides of the final deck, with the same steps, intuition lines and examples. In the deck the formulas are LaTeX images; the LaTeX source of each one is below. The full explanations and more derivations (gradient descent, fine-tuning bound, policy gradient, PPO) are in `../03_tataryntsev.md`.

## 1. Cross-entropy loss

Intuition: it punishes surprise. A network that is sure about the wrong answer gets a huge loss, one that is right gets almost none.

| Step | What happens | LaTeX |
| --- | --- | --- |
| 1 | The sigmoid squashes the raw score $`z`$ into a probability $`p`$ between 0 and 1 | `p=\sigma(z)=\frac{1}{1+e^{-z}}` |
| 2 | Each pixel is a Bernoulli coin (label $`y=1`$ corner, 0 not). Independent pixels: multiply | `P(y\mid x,\theta)=\prod_{i=1}^{N}p_i^{\,y_i}(1-p_i)^{1-y_i}` |
| 3 | Take the log, the product becomes a sum | `\ln P=\sum_i\big[y_i\ln p_i+(1-y_i)\ln(1-p_i)\big]` |
| 4 | Flip the sign and average, this is the loss | `L_{BCE}=-\frac{1}{N}\sum_{i=1}^{N}\big[y_i\ln p_i+(1-y_i)\ln(1-p_i)\big]` |
| 5 | Gradient for $`z`$: the sigmoid slope $`p(1-p)`$ cancels the fractions | `\frac{\partial \ell}{\partial z}=\Big(-\frac{y}{p}+\frac{1-y}{1-p}\Big)p(1-p)=p-y` |

Example: the pixel really is a corner ($`y=1`$). The network says 69% and pays a small loss of 0.37. It says only 10% and pays 2.30, six times more.

Where the sigmoid comes from: the network's last layer gives any real number $`z`$, and the loss needs a probability. The sigmoid maps $`z`$ to $`(0,1)`$ and has the derivative $`p(1-p)`$, which is the factor that cancels in step 5.

## 2. Dice loss

Intuition: it asks how well two shapes overlap. Predicting no corners, or all pixels as corners, both score badly.

Definitions: $`A`$ is the set of pixels the network calls corners, $`B`$ the set of pixels that really are corners, $`|A|`$ the number of pixels in $`A`$, and $`A\cap B`$ the pixels in both.

| Step | What happens | LaTeX |
| --- | --- | --- |
| 1 | Overlap of the predicted set $`A`$ and the true set $`B`$ | `\mathrm{Dice}(A,B)=\frac{2\,|A\cap B|}{|A|+|B|}` |
| 2 | Count with sums, so it can be differentiated | `|A\cap B|\to\sum_i p_iy_i,\quad |A|\to\sum_i p_i,\quad |B|\to\sum_i y_i` |
| 3 | A high score should mean a small loss | `L_{Dice}=1-\frac{2\sum_i p_iy_i}{\sum_i p_i+\sum_i y_i}` |
| 4 | In training it is added to cross-entropy, so both are minimized together | `L=L_{BCE}+L_{Dice}` |

Why both: cross-entropy gives smooth gradients, Dice is not fooled by the huge background. Example: $`p=0.002`$ on 10,000 pixels with 20 corners. BCE is 0.014 (looks great), Dice loss is 0.998 (exposes the failure).

## 3. Return

Intuition: a reward now beats the same reward later, like money today versus a promise. $`\gamma`$ sets how fast the future fades.

Symbols: $`G`$ return (total future score), $`r`$ reward at one step, $`t`$ step number, $`\gamma`$ discount between 0 and 1 (0.9: each later step counts 10% less), $`\mathbb{E}`$ average over many flights.

| Step | What happens | LaTeX |
| --- | --- | --- |
| 1 | Return: all future rewards added up, nearer ones count more | `G_t=r_t+\gamma\,r_{t+1}+\gamma^{2}r_{t+2}+\cdots` |
| 2 | The same sum as a recursion: reward now plus the discounted return from the next step | `G_t=r_t+\gamma\,G_{t+1}` |
| 3 | Goal: choose the policy $`\theta`$ with the best average return | `J(\theta)=\mathbb{E}\,[\,G\,]` |

Example: rewards 1, 1, 1 and $`\gamma=0.9`$. Last step $`G_2=1`$. Before it $`G_1=1+0.9\cdot1=1.9`$. First step $`G_0=1+0.9\cdot1.9=2.71`$.

## 4. Imitation: compounding error

Intuition: a driver trained only on perfect driving panics after the first slip, because nobody showed a recovery.

Symbols: $`T`$ steps in a flight, $`\epsilon`$ chance of a mistake per step on the expert's states.

| Step | What happens | LaTeX |
| --- | --- | --- |
| 1 | The first mistake at step $`t`$ has probability at most $`\epsilon`$ | `P(\mathrm{first\ mistake\ at}\ t)\le\epsilon` |
| 2 | Worst case: no recovery, $`T-t+1`$ bad steps follow | `\mathrm{extra\ cost}\le\sum_{t=1}^{T}\epsilon\,(T-t+1)` |
| 3 | Add up $`T+(T-1)+\dots+1`$ | `=\epsilon\,\frac{T(T+1)}{2}\le T^{2}\epsilon` |
| 4 | Independent mistakes would cost only | `T\,\epsilon` |

Example: $`T=100`$, $`\epsilon=0.01`$: independent mistakes mean about 1 bad step, worst-case compounding up to 50 (bound 100). DAgger removes the mismatch by training on the states the student itself visits.

### The process behind it

Behavior cloning is supervised learning on the expert's actions $`a_i=\pi^*(s_i)`$:

```math
L(\theta)=\frac{1}{N}\sum_{i=1}^{N}\ell\big(\pi_\theta(s_i),\,a_i\big),\qquad \theta\leftarrow\theta-\eta\,\nabla_\theta L(\theta)
```

DAgger repeats four steps: fly the current policy and record the states it visits, ask the expert for the action $`\pi^*(s)`$ in each, add the pairs to the dataset $`D\leftarrow D\cup\{(s,\pi^*(s))\}`$, and retrain on all of $`D`$. The student then trains on the states it will really meet, so the error grows roughly like $`T\epsilon`$ instead of $`T^{2}\epsilon`$.

Sources: Ross, Gordon and Bagnell, arXiv 1011.0686 (compounding error and DAgger); Nature 2023 (Swift); research notes in `../03_tataryntsev.md`.
