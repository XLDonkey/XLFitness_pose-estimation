# Prototype Setup Guide
**XLFitness AI Rep Counter — Lat Pulldown Prototype**

---

## What You Need

| Item | Spec | Notes |
|---|---|---|
| Tablet or phone | Any modern Android/iPhone | iPad preferred for screen size |
| Mount | Tripod + phone/tablet clamp | ~$30 from any camera store |
| Chrome browser | Latest version | Must be Chrome — not Safari |
| WiFi | Any connection for first load | Works offline after first load |

---

## Camera Position (Critical)

```
          📷 CAMERA
          Tripod here
          ~2–3 metres away
          Chest/shoulder height
          
          ←— Side-on, 90° to user ——→
          
                        🏋️ User at
                           Lat Pulldown
```

**Checklist:**
- [ ] Camera directly to the side of the machine (not in front, not behind)
- [ ] Distance: 2–3 metres from the user
- [ ] Height: roughly the user's chest/shoulder height when seated
- [ ] User's full upper body visible (head to hips at minimum)
- [ ] Good lighting — avoid strong backlight from windows behind user

---

## Setup Steps

1. **Mount the tablet** on a tripod at the correct position (see above)
2. **Open Chrome** on the tablet
3. **Go to:** `https://xldonkey.github.io/gym-ai-system/kiosk.html`
4. **Allow camera access** when prompted
5. Wait ~20 seconds for AI to load (one-time per session)
6. **It starts automatically** — no buttons to tap

---

## How It Works

1. User sits at the machine
2. Green skeleton appears on their body
3. Angle shows on elbow in degrees
4. User pulls bar down → AI counts the rep
5. **Only good reps count** — bad form and false movements are ignored
6. Hit **↺ Reset** between members

---

## What the Screen Shows

```
┌─────────────────────────────────────┐
│ XLFITNESS                    🟢 Counting │
│                                     │
│       [live camera + skeleton]       │
│                                     │
│                    ↓ Pull bar down  │
│                                     │
│  REPS              123°             │
│   8              elbow °            │
│  ✅ Good rep!                       │
│                                     │
│     [↺ Reset]  [🔄 Flip]           │
└─────────────────────────────────────┘
```

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Black screen | Hard refresh Chrome (hold reload → Hard Reload) |
| No skeleton | Check camera angle — must be side-on, not front-on |
| Not counting reps | Wrists must start overhead (gripping the bar) |
| Too many false counts | Retrain NN with more data (send Matt longer videos) |
| Camera permission denied | Settings → Chrome → Camera → Allow |

---

## URL
`https://xldonkey.github.io/gym-ai-system/kiosk.html`

For stakeholder demo (two-panel with NN brain visible):
`https://xldonkey.github.io/gym-ai-system/tablet.html`
