# MotionCoder

[![License: Apache-2.0](https://img.shields.io/badge/License-Apache--2.0-blue.svg)](#-license)
[![Status: MVP](https://img.shields.io/badge/status-MVP--planning-yellow)]()
[![Python 3.11+](https://img.shields.io/badge/Python-3.11+-green)]()

**AI Gesture Control Engine** — translating hand gestures and signs into **precise CAD/DCC commands** in **real time**.

> ⚠️ **Status:** Early-stage, **pre-alpha**. Public APIs may changea.

---

## ✨ TL;DR

* **Real-time**: gestures → **intent + parameters** → CAD/DCC commands (e.g., Blender).
* **Modular stack**: capture & reconstruction → semantics → adapters.
* **On-prem, low latency** with deterministic execution, undo/redo grouping, and reproducible runs.

---

## 🧠 Why MotionCoder Needs Dedicated Hardware

Reliable gesture-driven authoring requires **stable, high-quality 3D keypoints** (true **3D pose**, not 2D projections).
Jittery or temporally unstable keypoints make precise CAD/DCC interaction impossible.

For this reason, MotionCoder is designed to work with **deterministic multi-view capture hardware**.
The reference implementation uses **EdgeTrack**, a dedicated on-edge stereo capture and reconstruction stack:

➡️ **EdgeTrack:** [https://github.com/xtanai/edgetrack](https://github.com/xtanai/edgetrack)

EdgeTrack provides **time-consistent, metric 3D keypoints**, forming a robust foundation for MotionCoder’s semantic layer.

---

## 🧩 Key Features

* **Baseline models (early experiments):**

  * **ST-GCN**, **CTR-GCN**, **PoseC3D** for fast iteration on temporal gesture recognition.
* **Custom re-implementation (full control):**

  * **PyTorch** with custom **GRU / TCN / lightweight Transformer** architectures for deterministic, low-latency, on-prem inference.
* **Two learning modes:**

  * **Supervised learning** on labeled gesture sequences.
  * **Self-supervised pretraining → supervised fine-tuning** for data efficiency.
* **Real-time semantics:**

  * 3D hand gestures mapped to actionable **intents** with **continuous parameter updates**
    (e.g. *draw circle*, *split edge*, *move 5 mm*, *virtual jog wheel*).
* **Gesture → command mapping** via a simple **Intent-DSL (JSON)**:

  ```json
  {
    "intent": "mesh.cut",
    "params": {
      "plane_normal": [0, 0, 1],
      "offset_mm": {
        "$live": true,
        "source": "pinch_dial",
        "unit": "mm",
        "range": [-20, 20]
      }
    }
  }
  ```

  * `$live: true` → parameter is continuously driven by a gesture.
  * `source` defines the gesture binding (e.g., `pinch_dial`, `twist`, `handwheel`).
  * `unit` / `range` enable validation, clamping, and UI feedback.
  * **BSON / MessagePack** planned for low-latency IPC.
* **Adapters:** `Coder2XY` bridges intents to target applications
  (starting with **Blender**; CAD/DCC tools follow).
* **Deterministic pipelines:** synchronized multi-view capture, reproducible outputs.
* **Human-centric design:** beginner-friendly core gestures; no sign-language expertise required.
* **Accessible by design:** gestures/signs as a **first-class interface**, with optional speech or text feedback.

---

## 🚀 Quickstart (Dev)

> Python 3.10+, **PyTorch ≥ 2.3** recommended.
> Linux and Windows tested; macOS possible (CPU/GPU dependent).

```bash
# 1) clone
git clone https://github.com/xtanai/motioncoder.git
cd motioncoder

# 2) (optional) create venv
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# 3) install
pip install -e .[dev]

# 4) run demo (synthetic stream)
python -m motioncoder.demos.hand_gesture_demo --ui minimal
```

---

## 🔧 Build & Dev Notes

* **Calibration:** checkerboard / Charuco; intrinsics & extrinsics stored in YAML (unit-tested loader).
* **Latency budget:** target end-to-end **< 30–50 ms** (pipeline dependent).
* **Time sync:** hardware triggers preferred; precise software fallback supported.
* **Undo/Redo:** macro groups per intent; deterministic parameter logging.

---

## 🤝 Contributing

Contributions are welcome — ideas, experiments, benchmarks, and adapters are especially appreciated.

---

## ⚖️ License

Unless stated otherwise, all code is released under **Apache-2.0**.
See **LICENSE** for details.

---

## 🙏 Acknowledgements

* Community research on multi-view reconstruction, low-latency streaming, and real-time intent modeling.
* Thanks to early testers for valuable feedback on ergonomics and core gesture design.

---
