# Sentinel Rover: Product Design Specification

## Section 1: Product Overview
Sentinel Rover is an autonomous, ground-based indoor patrol robot acting as a mobile perception node for the Sentinel ecosystem. While standard security systems rely on static cameras with fixed blind spots, the Rover can dynamically navigate the floorplan to investigate anomalies. If a Swarm node triggers outside, or a glass-break sensor fires in the kitchen, the Sentinel Hub can dispatch the Rover to the exact location to provide live video and thermal verification before waking the homeowner or calling the police. It docks automatically to charge and serves as a physical, mobile deterrent to intruders.

## Section 2: Problem Statement
Static indoor cameras are invasive. Many homeowners refuse to install cameras in hallways or living areas due to privacy concerns, leaving vast blind spots in their security coverage. When an alarm triggers (e.g., a door sensor), the homeowner has no way to visually verify if it was an intruder or just the wind blowing the door open. A mobile robot solves this by keeping cameras stowed away at a charging dock during normal hours, only moving into the home to stream video when an anomaly actually requires investigation.

## Section 3: Market Research and Existing Solutions
1. **Amazon Astro**: The most famous home robot. However, reviews indicate it suffers from severe navigation issues (getting stuck on rugs), is incredibly expensive, requires cloud subscriptions, and its patrol functions often trigger false alarms on coats or pets. It is a "robot for the sake of a robot" rather than a dedicated security tool.
2. **Enabot Ebo Air**: A small, affordable camera bot. However, it is largely a toy for pet monitoring. It lacks robust autonomous navigation to specific rooms on command and operates outside the primary home security ecosystem.
3. **Moorebot Scout**: Good localized AI, but struggles with complex obstacle avoidance. Sentinel Rover aims to integrate directly with the central Hub's intelligence rather than relying solely on onboard compute.

## Section 4: Technical Architecture
*   **Sensing Pipeline**: A VL53L0X Time-of-Flight (ToF) sensor array for forward obstacle avoidance. Bottom-facing IR sensors for cliff/stair detection. An ESP32-CAM provides the primary visual feed.
*   **Processing Pipeline**: The Rover relies on a split-compute architecture. Low-level motor control and obstacle avoidance (stopping before hitting a wall) run locally on the Rover's microcontroller. Heavy pathfinding and room mapping are offloaded via Wi-Fi to the central Sentinel Hub.
*   **Communication Pipeline**: Local Wi-Fi (UDP for low-latency video streaming, MQTT for movement commands).
*   **Action Pipeline**: The Hub sends coordinate commands (e.g., "Move to Node 4"). The Rover executes using mecanum wheels for omnidirectional strafing, investigates, and returns to its IR-beacon charging dock.

## Section 5: Hardware Component Selection
*   **Microcontroller**: ESP32-S3 — Handles Wi-Fi streaming and local sensor polling. Cost: ~₹600.
*   **Motor Control**: L298N or TB6612FNG motor drivers controlling 4 DC gear motors. Cost: ~₹300.
*   **Mobility**: 4x Mecanum wheels — Essential for tight indoor navigation and strafing without complex turning radii. Cost: ~₹1,200 (Robu.in).
*   **Sensors**: 3x VL53L0X ToF sensors (Front, Left, Right) for precise distance measuring. Cost: ~₹900. ESP32-CAM module. Cost: ~₹500.
*   **Total Prototype BOM**: ~₹3,500 INR (excluding chassis/battery).

## Section 6: AI and Software Layer
Because full Visual Simultaneous Localization and Mapping (vSLAM) is too computationally heavy for an ESP32, the AI is managed by the Hub. The Hub maintains a 2D grid map of the home. It issues simple vector commands to the Rover ("Forward 2 meters, strafe left 1 meter"). The Rover's local AI (simple heuristics) merely ensures it doesn't hit a chair while executing that vector. If the ESP32-CAM detects a human (using a lightweight MobileNet model on the Hub), the Hub overrides the patrol path and commands the Rover to track the intruder's bounding box.

## Section 7: SENTINEL Ecosystem Integration
*   **Perceive Layer**: The ultimate mobile eye, providing visual verification in rooms without static cameras.
*   **Verify Layer**: If a smoke alarm triggers, the Rover navigates to the room to confirm the presence of fire via visual/thermal data before the Hub calls the fire department.
*   **Contain Layer**: Equipped with a loud speaker, the Rover can emit deterrence audio ("Police have been dispatched, leave immediately") while physically following the intruder, applying massive psychological pressure.

## Section 8: Power and Connectivity
*   **Power**: 2x 18650 Li-ion batteries providing roughly 2 hours of continuous driving. 
*   **Charging**: Autonomous return-to-dock. The dock uses an IR beacon that the Rover's camera tracks to align itself with contact charging pads.
*   **Connectivity**: 2.4GHz Wi-Fi.

## Section 9: Privacy and Safety Constraints
*   **Privacy**: When docked, the Rover physically faces a blank wall or has a mechanical shutter over the camera, providing absolute assurance to the homeowner that they are not being watched.
*   **Safety Constraints**: The cliff sensors (bottom IR) are hardware-interlocked to the motor drivers to guarantee the Rover cannot drive itself down a flight of stairs and injure someone below, even if the Wi-Fi link drops.

## Section 10: Prototype Scope vs Production Scope
*   **Prototype Scope (Student Team)**: An acrylic chassis with mecanum wheels and an ESP32. Acknowledging the extreme difficulty of vSLAM, the prototype will rely on a "line-following" or "tape-guided" track on the floor for reliable point-to-point navigation, or operate via manual teleoperation from the Sentinel dashboard. 
*   **Production Scope**: A sleek, enclosed unit with a dedicated LiDAR puck (like a robot vacuum) for flawless, dynamic vSLAM mapping, self-righting mechanisms, and integrated thermal cameras.

## Section 11: Known Risks and Mitigations
1. **Risk**: Getting stuck on thick carpets or thresholds. **Mitigation**: Mecanum wheels chosen for their specific ability to climb minor ridges compared to standard caster wheels.
2. **Risk**: Wi-Fi dead zones in the house. **Mitigation**: The Rover caches its return path. If ping to the Hub is lost for >5 seconds, it automatically reverses its exact path until connectivity is restored.
3. **Risk**: Stepped on by humans in the dark. **Mitigation**: High-visibility LED strips illuminate when the Rover is in motion at night.
4. **Risk**: Pets attacking the Rover. **Mitigation**: The chassis is highly durable, and if the IMU detects it has been flipped over, it cuts power to the motors to prevent burning them out.
5. **Risk**: Docking failure. **Mitigation**: Wide, forgiving magnetic contact pads on the charging base to account for millimeter-level misalignment.

## Section 12: Future Scope
Future integration includes the Rover acting as a mobile mesh relay for the Sentinel Swarm, or incorporating a small manipulator arm to physically unlock a smart-deadbolt from the inside to allow paramedics into the home during a medical emergency if the primary smart-lock network fails.