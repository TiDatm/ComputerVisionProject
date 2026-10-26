# v1 verify: drone / sim-to-real claims (2026-10-26)

Caveat: pages were read through WebFetch (a small model summarises the page), so quotes are as returned by it, not eyeballed in the raw HTML. Pages that returned only PDF binary were re-fetched via ar5iv.

| # | verdict | quote / location | URL | live? |
|---|---|---|---|---|
| 1 | confirmed | "three full rollouts in the real world, corresponding to approximately 50 s of flight time" (Methods, residual model identification); mocap pose estimates used as ground truth | https://pmc.ncbi.nlm.nih.gov/articles/PMC10468397/ | live |
| 2 | confirmed | "we do not perform randomization of the platform dynamics at training time"; GPs for perception residual, kNN regression (k=5) for dynamics residual (Methods) | same | live |
| 3 | confirmed (number differs slightly in wording) | 5/9 Vanover, 4/7 Bitmatta, 6/9 Schaepper (Results); fastest race time ahead of best human by "half a second" | same | live |
| 4 | confirmed | "If this assumption fails, the system can fail." (Discussion), assumption = environment appearance consistent with training | same | live |
| 5 | confirmed | obs: "an estimate of the current robot state", "the relative pose of the next gate", "the action applied in the previous step" (Methods, Observations, actions and rewards); Fig. 1: "rely only on onboard sensory measurements". Policy gets no images (images feed the perception/state-estimation stage) | same | live |
| 6 | partly | Confirmed: single rolling-shutter camera + IMU (Intro); "3500:500 synthetic to real split" (GateNet data augmentation); "most parameters are randomized >=40%" (footnote 2 in Performance; says eval used 30%); best 16.56 s; max 28.23 m/s (Discussion); won April 2025 A2RL Abu Dhabi vs 3 world champion pilots. NOT confirmed: "PPO" was not returned in my extracts (page read only to char 100000 of 118564; PPO not checked). | https://arxiv.org/html/2601.15222 | live |
| 7 | partly | Table II (ar5iv) is sim-to-sim to unseen backgrounds: Image policy Train 80% / Test1 0% / Test2 0%; Feature tracks 100% / 100% / 100%. So the raw-image policy is 80% on its training background, 0% on unseen. Real-world: "exactly the same sensorimotor controllers that were learned in simulation ... no fine-tuning" | https://ar5iv.labs.arxiv.org/html/2006.05768 (arxiv.org/pdf returned binary only) | live |
| 8 | confirmed | Table 1, RCAN row: 70% (0 real grasps), 91% (5,000 real grasps). QT-Opt "Real" row (580,000 real grasps): 87%. Other rows e.g. Heavy Randomization 33% / 85% | https://ar5iv.labs.arxiv.org/html/1812.07252 (arxiv.org/pdf too large for fetch) | live |
| 9 | confirmed | Abstract: "trained in simulation and deployed on a physical quadrotor without any fine-tuning"; Table II (Sec. IV-D): task completion 95% static and dynamic (pilot 90% / 80%); best lap 12.1 s static vs pilot 5.0 s (dynamic 15.0 s vs 6.5 s). Note "only simulation" per the abstract; DR in sim | https://ar5iv.labs.arxiv.org/html/1905.09727 | live |
| 10 | confirmed | Sec. 1: "there are no perfect simulators, and therefore there is always a difference from the real world ... which we call reality gap"; Sec. 2.3: reality gap = difference between simulated and real environments; performance gap = difference in performance in sim vs real "for a given policy" | https://arxiv.org/html/2510.20808 (abs page shows only abstract) | live |

## Corrections / caveats
- Claim 7: add that the image policy is 80% on the training background (not 0% everywhere); the 0%/100% are for the two unseen-background test sets. It is a sim-to-sim test, not real.
- Claim 6: "PPO" unverified by me; check the remaining ~18k chars of the HTML (offset 100000). The 40% randomization is a footnote and the training value, evaluated at 30%.
- Claim 3: source says "a lead of half a second" (not "about"); fine.
- Claim 9: "around 95%" is exactly 95% in Table II for both tracks; the pilot's completion was 90%/80%, not stated in the claim.
- Claim 10: the "performance gap" distinction is in Sec. 2.3, not the abstract.
