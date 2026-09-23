# Maximilian Hassel

**Full-stack engineer. I designed, built, and shipped a production AI coaching platform solo — architecture through App Store approval.**

Tucson, AZ · [integrativeaisolutions.com](https://integrativeaisolutions.com) · mchassel2@gmail.com
B.S. Software Engineering, Western Governors University (expected June 2027) — **available for full-time work now**

I coached lifters and competed in bodybuilding for ten years before I wrote production code. The domain knowledge is why the product works; the engineering is what turned it into software. Alongside this I currently run the personal training department at an LA Fitness club — leading a team of 4 trainers serving several hundred clients and owning roughly $25K a month in recurring PT revenue — so I'm used to being accountable for a team and a number, not just a codebase.

The app's repositories are private because they hold the source of a commercial product, so this page is the case study instead. Public code: [reportgen](https://github.com/VHcodehead/reportgen), a Go CLI with CI and tests, plus two client sites I built and maintain.

---

## My Pocket Coach

An AI coaching app that programs nutrition and training, adapts to biometrics, and talks to the user. Live on the Apple App Store since May 25, 2026. Built solo — architecture, backend, frontend, data, infrastructure, and release.

**1,298 commits · 212,443 lines · 8 months from empty repo to App Store approval, 12 months and still shipping**

### What's in it

| Layer | Build |
|---|---|
| **Backend** | Node.js / Express in TypeScript — **132 services**, **345 REST endpoints** across **50 route modules**, Zod-validated |
| **Frontend** | React Native + Expo — **94 screens**, **138 shared components**, custom design system, Expo Router |
| **Data** | PostgreSQL on Supabase — **99 tables**, **139 migrations**, **271 row-level-security policies** |
| **AI** | **16 GPT-4o decision engines**; GPT-4 Vision food-photo logging; voice in/out coaching chat |
| **Forecasting** | Least-squares linear regression written from scratch, R² as confidence, LLM reasoning layered on top |
| **Integrations** | Apple HealthKit, Oura, RevenueCat, 800K-item food database with barcode scanning |
| **Testing & ops** | **182 test files**; Railway auto-deploy, EAS Build, Sentry, PostHog, rate limiting, request tracing |

### The forecasting engine

The app predicts next week's weight change and adjusts macros and training accordingly. That runs on statistics I implemented directly rather than a model I imported:

- **Least-squares linear regression from scratch** — slope, intercept, and R² computed in ~40 lines, no ML library
- **R² becomes the user-facing confidence score.** If the fit is poor, the app says so instead of asserting a number it can't support
- **GPT-4o-mini layers explanation on top**, receiving the regression output, adherence analysis, and biometrics as structured context — the LLM explains and adjusts, it does not invent the prediction
- **Biometric fusion** across Oura and Apple HealthKit: sleep score, duration, efficiency, latency, deep and REM, HRV, HRV balance, readiness, respiratory rate, resting heart rate, steps, activity — with a 30-day rolling HRV baseline driving deload detection against RPE and volume trends
- A gate refuses to predict at all below four check-ins, because early data isn't reliable enough to act on

---

## Engineering decisions I'd defend in an interview

**I deleted my own ML layer two days after shipping it.** I built TensorFlow.js models for weight prediction, progressive overload, and deload detection in October 2025. I measured them, they were unreliable, and I replaced the whole layer with explainable regression plus LLM reasoning within 48 hours. The commit is still in the history: *"Replace broken TensorFlow ML with GPT-4o-mini AI prediction system."* Shipping the impressive-sounding thing mattered less than shipping the thing that worked, and the replacement is easier to debug, cheaper to run, and can explain itself to a user.

**Authorization lives in Postgres, not the application layer.** 271 row-level-security policies across 99 tables. Health and nutrition data is sensitive and I'm one person — putting authorization in the database means a forgotten `WHERE user_id = ?` in a route handler doesn't leak somebody's data. The database refuses regardless of what the API layer got wrong.

**Services own logic; routes only parse.** 132 services behind 50 route modules. It's why a nightly cron, a REST call, and the coaching chat can all trigger the same macro recalculation without three copies of it drifting apart.

**Zod at every boundary — especially the LLM one.** Every request body, third-party webhook, and model response is schema-parsed before it reaches business logic. LLM output is untrusted input. Treating a GPT-4o response as something to validate rather than something to trust is what keeps a hallucinated field out of a user's program.

**TypeScript end to end, no exceptions.** One language across client and backend meant shared types on every API contract. Working alone, the compiler was my code reviewer — the thing that caught what nobody else was there to catch.

## Things that went wrong

**Five Apple review cycles.** Rejections over subscription presentation, account deletion, and health-data disclosure. I learned to read the guidelines as a specification rather than a formality, and to build the compliance surface — deletion flows, disclosures, restore-purchases — before submitting instead of after being told to.

**LLM output is not an API.** The first pass treated GPT-4o responses as structured data and broke whenever the model got creative. Now every call is schema-parsed with a deterministic fallback, so a malformed response degrades into a sane default instead of a crash.

**Wearables disagree about reality.** Oura and HealthKit differ on timestamps, units, timezones, and where a "day" begins. A large share of the biometric pipeline is reconciliation and gap handling, not modeling — the unglamorous work that decides whether anything downstream gets usable input.

---

## Also

- **Provisional patent drafted** (not yet filed) — 3 independent and 13 dependent claims covering multi-modal biometric-adaptive coaching
- **NASM Certified Personal Trainer** · J3 University Curriculum Level 1
- 50+ client body-recomposition programs run before I built the software version

## Stack

`TypeScript` `JavaScript` `React Native` `Expo` `React` `Node.js` `Express` `PostgreSQL` `Supabase` `Zod` `OpenAI GPT-4o / Vision` `Railway` `EAS Build` `Sentry` `PostHog` `Git` `SQL` `REST` `OAuth 2.0`

---

**Open to junior and mid-level software engineering roles — full-stack, backend, or React Native. Remote preferred.**
Reach me at mchassel2@gmail.com.
