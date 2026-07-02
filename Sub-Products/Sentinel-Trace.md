# Sentinel Trace: Product Design Specification

## Section 1: Product Overview
Sentinel Trace is a covert, intelligent GPS and LTE-M tracking node designed to protect highly mobile or vulnerable assets, ranging from vehicles (cars/bicycles) to elderly family members prone to wandering. Unlike standard commodity GPS trackers that blindly ping a server every few minutes and drain their batteries, Trace is tightly integrated into the Sentinel home ecosystem. It remains in a deep-sleep, ultra-low power state, activating high-frequency tracking only when the Sentinel Hub's Intelligence layer declares an "Asset Breach" or when a vulnerable user breaches a geofence. It acts as the ultimate extension of the Sentinel Escalation and Document layers out into the wider world.

## Section 2: Problem Statement
Vehicle theft in India remains a massive issue, and Alzheimer's/dementia wandering is a critical safety concern for the elderly. Current solutions like Apple AirTags rely entirely on the presence of nearby iPhones (useless in rural areas or when intentionally disabled by a thief). Dedicated GPS trackers, on the other hand, require expensive subscriptions, suffer from terrible battery life due to constant polling, and operate in isolated apps rather than integrating with a unified home security response.

## Section 3: Market Research and Existing Solutions
1. **Apple AirTag / Tile**: Excellent battery life and zero subscription fees. However, they lack true GPS and cellular connectivity. If a stolen bicycle is taken to a rural area without iPhones nearby, it disappears forever. Sentinel Trace uses true GNSS/LTE.
2. **JioMotive / Standard OBD GPS Trackers**: Reliable true tracking, but they are easily identifiable by thieves, easily unplugged from the OBD port, and drain car batteries if left active. Trace is battery-independent and covert.
3. **Standalone Medical GPS Pendants**: Often bulky, carry a stigma for the elderly, and require manual charging every day. Trace mitigates battery issues by relying on the Hub to wake it only when necessary.

## Section 4: Technical Architecture
*   **Sensing Pipeline**: A highly sensitive GNSS receiver acquires GPS/GLONASS coordinates. A 3-axis accelerometer detects physical movement or tampering.
*   **Processing Pipeline**: A low-power microcontroller manages the state machine: Deep Sleep -> Wake on Motion -> Check Hub Status -> Transmit or Sleep.
*   **Communication Pipeline**: Utilizes LTE Cat-1 / LTE-M for long-range cellular data transmission directly to the owner's encrypted endpoint via MQTT/HTTPS.
*   **Action Pipeline**: When tracking is active, it streams live coordinates, heading, and speed, which the Sentinel Hub formats into a dynamic map link forwarded to emergency contacts or police.

## Section 5: Hardware Component Selection
*   **Connectivity & Compute**: Waveshare A7670E Cat-1/GNSS module — Provides reliable 4G LTE connectivity and GPS positioning in India. Can interface with an ESP32 for logic control. Cost: ~₹3,500 (Robu.in).
*   **Motion Sensor**: BMA400 Ultra-low power accelerometer — Triggers wake interrupts on movement. Cost: ~₹200.
*   **Battery**: 3.7V 3000mAh LiPo battery + TP4056 charging circuit. Cost: ~₹400.
*   **Total Prototype BOM**: ~₹4,100 INR.

## Section 6: AI and Software Layer
AI processing on the Trace node itself is minimal to preserve battery. The intelligence lies in the state-machine logic. The Edge AI resides on the Sentinel Hub, which analyzes the Trace's telemetry (speed, route deviation, time of day) to classify the event. For example, if Trace moves at 60 km/h at 3 AM while the house is in "Armed Away" mode, the Hub classifies it as "Vehicle Theft" and triggers the escalation ladder.

## Section 7: SENTINEL Ecosystem Integration
*   **Perceive Layer**: Detects unauthorized movement of the tagged asset.
*   **Verify Layer**: The Hub cross-references the movement with the home's armed state. If the Trace moves but the homeowner's Sentinel Band is present in the car, it verifies as "Authorized Travel" and stays silent.
*   **Escalate Layer**: Actively updates the emergency dispatch chain with live tracking coordinates if a theft or wandering incident is verified.

## Section 8: Power and Connectivity
*   **Power**: 3000mAh LiPo. Because it only wakes up when the Hub arms the system and motion is detected, the battery can last for 3-6 months on a single charge in covert mode.
*   **Charging**: USB-C or inductive wireless charging (for fully sealed, waterproof covert variants).
*   **Connectivity**: LTE Cat-1 / GPRS fallback.
*   **Fallback**: If cellular networks are jammed by thieves, the device logs all GPS points locally to flash memory and burst-transmits the entire route history the second connectivity is restored.

## Section 9: Privacy and Safety Constraints
*   **Privacy**: Real-time location tracking poses severe privacy risks if abused (e.g., stalking). Trace mitigates this by enforcing strict ecosystem coupling; the device is hardcoded to only communicate with the owner's specific Sentinel Hub cryptographic keys, and the hub logs every time the location is queried, ensuring an audit trail.
*   **Safety Constraints**: LiPo batteries in hot Indian vehicle interiors pose a fire risk. The device must utilize high-temperature tolerant battery chemistries (e.g., LiFePO4 for production) and robust thermal shielding.

## Section 10: Prototype Scope vs Production Scope
*   **Prototype Scope (Student Team)**: An ESP32 hooked to a Waveshare A7670E module and an accelerometer in a 3D-printed box. Demonstrating the ability to detect movement, wake up, acquire a GPS lock, and send coordinates to a local server over a 4G SIM card.
*   **Production Scope**: A miniaturized, fully waterproof (IP68) puck with a LiFePO4 battery, integrated eSIM for global roaming, and wireless charging, designed to be hidden inside bicycle frames or car door panels.

## Section 11: Known Risks and Mitigations
1. **Risk**: Cellular dead zones preventing SOS transmission. **Mitigation**: Local buffering of GPS route data for burst transmission upon reconnection.
2. **Risk**: GPS spoofing/jamming by sophisticated car thieves. **Mitigation**: Cell-tower triangulation (LBS) fallback via the A7670E module.
3. **Risk**: Battery thermal runaway in hot cars. **Mitigation**: Transition to LiFePO4 batteries in production.
4. **Risk**: Privacy abuse (stalking). **Mitigation**: Mandatory notification to the Sentinel Hub admin whenever the device is polled for location.
5. **Risk**: High recurring cellular costs. **Mitigation**: Optimized MQTT payloads keeping data usage to less than 1MB per month.

## Section 12: Future Scope
Future iterations of Sentinel Trace will integrate local mesh capabilities (LoRa), allowing it to communicate with the Sentinel Swarm even if it has no cellular SIM card, provided it is within a few kilometers of a Swarm node. This establishes a community-driven tracking network for lost pets or stolen bicycles without cellular fees.