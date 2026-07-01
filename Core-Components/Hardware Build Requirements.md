# Hardware Build Requirements

## The Brain (Central Hub)
*   **Prototype:** Raspberry Pi 5 or NVIDIA Jetson Nano.
*   **Production/Future:** Custom PCB featuring an ESP32-S3 (for control), an Edge TPU / Hailo AI accelerator, and an Iridium 9604 module (Satellite/Cellular/GPS combination).

## Perception Nodes (Mesh Sensors)
*   **Microcontrollers:** ESP32 / ESP8266 / Arduino Nano 33 BLE for distributed nodes.
*   **Sensors:** PIR motion, HC-SR04 (ultrasonic), MQ-5/MQ-2 (gas/smoke), Piezo (vibration/glass break), water leak contacts.
*   **Audio:** INMP441 (I2S microphones) for [[Acoustic Event Detection]].

## Vision & Tracking
*   **Cameras:** ESP32-CAMs (for prototype zones) or standard 1080p IP Cameras.
*   **Actuators:** SG90 or MG996R Pan-Tilt Servos for [[Pan-Tilt Camera Tracking]].

## Containment & Automation Relays
*   **Hardware:** 5V/12V Relay Modules connected to lighting circuits, sirens, smart locks, and water valves.
*   **Connectivity:** MQTT over local Wi-Fi for node-to-hub communication. 

## Communications & Escalation
*   **Twilio API:** For [[Press-1 Press-2 Verification]] and [[AI Voice Agent Dispatch]].
*   **Satellite Hardware:** Iridium 9604 or Skylo Dev Kit for [[Satellite Fallback]].
*   **Wearable:** Programmable BLE wristbands (like PineTime) or custom ESP32-C3 watch bodies for the [[Sentinel Band]].

See also: [[Software Stack]]
