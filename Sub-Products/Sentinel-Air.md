# Sentinel Air: Product Design Specification

## Section 1: Product Overview
Sentinel Air is an autonomous, free-flying perimeter response drone designed as the ultimate mobile extension of the Sentinel Contain and Document layers. While static security cameras have fixed fields of view and ground-based patrols struggle with terrain, Sentinel Air provides an elevated, dynamic visual angle of an active threat. It launches from a weatherproof docking station only upon confirmed perimeter breach, tracks the subject from a safe altitude, captures high-resolution facial imagery, emits deterrence audio, and returns to base to upload its evidence. It operates entirely within the constraints of local drone regulations by remaining within the property boundaries and maintaining line-of-sight telemetry.

## Section 2: Problem Statement
Fixed perimeter security cameras are easily bypassed by intruders who learn their blind spots or physically disable them. Furthermore, when a large property is breached, ground personnel or homeowners are put at immense physical risk if they go outside to investigate. Existing commercial security drones are massive, expensive, and require licensed pilots, rendering them entirely inaccessible for residential or small-compound use. A lightweight, fully autonomous aerial node is required to investigate threats safely without human intervention.

## Section 3: Market Research and Existing Solutions
1. **Ring Always Home Cam**: An indoor autonomous drone. It maps the home and flies on command, but it is strictly for indoor use, possesses limited flight time, and does not actively pursue a moving target. It is also reliant on the Ring cloud ecosystem.
2. **Skydio 2+ / Skydio X2**: Phenomenal autonomous tracking and obstacle avoidance. However, they are exceedingly expensive, heavy (falling outside the Nano category in India), and targeted at enterprise/military or cinematic users, lacking native integration into a standard residential smart home hub.
3. **DJI Mini Series**: Excellent lightweight hardware (sub-250g), but they are closed ecosystems designed for manual flight. They do not support open autonomous APIs for seamless home security integration.

## Section 4: Technical Architecture
*   **Sensing Pipeline**: A downward-facing optical flow sensor and LiDAR for precision hovering. A forward-facing high-resolution RGB camera for subject tracking.
*   **Processing Pipeline**: The Flight Controller (e.g., Pixhawk/ArduPilot) handles low-level stabilization. A companion computer (ESP32-S3 or Raspberry Pi Zero) processes the video feed using Haar Cascades or MobileNet to draw bounding boxes around humans.
*   **Communication Pipeline**: Maintains a high-bandwidth local Wi-Fi or proprietary RF link (e.g., ExpressLRS for control, local RTSP for video) directly to the Sentinel Hub.
*   **Action Pipeline**: Upon receiving a launch command from the Hub, the drone calculates a flight path to the breached zone, ascends to a 5-meter hover, centers the human subject in frame, tracks their movement, and triggers a return-to-home (RTH) sequence when the battery drops below 30%.

## Section 5: Hardware Component Selection
*   **Frame & Propulsion**: Custom lightweight carbon fiber 3-inch or 4-inch frame with micro brushless motors (e.g., 1404 size) and 3-inch props. Cost: ~₹3,000.
*   **Flight Controller**: Mamba F405 Mini or Holybro Pixhawk Mini — Reliable ArduPilot/Betaflight support. Cost: ~₹4,500.
*   **Companion/Vision Node**: ESP32-S3-EYE or Raspberry Pi Zero 2 W with CSI Camera — For local object detection and video streaming. Cost: ~₹1,500.
*   **Battery**: 2S or 3S 850mAh LiPo for approximately 8-10 minutes of active pursuit flight time. Cost: ~₹800.
*   **Total Prototype BOM**: ~₹9,800 INR (excluding ground dock mechanics).

