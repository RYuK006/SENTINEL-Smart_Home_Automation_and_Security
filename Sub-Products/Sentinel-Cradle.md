# Sentinel Cradle: Product Design Specification

## Section 1: Product Overview
The Sentinel Cradle is an advanced, edge-AI powered infant and toddler safety monitor designed to operate completely locally, ensuring absolute privacy for nursery environments. Unlike traditional baby monitors that stream vulnerable video to the cloud or simple audio monitors that lack intelligence, the Cradle utilizes 60GHz mmWave radar to monitor breathing and heart rate without any physical contact or wearables. It classifies audio locally to distinguish between crying, coughing, or breaking glass. It integrates directly into the Sentinel ecosystem, allowing the nursery to automatically lock its doors and deploy smart window shields if a perimeter threat is detected while the infant is sleeping.

## Section 2: Problem Statement
Parents are heavily reliant on baby monitors, but the current market is fundamentally flawed. Cloud-connected Wi-Fi cameras are notoriously vulnerable to hacking, leading to numerous documented cases of unauthorized individuals viewing and speaking to children. Furthermore, wearable vitals monitors (like smart socks) can cause skin irritation, false alarms if kicked off, and battery anxiety. Parents need enterprise-grade biological monitoring that doesn't compromise digital security or physical comfort.

## Section 3: Market Research and Existing Solutions
1. **Nanit Pro Camera**: Excellent computer-vision based breathing monitoring, but it requires the baby to wear specific patterned clothing (breathing bands), and it relies heavily on cloud processing and subscription models.
2. **Owlet Smart Sock**: Accurate pulse oximetry, but requires a physical wearable on a growing infant's foot, leading to occasional discomfort and battery management issues.
3. **Miku Pro Smart Baby Monitor**: Uses proprietary radar/vision fusion for contactless breathing. However, it is an isolated product that cannot orchestrate whole-home responses (e.g., it can't lock the nursery door if an intruder enters the house). Sentinel Cradle solves this via ecosystem integration.

## Section 4: Technical Architecture
*   **Sensing Pipeline**: A 60GHz Frequency Modulated Continuous Wave (FMCW) mmWave radar sensor captures micro-movements (chest displacement). An I2S MEMS microphone captures ambient nursery audio.
*   **Processing Pipeline**: The ESP32-S3 microcontroller handles the local TinyML audio classification. The mmWave module handles its own radar point-cloud processing, outputting clean serial data (respiratory rate, presence) to the ESP32.
*   **Communication Pipeline**: The ESP32-S3 communicates securely via local MQTT over Wi-Fi directly to the Sentinel Hub.
*   **Action Pipeline**: If apnea (breathing cessation) is detected, the Hub immediately triggers a high-priority alert to the parents' smartphones and flashes the master bedroom lights.

## Section 5: Hardware Component Selection
*   **Microcontroller**: ESP32-S3 WROOM module — Provides Wi-Fi and vector instructions for audio ML inference. Cost: ~₹600.
*   **Radar Sensor**: SeeedStudio 60GHz mmWave Human Breathing and Heartbeat Sensor (MR60BHA2) — Capable of detecting respiratory rate from 0.4m to 1.5m. Cost: ~₹4,500 (Robu.in).
*   **Microphone**: INMP441 Omnidirectional I2S MEMS Microphone — Clean digital audio for cry classification. Cost: ~₹300.
*   **Camera (Optional/Local Only)**: OV2640 or standard ESP32-CAM module for local RTSP streaming without cloud egress. Cost: ~₹500.
*   **Total Prototype BOM**: ~₹5,900 INR.

## Section 6: AI and Software Layer
Audio classification is powered by TensorFlow Lite Micro. A CNN (Convolutional Neural Network) trained on audio spectrograms classifies sounds into: Silence, Normal Noise, Baby Crying, Coughing/Choking, and High-Impact (glass break/door kick). The mmWave sensor utilizes proprietary DSP algorithms onboard the SeeedStudio chip to filter environmental noise and isolate the 0.2-0.5 Hz frequency of infant breathing. All inference occurs on-device; no audio is ever sent to a server.

## Section 7: SENTINEL Ecosystem Integration
*   **Perceive Layer**: Feeds localized nursery audio events and occupancy status to the Hub.
*   **Contain Layer**: If the Hub detects an active home intrusion via perimeter sensors, it sends a command to the nursery's smart lock to secure the room instantly.
*   **Escalate Layer**: If breathing stops for >20 seconds, the Hub bypasses normal verification and immediately triggers the "Medical Emergency" escalation ladder, waking parents and preparing to dial emergency services if not acknowledged.

## Section 8: Power and Connectivity
*   **Power**: Mains powered via a standard 5V USB-C adapter. Contains a small 18650 Li-ion backup battery (2500mAh) to survive grid power outages for up to 6 hours.
*   **Connectivity**: Local Wi-Fi (MQTT).
*   **Fallback**: BLE beaconing to the Sentinel Hub if the primary Wi-Fi router fails.

## Section 9: Privacy and Safety Constraints
*   **Privacy**: The Cradle explicitly lacks cloud connectivity. Video feeds are only accessible when the viewing device is on the same local subnet or via a secure, user-managed WireGuard VPN.
*   **Safety Constraints**: To mitigate strangulation risks, the device is designed to be rigidly mounted to the wall at least 1.5 meters away from the crib, utilizing the long-range capabilities of the 60GHz radar. It operates on low-voltage 5V DC to eliminate electrocution hazards.

## Section 10: Prototype Scope vs Production Scope
*   **Prototype Scope (Student Team)**: An ESP32-S3 paired with the SeeedStudio MR60BHA2 radar and an I2S mic. Capable of outputting live breathing rates to a local dashboard and triggering an MQTT alert when a "crying" audio model fires.
*   **Production Scope**: A beautifully designed, injection-molded enclosure with a motorized pan-tilt base, integrated infrared night vision, and medical-grade validation for the radar vitals algorithms to seek FDA/CE clearance as a medical device.

## Section 11: Known Risks and Mitigations
1. **Risk**: Radar interference from oscillating fans causing false breathing readings. **Mitigation**: Advanced DSP filtering within the mmWave module to ignore repetitive non-biological frequencies.
2. **Risk**: Power cord strangulation. **Mitigation**: Wall-mount requirement with rigid cable-concealment tracking included in the box.
3. **Risk**: Local network failure. **Mitigation**: Battery backup and BLE fallback communication to ensure critical apnea alerts still reach the Hub.
4. **Risk**: Overheating in a warm nursery. **Mitigation**: Ample passive thermal venting in the enclosure design.
5. **Risk**: Parent anxiety from false apnea alarms. **Mitigation**: Multi-second confirmation delays and correlation with audio (lack of crying/movement) before alarming.

## Section 12: Future Scope
Future iterations will integrate local generative AI to provide sleep-coaching analytics directly on the Hub. Furthermore, thermal imaging could be added to the sensor suite to detect infant fevers contactlessly, providing parents with a complete, hospital-grade, privacy-first diagnostic tool in the home.