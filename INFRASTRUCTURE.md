# XLFitness AI Platform — Infrastructure Plan
**PM:** Donkey | **Owner:** Matthew McArdle  
**Last Updated:** 2026-03-13

---

## The Physical Setup

```
CEILING CAMERA NETWORK (tracking + face ID)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    📷  📷  📷  📷  📷  📷  📷  📷  📷
    |                                 |
    └──────── PoE Switch ─────────────┘
                   |
            Edge Server Room
           (Mac Mini cluster)
                   |
         ┌─────────┴──────────┐
         |                    |
   Cloud Sync            Local API
  (member data)       (real-time reps)
         |                    |
   Member App           📱 Tablet
   (iOS/Android)      (at each machine)
                            |
                    📷 Tablet Camera
                   (pose estimation)
```

---

## Layer 1 — Camera Network

### Ceiling Cameras (tracking + face ID)
| Spec | Recommendation |
|---|---|
| **Model** | Hikvision DS-2CD2T47G2-L (4MP ColorVu) or Axis Q6135-LE PTZ |
| **Resolution** | 4K for face ID, 2K minimum for tracking |
| **Coverage** | 1 camera per 4–6 machines + entrance cameras |
| **Connection** | PoE (Power over Ethernet) — single cable, power + data |
| **Qty for 120 machines** | ~25–30 ceiling cameras + 3–4 entrance cameras |
| **Cost estimate** | ~$300–600 per camera = $8,000–$18,000 |

### Tablet Cameras (pose estimation — rep counting)
One tablet per machine, front-facing camera does the pose work.
Already proven in our POC. No extra cameras needed.