## Section 6: AI and Software Layer
The flight controller utilizes standard PID loops and EKF (Extended Kalman Filters) for stability. The AI layer resides on the companion computer (ESP32-S3/Pi Zero), running TensorFlow Lite Micro. The model is a lightweight object detector (e.g., MobileNetV2-SSD) trained specifically to identify "Person" from an elevated angle. The bounding box coordinates are translated into yaw and pitch commands sent via serial (MAVLink) to the flight controller, keeping the subject centered in the camera frame autonomously.

## Section 7: SENTINEL Ecosystem Integration
*   **Perceive Layer**: Streams aerial video and thermal signatures back to the Hub for secondary verification.
*   **Contain Layer**: The physical presence of a loud, hovering drone acts as a massive psychological deterrent to an intruder, often forcing them to flee.
*   **Document Layer**: The high-resolution aerial footage is hashed and appended to the Incident Memory Graph, providing law enforcement with unassailable evidence of the intruder's escape route.

## Section 8: Power and Connectivity
*   **Power**: Flight battery (LiPo). The docking station provides contact-based charging (pogo pins) when the drone lands.
*   **Connectivity**: High-throughput 5.8GHz or 2.4GHz Wi-Fi for video; 868/915MHz LoRa or ExpressLRS for failsafe telemetry.
*   **Fallback**: If video link drops, the drone executes a hardcoded RTL (Return to Launch) utilizing its onboard GPS and optical flow sensors.

## Section 9: Privacy and Safety Constraints
*   **Legal / DGCA Rules 2021**: This is the most critical constraint. Sentinel Air is explicitly designed to weigh **under 250 grams**, classifying it as a **Nano Drone**. Under India's DGCA Drone Rules 2021, Nano drones operated for non-commercial purposes are exempt from obtaining a Type Certificate, a Unique Identification Number (UIN), and a Remote Pilot License. However, autonomous Beyond Visual Line of Sight (BVLOS) flight remains highly restricted. Therefore, Sentinel Air is geofenced to the property boundaries (Green Zone) and operates strictly as a low-altitude perimeter defense, satisfying safety requirements while maintaining functional legality.
*   **Safety Constraints**: Propeller guards are mandatory to prevent injury if the drone descends near the homeowner or pets.

## Section 10: Prototype Scope vs Production Scope
*   **Prototype Scope (Student Team)**: A manually assembled FPV-style drone running ArduPilot. It can launch vertically from a known point, stream video to the hub, and execute an automated Return-To-Launch. True autonomous human-chasing is extremely complex for a semester project and will likely be simulated or demonstrated in a highly controlled, static environment.
*   **Production Scope**: A bespoke, injection-molded, weather-sealed (IP54) quadcopter with a motorized clamshell docking station that protects it from rain and dust. Complete optical obstacle avoidance and robust autonomous pursuit capabilities.

## Section 11: Known Risks and Mitigations
1. **Risk**: Drone flyaway due to GPS glitch. **Mitigation**: Redundant optical flow sensors and a hardcoded altitude ceiling (max 10 meters).
2. **Risk**: Weather conditions (rain/wind). **Mitigation**: The Hub pulls local weather APIs and physically disables the launch sequence if wind speeds exceed the drone's thrust capabilities.
3. **Risk**: Landing pad misalignment. **Mitigation**: IR beacons on the docking station and an inverted cone design to physically funnel the drone onto the charging pins.
4. **Risk**: Intruder knocks the drone out of the air. **Mitigation**: The drone maintains a minimum hover altitude of 4 meters, safely out of reach of handheld projectiles.
5. **Risk**: Privacy complaints from neighbors. **Mitigation**: The drone is strictly geofenced and programmed to keep its camera pitched downward at a 45-degree angle, ensuring it only records the owner's property.

## Section 12: Future Scope
The ultimate future scope involves true BVLOS authorization and mesh-drone swarms. If an intruder flees the property, multiple Sentinel Air nodes from neighboring properties (within a community watch framework) could seamlessly hand off tracking duties, following the suspect across miles of terrain while feeding live coordinates directly to a centralized police dashboard.