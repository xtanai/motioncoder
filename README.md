# MotionCoder

[![License: Apache-2.0](https://img.shields.io/badge/License-Apache--2.0-blue.svg)](#-license) [![Status: MVP](https://img.shields.io/badge/status-MVP--planning-yellow)]() [![Python 3.11+](https://img.shields.io/badge/Python-3.11+-green)]()

**AI Gesture Control Engine** — translate hand gestures/signs into **precise CAD/DCC commands** in **real time**.

> ⚠️ Status: **Early-stage, pre-alpha**. Public API may change.

---

## ✨ TL;DR

* **Real-time** gesture → **intent + parameters** → CAD/DCC commands (e.g., Blender).
* **Modular**: capture/reconstruct → semantics → code/adapters.
* **Sensor-flexible**: choose **EdgeTrack**, **MVCore3D**,  **MVYUV3D**,  **MVRAW3D**, **Leap2Pose**, or **MVMono3D** (budget → quality).
* **On‑prem, low latency**, undo/redo grouping, reproducible runs.

---

## 🧩 Key Features

* **Stack (first experiments):** **ST-GCN**, **CTR-GCN** or **PoseC3D** as quick baselines for temporal gesture recognition.
* **Stack (re‑implementation for full control):** **PyTorch** (custom **GRU/TCN** or lightweight **Transformer**) for real‑time intent inference and easier on‑prem optimization.
* **Two learning modes:**
  * **Supervised:** train on labeled gesture sequences.
  * **Self‑supervised pretraining** → **supervised fine‑tuning:** learn representations first, then adapt with few labels.
* **Real‑time semantics:** 3D hand gestures → actionable **intents** with **live parameter updates** (e.g., *draw circle*, *split edge*, *move 5 mm*, *jog wheel*).
* **Gesture → command mapping:** simple **Intent‑DSL (JSON)**, e.g.:

  ```json
  {
    "intent": "mesh.cut",
    "params": {
      "plane_normal": [0, 0, 1],
      "offset_mm": { "$live": true, "source": "pinch_dial", "unit": "mm", "range": [-20, 20] }
    }
  }
  ```
  
  * *$live: true = this parameter is continuously driven by a gesture (instead of a fixed number).*
  * *source describes the gesture binding (e.g., pinch_dial, twist, handwheel).*
  * *unit / range are optional for UI/clamping and validation.*
  * *BSON/MessagePack optional later for low‑latency IPC.*
  
* **Adapters:** `Coder2XY` bridges intents to target apps (start with **Blender**; CAD/DCC next).
* **Deterministic pipelines:** sync/triggered multi‑view capture; reproducible outputs.
* **Human‑centric:** beginner‑friendly **core gestures**; no sign‑language proficiency required.
* **Accessible by design:** gestures/signs as a **first‑class interface**; optional speech/text feedback.

---

## 🎥 Recommended Sensors

Choose from six sensor modules — **EdgeTrack**, **MVCore3D**, **MVYUV3D**, **MVRAW3D**, **MVMono3D**, **Leap2Pose** — based on your **budget** and target **quality**. Docs & hardware picks: 👉 [Sensor Guide](https://github.com/xtanai/sensor-guide)

> **Note:** Many cameras/ToF sensors marketed as *3D hand tracking* are **not** suitable for this use case (reasons include **rolling shutter**, missing **HW sync/trigger**, low **FPS/high latency**, poor **NIR sensitivity**, unsuitable **optics/FOV**). See the Sensor Guide for specifics.


---

## 🚀 Quickstart (Dev)

> Python 3.10+, PyTorch ≥ 2.3 recommended. Linux/Windows tested; macOS possible (CPU/GPU varies).

```bash
# 1) clone
git clone https://github.com/xtanai/motioncoder.git
cd motioncoder

# 2) (optional) create env
python -m venv .venv && source .venv/bin/activate  # Windows: .venv\\Scripts\\activate

# 3) install
pip install -e .[dev]

# 4) run demo (synthetic stream)
python -m motioncoder.demos.hand_gesture_demo --ui minimal
```


---

## 🧪 Minimal API Sketch

```python
from motioncoder import MotionCoder
from motioncoder.capture import SensorConfig

# 1) configure sensors (choose one pipeline)
cfg = SensorConfig.from_preset("MVMono3D")  # or "Leap2Pose", "MVCore3D"

# 2) run in real-time
for event in mc.run():
    # event: {"intent": "move", "params": {"axis": "x", "value_mm": 5}}
    pass
```

---

## 🔧 Build & Dev Notes

* **Calibration**: checkerboard/charuco; store intrinsics/extrinsics in YAML; unit-tested loader.
* **Latency budget**: target end-to-end < **30–50 ms** (pipeline dependent).
* **Time sync**: hardware trigger where possible; fallbacks with precise software sync.
* **Undo/Redo**: macro groups per intent; deterministic param logging.



---

## 🤝 Contributing

Contributions are welcome! 

---

## ⚖️ License

Unless stated otherwise, code is released under **Apache-2.0**. See **LICENSE**.

---

## 🙏 Acknowledgements

* Community work on multi-view reconstruction, low-latency streaming, and real-time intent modeling.
* Thanks to early testers for feedback on ergonomics and core gestures.

---


