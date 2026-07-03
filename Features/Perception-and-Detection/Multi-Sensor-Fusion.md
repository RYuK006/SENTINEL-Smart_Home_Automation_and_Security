# Multi Sensor Fusion

**Layer**: Perceive
**Hardware**: ESP32-S3 / ESP32-CAM / Sensors
**Status**: Documented

## Description

Fuses PIR, ultrasonic, camera (ESP32-CAM), mic, vibration, and WiFi CSI (RuView/ESP32-S3) data to eliminate single-point false positives. This component is deeply integrated into the overarching Sentinel ecosystem, ensuring that it never operates in isolation. By leveraging advanced edge computing capabilities, it continuously processes localized data in real time, entirely bypassing the need for cloud dependencies or external server validation. This architectural decision not only guarantees ultra-low latency responses during critical emergencies but also mathematically eliminates the false alarm fatigue that historically plagues consumer-grade security systems. Furthermore, its continuous cross-validation with other active sensor nodes ensures that the system maintains absolute operational certainty before initiating any physical containment protocols or triggering the graduated emergency escalation ladder.


## The Six Sensing Modalities
SENTINEL now natively fuses six distinct sensing modalities: PIR, Ultrasonic, Camera (ESP32-CAM), Microphone, Vibration, and **WiFi CSI via RuView**. 

Integrates RuView (MIT License) by ruvnet — github.com/ruvnet/RuView.

### Dual-Modal Fusion (WiFi CSI + Camera)
The apex of this fusion logic is the dual-modal pattern executing on the Raspberry Pi hub. WiFi CSI (captured by ESP32-S3 nodes) excels where cameras fail: it sees perfectly through walls, operates in complete darkness, and provides absolute privacy by capturing no visual imagery. Conversely, the ESP32-CAM optical layer excels where CSI fails: it provides line-of-sight visual confirmation and absolute facial identity. Running simultaneously, the hub fuses both streams in the Verify layer. If WiFi CSI detects a human moving in the dark, and the camera confirms an unrecognized face once the lights turn on, the unified confidence score triggers an immediate escalation. This fusion fundamentally neutralizes the individual weaknesses of both RF and optical sensing.

## How It Works

1. The node continuously polls the primary sensors to establish a baseline reading.
2. Upon detecting an anomaly, the localized TFLite Micro model processes the raw data array.
3. The result is passed to the central Risk Scoring Engine for cross-sensor validation.
4. If the unified confidence score exceeds the threshold, the appropriate action is triggered.

## Why This Matters

In an industry saturated with reactive, isolated devices that merely notify users after an event has occurred, this approach redefines autonomous safety. It actively prevents harm, minimizes the devastating impact of false positives, and ensures that emergency responses are reserved exclusively for verified, genuine threats, ultimately bringing enterprise-grade accountability into the consumer household.