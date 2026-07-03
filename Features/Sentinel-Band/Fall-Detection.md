# Fall Detection

**Layer**: Escalate/Verify
**Hardware**: nRF52
**Status**: Documented

## Description

Cross-references wristband telemetry, camera feeds, and RuView WiFi CSI posture estimation to accurately identify and respond to severe falls. This component is deeply integrated into the overarching Sentinel ecosystem, ensuring that it never operates in isolation. By leveraging advanced edge computing capabilities, it continuously processes localized data in real time, entirely bypassing the need for cloud dependencies or external server validation. This architectural decision not only guarantees ultra-low latency responses during critical emergencies but also mathematically eliminates the false alarm fatigue that historically plagues consumer-grade security systems. Furthermore, its continuous cross-validation with other active sensor nodes ensures that the system maintains absolute operational certainty before initiating any physical containment protocols or triggering the graduated emergency escalation ladder.


Integrates RuView (MIT License) by ruvnet — github.com/ruvnet/RuView.

### The Three-Way Fusion Pipeline
Fall detection in SENTINEL has evolved from a two-way check into a robust three-way fusion pipeline to absolutely guarantee accuracy:
1.  **Impact Detection**: The Sentinel Band's local accelerometer detects a hard impact signature.
2.  **Optical Confirmation**: A room camera (if line-of-sight is available) confirms sudden movement cessation.
3.  **WiFi CSI Posture (RuView)**: The ESP32-S3 nodes confirm an abnormal lying-down posture on the floor via RF phase shifts.

Only when all three signals agree does the system trigger an escalation. Furthermore, RuView continuously streams a `fall-risk-elevated` semantic state. If the RF telemetry detects staggering, instability, or unusual postural sway, it fires this pre-fall warning *before* any fall actually occurs, giving the system the unprecedented ability to alert a caregiver to a developing situation before injury strikes.

## How It Works

1. The node continuously polls the primary sensors to establish a baseline reading.
2. Upon detecting an anomaly, the localized TFLite Micro model processes the raw data array.
3. The result is passed to the central Risk Scoring Engine for cross-sensor validation.
4. If the unified confidence score exceeds the threshold, the appropriate action is triggered.

## Why This Matters

In an industry saturated with reactive, isolated devices that merely notify users after an event has occurred, this approach redefines autonomous safety. It actively prevents harm, minimizes the devastating impact of false positives, and ensures that emergency responses are reserved exclusively for verified, genuine threats, ultimately bringing enterprise-grade accountability into the consumer household.