| Spec | Recommendation |
|---|---|
| **Device** | iPad 10th Gen (10.9") or Samsung Galaxy Tab A9+ |
| **Mount** | VESA wall arm or machine-clamp mount |
| **Connection** | WiFi 6 or wired USB-C ethernet |
| **Cost per machine** | ~$400–600 AUD incl. mount |
| **Qty** | 120 (one per machine) — deploy in phases |

---

## Layer 2 — Edge Servers (The Brain)

**Why edge, not cloud?**
120 cameras × 1080p × 30fps = ~14 Gbps of raw video.
Sending that to the cloud = impossible latency + massive cost.
All AI inference runs **locally in the gym**.

### Recommended: Mac Mini M4 Pro Cluster

| | Spec |
|---|---|
| **CPU** | M4 Pro (12-core) |
| **Neural Engine** | 38 TOPS — runs multiple pose models simultaneously |
| **RAM** | 24GB unified memory |
| **Storage** | 512GB SSD (video buffer, model cache) |
| **Size** | 12.7cm × 12.7cm — fits in a server cabinet |
| **Cost** | ~$2,300 AUD each |
| **Qty needed** | 2–3 for 120 machines (each handles ~40–50 machine feeds) |

**Why Mac Mini?**
- Silent, fanless, reliable for 24/7 operation
- Apple Neural Engine runs TensorFlow and CoreML natively
- Easy to manage (macOS, SSH, no Linux config headaches)
- Can be remotely managed via your laptop

**Alternative: NVIDIA Jetson Orin NX**
- Purpose-built for AI edge inference
- 100 TOPS (more raw AI power)
- More Linux setup required
- ~$800 AUD each + rack/case
- Better if you want to run large vision models long-term

---

## Layer 3 — Network Infrastructure

```
Internet (NBN/Fibre)
        |
    Router/Firewall
        |
   Core Switch (managed, 10GbE)
        |
   ┌────┴────────────────────┐
   |                         |
PoE Switch × 3          WiFi APs × 4
(cameras)               (tablets + members)
```

| Component | Spec | Cost est. |
|---|---|---|
| **Core switch** | Ubiquiti UniFi 24-port 10GbE | ~$800 |
| **PoE switches** | Ubiquiti UniFi 24-port PoE (×3) | ~$600 each |
| **WiFi APs** | Ubiquiti UniFi U6 Pro (×4) | ~$350 each |
| **Router/firewall** | Ubiquiti Dream Machine Pro | ~$700 |
| **Cabling** | Cat6A throughout | ~$3,000 installed |

**VLANs:**
- VLAN 10: Cameras (isolated, no internet access)
- VLAN 20: Tablets (gym staff + system only)
- VLAN 30: Member WiFi (internet only, isolated from system)
- VLAN 40: Edge servers (full access)

---

## Layer 4 — Software Stack

### On-Device (Each Tablet)
| Component | Technology |
|---|---|
| Pose estimation | MediaPipe BlazePose (33 keypoints) |
| Rep counting | Our LSTM model (TF.js) |
| Form scoring | Multi-feature classifier |
| Display UI | React PWA or native app |
| Communication | WebSocket to edge server |

### Edge Server
| Component | Technology |
|---|---|
| Person tracking | YOLOv8 + DeepSORT (re-ID across cameras) |
| Face recognition | InsightFace (open source, runs locally) |
| Session management | Node.js / FastAPI |
| Local database | PostgreSQL (session buffer) |
| Video processing | OpenCV + FFmpeg |

### Cloud (Supabase — hosted PostgreSQL + Auth)
| Component | Technology |
|---|---|
| Member database | Supabase (PostgreSQL) |
| Authentication | Supabase Auth (email + QR + face) |
| Real-time sync | Supabase Realtime (WebSocket push) |
| File storage | Supabase Storage (session videos if needed) |
| Cost | ~$25–100 AUD/month |

### Member App
| Component | Technology |
|---|---|
| Mobile app | React Native (iOS + Android) |
| Workout history | Supabase queries |
| Notifications | Expo Push Notifications |

### Admin Dashboard
| Component | Technology |
|---|---|
| Framework | Next.js + Supabase |
| Analytics | Machine utilisation, member trends, form stats |
| Access | Web browser, any device |

---

## Layer 5 — AI Models (Full Stack)

```
Frame arrives from camera
         ↓
[Person Detection] — YOLOv8 — "2 people in frame"
         ↓
[Person Re-ID] — OSNet — "Person #3 is Matt"
         ↓
[Pose Estimation] — MediaPipe BlazePose — 33 keypoints
         ↓
[Exercise Detection] — CNN on pose — "Lat Pulldown"
         ↓
[Rep Segmentation] — LSTM on sequences — "Rep started/ended"
         ↓
[Form Quality] — Multi-feature LSTM — "Score: 84/100"
         ↓
[Coaching Output] — Rule engine — "Slow down the return"
```

---

## Data Flow (End-to-End)

```
1. Matt enters gym
   → Entrance camera captures face
   → InsightFace matches to member DB
   → "Matt McArdle — Member since 2023"
   → Session opened in Supabase

2. Matt walks to lat pulldown
   → Ceiling cameras track Matt (re-ID)
   → "Matt is at machine #7"
   → Tablet at machine #7 activates
   → Shows Matt's profile + last session

3. Matt does a set
   → Tablet camera tracks pose
   → LSTM counts reps
   → Weight read from stack pin (CV) or load cell
   → Each rep scored 0–100
   → Live display on tablet

4. Matt finishes set
   → 90s no movement → set complete
   → Summary pushed to Supabase
   → Matt's app gets notification

5. End of workout
   → Full session saved
   → Personal records updated
   → Form trends updated
   → Matt gets summary in app
```

---

## Phased Rollout & Cost

### Phase 1 — POC (Now) — ~$0
- Browser app on one phone/tablet
- Manual weight entry
- Rule-based rep counting + NN form scoring

### Phase 2 — Pilot (Month 1) — ~$5,000–8,000
- 5 machines with tablets + mounts
- Edge server (1× Mac Mini M4)
- Basic WiFi infrastructure
- Member check-in via QR code
- Session persistence (Supabase)

### Phase 3 — Half Gym (Month 3) — ~$25,000–35,000
- 60 machines with tablets
- Ceiling camera network (partial)
- Person re-ID (no face ID yet)
- Full analytics dashboard
- Load cells on 20 priority machines

### Phase 4 — Full Gym (Month 6) — ~$60,000–90,000
- All 120 machines
- Full ceiling camera network
- Face ID at entrance
- Weight tracking on all machines
- Member app (iOS + Android)
- Complete analytics platform

### Phase 5 — Scale (Month 12+)
- Multi-gym rollout
- Cloud-based model updates
- Centralised analytics across locations
- Potential: licence to other gyms

---

## Running Costs (Monthly, Full Gym)

| Item | Cost/month |
|---|---|
| Supabase (Pro plan) | ~$50 AUD |
| Apple Developer (app store) | ~$15 AUD |
| Google Play | ~$3 AUD |
| Cloud backups (S3) | ~$20 AUD |
| **Total** | **~$90 AUD/month** |

Infrastructure is owned, not rented. No per-machine SaaS fees.

---

## Key Decisions Required (Matt)

1. **Tablet vs dedicated screen?** iPad gives camera + display in one. Dedicated monitor + separate camera is more flexible.
2. **Phased or all-at-once?** Recommend phased — prove ROI on 5 machines before 120.
3. **Member app in Phase 2 or 3?** Can start with tablet-only in Phase 2, app in Phase 3.
4. **Load cells or CV for weight?** CV first (free), load cells for accuracy later.
5. **Budget for Phase 2 pilot?** This drives the timeline.

