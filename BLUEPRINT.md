# Kanun — Product Blueprint

> Malaysian HR & Employment Law, played in 60 seconds.
> Working name: **Kanun** ("statute / canon" in Malay/Arabic — short, ownable, mobile-friendly).

A gamified micro-learning platform covering the **Employment Act 1955** (Peninsular Malaysia & Labuan), the **Sabah Labour Ordinance (Cap. 67)** and the **Sarawak Labour Ordinance (Cap. 76)**. Built game-first; learning is a side effect of trying to keep your streak alive.

---

## 1. North-star design principles

1. **Game first, learning second.** If a screen looks like a textbook, the design is wrong.
2. **One-minute sessions.** Every loop must work on a Grab ride. No mode runs longer than 3 minutes.
3. **Immediate feedback.** Every answer reveals the legal source within 200ms — wrong answers are the most valuable teaching moment.
4. **Earned, not given.** XP and levels gate content. The paywall is a level-up moment, not a popup.
5. **Localised content.** All scenarios use Malaysian names, ringgit, KL/Kuching/KK locations. Real provisions, not made-up "labour rules."
6. **Mobile-first.** Designed for one-thumb use on a 375px screen. Desktop is a nice-to-have.

---

## 2. Core experience loop

```
   Open app  →  Pick a mode (3 visible)  →  Play (60–180s)
       ↑                                            ↓
       └── Streak +1 / level-up tease  ←──  Results (XP, accuracy, badges)
```

Every loop ends in a **micro-celebration** (XP popup, level-up ring, badge unlock) and a **next-action CTA** (play again, claim daily reward, "you're 80% to Lv 5").

---

## 3. The six game modes

| Mode | Length | Mechanic | Scoring |
|---|---|---|---|
| **Quiz Blitz** | 5 × 15s | Timed MCQ. Time-left becomes XP bonus. | 10 XP base + remaining seconds bonus |
| **True/False Rush** | 8 × 8s | Rapid statements, T or F. | 8 XP + time bonus |
| **Scenario Challenge** | 3 cases, no timer | Read narrative, pick best HR action. | 18–30 XP per correct (scaled by difficulty) |
| **Spot the Violation** | 3–4 cases | Identify which provision is breached. Multi-select variants for advanced levels. | 22 XP per correct, half for partial |
| **Decision Simulator** | 2 dilemmas | Branching choices with weighted scores; every option is annotated. | (max(score,0) × 3) + 5 |
| **Boss Battle** | 3 hard cases, 1 life | Triggered at level milestones (5, 10, 15…). Wrong answer ends the run. | 50 + difficulty bonus per case; "Boss slayer" badge on win |

### Per-mode UI behavior

- Header: mode name, progress dots, timer (if any), close.
- Body: difficulty/law/topic chips → setup card (if scenario) → question → answer buttons.
- Feedback: green/red flash + plain-English rationale + statutory reference.
- Footer: "Quit" before answering; "Next →" or "See results" after.
- Boss Battle adds a **boss bar** at the top with the inspector's avatar and an HP bar that drains as you progress.

---

## 4. Level & progression system

### XP curve

`xpForLevel(L) = 80 + (L − 1) × 40`

| Level | XP needed (this level) | Cumulative |
|---|---:|---:|
| 1 | 80 | 0 |
| 2 | 120 | 80 |
| 3 | 160 | 200 |
| 5 | 240 | 600 |
| 10 | 440 | 2,180 |
| 15 | 640 | 4,800 |
| 20 | 840 | 8,420 |

Curve is gentle enough to give a level-up in nearly every session for the first hour, then steepens. We re-tune empirically once we have ≥1,000 sessions of telemetry.

### Level tiers (content gating)

| Levels | Theme | Free? |
|---|---|---|
| 1–4 | **Foundations** — wages, hours, leave basics | ✓ Free |
| 5–9 | **Workplace operations** — termination, OSH, scenarios | Paywall at Lv 5 |
| 10–14 | **Compliance & enforcement** — DG inquiries, IRA Section 20 | Premium |
| 15+ | **Mastery** — case-law nuance, Sabah/Sarawak specialisations | Premium |

