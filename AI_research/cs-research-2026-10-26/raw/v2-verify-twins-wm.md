# Verification v2: twins / world models (2026-10-26)
Verified via WebFetch (page text summarised by a small model; quotes are as returned, not byte-checked against PDFs).

| # | Verdict | Quote / location | URL | Live? |
|---|---|---|---|---|
| 1 | confirmed | Table 1, Mip-NeRF360: 3DGS "Ours-30K" Train 41m33s, FPS 134; Mip-NeRF360 Train 48h, FPS 0.06 | https://arxiv.org/html/2308.04079 | live |
| 2 | confirmed | Sec 5.3: "to converge on a single NVIDIA V100 GPU (about 1-2 days)"; Appendix D: "On an NVIDIA V100, this takes approximately 30 seconds per frame" (150-200M queries/image). Abstract page alone has neither. | https://ar5iv.labs.arxiv.org/html/2003.08934 | live |
| 3 | confirmed | "short video recordings (2-3 minutes) ... handheld camera"; abstract "up to 130 fps" (peak, not typical); "campaign of 105 hardware experiments"; Table II "Static" (objects removed): 0/5 top rating, 4/5 "~" (position reached, orientation not), 1/5 failed | https://arxiv.org/html/2412.16346 | live |
| 4 | partly | Abstract: "98.6% success rate (69 / 70 gates) over 30 trials spanning two three-gate tracks and a moving-gate track". 3-min onboard-camera video, COLMAP + Splatfacto confirmed. BUT Table II does not show 98.6%/69-70: real SR is 96.7% (Left-Turn), 100% (Random), 100%* (Moving, lateral tracking only, no gate crossing, for safety). Per-track trial count not stated (text: 10 initial conditions per track). Paper does not call it "digital twin" for its own scene. | https://arxiv.org/html/2510.02248 | live |
| 5 | partly | Table V "Proposed" real 7/10, 7/10, 6/10 (L/M/R) confirmed. "w/o CENet" sim|real: L 9|0, M 10|2, R 7|1 -> real 0,2,1 confirmed, but sim is 9, 10, 7 (not all 9-10). | https://arxiv.org/html/2503.03984 | live |
| 6 | partly | Abstract: "less than 62 hours of unlabeled robot videos from the Droid dataset"; Table 3 Pick-&-Place Avg: V-JEPA 2-AC 80% cup / 65% box; Octo 15% / 10%. Zero-shot on Franka in two labs confirmed. "10 trials per condition": Table 2 caption says out of 10 trials; Table 3 caption says "10 tasks and average" - wording inconsistent. Only first 100k of 228k chars read. | https://arxiv.org/html/2506.09985 | live |
| 7 | confirmed | Abstract: "the first algorithm to collect diamonds in Minecraft from scratch without human data or curricula"; Nature 640 (8059), 647-653. Cited PMC URL returned a reCAPTCHA wall to the fetcher; quote confirmed from Europe PMC XML for PMC12003158 and arXiv 2301.04104 abstract. | https://pmc.ncbi.nlm.nih.gov/articles/PMC12003158/ | live but bot-gated; use https://www.ebi.ac.uk/europepmc/webservices/rest/PMC12003158/fullTextXML |
| 8 | confirmed | Table 1: Physical Variance 100.0; VideoPoet (multiframe) 29.5 (top); Sora (i2v) 10.0; abstract "physical understanding is severely limited, and unrelated to visual realism" | https://arxiv.org/html/2501.09038 | live |
| 9 | confirmed (with caveats) | State = position, velocity, attitude (rotation matrix), body angular velocity; action = 4 motor forces; no camera mentioned; Sec VI-C "entirely on domain-randomized simulation data"; Table IV circle mean position RMSE 0.24 m (Ours) vs 0.39 m (MPPI with standard predictive model, not physics-regularized); 5 runs per trajectory. State estimate comes from onboard GPS localization (so not "no sensing"). Only first 100k of 137k chars read. | https://arxiv.org/html/2606.23444 | live |
| 10 | partly | Table I: avg real success 45.3% (real-only) vs 83.2% (co-trained on real + digital cousin + prior sim). Table has no ratio; Sec V-D: "a co-training ratio of 99% yielded the best performance" - on the CupPnP task only. | https://arxiv.org/html/2503.24361 | live |

## Corrections
- 4: Do not cite "69/70 gates, 98.6%" as Table II. It is the abstract/intro figure; Table II per-track real SR is 96.7/100/100*, and the Moving track is lateral tracking only (no gate crossing). Avoid saying "digital twin" as the paper's own term.
- 5: Without-CENet sim success is 9/10, 10/10, 7/10, not "9-10/10".
- 6: "10 trials per condition" is supported by Table 2 caption only; Table 3 wording differs.
- 7: Use Europe PMC / arXiv 2301.04104 as fetchable source; PMC page is bot-gated for automated fetch.
- 9: Baseline is MPPI with a standard predictive model; state comes from GPS-based localization; 5 runs per trajectory.
- 10: 83.2% is real + digital-cousin + prior-sim co-training; 99% ratio is from CupPnP only, in text not Table I.
- 2: abstract page lacks the numbers; cite Sec 5.3 and Appendix D (full text).
