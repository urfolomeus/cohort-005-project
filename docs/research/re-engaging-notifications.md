# Re-engaging Students with event-triggered Notifications

Researched 2026-11-18.

## Question

Which kinds of event-triggered Notification (not time-based or inactivity) most effectively bring Students back to resume an online video course? How do they map onto events Cadence already records? What is known to backfire (fatigue, unsubscribes)?

Constraints for v1: in-app only, Students only, event-triggered only, never promotional.

> **Glossary gap.** `GLOSSARY.md` does not yet define **Notification**, **Alert** or **Resumed**, so this doc uses them informally: a Notification is an in-app message to a Student, and Resumed means a Student who had stopped comes back and Watches more of a course they're Enrolled on. Pin these terms down before building.

## Short answer

The evidence is thin and mostly indirect. No study we found isolates an **in-app, event-triggered** Notification and measures whether Students resume. Read the ranking as "best-supported bet", not "proven".

| Rank | Notification kind | Cadence event (`app/db/schema.ts`) | Evidence strength |
| --- | --- | --- | --- |
| 1 | **Your question was answered by the Instructor** | `comments` row with `parentId` = the Student's top-level comment, written by the course's `instructorId` or an admin (same predicate as `staffReply` in `app/routes/instructor.questions.tsx`) | Moderate, but observational and about *being answered*, not about the Notification |
| 2 | **Someone replied to your comment** (another Student) | `comments` reply where `parentId` = the Student's comment and the author is not staff | Moderate-to-weak. Same mechanism as rank 1; the newsgroup evidence covers any reply |
| 3 | **New lesson/module on a course you're Enrolled on** | `lessons.createdAt` / `modules.createdAt` for a course with an `enrollments` row (best when `completedAt` is null, or the Student has Watched everything published so far) | Weak-to-moderate. One MOOC A/B test of email reminders about unseen videos |
| — | **Don't trigger on the Student's own actions** | `lessonProgress`, `quizAttempts`, `videoWatchEvents`, Seat claim (`teamMembers`) | These events fire while the Student is already active, so they can't re-engage anyone. Platform guidance and one MOOC trial both point against it |

**Biggest scope risk (inference, high confidence):** an in-app Notification is only seen once the Student is back in the app. It can tell a returning Student what to do next, but it can't bring back a Student who has stopped. Every positive "return" effect below came through email or push.

## Evidence per candidate

### 1. Instructor answered your question

