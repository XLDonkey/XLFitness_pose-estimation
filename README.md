# XLFitness — Pose Tracking

**AI-powered rep counter for the Nautilus Nitro Lat Pulldown.**  
Part of the XL Fitness AI system — Australia's first tech-enabled gym.

---

## Live Demo

👉 **https://xldonkey.github.io/XLFitness_pose-estimation/alpha.html**

Open in Chrome. Point camera side-on to the machine. Start pulling.

---

## What It Does

- Detects a person using MoveNet (TensorFlow.js)
- Tracks elbow angle in real time
- Counts reps automatically — no buttons
- Neural network classifies movement quality
- Shows live rep progress bar

---

## Repo Structure

```
xlf-pose-tracking/
├── alpha.html          ← The demo (always works, always live)
├── index.html          ← GitHub Pages landing
├── office/             ← Internal dashboard
├── training-data/      ← Machine configs + training JSON
├── docs/               ← Product vision, infrastructure, setup guides
├── archive/            ← Old versions (kept for reference)
└── README.md
```

---

## How to Run

No build tools. No server. Just open `alpha.html` in Chrome.

For the full setup guide: `docs/PROTOTYPE_SETUP.md`

---

## Branch Strategy

| Branch | Purpose |
|--------|---------|
| `main` | Always stable — live demo runs from here |
| `dev` | Active development — test here before merging |
| `feature/xxx` | One branch per new feature |

**Never commit broken code to `main`.**

---

## The XL Fitness AI System (Roadmap)

This repo is one piece of a larger system:

| Repo | Status | Purpose |
|------|--------|---------|
| `xlf-pose-tracking` | ✅ Active | Rep counting, form scoring |
| `xlf-face-tracking` | 🔜 Planned | Face ID, member recognition |
| `xlf-weight-tracking` | 🔜 Planned | Weight stack reading |
| `xlf-member-data` | 🔜 Planned | Member profiles, session history |

---

## Training the Neural Network

1. Record 3 videos: **good reps**, **bad reps**, **false movements**
2. Upload to Google Drive → send link to Donkey
3. Donkey processes videos with MoveNet → extracts sequences → retrains NN → deploys

Current model: **v4** — 445 sequences, 75.1% CV accuracy

---

*Built by Donkey 🫏 for XL Fitness Australia*
