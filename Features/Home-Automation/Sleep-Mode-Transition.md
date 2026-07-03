# Sleep Mode Transition

**Layer**: Contain/Automation
**Hardware**: Relays/Plugs
**Status**: Documented

## Description

Gradually dims interior lights, arms the perimeter, and locks all exterior doors based on continuous biological telemetry and postural states. This component is deeply integrated into the overarching Sentinel ecosystem, ensuring that it never operates in isolation. By leveraging advanced edge computing capabilities, it continuously processes localized data in real time, entirely bypassing the need for cloud dependencies or external server validation. This architectural decision not only guarantees ultra-low latency responses during critical emergencies but also mathematically eliminates the false alarm fatigue that historically plagues consumer-grade security systems. Furthermore, its continuous cross-validation with other active sensor nodes ensures that the system maintains absolute operational certainty before initiating any physical containment protocols or triggering the graduated emergency escalation ladder.


Integrates RuView (MIT License) by ruvnet — github.com/ruvnet/RuView.

### The Three-Signal Sleep Ground Truth
Historically, smart homes infer sleep clumsily—assuming that because it is 11:00 PM and the PIR sensor hasn't fired in 30 minutes, the house is asleep. SENTINEL abandons this proxy-based logic in favor of a biological three-signal approach:
1.  **Sentinel Band**: Confirms a sustained period of low wrist motion and reduced resting heart rate.
2.  **RuView WiFi CSI**: Positively outputs the `someone-sleeping` semantic state by detecting a lying-down posture and an actively reduced, rhythmic breathing rate directly through the bedroom wall.
3.  **Context**: The time-of-day heuristic acts merely as a final gating check.

Because RuView directly detects the physical states of sleep (posture and respiration) rather than guessing based on the absence of motion, SENTINEL can execute the Sleep Mode Transition (locking doors, killing standby power, arming exterior perimeter) with absolute certainty, never accidentally plunging a late-night reader into darkness.

## How It Works

1. The node continuously polls the primary sensors to establish a baseline reading.
2. Upon detecting an anomaly, the localized TFLite Micro model processes the raw data array.
3. The result is passed to the central Risk Scoring Engine for cross-sensor validation.
4. If the unified confidence score exceeds the threshold, the appropriate action is triggered.

## Why This Matters

In an industry saturated with reactive, isolated devices that merely notify users after an event has occurred, this approach redefines autonomous safety. It actively prevents harm, minimizes the devastating impact of false positives, and ensures that emergency responses are reserved exclusively for verified, genuine threats, ultimately bringing enterprise-grade accountability into the consumer household.