### Skill tracks

Five orthogonal tracks, each capped at 100%. A user's overall level reflects total XP; skill % reflects topical mastery.

- Recruitment & Hiring
- Termination & Dismissal
- Compliance & OSH
- Leave & Benefits
- Wages & Working Hours

100% in any track unlocks a **printable certificate** (e.g., "HR Compliance Certified — Termination Track, EA1955") — a strong trigger for HR practitioners building professional credibility.

### Unlockables

- Mode unlocks at minimum level (Boss Battle = Lv 5).
- Cosmetic avatar borders at Lv 3, 7, 12.
- "Inspector" persona replaces the default avatar in Boss Battles after first win.
- Weekly Ranked Challenge (premium only) opens at Lv 6.

---

## 5. Monetization

### Free tier

- 5 lives, refilling 1 every 30 minutes (cap 5).
- Levels 1–4 (all modes within those caps).
- Daily login reward (XP only, no streak protection).
- Unlimited replays of unlocked content.

### Premium tiers

| Tier | Price (MYR) | Notes |
|---|---:|---|
| Monthly | RM 19 / month | Cancel anytime. |
| **Yearly** | **RM 119 / year** | Default selected. ~RM 9.92/mo. "SAVE 47%" tag. |
| Team (5 seats) | RM 399 / year | Per-employee progress + admin dashboard. |
| Enterprise | Custom | SSO, custom content, audit logs, on-prem option. |

### Paywall psychology

The paywall fires at moments of high motivation — **never cold**:

1. **Progress lock** — "You're 90% to Level 5. Boss Battle unlocks at Lv 5." Loss aversion + completion bias.
2. **Out of lives** — context-aware: "Wait 28 min, or play unlimited."
3. **Streak protection** — when a streak is at risk: "Don't lose your 14-day streak — Premium auto-saves it."
4. **Premium-only weekly challenge** — leaderboard FOMO surfaced on Sunday evening.
5. **Certificate teaser** — at 80% in any skill track: "Finish to earn your HR Compliance Certificate."
6. **Default-yearly framing** — yearly is pre-selected with a savings chip; monthly looks comparatively expensive.
7. **Social proof** — "4,200+ HR practitioners across Malaysia" with logos.

We do **not** use:
- Forced ads
- Confusing trial → auto-billing tricks
- Pay-to-win XP boosters (kills learning legitimacy)

---

## 6. Content engine — Legal Q&A

### Tagging schema

Each question carries:

```ts
type Question = {
  id: string;
  type: 'mcq' | 'tf' | 'scenario' | 'violation' | 'decision';
  law: 'EA1955' | 'Sabah' | 'Sarawak';
  topic: 'wages' | 'hours' | 'leave' | 'termination' | 'recruitment' | 'compliance';
  diff: 1 | 2 | 3 | 4 | 5;       // difficulty
  text: string;                   // the question
  setup?: string;                 // narrative for scenarios
  who?: string;                   // role label, e.g. "HR Manager, KL fintech"
  options: string[] | DecisionOption[];
  answer?: number;                // index into options (not used for decision)
  why: string;                    // rationale shown after answer
  source?: string;                // statute reference, e.g. "EA1955 s.60A(3)(a)"
  reviewedBy?: string;            // counsel sign-off ID
  reviewedAt?: string;            // ISO date
};
```

### Authoring pipeline

1. **Author** writes draft Q in admin CMS, picks tags + statute reference.
2. **Reviewer** (qualified Malaysian counsel) approves; signs off with name + date.
3. **Editor** fact-checks against the latest gazetted version of the act/ordinance.
4. Question goes live in the bank with `reviewedAt` stamp visible in admin only.
5. **Recurring audit**: every act/ordinance amendment triggers a review queue for all `affected` tags.

