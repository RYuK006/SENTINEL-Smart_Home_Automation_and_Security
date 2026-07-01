# Sentinel: AI-Driven Autonomous Household Safety System

Sentinel is an advanced, edge-AI powered household safety and home automation operating layer. Moving beyond traditional "dumb" alarm systems, Sentinel perceives its environment using multi-modal sensor fusion, verifies threats locally, contains them physically, and escalates emergencies autonomously. 

This repository (Vault) serves as the complete product architecture, feature breakdown, and use-case matrix for the Sentinel ecosystem.

## How to Navigate This Vault
This project is structured as an **Obsidian Vault** (Second Brain). We highly recommend opening this directory directly in [Obsidian](https://obsidian.md/) to explore the beautifully interconnected graph of features, use-cases, and sub-products.

### Key Entry Points
*   **[[Sentinel Overview]]**: The master index connecting all aspects of the product. **Start here!**
*   **[[Features Overview]]**: A breakdown of Sentinel's capabilities organized by layer (Perception, Intelligence, Containment, Escalation).
*   **[[Use Cases Overview]]**: Real-world scenarios demonstrating how Sentinel's hardware and software orchestrate together.

### Core Philosophy: The Verification-Before-Action Ladder
No single sensor ever triggers a critical action alone. The system operates on a graduated ladder:
1. **Perceive:** Multi-sensor fusion (PIR + ultrasonic + vision + audio + vibration).
2. **Verify:** On-device AI (person/animal classification and dynamic risk scoring).
3. **Contain:** Physical response (smart locks, automated water/light deterrents).
4. **Escalate:** Owner verification (Press-1/Press-2 IVR) followed by structured AI-Voice Dispatch to emergency services.
5. **Document:** Tamper-proof, auto-packaged evidence clips.

## System Architecture Highlights
*   **Privacy-First:** All AI inference (face recognition, object classification, audio analysis) runs entirely on-device (e.g., Edge TPU, ESP32-S3). No cloud dependency for core processing.
*   **Closed Ecosystem:** To prevent critical security vulnerabilities, Sentinel intentionally avoids an Open API, operating as a secure, closed loop.
*   **Satellite Resilience:** If Wi-Fi and Cellular are jammed or cut by an intruder, Sentinel uses a fallback Satellite SOS payload (via Iridium/Skylo modules) to guarantee emergency message delivery.

## Sub-Products Ecosystem
The ecosystem extends far beyond the central hub into specialized hardware nodes tailored for specific scenarios (e.g., **Sentinel Cradle** for infant vitals, **Sentinel Trace** for vehicle theft, **Sentinel Whisper** for acoustic privacy, and **Sentinel Swarm** for vector control). 

See the **[[Sentinel Overview]]** for the complete list of 17 integrated hardware devices.
