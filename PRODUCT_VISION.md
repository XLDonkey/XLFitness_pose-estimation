# XLFitness AI Gym System — Product Vision
**Owner:** Matthew McArdle / XL Fitness Australia  
**PM Agent:** Donkey  
**Last Updated:** 2026-03-13

---

## The Dream

Every member walks into XL Fitness. The gym knows who they are.
When they sit at a machine — it greets them, watches their form,
counts their reps, records the weight, and logs everything to their profile.
Zero friction. Zero staff involvement. Pure data.

---

## System Architecture

```
┌─────────────────────────────────────────────────────────┐
│  ENTRY POINT                                            │
│  Face ID camera at door → "Matt has arrived"           │
│  Member session opened in cloud                        │
└─────────────────────┬───────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────┐
│  GYM FLOOR — OVERHEAD CAMERA NETWORK                   │
│  • Zone cameras cover every machine                    │
│  • Person re-ID tracks Matt across the gym floor       │
│  • "Matt is now at Nautilus Nitro Lat Pulldown"        │
│  • Machine session auto-opened                         │
└─────────────────────┬───────────────────────────────────┘
                      │
┌─────────────────────▼───────────────────────────────────┐
│  MACHINE LAYER (per machine)                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐ │
│  │ Tablet Cam   │  │ Weight Sense │  │ Tablet Screen│ │
│  │ Pose est.    │  │ (load cell   │  │ Member profile│ │
│  │ Rep counting │  │  or CV stack)│  │ Reps, weight │ │
│  │ Form scoring │  │              │  │ Form score   │ │
│  └──────┬───────┘  └──────┬───────┘  └──────────────┘ │
└─────────┼─────────────────┼───────────────────────────┘
          │                 │
┌─────────▼─────────────────▼───────────────────────────┐
│  EDGE SERVER (one per gym — Mac Mini or Jetson)        │
│  • Receives all camera feeds                          │
│  • Runs inference at scale                            │
│  • Real-time session management                       │
│  • Member database sync                               │
└─────────────────────┬──────────────────────────────────┘
                      │
┌─────────────────────▼──────────────────────────────────┐
│  CLOUD / MEMBER APP                                    │
│  • Full workout history                               │
│  • Form trends over time                              │
│  • Personal records                                   │
│  • Gym owner analytics dashboard                      │
└────────────────────────────────────────────────────────┘
```

---

## What Shows on the Tablet (Member View)

```
┌────────────────────────────────┐
│  👋 Hey Matt                  │
│  Lat Pulldown · Set 3         │
│                                │
│  REPS        WEIGHT           │
│   8           62.5 kg         │
│                                │
│  FORM SCORE                   │
│  ████████░░  84 / 100         │
│                                │
│  💬 Good depth — slow         │
│     down the return phase      │
│                                │
│  LAST SESSION                 │
│  3×10 @ 60kg — avg 81/100    │
└────────────────────────────────┘
```

---

## Technology Stack

| Layer | Now (POC) | Production |
|---|---|---|
| Pose model | MoveNet Lightning (17kp) | MediaPipe BlazePose (33kp) |
| Rep counting | Rule-based thresholds | LSTM on pose sequences |
| Form scoring | MLP (5 features) | Multi-feature LSTM |
| Inference location | On-device browser | Edge server per gym |
| Weight tracking | Manual / none | CV stack reading → load cells |
| Person ID | Manual / occupancy | Face ID at entry + person re-ID |
| Session storage | None | Supabase / Firebase |
| Member profile | None | React Native app + tablet UI |
| Analytics | None | Gym owner dashboard |

---

## Weight Tracking Options (ranked by cost/complexity)

1. **CV reading stack pin position** — zero hardware cost, camera reads which pin is inserted in weight stack. ~80% accurate
2. **Load cells on weight stack** — ~$80/machine installed, 99% accurate. Best long-term
3. **Smart cable force sensor** — measures actual load on cable, accounts for friction. Premium option
4. **Manual entry on tablet** — simplest interim, member types weight

**Recommendation:** CV stack reading for Phase 1, load cells for Phase 3+

---

## Roadmap

### ✅ Phase 0 — Proof of Concept (Done)
- Browser-based pose estimation + rep counting
- MLP form classifier trained on real reps
- Stakeholder demo page

### 🔴 Phase 1 — Working Demo (This Week)
- Skeleton confirmed working on phone/tablet
- LSTM trained on 3×10min videos
- Accurate rep counting on Nautilus Nitro Lat Pulldown
- Form score + coaching cues working

### 🟡 Phase 2 — Single Machine Pilot (Month 1)
- Tablet mounted at lat pulldown machine
- Session persistence (local → cloud)
- Simple member check-in (QR or NFC tap at machine)
- Weight tracking via CV stack reading
- Basic member profile on tablet

### 🟢 Phase 3 — 5 Machine Rollout (Month 2)
- Per-machine calibration tool (10 reps → auto-tune)
- 5 machines live: lat pulldown + 4 others
- Real member sessions being logged
- Gym owner dashboard v1 (reps/machine, usage hours)

### 🔵 Phase 4 — Full Gym (Month 3–4)
- All 120 machines
- Overhead camera network installed
- Person re-ID tracking across floor
- Member app (iOS + Android)
- Full analytics dashboard

### 🟣 Phase 5 — The Dream Product (Month 6+)
- Face ID at entry — fully frictionless
- Personalized coaching ("your form is deteriorating — rest?")
- Injury risk detection (form breakdown patterns)
- Machine utilisation analytics (which machines need more?)
- Cross-gym benchmarking ("you're in the top 20% for lat pulldown form")

---

## What Matt Does
1. Film training videos (good form / bad form / false movements)
2. Provide gym access for mounting tablets
3. Stakeholder relationships + commercial decisions

## What the Agents Do
- **Donkey** — PM, roadmap, coordination, system design
- **Forge** — Frontend, browser app, tablet UI
- **Data Agent** — Video processing, training data pipeline
- **Model Agent** — LSTM training, model evaluation, deployment
- **Analytics Agent** — Dashboards, session storage, member app backend

---

## North Star Metric
**Form score improvement per member per month.**
Not just rep counts — better form over time = fewer injuries, better results, happier members, premium positioning for XL Fitness.

