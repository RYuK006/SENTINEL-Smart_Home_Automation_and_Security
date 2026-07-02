# Sentinel Whisper: Product Design Specification

## Section 1: Product Overview
Sentinel Whisper is a covert, localized acoustic threat detection and privacy-enforcement node. Designed to blend invisibly into home environments as a generic object (like a smart speaker or lamp base), it serves two distinct functions. First, it actively listens for extreme acoustic anomalies associated with domestic violence or home invasion (screaming, physical impacts, breaking glass) without capturing intelligible speech. Second, upon detecting an active threat or when triggered via the Sentinel Band, it can emit a highly directional, 40kHz ultrasonic acoustic jamming signal designed to overload the MEMS microphones on smartphones or hidden recording devices, providing victims of domestic abuse a safe, unrecorded window to summon help or speak freely.

## Section 2: Problem Statement
In domestic abuse scenarios or targeted home invasions, victims are often prevented from calling for help, and abusers may use hidden cameras or smartphone recorders to intimidate victims. Standard security cameras are highly visible, easily unplugged, and entirely inappropriate for bedrooms or private spaces. Victims need a covert way to trigger help that doesn't rely on holding a phone, and they need a way to neutralize adversarial recording devices in their own home.

## Section 3: Market Research and Existing Solutions
1. **Commercial Audio Jammers (e.g., Rabbler, BugaHunter)**: These exist in corporate counter-espionage, but they are expensive, handheld, look like spy gear, and cannot integrate into a smart home system to trigger an alarm.
2. **Amazon Echo Guard**: Can detect breaking glass and smoke alarms, but it is highly visible, relies entirely on the cloud (privacy risk), and has no active countermeasures (jamming).
3. **Covert Spy Cameras**: Highly unethical, legally problematic, and generally used *by* bad actors rather than *against* them. Sentinel Whisper provides covert *protection*, not covert *surveillance*.

## Section 4: Technical Architecture
*   **Sensing Pipeline**: A local I2S MEMS microphone (e.g., MAX9814) captures audio. The hardware is deliberately bottlenecked so it cannot stream high-fidelity audio over the network; it only buffers short 2-second windows for analysis.
*   **Processing Pipeline**: An ESP32 microcontroller runs Edge Impulse TinyML. The model is trained exclusively on non-speech acoustic violence (screams, thuds, glass). 
*   **Action Pipeline (Jamming)**: If the user double-taps their Sentinel Band, the Whisper node drives an array of 40kHz ultrasonic transducers via a PWM signal amplified by a MOSFET. This ultrasonic frequency is inaudible to human ears but causes massive harmonic distortion (white noise) inside the hardware of standard smartphone microphones, rendering nearby recordings useless.
*   **Communication Pipeline**: Connects securely via local Wi-Fi to the Sentinel Hub to trigger the silent escalation ladder.

## Section 5: Hardware Component Selection
*   **Microcontroller**: ESP32 WROOM — Sufficient for basic audio classification and PWM generation. Cost: ~₹400.
*   **Microphone**: MAX9814 or INMP441 — Standard local MEMS mic. Cost: ~₹200.
*   **Ultrasonic Transducer Array**: Multiple HY40A16T12 (40kHz) transmitters driven by a L298N or custom MOSFET amplifier circuit to achieve sufficient acoustic pressure. Cost: ~₹800.
*   **Total Prototype BOM**: ~₹1,400 INR (excluding 3D printed covert housing).

## Section 6: AI and Software Layer
The software strictly isolates the audio buffer. TensorFlow Lite Micro processes Mel-frequency cepstral coefficients (MFCCs) of the audio. To respect privacy, the model is a binary classifier: `Acoustic_Violence` vs `Background_Noise/Speech`. The model intentionally discards the frequencies required to understand human speech, focusing purely on volume spikes and chaotic frequency signatures. No audio is ever written to non-volatile memory or transmitted over Wi-Fi.

## Section 7: SENTINEL Ecosystem Integration
*   **Perceive Layer**: Provides critical acoustic intelligence in rooms where cameras are banned (bedrooms, bathrooms).
*   **Contain Layer**: The ultrasonic jamming acts as a containment mechanism against digital surveillance or extortion by an abuser.
*   **Escalate Layer**: Feeds into the "Silent Escalation" ladder. If Whisper detects a scream, the Hub does not sound a siren (which could anger an abuser); instead, it silently texts a trusted emergency contact or initiates a silent police dispatch.

## Section 8: Power and Connectivity
*   **Power**: Mains powered (plugged into the wall) because driving ultrasonic transducers requires significant current. Small LiPo backup (1000mAh) ensures it works if the breaker is flipped.
*   **Connectivity**: Local Wi-Fi (MQTT).
*   **Fallback**: BLE beacon to the Sentinel Hub.

## Section 9: Privacy and Safety Constraints
*   **Legal / Ethical (Indian Law)**: Under the Indian Right to Privacy (Puttaswamy judgement) and Section 66E of the IT Act, recording individuals in private spaces without consent is a severe violation. Sentinel Whisper respects this entirely by being physically incapable of storing or transmitting intelligible voice recordings. It is an anomaly detector, not a wiretap. 
*   **Safety Constraints**: Sustained exposure to high-decibel ultrasonic frequencies can cause headaches or nausea in some humans and extreme distress in pets (dogs/cats). Therefore, the jamming feature is strictly limited to 60-second bursts and is manually triggered via the Band, not left on continuously.

## Section 10: Prototype Scope vs Production Scope
*   **Prototype Scope (Student Team)**: An ESP32 inside a generic plastic box with a single MAX9814 and a small array of 40kHz transducers. Capable of classifying a loud clap or scream locally, and successfully degrading audio recording on an iPhone placed within 1 meter of the transducers.
*   **Production Scope**: Disguised entirely as a functional, aesthetic bedside lamp or Bluetooth speaker. A phased-array of high-power transducers capable of jamming microphones across an entire medium-sized room (3-4 meters).

## Section 11: Known Risks and Mitigations
1. **Risk**: Dogs/cats severely agitated by ultrasonic frequencies. **Mitigation**: The system disables the jamming function if the home's primary cameras detect a pet in the same room.
2. **Risk**: Model misclassifying a loud movie as a scream. **Mitigation**: The Hub checks if the smart TV in that room is currently drawing power/playing media before escalating.
3. **Risk**: Abuser discovers the device. **Mitigation**: Covert design. The device looks and functions exactly like a normal nightlight or clock radio.
4. **Risk**: Transducer hardware failure due to heat. **Mitigation**: strict 60-second duty cycles for the PWM amplifier.
5. **Risk**: Legal liability if used to jam lawful police recordings. **Mitigation**: The device only operates when the home is in an armed state or triggered by the owner; it is a defensive tool for the home.

## Section 12: Future Scope
Future acoustic models will implement advanced Sound Event Detection (SED) to specifically identify the sound of a firearm being racked, a specific phrase (a vocal safe-word), or the distinct frequency of a struggle, providing unprecedented protection for vulnerable individuals living alone.