### Picker logic (game runtime)

For each session, draw N questions where:

```ts
poolFilter(q) =
  matchesMode(q.type) AND
  q.diff <= max(2, user.level) AND
  q.id NOT IN user.recentlySeen[last 24h]
```

If pool < N, relax difficulty constraint, then fall back to repeat with a "seen recently" badge to be transparent.

### Content scope at launch (MVP)

- 150 vetted questions: 90 MCQ, 30 T/F, 18 scenarios, 8 violations, 4 decisions.
- Coverage skew: EA1955 (60%), Sabah (20%), Sarawak (20%).
- Topic skew: termination & wages over-indexed (where most disputes actually arise).

---

## 7. UX flow

### First-run

```
Splash (logo)
  → 1-tap "Sign in with Google" or "Continue as guest"
  → Choose role: HR / Manager / Founder / Counsel / Curious
  → Choose jurisdiction(s): Peninsular / Sabah / Sarawak (multi-select)
  → 3-question onboarding quiz (calibrates starting level)
  → Land on Home (Quick Play CTA glowing)
```

### Home

- Top bar: brand · lives · streak · level/XP pill.
- Hero: bold headline + Quick Play (Blitz, 60s).
- Daily streak card: big number, week strip, MOTIVATION copy.
- Modes grid (3 cols desktop, 1 col mobile): 6 cards with lock pills for gated ones.
- Progress section: 5 skill tracks (animated bars) + badge wall.
- Leaderboard panel: weekly top 10, "You" row highlighted.
- Footer disclaimer.

### In-game

Full-screen modal slides up. Header has progress dots and timer. Each answer reveals the rationale; user advances at their pace. Results screen shows XP, accuracy, level-up, and a "Play another" CTA.

### Paywall

Triggered by mode lock, life depletion, or streak risk. Shows progress tease, three plans (yearly preselected), perks list, and trust line. CTA: "Continue → Unlock". Secondary: "Maybe later".

---

## 8. Database structure (PostgreSQL / Firestore-compatible)

