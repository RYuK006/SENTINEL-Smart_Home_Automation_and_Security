# Hardware Build Requirements

## The Brain (Central Hub)
*   **Prototype:** Raspberry Pi 5 or NVIDIA Jetson Nano.
*   **Production/Future:** Custom PCB featuring an ESP32-S3 (for control), an Edge TPU / Hailo AI accelerator, and an Iridium 9604 module (Satellite/Cellular/GPS combination).

## Perception Nodes (Mesh Sensors)
*   **Microcontrollers:** ESP32 / ESP8266 / Arduino Nano 33 BLE for distributed nodes.
*   **Sensors:** PIR motion, HC-SR04 (ultrasonic), MQ-5/MQ-2 (gas/smoke), Piezo (vibration/glass break), water leak contacts.
*   **Audio:** INMP441 (I2S microphones) for [Acoustic Event Detection](../Features/Acoustic%20Event%20Detection.md).

## Vision & Tracking
*   **Cameras:** ESP32-CAMs (for prototype zones) or standard 1080p IP Cameras.
*   **Actuators:** SG90 or MG996R Pan-Tilt Servos for [Pan-Tilt Camera Tracking](../Features/Pan-Tilt%20Camera%20Tracking.md).

## Containment & Automation Relays
*   **Hardware:** 5V/12V Relay Modules connected to lighting circuits, sirens, smart locks, and water valves.
*   **Connectivity:** MQTT over local Wi-Fi for node-to-hub communication. 

## Communications & Escalation
*   **Twilio API:** For [Press-1 Press-2 Verification](../Features/Press-1%20Press-2%20Verification.md) and [AI Voice Agent Dispatch](../Features/AI%20Voice%20Agent%20Dispatch.md).
*   **Satellite Hardware:** Iridium 9604 or Skylo Dev Kit for [Satellite Fallback](../Features/Satellite%20Fallback.md).
*   **Wearable:** Programmable BLE wristbands (like PineTime) or custom ESP32-C3 watch bodies for the [Sentinel Band](Sentinel%20Band.md).

See also: [Software Stack](Software%20Stack.md)
