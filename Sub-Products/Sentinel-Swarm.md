# Sentinel Swarm: Product Design Specification

## Section 1: Product Overview
Sentinel Swarm is a decentralized, long-range wireless mesh of low-cost sensor nodes designed to stretch the perception layer of the Sentinel ecosystem across vast properties. Ideal for farms, rural estates, large industrial compounds, or multi-building residential plots, the Swarm utilizes LoRa (Long Range) RF technology to transmit simple binary telemetry (motion, vibration, gate opening) over kilometers. Rather than wiring expensive cameras across acres of land, a property owner can deploy dozens of independent, solar-powered Swarm nodes to create a highly responsive, invisible perimeter that instantly alerts the central Sentinel Hub of distant breaches.

## Section 2: Problem Statement
Securing a large property is prohibitively expensive. Standard Wi-Fi cameras and sensors max out at about 30-50 meters of range. Running Ethernet or trenching power cables across hundreds of meters of outdoor terrain is labor-intensive and costly. Consequently, the perimeters of large properties are often left completely blind, allowing intruders to cross the property line and approach the main residence undetected before the primary security system even registers their presence.

## Section 3: Market Research and Existing Solutions
1. **YoLink Smart Home Devices**: Utilizes LoRa for impressive 1/4 mile range. However, it relies on a cloud-connected YoLink hub and closed ecosystem APIs, meaning it cannot easily process localized AI logic without internet access.
2. **Ring Smart Lighting System**: Offers a proprietary bridge to connect distant outdoor motion sensors, but the range is limited compared to LoRa, and the system is deeply entrenched in the Amazon cloud ecosystem.
3. **Traditional Hardwired Alarm Systems (e.g., Honeywell)**: Extremely reliable, but strictly wired. Trenching wires across a 5-acre property is economically unviable for most consumers.

## Section 4: Technical Architecture
*   **Sensing Pipeline**: Each Swarm node contains an ultra-low power PIR sensor (motion), a reed switch (gate/door open), or a piezo vibration sensor (fence climbing).
*   **Processing Pipeline**: An ultra-low power microcontroller (e.g., STM32L or ESP32-C3) spends 99% of its life in deep sleep, waking only via hardware interrupt when a sensor triggers.
*   **Communication Pipeline**: The node uses an SX1276 LoRa module (868/915 MHz or 433 MHz depending on local ISM bands) to broadcast a tiny encrypted payload (Node ID + Event Type) to the central Hub. LoRa enables a star-topology range of 1km+ through foliage and structures.
*   **Action Pipeline**: The Hub receives the LoRa packet, decrypts it, maps the Node ID to a physical zone on the property, and escalates the event based on the Risk Scoring Engine (e.g., "Motion at North Gate at 2 AM").

## Section 5: Hardware Component Selection
*   **Microcontroller & Radio**: ESP32-C3 with integrated SX1276 LoRa module (e.g., Heltec WiFi LoRa 32 or custom board) — Offers deep sleep capabilities and long-range RF. Cost: ~₹1,500 (Robu.in).
*   **Sensors**: AM312 Mini PIR module (ultra-low quiescent current) and generic magnetic reed switches. Cost: ~₹150.
*   **Power**: 18650 Li-ion battery (2500mAh) coupled with a small 5V 100mA mini solar panel and a TP4056 charging IC. Cost: ~₹500.
*   **Total Prototype BOM (Per Node)**: ~₹2,150 INR.

## Section 6: AI and Software Layer
Because the Swarm nodes are incredibly power-constrained, they do not run heavy AI models locally. Their firmware is a simple, highly optimized C++ state machine (Interrupt -> Wake -> Transmit LoRa -> Sleep). The AI resides entirely on the central Sentinel Hub, which acts as the LoRa gateway. The Hub uses machine learning on the *pattern* of incoming LoRa packets to determine the threat. For example, a single node triggering briefly might be a deer; but nodes triggering sequentially in a line heading toward the house indicates a human walking a path.

## Section 7: SENTINEL Ecosystem Integration
*   **Perceive Layer**: Acts as the ultimate early-warning system, pushing the perception perimeter hundreds of meters outward.
*   **Contain Layer**: If Swarm Node A (Driveway Gate) triggers, the Hub can preemptively turn on the exterior lights at the main house (Adaptive Lighting) before the intruder even gets close.
*   **Document Layer**: The specific sequence of Swarm triggers is logged in the Incident Memory Graph to map the intruder's exact path of approach and escape.

## Section 8: Power and Connectivity
*   **Power**: Self-sustaining. The 18650 battery provides months of power, and the integrated mini solar panel easily tops off the battery during daylight hours, resulting in zero maintenance.
*   **Connectivity**: LoRa (Long Range RF). 
*   **Fallback**: If a node goes offline (destroyed or battery failure), the Hub notes the missing periodic "heartbeat" ping and alerts the user to a blind spot.

## Section 9: Privacy and Safety Constraints
*   **Privacy**: Because Swarm nodes transmit binary data (Motion: 1 or 0) rather than audio or video, they present zero privacy risk to individuals walking on public roads adjacent to the property.
*   **Safety Constraints**: The Li-ion batteries must be housed in IP67-rated waterproof enclosures with proper thermal relief to withstand the extreme summer temperatures of the Indian subcontinent without swelling or combusting.

## Section 10: Prototype Scope vs Production Scope
*   **Prototype Scope (Student Team)**: 2 or 3 Heltec LoRa32 boards with PIR sensors running on USB power banks, successfully transmitting motion events across a campus field to a central receiver hooked to the Sentinel Hub.
*   **Production Scope**: Fully potted, UV-resistant, IP67-rated stakes that can be hammered into the ground. Optimized custom PCBs drawing sub-microamp current in deep sleep, paired with high-efficiency solar films.

## Section 11: Known Risks and Mitigations
1. **Risk**: Wildlife triggering constant false alarms. **Mitigation**: AI pattern recognition on the Hub (ignoring random single-node hits, alerting only on directional sequences).
2. **Risk**: LoRa packet collisions if multiple nodes trigger simultaneously. **Mitigation**: Implement CAD (Channel Activity Detection) and randomized backoff delays in the node firmware before transmitting.
3. **Risk**: Radio jamming by sophisticated intruders. **Mitigation**: The Hub constantly monitors for excessive RF noise on the LoRa frequency and triggers a "Jamming Detected" high-severity alert if the spectrum is flooded.
4. **Risk**: Physical theft of the nodes. **Mitigation**: The nodes are cheap enough to be somewhat disposable, and their internal IDs are cryptographically bound to the specific Hub; they are useless if stolen.
5. **Risk**: Solar panel degradation due to dust. **Mitigation**: Battery sizing allows for 3+ months of operation with zero solar input, giving ample time for rain to wash the panels.

## Section 12: Future Scope
The production evolution of the Swarm will transition from a Star topology (all nodes talk to the Hub) to a true LoRa Mesh topology, where nodes can bounce signals off one another. This would allow the network to snake around massive physical obstructions (like mountains or large concrete complexes), theoretically extending the Sentinel perimeter infinitely as long as nodes maintain line-of-sight with each other.