```sql
-- USERS
CREATE TABLE users (
  id            UUID PRIMARY KEY,
  email         TEXT UNIQUE NOT NULL,
  display_name  TEXT,
  role          TEXT,             -- hr | manager | founder | counsel | other
  jurisdictions TEXT[],           -- {EA1955, Sabah, Sarawak}
  created_at    TIMESTAMPTZ DEFAULT now(),
  premium_tier  TEXT,             -- monthly | yearly | team | null
  premium_until TIMESTAMPTZ
);

-- PROGRESS  (one row per user)
CREATE TABLE progress (
  user_id    UUID PRIMARY KEY REFERENCES users(id) ON DELETE CASCADE,
  xp         INT NOT NULL DEFAULT 0,
  level      INT NOT NULL DEFAULT 1,
  streak     INT NOT NULL DEFAULT 0,
  last_played DATE,
  lives      INT NOT NULL DEFAULT 5,
  lives_refilled_at TIMESTAMPTZ DEFAULT now()
);

-- SKILL SCORES  (long form — easier to query for radars)
CREATE TABLE skill_scores (
  user_id  UUID REFERENCES users(id) ON DELETE CASCADE,
  track    TEXT,                  -- recruitment | termination | compliance | leave | wages
  pct      INT NOT NULL DEFAULT 0,
  PRIMARY KEY (user_id, track)
);

-- QUESTIONS  (versioned, soft-delete on amendment)
CREATE TABLE questions (
  id        TEXT PRIMARY KEY,
  type      TEXT NOT NULL,        -- mcq | tf | scenario | violation | decision
  law       TEXT NOT NULL,        -- EA1955 | Sabah | Sarawak
  topic     TEXT NOT NULL,
  diff      INT  NOT NULL,        -- 1..5
  text      TEXT NOT NULL,
  setup     TEXT,
  who       TEXT,
  options   JSONB NOT NULL,
  answer    INT,                  -- nullable for decision
  why       TEXT NOT NULL,
  source    TEXT,
  reviewed_by TEXT,
  reviewed_at TIMESTAMPTZ,
  active    BOOLEAN NOT NULL DEFAULT TRUE,
  created_at TIMESTAMPTZ DEFAULT now()
);
CREATE INDEX ON questions (active, type, diff);
CREATE INDEX ON questions (law, topic);

-- SESSIONS  (one row per played game)
CREATE TABLE sessions (
  id          UUID PRIMARY KEY,
  user_id     UUID REFERENCES users(id),
  mode        TEXT NOT NULL,
  started_at  TIMESTAMPTZ DEFAULT now(),
  ended_at    TIMESTAMPTZ,
  xp_earned   INT,
  correct     INT,
  total       INT,
  accuracy    INT
);

-- ANSWERS  (analytics gold — every interaction)
CREATE TABLE answers (
  id          BIGSERIAL PRIMARY KEY,
  session_id  UUID REFERENCES sessions(id) ON DELETE CASCADE,
  question_id TEXT REFERENCES questions(id),
  choice      INT,
  correct     BOOLEAN,
  response_ms INT,
  answered_at TIMESTAMPTZ DEFAULT now()
);

-- BADGES (catalogue + grants)
CREATE TABLE badges (
  key        TEXT PRIMARY KEY,
  name       TEXT NOT NULL,
  icon       TEXT,
  criteria   TEXT
);
CREATE TABLE user_badges (
  user_id    UUID REFERENCES users(id) ON DELETE CASCADE,
  badge_key  TEXT REFERENCES badges(key),
  earned_at  TIMESTAMPTZ DEFAULT now(),
  PRIMARY KEY (user_id, badge_key)
);

-- LEADERBOARDS (denormalised for cheap reads)
CREATE MATERIALIZED VIEW leaderboard_weekly AS
  SELECT user_id, SUM(xp_earned) AS xp_week
  FROM sessions
  WHERE started_at >= date_trunc('week', now())
  GROUP BY user_id
  ORDER BY xp_week DESC
  LIMIT 100;

-- SUBSCRIPTIONS (Stripe / iAP)
CREATE TABLE subscriptions (
  user_id      UUID PRIMARY KEY REFERENCES users(id) ON DELETE CASCADE,
  provider     TEXT,             -- stripe | apple | google
  external_id  TEXT,
  tier         TEXT,
  starts_at    TIMESTAMPTZ,
  ends_at      TIMESTAMPTZ,
  status       TEXT
);
```

---

## 9. UI wireframe descriptions

### 9.1 Home (mobile, 375 × 812)

```
┌──────────────────────────────────┐
│ [K] Kanun  · HR Law, played      │  top bar — sticky
│   ♥♥♥♥♥   🔥3   Lv ▮▮▮ [4]      │
├──────────────────────────────────┤
│ ▪ MASTER MALAYSIAN LABOUR LAW    │
│ Learn the Employment Act         │
│ the way you learned Tetris.      │
│ [▶ Quick play · 60s] [Browse]    │
│  230 Q · 3 jurisdictions ·       │
│  5 tracks · 22 levels            │
├──────────────────────────────────┤
│ ▮▮ DAILY STREAK                  │  black card
│ 3                                │
│ Best yet — 3 days in a row.      │
│ [M][T][W*][T][F][S][S]           │
├──────────────────────────────────┤
│ Today's modes        Refill 28:14│
│ ┌─────────┐ ┌─────────┐         │
│ │ ⚡ Blitz│ │ ✓ T/F   │         │
│ │ +18 XP  │ │ +12 XP  │         │
│ └─────────┘ └─────────┘         │
│ ┌─────────┐ ┌─────────┐ Lv 3 🔒│
│ │ 💬 Scen │ │ 🔍 Spot │         │
│ └─────────┘ └─────────┘         │
│ ┌─────────┐ ┌─────────┐ Boss   │
│ │ ☰ Decid │ │ ⚔ Boss  │         │
│ └─────────┘ └─────────┘         │
├──────────────────────────────────┤
│ Your progress                    │
│ Recruitment    ████░░░░ 42%     │
│ Termination    ██████░░ 61%     │
│ Compliance     ███░░░░░ 30%     │
│ Leave          █████░░░ 55%     │
│ Wages          ██████░░ 65%     │
│ ★🔥▲· · ·  badges                │
├──────────────────────────────────┤
│ This week's leaderboard          │
│ 1  Aisyah K. KL · Lv14   4,820  │
│ 2  Ravi P.   PG · Lv13   4,490  │
│ ...                              │
│ 6  YOU       KL · Lv 4   1,310  │← highlighted accent-soft
└──────────────────────────────────┘
```

