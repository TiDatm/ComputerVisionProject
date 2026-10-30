# Visuals for block 3 slides

Candidate images. The final deck (see `../notes.md`) uses only `nature2023_fig2.png` (cropped twice) and `deep_drone_acrobatics_fig1_photos.png`; every other file is an unused candidate. Every slide that uses an image needs the credit line from the table. Nature 2023 is CC BY 4.0; the other paper figures are used as quotations with credit.

## From the papers

| File | What it shows | Credit line | Use on |
| --- | --- | --- | --- |
| `nature2023_fig1.png` | Human pilot against Swift: light trails, head-to-head photo, the three champions | Kaufmann et al., Nature 2023, Fig. 1 (CC BY 4.0) | Opening or hand-off slide |
| `nature2023_fig2.png` | Swift's full pipeline (IMU, VIO, gate detector, Kalman filter, policy 2×128) and the RL training loop | Kaufmann et al., Nature 2023, Fig. 2 (CC BY 4.0) | Final deck: slide 1 (camera photo and gate detector output, cropped) and slide 2 (simulated flights, Fig. 2b, cropped) |
| `nature2023_fig3.png` | Lap-time distributions and the head-to-head table (15 wins of 25) | Kaufmann et al., Nature 2023, Fig. 3 (CC BY 4.0) | Backup. The pilot rows show the pilot's wins, the bottom row shows Swift's |
| `nature2023_fig4.png` | Time gap per race segment and trajectories | Kaufmann et al., Nature 2023, Fig. 4 (CC BY 4.0) | Backup |
| `nature2023_extdata1_residual_models.jpg` | Real flight data and the residual force used to correct the simulator | Kaufmann et al., Nature 2023, Extended Data (CC BY 4.0) | Hand-off to block 4 |
| `nature2023_extdata2_track_trajectories.jpg` | Simulated (blue) and real (orange) flight paths over the 7 gates | Kaufmann et al., Nature 2023, Extended Data (CC BY 4.0) | Hand-off to block 4, fine-tuning |
| `ppo_fig1_clipping.png` | The PPO clipped objective for positive and negative advantage | Schulman et al., arXiv 1707.06347, Fig. 1 | Backup "PPO" |
| `ewc_fig1_weight_consolidation.png` | Why EWC remembers task A while learning task B | Kirkpatrick et al., arXiv 1612.00796, Fig. 1 | Backup "Fine-tuning" |
| `lora_fig1_reparametrization.png` | LoRA: frozen weights plus a small trainable A and B | Hu et al., arXiv 2106.09685, Fig. 1 | Backup "Fine-tuning" |
| `deep_drone_acrobatics_fig1.png` | Barrel roll, power loop and Matty flip, trained only in simulation | Kaufmann et al., RSS 2020 (arXiv 2006.05768), Fig. 1 | Not used on its own, see the photos version below |

Sources: https://www.nature.com/articles/s41586-023-06419-4, https://arxiv.org/abs/1707.06347, https://arxiv.org/abs/1612.00796, https://arxiv.org/abs/2106.09685, https://arxiv.org/abs/2006.05768

## From Wikimedia Commons

| File | What it shows | Licence and credit | Use on |
| --- | --- | --- | --- |
| `wikimedia_unet_architecture.png` | A U-Net: encoder, decoder and skip connections | CC BY-SA 4.0, Mehrdad Yazdani. https://commons.wikimedia.org/wiki/File:Example_architecture_of_U-Net_for_producing_k_256-by-256_image_masks_for_a_256-by-256_RGB_image.png | Slide 1 or backup "Supervised" |
| `wikimedia_rl_agent_environment_loop.svg` | Agent, environment, action, reward, state | CC0, Megajuice. https://commons.wikimedia.org/wiki/File:Reinforcement_learning_diagram.svg | Slide 2 |
| `wikimedia_fpv_drone_race.jpg` | An FPV drone race | CC BY-SA 4.0, Qwentii. https://commons.wikimedia.org/wiki/File:Course_de_drones_FPV_.jpg | Optional title image |
| `wikimedia_racing_drone.jpg` | A racing quadcopter | CC BY-SA 4.0, Commanderbryce. https://commons.wikimedia.org/wiki/File:Racing_Drone.jpg | Optional |

CC BY-SA means the credit line is required and a derived image must carry the same licence.

## Videos (link or QR code, do not embed)

- Swift, Nature 2023 overview: see the Sources section of `../../03_tataryntsev.md`.
- Deep Drone Acrobatics demo: https://youtu.be/2N_wKXQ6MXA

## Drawn for the slides

| File | What it shows | Credit line | Used on |
| --- | --- | --- | --- |
| `formula_supervised.png`, `formula_rl.png`, `formula_ppo.png` | Formulas rendered with KaTeX (first versions of the slide images) | Own work | Not used in the final deck |
| `plots_supervised.png` | Cross-entropy loss, the loss curve for y = 1, a gradient descent picture, the weight update | Own work | Not used in the final deck |
| `plots_reinforcement.png` | Return with discounted rewards, action probabilities and advantages, the policy gradient | Own work | Not used in the final deck |
| `plot_finetuning_papers.png` | EWC figure and LoRA figure side by side | Kirkpatrick et al., arXiv 1612.00796, Fig. 1; Hu et al., arXiv 2106.09685, Fig. 1 | Not used in the final deck (the fine-tuning slide was dropped) |
| `deep_drone_acrobatics_fig1_photos.png` | The three photos of the paper's Fig. 1 without its caption | Kaufmann et al., RSS 2020 (arXiv 2006.05768), Fig. 1 | Final deck: slide 3 (imitation) |
| `wikimedia_rl_agent_environment_loop.png` | PNG render of the CC0 diagram above | Megajuice, Wikimedia Commons, CC0 | Not used in the current deck |

## Formulas in the appendix slides

The appendix formulas are not files in this folder. They are LaTeX rendered by URL (latex.codecogs.com) and inserted as images in the Google deck. The LaTeX source of every formula is in `../appendix.md`, so they can be rendered again or replaced by local images.
