# MotionCoder

[![License: Apache-2.0](https://img.shields.io/badge/License-Apache--2.0-blue.svg)](#-license)
[![Status: pre-alpha](https://img.shields.io/badge/status-pre--alpha-yellow)]()
[![Python 3.11+](https://img.shields.io/badge/Python-3.11+-green)]()

**MotionCoder** is a **gesture interaction engine** that translates spatial hand motion into **structured commands for 3D creation tools, VR environments, and professional authoring software**.

> ⚠️ **Status:** Early-stage **pre-alpha**. Public APIs, architecture, and internal modules may change.

---

## TL;DR

- Real-time pipeline: **motion → intent → application command**
- Built for **3D creation workflows**, not casual novelty interaction
- Designed for **low-latency, deterministic, and reproducible input**
- Integrates with **precise spatial tracking systems** such as **EdgeTrack**
- Intended for **Blender, Unreal Engine, VR workflows, CAD, simulation, and other professional tools**

---

## Why MotionCoder Needs Dedicated Hardware

Reliable gesture-driven authoring requires **stable, high-quality 3D keypoints** (true **3D pose**, not 2D projections).
Jittery or temporally unstable keypoints make precise CAD/DCC interaction impossible.

For this reason, MotionCoder is designed to work with **deterministic multi-view capture hardware**.
The reference implementation uses **EdgeTrack**, a dedicated on-edge stereo capture and reconstruction stack:

➡️ **EdgeTrack:** [https://github.com/edgetrackorg/overview](https://github.com/edgetrackorg/overview)

EdgeTrack provides **time-consistent, metric 3D keypoints**, forming a robust foundation for MotionCoder’s semantic layer.

---

## ML / Modeling Direction

MotionCoder explores multiple approaches for **temporal gesture interpretation** and real-time spatial interaction.

The goal is not only gesture recognition, but **stable mapping from motion → intent → application command** suitable for professional 3D creation workflows.

### Current and experimental directions

#### Baseline models (early experimentation)

Initial experiments evaluate established architectures for temporal gesture recognition:

- **ST-GCN**
- **CTR-GCN**
- **PoseC3D**

These models allow rapid experimentation with motion sequences and gesture classification.

---

#### Custom architectures

For production-oriented workflows, MotionCoder explores custom **PyTorch implementations** focused on deterministic and low-latency inference:

- **GRU-based temporal models**
- **TCN (Temporal Convolution Networks)**
- **Lightweight Transformer architectures**

The emphasis is on:

- predictable latency
- on-prem inference
- reproducible outputs
- minimal runtime overhead

---

### Learning modes

MotionCoder supports two training approaches:

**Supervised learning**

- trained on labeled gesture sequences
- optimized for specific workflows or applications

**Self-supervised pretraining → supervised fine-tuning**

- improves data efficiency
- enables adaptation to different environments or gesture sets

---

### Real-time semantic interpretation

Instead of producing raw skeleton streams, MotionCoder converts motion into **structured intents with continuous parameters**.

Example interactions:

- draw circle
- split edge
- move object by distance
- control virtual dial / jog wheel
- adjust tool parameters

This enables gestures to act as **continuous spatial controllers** rather than simple triggers.

---

### Gesture → command mapping

MotionCoder uses a simple **Intent DSL (JSON)** to map gestures to application commands.

Example:

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
````

Explanation:

* `$live: true` → parameter is continuously driven by a gesture
* `source` → defines the gesture binding (e.g. `pinch_dial`, `twist`, `handwheel`)
* `unit` / `range` → enable validation, clamping, and UI feedback
* **BSON / MessagePack** are planned for low-latency IPC transport

---

### Application adapters

MotionCoder uses adapter modules (`Coder2XY`) that translate intents into application commands.

Initial targets include:

* **Blender**
* **Unreal Engine**
* **VR environments**
* **CAD / DCC tools**

The adapter architecture allows MotionCoder to support multiple applications without modifying the core gesture engine.

---

### Deterministic pipelines

The system prioritizes:

* synchronized multi-view capture
* deterministic processing
* reproducible gesture interpretation
* stable parameter control

This is essential for professional authoring workflows.

---

### Human-centric interaction

MotionCoder focuses on:

* intuitive core gestures
* minimal learning curve
* no requirement for sign-language knowledge

The aim is to provide **natural spatial interaction for creators and developers**.

---

### Accessibility

Gestures and spatial motion are treated as **first-class input methods**.

Optional interaction layers may include:

* speech feedback
* textual confirmation
* visual UI feedback

---

### Future implementation direction

If the architecture proves stable and the concept successful, the core runtime may later be **reimplemented in C/C++** for lower latency, better resource control, and production-oriented deployment.

The **PyTorch-based research and training pipeline** would continue to serve as the main environment for model development and experimentation. This would allow newer model versions to be trained, evaluated, and exchanged more easily, while keeping the runtime layer stable and focused on execution.

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