### 9.2 In-game (Quiz Blitz)

```
┌──────────────────────────────────┐
│ Quiz Blitz [5×15s]  ●●○○○   12  │  timer warns under 6s
├──────────────────────────────────┤
│ [EA1955] [hours] [diff 2]        │
│ The maximum normal working       │
│ hours per week under EA1955      │
│ (post-2023) is:                  │
│                                  │
│ [A] 40 hours                     │
│ [B] 45 hours      ← selected     │
│ [C] 48 hours                     │
│ [D] 50 hours                     │
│                                  │
│ ✓ Correct                        │
│ Section 60A reduced the max from │
│ 48 to 45 hours per week,         │
│ effective 1 January 2023.        │
├──────────────────────────────────┤
│ 2 of 5         [Next →]          │
└──────────────────────────────────┘
```

### 9.3 Boss Battle header

```
┌──────────────────────────────────┐
│ Boss Battle · The Auditor [1L 3Q]│
│ ⚖ Datin Sharifah, KESUMA Inspector│
│   Audit visit — answer right or  │
│   die.                           │
│   ▰▰▰▰▰▰▱▱▱  HP                │
└──────────────────────────────────┘
```

### 9.4 Paywall

```
┌──────────────────────────────────┐
│ Unlock Premium  [Lv 5+]          │
├──────────────────────────────────┤
│ 🔒 Boss Battle is locked at Lv 5│
│ You're 80% there. Unlock all     │
│ levels and unlimited play.       │
│                                  │
│ Your progress to Lv 5     80%   │
│ ████████░░                       │
│                                  │
│ ┌──────┐ ┌────────┐ ┌────────┐  │
│ │Month │ │ Yearly │ │Team    │  │
│ │RM19/m│ │RM119/y │ │RM399/y │  │
│ │      │ │SAVE47% │ │5 seats │  │
│ └──────┘ └────────┘ └────────┘  │
│                                  │
│ ✓ Unlimited lives                │
│ ✓ All levels & jurisdictions     │
│ ✓ Boss Battles + Ranked          │
│ ✓ Streak protection              │
│ ✓ HR Compliance Certificate      │
│                                  │
│ Join 4,200+ HR practitioners.    │
├──────────────────────────────────┤
│ [Maybe later] [Continue → Unlock]│
└──────────────────────────────────┘
```

---

## 10. Retention mechanics

| Mechanic | Trigger | Reward |
|---|---|---|
| **Daily login reward** | First visit each day | Lives refill cap +1 (today only); 5 XP grant |
| **Streak system** | Consecutive daily plays | Cosmetic flame, escalating bonuses (10/25/50 XP at days 3/7/14) |
| **Streak freeze** | Streak ≥ 7, premium only | Auto-protects one missed day per week |
| **Push at 8pm MYT** | Streak at risk | "Don't lose your X-day streak" |
| **Push Sunday 6pm** | Premium | "Ranked Challenge starts in 2h" |
| **Re-engagement at D7 dormant** | Hasn't played 7d | "We miss you — your skill scores are decaying" with a free 1-life gift |
| **Weekly leaderboard reset** | Mon 00:00 MYT | New chase; top 3 get a profile flair |
| **HR Mastery Rank** | All-time XP cohorts | Apprentice → Practitioner → Specialist → Master → Grand Master, with soft-prestige cosmetics |