- **Verified (observational, not a course setting):** newcomers to Usenet groups who got a reply to their first post were about 12 percentage points more likely to post again (n = 2,777). Reply quality and tone made no difference. The authors say they can't claim causality. [Joyce & Kraut 2006](https://doi.org/10.1111/j.1083-6101.2006.00033.x), figures as reported via [ResearchGate](https://www.researchgate.net/publication/220437981_Predicting_Continued_Participation_in_Newsgroups) and [MISQ 2025](https://misq.umn.edu/misq/article/49/2/731/3183/Behavior-Toward-Newcomers-and-Contributions-to). We did not read the full text.
- **Verified (matched difference-in-differences, 21M questions):** on Stack Overflow, getting an answer raises a user's later activity (helping others). The effect is concentrated in newcomers and peaks when the answer arrives **30–60 minutes** after the question. Very fast or very late answers show less effect. [Strahringer et al. 2026, arXiv:2604.03209](https://arxiv.org/abs/2604.03209). The outcome is contribution, not resuming a course.
- **Verified (correlational, MOOC):** posting on the forum is a strong indicator of persistence. [arXiv:1710.03323](https://arxiv.org/pdf/1710.03323). Forum participation is linked to persistence, as summarised in [arXiv:2307.07614](https://arxiv.org/pdf/2307.07614).
- **Counter-evidence (RCT):** in a MOOC, a "high-touch" condition where instructors answered forum questions and sent weekly summaries did **not** improve satisfaction, persistence or completion. [Tomkin & Charlevoix 2014](https://doi.org/10.1145/2556325.2566245), as summarised in [Kizilcec & Brooks 2017, Handbook of Learning Analytics ch. 18](https://rene.kizilcec.com/wp-content/uploads/2017/08/kizilcec2017diverse.pdf). That trial tested general instructor presence, not a targeted message to the Student who asked, so it limits rank 1 without refuting it.
- **Why it still ranks first (inference):** it is the only candidate that is personal, caused by someone else, and closes a loop the Student opened. That matches the platform rule to notify only about things "directed specifically at" the user ([Android design guidance, archived](https://spot.pcc.edu/~mgoodman/developer.android.com/design/patterns/notifications.html)) and Apple's "timely, high-value information" ([Apple HIG mirror, v15](https://developerguidelines.com/human-interface-guidelines/version/15/notifications/)).

### 2. Another Student replied to your comment

- Joyce & Kraut measured *any* reply, so their evidence applies here as much as to rank 1. We found no evidence that replies from staff matter more than replies from peers.
- **Risk:** threads can get busy. Moodle-style forced subscriptions led to "everyone started getting email" ([moodle.org thread](https://moodle.org/mod/forum/discuss.php?d=402047), anecdotal). Notify only the parent's author, and collapse several replies into one Notification (see pitfalls).

### 3. New lesson or module added

- **Verified (A/B test, openHPI MOOC, email):** reminder emails about **unseen lecture videos** increased lecture views compared with other reminder types. Emails showing popular discussions and unanswered questions increased forum activity. [Renz, Hoffmann, Staubitz & Meinel, LAK 2016](https://doi.org/10.1145/2883851.2883876), as summarised in [Kizilcec & Brooks 2017](https://rene.kizilcec.com/wp-content/uploads/2017/08/kizilcec2017diverse.pdf). These emails were scheduled, not triggered by new content, so transferring the result to a "new lesson" event is an inference.
- **Supporting (time-based push):** Duolingo found that fresh content beats repeated content. New templates boost conversion even when they are no better in the long run, and sending the same message repeatedly "will get repetitive, desensitize users". [Yancey & Settles, KDD 2020](https://research.duolingo.com/papers/yancey.kdd20.pdf).
- **Inference:** "new lesson" news is most useful to a Student who is caught up or has finished. For a Student stuck mid-course, a lesson added at the end doesn't address why they stopped. The Notification should link to the Student's resume point, not only to the new lesson.

### Self-generated events (don't use as triggers)

- `lessonProgress`, `quizAttempts` and `videoWatchEvents` record the Student's own activity. A Seat claim is also the Student's own action. A Notification about any of them lands while the Student is already present.
- Android guidance says not to notify when "the relevant new information is currently on screen" ([archived](https://spot.pcc.edu/~mgoodman/developer.android.com/design/patterns/notifications.html)).
- **Verified (sequential randomised trial, Coursera, n = 8,681, email):** by week 3, reminder emails had positive effect estimates for *inactive* learners and **negative** estimates for *active* learners ("potentially harmful to active learners"). Confidence intervals mostly overlapped. [NeCamp, Gardner & Brooks, arXiv:1810.11185](https://arxiv.org/abs/1810.11185) (PERCS).
- These events are still useful as **inputs**, for example to decide whether to show "new lesson" (has the Student Watched everything?) and where to deep-link (resume point).

## Pitfalls and backfire

- **Volume costs long-term engagement.** Meta ran a one-year-plus holdout that sent only relevant notifications. Usage dropped in the short term and rose relative to control after a year. Reported in [Sigerson, Cunningham et al., arXiv:2608.08043](https://arxiv.org/pdf/2608.08043), citing [Analytics at Meta 2022](https://medium.com/@AnalyticsAtMeta/notifications-why-less-is-more-how-facebook-has-been-increasing-both-user-satisfaction-and-app-9463f7325e7d). The original returned 403 to us, so we haven't read it directly.
- **Volume optimisation.** Pinterest cut notification volume by 6–24% while CTR rose 11–31% and site engagement 1–3%. [Zhao et al., KDD 2018](https://doi.org/10.1145/3219819.3219906). The figures come via [Eugene Yan's summary](https://eugeneyan.com/writing/push/); the ACM PDF returned 403. LinkedIn's RL notification system *reduced* volume while increasing sessions (+0.24%) and WAU (+0.18%). [Prabhakar et al., KDD 2022, arXiv:2207.03029](https://arxiv.org/pdf/2207.03029).
- **Repeats lead to users turning everything off.** Apple says to avoid "sending multiple notifications for the same thing, even if someone hasn't responded", because "people may turn off all notifications from your app". [Apple HIG mirror, v15](https://developerguidelines.com/human-interface-guidelines/version/15/notifications/). This argues for **one Notification per thread**, updated in place, not one per reply.
- **Promotional content.** Android: "Don't create superfluous notifications just to get your brand in front of users… will only frustrate and likely alienate your audience." [archived guidance](https://spot.pcc.edu/~mgoodman/developer.android.com/design/patterns/notifications.html). This matches the v1 never-promotional rule.
- **Frequency drives uninstalls (push, retail).** A field experiment with 17,500 users and 5 frequencies over 7 weeks linked higher frequency to more uninstalls. [Innovative Marketing 2021](https://businessperspectives.org/journals/innovative-marketing/issue-382/mobile-apps-in-retail-effect-of-push-notification-frequency-on-app-user-behavior). We saw the abstract only.
- **Scaled nudges shrink and fade.** Across 247 MOOCs and about 250k students, interventions that worked in small studies had effects about an order of magnitude smaller. Self-regulation interventions raised engagement "in the first few weeks but not final completion rates", attenuating after 1–2 weeks. ML personalisation did not help. [Kizilcec et al., PNAS 2020](https://doi.org/10.1073/pnas.1921417117). These were in-course prompts, not notifications.
- **Effects vary by audience.** In PERCS, emails helped Indian learners but "may even deter [US learners] from returning". [arXiv:1810.11185](https://arxiv.org/abs/1810.11185).

## Evidence gaps and open questions

- **No direct evidence for in-app Notifications causing return.** Every return effect above came through email or push, or was the effect of the underlying event (being answered), not of being told about it.
- **No study of "new content added" as a trigger in courses.** The openHPI result used scheduled emails about unseen videos.
- **Instructor answer vs peer reply.** No evidence separates the two.
- **Answer latency.** The Stack Overflow 30–60 min window suggests that *how fast Instructors answer* may matter as much as notifying. Cadence's unanswered-questions queue (`app/routes/instructor.questions.tsx`) is a lever here. Untested in courses.
- **Measurement.** Resumed needs a definition (e.g. new `videoWatchEvents` on the course within N days of the Notification after M days without any). Also needed: a holdout group, and a long enough window to see the Meta-style reversal.

## Findings that suggest scope changes

1. **In-app-only probably can't re-engage absent Students** (inference). v1 will mostly help Students who come back on their own: it shows them why to continue and where. If the goal is to bring Students back, email or push for rank 1 (and possibly rank 3) is likely needed. Both are outside v1.
2. **The best-measured return effects are time-based** (Duolingo daily reminders, PERCS and openHPI scheduled emails), which v1 excludes. The research doesn't show event-only beats time-based. It shows that *content* referring to something specific to the Student (unseen videos, the Student's own streak) performs better.
3. **Targeting by activity matters.** PERCS suggests messaging active learners can hurt. That argues for suppressing Notifications to Students who are currently active, which needs `videoWatchEvents` as a suppression input. This isn't an inactivity *trigger*, so it doesn't conflict with v1.
4. **Instructor response speed may be a bigger lever than any Notification.**

## Sources

- Joyce & Kraut (2006), Predicting Continued Participation in Newsgroups, JCMC 11(3). https://doi.org/10.1111/j.1083-6101.2006.00033.x
- Strahringer, Prüß & Riemer (2026), Help Converts Newcomers, Not Veterans. https://arxiv.org/abs/2604.03209
- Tomkin & Charlevoix (2014), Do professors matter? L@S '14. https://doi.org/10.1145/2556325.2566245
- Kizilcec & Brooks (2017), Diverse Big Data and Randomized Field Experiments in MOOCs. https://rene.kizilcec.com/wp-content/uploads/2017/08/kizilcec2017diverse.pdf
- Renz et al. (2016), Using A/B testing in MOOC environments, LAK '16. https://doi.org/10.1145/2883851.2883876
- NeCamp, Gardner & Brooks (2018), Beyond A/B Testing (PERCS). https://arxiv.org/abs/1810.11185
- Kizilcec et al. (2020), Scaling up behavioral science interventions in online education, PNAS 117(26). https://doi.org/10.1073/pnas.1921417117
- Yancey & Settles (2020), A Sleeping, Recovering Bandit Algorithm for Optimizing Recurring Notifications, KDD '20. https://research.duolingo.com/papers/yancey.kdd20.pdf
- Analytics at Meta (2022), Notifications: why less is more. https://medium.com/@AnalyticsAtMeta/notifications-why-less-is-more-how-facebook-has-been-increasing-both-user-satisfaction-and-app-9463f7325e7d
- Sigerson, Cunningham et al. (2026), Evaluating for the long term: Learnings from industry. https://arxiv.org/pdf/2608.08043
- Zhao et al. (2018), Notification Volume Control and Optimization System at Pinterest, KDD '18. https://doi.org/10.1145/3219819.3219906
- Prabhakar et al. (2022), Multi-objective Optimization of Notifications Using Offline RL, KDD '22. https://arxiv.org/pdf/2207.03029
- Apple Human Interface Guidelines: Notifications (v15 mirror; official page https://developer.apple.com/design/human-interface-guidelines/notifications). https://developerguidelines.com/human-interface-guidelines/version/15/notifications/
- Android Design: Notifications (archived official guidance). https://spot.pcc.edu/~mgoodman/developer.android.com/design/patterns/notifications.html
- Mobile apps in retail: Effect of push notification frequency (2021). https://businessperspectives.org/journals/innovative-marketing/issue-382/mobile-apps-in-retail-effect-of-push-notification-frequency-on-app-user-behavior
- Temporal profiling for MOOC dropout prediction. https://arxiv.org/pdf/1710.03323
