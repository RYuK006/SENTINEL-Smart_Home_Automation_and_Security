# Sentinel Band: Product Design Specification

## Section 1: Product Overview
The Sentinel Band is an edge-AI powered wearable identity token and health monitor that serves as the primary human-to-system interface for the Sentinel ecosystem. While commodity smartwatches require complex pairing, constant charging, and cloud dependencies for data processing, the Sentinel Band operates strictly locally. It acts as a proximity-based BLE disarming token for the home, a continuous local vitals monitor for the elderly, a panic button trigger, and a fall-detection node. Crucially, it feeds directly into the "Verify" and "Escalate" layers of Sentinel — overriding camera false alarms if an authorized band is detected, or instantly triggering an emergency escalation if a severe fall or critical heart rate anomaly is recognized.

## Section 2: Problem Statement
Elderly residents and young children are often the most vulnerable during home emergencies but are the least likely to reliably operate complex smartphone apps or wall-mounted alarm panels. Furthermore, existing medical alert bracelets (like LifeAlert) are single-function, reactive devices that rely on cellular subscriptions and cannot integrate with a broader home automation network to unlock doors for paramedics or flash exterior lights during an emergency. The Sentinel Band bridges this gap by acting as a passive, unified health and identity token that commands the home environment dynamically.

## Section 3: Market Research and Existing Solutions
1. **Apple Watch / Garmin**: Excellent vitals accuracy, but extremely poor battery life (1-2 days), reliant on cloud syncing for advanced metrics, and closed ecosystems that do not natively interface with local DIY home security hubs.
2. **Traditional Medical Alert Pendants (e.g., LifeAlert)**: Reliable emergency calling, but entirely reactive. They cannot monitor ongoing vitals to predict emergencies, and they provide zero home automation integration (e.g., they cannot unlock the front door).
3. **Bluetooth Proximity Fobs (e.g., Abode Key Fob)**: Good for local disarming, but lack any biological telemetry or fall detection capabilities. Sentinel Band combines the local authority of a fob with the telemetry of a smartwatch, processed locally.

## Section 4: Technical Architecture
The Sentinel Band operates on a low-power Nordic nRF52 series architecture. 
*   **Sensing Pipeline**: A PPG (Photoplethysmography) sensor continuously samples heart rate and SpO2 at low frequencies, while a 6-axis IMU samples acceleration.
*   **Processing Pipeline**: Localized TinyML (Edge Impulse) models running directly on the nRF52 analyze the IMU data to distinguish between normal arm movements, hard impacts, and severe falls. 
*   **Communication Pipeline**: Utilizes BLE 5.0 Mesh to maintain a constant heartbeat with the Sentinel Hub or Sentinel Swarm nodes. 
*   **Action Pipeline**: Upon detecting a fall or receiving a manual panic button press, the Band shifts from advertising mode to an interrupt-driven GATT characteristic write, instantly alerting the Hub to bypass the normal verification ladder and jump to Escalation.

## Section 5: Hardware Component Selection
*   **Microcontroller**: Nordic nRF52840 SoC module (e.g., Holyiot-nRF52840) — Chosen for ultra-low power BLE mesh capabilities and sufficient ARM Cortex-M4F compute for Edge Impulse models. Cost: ~₹750 (Robu.in).
*   **Vitals Sensor**: MAX30102 Pulse Oximeter and Heart Rate Sensor — Chosen for proven reliability in wrist-based form factors. Cost: ~₹2,089 (Robu.in).
*   **IMU**: MPU6050 (or BMA400 for ultra-low power) — Essential for fall detection and sleep tracking. Cost: ~₹150.
*   **Actuator/Interface**: Simple tactile push button (Panic) and a low-power eccentric rotating mass (ERM) vibration motor for haptic feedback. Cost: ~₹50.
*   **Total Prototype BOM**: ~₹3,039 INR.

## Section 6: AI and Software Layer
The Band relies on **Edge Impulse** for on-device TinyML. The primary model is a Random Forest or simple Neural Network classifier trained on IMU telemetry to distinguish "Fall" vs "Daily Activity" (running, dropping arm). It runs entirely on the nRF52840. The model requires labeled time-series accelerometer data for training. Realistic accuracy for fall detection on the wrist is ~85-90%, which is why the Sentinel Hub cross-verifies band-detected falls with indoor camera feeds (if available) before calling an ambulance, minimizing false positives.

## Section 7: SENTINEL Ecosystem Integration
The Band integrates into:
*   **Perceive Layer**: Constantly feeds presence and room-level location data via BLE signal strength (RSSI) triangulation.
*   **Verify Layer**: Acts as an identity token. If a camera detects a human but the hub sees the Band's BLE Mac Address in the same room, it verifies the human as "Authorized".
*   **Escalate Layer**: A 3-second hold on the panic button skips the Perceive/Verify layers entirely and triggers the Hub's emergency AI-Voice Dispatch.

## Section 8: Power and Connectivity
*   **Power**: Powered by a small 3.7V 200mAh LiPo battery. Due to the nRF52's deep sleep states and low-frequency sampling, prototype battery life is estimated at 5-7 days.
*   **Charging**: Magnetic pogo-pin USB charging.
*   **Connectivity**: BLE 5.0. 
*   **Fallback**: If the Hub is offline, the Band attempts to connect to a paired smartphone via an emergency background app to push an SOS out over cellular.

## Section 9: Privacy and Safety Constraints
*   **Privacy**: Health telemetry (heart rate, sleep patterns) is never uploaded to the cloud. It is processed locally on the band or encrypted and stored on the local Sentinel Hub.
*   **Safety Constraints**: As a wearable that may be used by infants or the elderly, the casing must be hypoallergenic, non-choking, and completely sealed against moisture (IP67) to prevent battery short-circuits during bathing.

## Section 10: Prototype Scope vs Production Scope
*   **Prototype Scope (Student Team)**: A 3D-printed bulky wristband housing an off-the-shelf nRF52840 dev board, MAX30102 breakout, and MPU6050. Able to reliably detect a button press, stream raw HR data, and trigger a basic fall alert to a local Raspberry Pi.
*   **Production Scope**: A custom, miniaturized rigid-flex PCB potted in medical-grade silicone. Battery life optimized to 14+ days. FDA-cleared ECG capabilities instead of just basic PPG.

## Section 11: Known Risks and Mitigations
1. **Risk**: False positive fall detection from sports/clapping. **Mitigation**: Cross-validation with room cameras via the Hub before escalation.
2. **Risk**: BLE signal jamming or interference. **Mitigation**: Local haptic feedback on the band will alert the user if it loses connection to the Hub.
3. **Risk**: Battery death leaving user unprotected. **Mitigation**: Hub alerts homeowner's phone when Band hits 15% battery.
4. **Risk**: Skin irritation. **Mitigation**: Use of medical-grade silicone for the strap.
5. **Risk**: Identity spoofing (MAC cloning). **Mitigation**: Implementation of BLE secure connections and rotating cryptographic tokens between Band and Hub.

## Section 12: Future Scope
In 3-5 years, the Sentinel Band will transition from BLE to Ultra-Wideband (UWB) for centimeter-accurate indoor positioning. This will allow the home to know exactly where a resident is, enabling micro-targeted HVAC and lighting, as well as precise room-level lockouts during an active home invasion.