---

## 11. Tech stack

| Layer | Choice | Why |
|---|---|---|
| Frontend | **Next.js 14 (App Router) + React** | SEO landing pages + app shell; PWA wrapper for "install on home screen" |
| Styling | **Tailwind + a thin design-token layer** | Fast iteration, consistent spacing/colour |
| State | **Zustand** for game state, **TanStack Query** for server | Game state is local-first, syncs on session end |
| Auth | **Firebase Auth** (Google, Apple, email) | Cheapest path, handles MY auth needs |
| Backend | **Node.js (Fastify)** + tRPC | Typed end-to-end; small surface |
| DB | **PostgreSQL** on Supabase | Relational fits questions/sessions/answers cleanly; row-level security |
| Realtime / leaderboards | **Redis sorted sets** | Cheap weekly resets; cached snapshots into a CDN |
| Payments | **Stripe** for web; Apple/Google IAP for mobile | Standard rails |
| Hosting | **Vercel** for frontend; **Fly.io** or **Railway** for API | One-click MY-region deploys |
| Analytics | **PostHog (self-hosted)** | Funnel tracking + session replay; PDPA-friendly |

---

## 12. MVP scope (3 months to first paying user)

**In scope**
- Modes: **Quiz Blitz** + **True/False Rush** (the 2 mandated MVP modes)
- 150 reviewed questions covering EA1955, Sabah, Sarawak
- Levels 1–5 free, paywall hits at Lv 5
- Lives, streaks, weekly leaderboard
- Yearly subscription only at launch (RM 119/yr) via Stripe
- PWA on web; iOS + Android wrappers in month 4

**Out of scope for MVP**
- Scenario, Violation, Decision, Boss Battle (kept as the v1.1 expansion — the demo file ships them anyway as "preview" content to validate)
- Team/Enterprise dashboard
- Certificates (gated behind track 100% — won't realistically hit at launch)
- Apple/Google IAP (web first; saves Apple's 30%)

**Success metrics for MVP**
- D1 retention ≥ 40%
- D7 retention ≥ 18%
- Median session length 1.8–3.0 minutes
- Free → paid conversion ≥ 3% by day 14
- Avg sessions / paying user / week ≥ 4

---

## 13. Risks & mitigations

| Risk | Mitigation |
|---|---|
| **Legal accuracy drift after amendments** | Counsel sign-off on each Q; statute version tags; review queue triggered by gazette monitor |
| **"This is just a quiz, not real training"** perception | Track-completion certificates with sign-off; case-citation footers on every answer |
| **Paywall feels mercenary** | Paywall only at high-motivation moments (lock, lives, streak risk). No interstitials. |
| **Sabah/Sarawak content thin** | Recruit a Sarawak-based counsel as content lead; weight rewards toward East-Malaysia content during ramp |
| **Apple 30% on subscriptions** | Web-first launch; mobile IAP added once unit economics stabilise |
| **PDPA / data residency** | Self-hosted PostHog; database in MY-region (Singapore) — communicated in the privacy policy |

---

## 14. What's in `index.html`

The shipped demo is a **single self-contained HTML file** (no build step) that proves the loop end-to-end:

- All six game modes are playable on real (vetted) statutory questions.
- XP, level, streak, lives, skill tracks, and badges all persist via `localStorage` so the next visit feels personal.
- The paywall fires correctly at Lv 5 and on life depletion, with the yearly tier pre-selected and SAVE 47% framing.
- The leaderboard inserts the user's row dynamically and re-sorts as their XP grows.
- Mobile-first layout collapses cleanly to one column at 540px.

Open `index.html` in any modern browser. State resets if you clear site data.
