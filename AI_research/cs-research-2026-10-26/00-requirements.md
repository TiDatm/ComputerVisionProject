# 00. Requirements shortlist

CS101 group project, team "CS project" (4 people). Collected 2026-10-26.
Sources: the task PDF `Group_Project.pdf` and `Group_Project_Topics.pdf` (received 2026-10-15), the CS101 syllabus, Moodle and calendar records, and the team Telegram chat up to 2026-10-24.

**Gap:** the Telegram connector was down on 10-26, so the chat after 2026-10-24 is unread. Check it for a changed date or new asks.

## Dates

| # | What | When | Source |
|---|---|---|---|
| D1 | Your block in the repo: `blocks/04_thorenko.md` (Latin, like Hlib's `03_tataryntsev.md`) + your slides, merged | ~1 week before the defence. Yablonskyi wrote "Друзі у нас 4 дні" on Sat 10-24, so assume **Wed 2026-10-28** (inference) | PDF step 2; TG 10-24 msg 102772 |
| D2 | Moodle item "Групова презентація" | **2026-11-01 23:59** | Moodle CS101 (life record course/cs101) |
| D3 | Live defence | Last two weeks of the course. The CS101 [P] slots on the calendar are **Nov 2, 5, 6, 9, 11, 12**, which are probably the defence slots (inference). You said you don't attend [P] slots, so confirm which slot is ours | syllabus "Final two weeks — in-person group-project presentations"; KSE calendar mails 10-24 |
| D4 | Topic agreed with the teacher | "до [дата]" (no date given). Done? Not seen in the records | PDF "Вибір теми" |

## Grading (20 of 100 course points)

| # | Criterion | Points | Source |
|---|---|---|---|
| G1 | Topic is current and covered in substance | 4 | PDF table |
| G2 | One coherent presentation, not 4 separate parts | 3 | PDF |
| G3 | Terms explained for a non-specialist | 1 | PDF |
| G4 | Within time, readable slides | 2 | PDF |
| G5 | Repo tidy: `README.md` and `slides.pdf` in place | 2 | PDF |
| G6 | **Your block** worked out in substance, with terms explained and sources | **6** (personal) | PDF |
| G7 | **Your block** written and committed by you, under your name | **2** (personal) | PDF |
| G8 | Defence Q&A gives no points, but if you can't answer a simple question about your block, the 8 can be cut | (risk to 8) | PDF |

## Format and content rules

| # | Rule | Source |
|---|---|---|
| F1 | Talk **≤ 10 min**, about **12–16 slides**. Team plan: intro 1–2, **3 slides per block**, conclusion 1–2, so ~2.5 min for block 4 | PDF; TG 10-19 msg 101951 |
| F2 | 1–2 people may present. The teacher then asks **random members about their own block** | PDF "Захист на парі" |
| F3 | **Explain every term at its first use** in plain words (example: "лідар (лазерний далекомір...)") | PDF "Пояснюйте кожен термін" |
| F4 | Block file = main thesis, examples, sources. About one page, like the "Сприйняття" example: thesis → explained terms → live industry debate → link to the next block → sources | PDF appendix |
| F5 | Sources **inline where the fact is used** and as a full list at the end. **Links must work** | PDF appendix |
| F6 | Each block **links to the neighbouring block**. For block 4 that is block 3 → 4 ("trained in sim — will it work in reality?") and 4 → conclusion | PDF; TG plan |
| F7 | Don't retell the lecture. Go deeper, with fresh examples and live debates | PDF; Topics PDF |
| F8 | Base is **vision** (camera, images), not lidar or scans. Even the environment is built from photos and video | TG plan 10-19 risks |
| F9 | No formulas. RL and adaptation are explained by intuition | TG plan 10-19 risks |
| F10 | Swift facts are checked in the Nature paper, not in retellings. Swift is "gate detection + state estimation + policy", not pixels-to-motors | TG plan 10-19; your notes 10-24 |
| F11 | Block 2/4 boundary: block 2 = how the simulator generates data (incl. domain randomization); block 4 = how to bring sim close to reality and move the model there. Write it in the README | TG plan 10-19 |

## Repo and git obligations

| # | Obligation | Source |
|---|---|---|
| R1 | Private repo `TiDatm/ComputerVisionProject`, teacher added as collaborator. Yablonskyi registered the team and repo on 10-22 | PDF; TG 10-22 msgs 102524–102525 |
| R2 | Work in **your own branch** (`Andrii-Thorenko`), then merge into the shared branch | PDF step 2; TG 10-22 msg 102533 |
| R3 | `git config user.name` = your real name, as in Practical 4. **Commits under your name only, no Claude co-author** (your standing rule) | PDF; owner rule |
| R4 | **Commit gradually**: "гіт має показувати роботу а не результат", so don't push everything in one day | TG 10-24 msgs 102773–102774 |
| R5 | Step 3 (whole team): joint intro and conclusion, remove repeats, align style, final `slides.pdf` in the repo | PDF step 3 |
| R6 | README: topic, members, who owns which block, block boundaries | PDF step 1; TG plan |

## Integrity and AI

| # | Point | Source |
|---|---|---|
| I1 | The syllabus says AI is "strictly prohibited" for quizzes, assignments and exams. Plagiarism in a presentation or group project means zero for the task and at least one letter grade off the course | CS101 syllabus |
| I2 | Your decision (10-25): don't ask the teacher. `AI_research/` with its README is the disclosure. The block `.md` is written **in your own words**; AI notes are never pasted into it | hub record ceo-261025-2; `AI_research/README.md` |
| I3 | Keep to public, non-operational sources on military use (sensitivity flagged 10-19) | planning session 10-19 |

## Keep in mind (5 lines)

1. Block file + 3 slides in your branch by **~10-28**, committed in several steps under your name.
2. Every term explained at first use, sources inline and listed, links checked.
3. One link from block 3 into block 4 and one out of it, with Swift as the thread.
4. Be ready for a random question on your block: the gap, the three fixes, digital twins, how Swift did it.
5. Confirm the defence slot (Nov 2–12) and that the topic was agreed with the teacher.
