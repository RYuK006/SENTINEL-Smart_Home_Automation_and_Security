# Dual-Modal Fusion Architecture

Integrates RuView (MIT License) by ruvnet — github.com/ruvnet/RuView.

## Section 1: The Fusion Paradigm
SENTINEL's Perceive and Verify layers achieve unprecedented accuracy through a specific Dual-Modal Fusion architecture running on the Raspberry Pi hub. This architecture simultaneously ingests WiFi Channel State Information (CSI) from ESP32-S3 nodes and visual data from ESP32-CAM modules. By combining these two orthogonal data streams, SENTINEL creates a unified, highly resilient perception model.

## Section 2: Modality Contributions and Failures
Each sensing modality brings unique strengths and suffers from distinct failure modes when operating alone:
*   **WiFi CSI (RuView on ESP32-S3)**: 
    *   *Strengths*: Sees entirely through walls, operates perfectly in pitch-black darkness, protects privacy by capturing zero visual data, and is immune to visual occlusions like shower curtains or blankets.
    *   *Failures*: Lacks any ability to perform visual identification (e.g., distinguishing between Aaron and a stranger) and is susceptible to heavy RF interference (microwaves).
*   **Optical Vision (ESP32-CAM)**: 
    *   *Strengths*: Provides absolute line-of-sight visual confirmation, enables facial recognition, and acts as the ground truth for legal evidence packaging.
    *   *Failures*: Fails completely in the dark without IR, cannot see through walls or closed doors, and represents a massive privacy violation if deployed in bedrooms or bathrooms.

## Section 3: The Verify Layer Fusion Logic
The Raspberry Pi hub serves as the fusion brain. It does not treat these sensors as independent alarms. Instead, the Verify layer mathematically combines them into a single Fused Confidence Score. 

If the ESP32-CAM detects a person but the RuView CSI nodes report an empty room, the hub flags a potential camera hallucination (e.g., a coat on a rack) and suppresses the alarm. Conversely, if RuView detects a human presence passing through a hallway in the pitch dark, but the camera sees nothing, the hub trusts the CSI data, flags a potential intruder operating in darkness, and triggers the adaptive lighting to illuminate the area for the camera to confirm.

This exact paradigm is demonstrated in RuView's own live dual-modal pose fusion demo (available at ruvnet.github.io/RuView/pose-fusion.html), proving the viability of merging CSI skeleton tracking with optical bounding boxes in real-time. This fused confidence score then feeds directly into the SENTINEL risk-scoring engine, guaranteeing that escalation only occurs when the laws of physics confirm a threat across two completely different spectrums.