# Occupancy Based Climate

**Layer**: Contain/Automation
**Hardware**: Relays/Plugs
**Status**: Documented

## Description

Dynamically adjusts the thermostat for individual rooms based solely on real-time human presence detected by the sensor nodes. This component is deeply integrated into the overarching Sentinel ecosystem, ensuring that it never operates in isolation. By leveraging advanced edge computing capabilities, it continuously processes localized data in real time, entirely bypassing the need for cloud dependencies or external server validation. This architectural decision not only guarantees ultra-low latency responses during critical emergencies but also mathematically eliminates the false alarm fatigue that historically plagues consumer-grade security systems. Furthermore, its continuous cross-validation with other active sensor nodes ensures that the system maintains absolute operational certainty before initiating any physical containment protocols or triggering the graduated emergency escalation ladder.


Integrates RuView (MIT License) by ruvnet — github.com/ruvnet/RuView.

### CSI as Occupancy Ground Truth
For highly sensitive zones like bedrooms and bathrooms where cameras are strictly prohibited, SENTINEL utilizes RuView WiFi CSI as the primary occupancy ground truth. Standard PIR sensors fail when a person sits perfectly still at a desk or reads a book in bed, causing the HVAC system to erroneously shut off. RuView solves this by providing continuous, passive occupancy detection directly through the walls. By streaming `room-active` and `no-movement` semantic states based on the microscopic chest movements of a breathing human, RuView ensures the climate control remains perfectly synced to the actual human presence, with PIR acting only as a secondary fast-response confirmation at doorways.

## How It Works

1. The node continuously polls the primary sensors to establish a baseline reading.
2. Upon detecting an anomaly, the localized TFLite Micro model processes the raw data array.
3. The result is passed to the central Risk Scoring Engine for cross-sensor validation.
4. If the unified confidence score exceeds the threshold, the appropriate action is triggered.

## Why This Matters

In an industry saturated with reactive, isolated devices that merely notify users after an event has occurred, this approach redefines autonomous safety. It actively prevents harm, minimizes the devastating impact of false positives, and ensures that emergency responses are reserved exclusively for verified, genuine threats, ultimately bringing enterprise-grade accountability into the consumer household.