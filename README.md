# SENTINEL
### AI-Driven Autonomous Household Safety Operating Layer

![Status](https://img.shields.io/badge/Status-Active%20Development-success)
![License](https://img.shields.io/badge/License-All%20Rights%20Reserved-critical)
![Platform](https://img.shields.io/badge/Platform-ESP32--S3%20%2B%20Raspberry%20Pi-blue)
![AI](https://img.shields.io/badge/AI-TensorFlow%20Lite%20Micro-orange)

Sentinel is not a sensor with a buzzer. It is a five-layer household safety operating system — Perceive, Verify, Contain, Escalate, and Document — that fuses motion, audio, vibration, and vision sensing with on-device edge AI to make intelligent decisions, take autonomous physical actions like locking doors and tracking intruders with pan-tilt cameras, and escalate genuine emergencies through a graduated response ladder ending in an AI-voice-mediated emergency call with satellite fallback when all terrestrial connectivity fails. It also functions as a full home automation layer for daily use.

## System Architecture
```text
┌─────────────────────────────────────────────────────┐
│                     SENTINEL                        │
├─────────────┬──────────────┬────────────────────────┤
│  PERCEIVE   │   VERIFY     │      CONTAIN           │
│  PIR        │  Risk Score  │  Smart Lock Logic      │
│  Ultrasonic │  TFLite Micro│  Pan-Tilt Tracking     │
│  Camera     │  Fusion Gate │  Adaptive Lighting     │
│  Mic        │  Vibration  │  Deterrent Audio       │
│  Vibration  │  Behaviour   │  Evidence Packaging    │
│  WiFi CSI   │              │                        │
├─────────────┴──────────────┴────────────────────────┤
│              ESCALATE                               │
│  Owner Verification (Press 1/2) → Contact Chain    │
│  → AI Voice Agent → Emergency Call → Satellite SOS │
├─────────────────────────────────────────────────────┤
│              DOCUMENT                               │
│  Hash-Verified Clips │ Incident Report │ Memory Graph│
└─────────────────────────────────────────────────────┘
```

## Core Principle: The Verification-Before-Action Ladder
No single sensor ever triggers an action alone, and no emergency escalation fires without a human confirmation window. By requiring cross-sensor agreement and running dynamic risk-scoring before taking physical action, Sentinel eliminates the false-alarm fatigue that plagues traditional security systems while remaining decisive when a true threat is verified.

## Repository Structure
```
/
├── Core-Components/       # Core system architecture and hardware details
├── Features/              # The 60 distinct features organized by system layer
├── Use-Cases/             # 35 specific scenarios demonstrating system orchestration
├── Sub-Products/          # The hardware nodes (Air, Band, Trace, Cradle, etc.)
└── Product-Report/        # The complete, unabridged product thesis
```

## Sub-Products
*   **Sentinel Band**: Wearable identity token providing panic buttons, vitals tracking, and fall detection.
*   **Sentinel Air**: Autonomous perimeter response drone for elevated visual tracking.
*   **Sentinel Cradle**: Edge-AI nursery hub tracking infant vitals via micro-radar and audio classification.
*   **Sentinel Trace**: Covert GPS/BLE vehicle tracker that wakes up on unauthorized movement.
*   **Sentinel Swarm**: Automated vector control mesh integrated with window grill sensors.
*   **Sentinel Whisper**: Ultrasonic audio jammer protecting confidential conversations from unauthorized recording.
*   **Sentinel Rover**: Autonomous indoor ground investigation bot providing mobile camera verification.

## Hardware Targets
*   **Edge Inference (Vision/Audio)**: ESP32-S3
*   **WiFi CSI Node (RuView)**: ESP32-S3 (Dedicated)
*   **Local Hub Reasoning**: Raspberry Pi 4/5
*   **Wearable**: nRF52
*   **Satellite Fallback**: Iridium 9604-class module

## Current Status & Roadmap
*   **Fully documented**: all features, use cases, architecture, product report
*   **Prototype scope**: sensor fusion node, risk scoring engine, pan-tilt tracking, press-1/press-2 IVR demo via Twilio, Sentinel Band panic + vitals
*   **Future scope**: Sentinel Air drone, satellite hardware integration, full multi-camera re-identification, Sentinel Swarm mesh


## Integrations
*   **[RuView](Integrations/RuView/RuView-Integration.md)**: SENTINEL integrates RuView (MIT License) by ruvnet — [github.com/ruvnet/RuView](https://github.com/ruvnet/RuView) to provide through-wall, camera-free human tracking, 17-keypoint pose estimation, and continuous biological vitals monitoring via WiFi Channel State Information (CSI).


---
Copyright 2026 Aaron. All rights reserved.