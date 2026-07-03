# RuView Integration: WiFi CSI Through-Wall Sensing

## Section 1: What RuView Is
RuView is an open-source, MIT-licensed through-wall sensing system that exploits ordinary WiFi Channel State Information (CSI) signals to detect human presence, track body poses, measure breathing rate, and monitor heart rate. It achieves this entirely without cameras, wearables, or cloud dependencies, making it a revolutionary addition to the SENTINEL Perceive layer. Built upon the foundational research presented in the CMU paper "DensePose From WiFi" (arXiv:2301.00250), RuView utilizes a state-of-the-art 1.8 million parameter neural network called WiFlow. By analyzing the microscopic phase shifts and amplitude variations in WiFi signals bouncing off human bodies, RuView can reconstruct a 17-keypoint human skeleton and classify complex behaviors through physical walls.

## Section 2: License and Attribution
Integrates RuView (MIT License) by ruvnet — github.com/ruvnet/RuView. 
Because RuView is released under the permissive MIT License, SENTINEL is free to use, modify, and integrate the system both for personal and commercial applications without requiring additional permissions. The only strict obligation is to retain the original copyright notice and this attribution line wherever the RuView system or its derivative models are referenced within the SENTINEL repository.

## Section 3: Why RuView Belongs in SENTINEL
RuView fills the most critical sensory gap in the SENTINEL ecosystem: the need for high-fidelity, continuous human monitoring in spaces where optical cameras are ethically or practically inappropriate, such as bedrooms and bathrooms. While PIR (Passive Infrared) sensors only detect gross movement and fail when a person is sleeping or sitting still, RuView provides continuous, through-wall presence detection. This makes it the ultimate ground-truth sensor for elderly fall detection (where a resident may fall behind a shower curtain), sleep-mode transition automation, zone occupancy tracking, and building deep behavioral baseline models without compromising the privacy of the occupants.

## Section 4: Hardware Requirements
RuView demands specific hardware: it runs exclusively on **ESP32-S3** nodes. The classic ESP32 and the ESP32-CAM do not possess the necessary silicon capabilities to capture the high-resolution CSI data required by the WiFlow model. Each ESP32-S3 node costs approximately $9–$15, making it highly cost-effective. A single node is the minimum deployment and is sufficient for basic presence and motion intensity detection. To achieve triangulated spatial positioning and room mapping, a minimum of 3 nodes is required. For full, multi-room whole-home coverage, 3 to 6 nodes are recommended. The ESP32-CAM plays a distinct but complementary role; it cannot act as a CSI node, but instead serves as the visual component in SENTINEL's dual-modal fusion setup, operating alongside the ESP32-S3 CSI nodes.

## Section 5: Signal Processing Pipeline
The RuView signal processing pipeline is a rigorous multi-stage process that transforms noisy RF data into clean semantic states:
1. **Phase Correction**: Raw CSI data suffers from random phase offsets due to unsynchronized transmitter and receiver clocks. This stage applies linear transformations to sanitize the phase data.
2. **Multipath Suppression**: Because WiFi signals bounce off walls and furniture, the system uses Singular Value Decomposition (SVD) denoising (implemented internally in `field_model.rs`) to strip away static environmental reflections and isolate the dynamic signals bouncing off moving human bodies.
3. **Neural Network Inference**: The cleaned signal is fed into the WiFlow architecture. This involves several specialized `ruvector` crates:
    *   `ruvector-temporal-tensor` compresses the CSI buffer.
    *   `ruvector-mincut` partitions sensitive and insensitive subcarriers.
    *   `ruvector-attention` and `ruvector-graph-transformer` handle the cross-attention mapping from CSI to pose.
    *   `ruvector-gnn` performs the final body skeleton graph reasoning.

## Section 6: Current Working Capabilities
Out of the box, RuView provides highly reliable detection of human presence and absence, motion intensity and direction, breathing rate, and heart rate. While person-counting is available as a heuristic on single-node deployments, it is not a learned classifier and can over-report. Crucially, the system actively outputs 11 semantic states: `someone-sleeping`, `possible-distress`, `room-active`, `elderly-inactivity-anomaly`, `meeting`, `bathroom`, `fall-risk-elevated`, `bed-exit`, `no-movement`, and `multi-room-transition`. A single-node deployment reliably provides presence, vitals, and basic states. Multi-node deployments unlock the 17-keypoint skeleton and spatial tracking.

## Section 7: 17-Keypoint Skeleton Training
The crown jewel of RuView is its 17-keypoint skeleton estimation, powered by the 1.8M parameter WiFlow architecture based on CMU's DensePose methodology. The training pipeline is entirely self-supervised, meaning it does **not** require a camera or manually labeled data to train. It utilizes the public MM-Fi dataset for pre-training. Weak labels are generated mathematically: RSSI triangulation estimates head position, subcarrier asymmetry isolates hand movements, and vibration signatures identify feet. From a 5-keypoint proxy, it interpolates 17 keypoints via strict bone-length constraints. The `ruvector-sona` (and MERIDIAN) adaptation layer then allows the model to adapt to the specific geometry of Aaron's room without retraining from scratch. The training command is simply `./target/release/sensing-server --train --dataset data/ --dataset-type mmfi --epochs 100`, and export is handled via `--save-rvf model.rvf`. Given its 1.8M parameter size, this trains comfortably on an RTX 3050. Realistic output quality relies on deploying at least 3 nodes and collecting a few hours of natural movement data.

## Section 8: Integration Architecture with SENTINEL
The data flow is robust and entirely local. The ESP32-S3 nodes capture CSI and stream it via UDP on port 5005 to the `sensing-server` binary running on the Raspberry Pi hub. The server processes the neural network inference and exposes live skeleton, vitals, and occupancy data over HTTP on port 3000, while simultaneously pushing discrete semantic states to the SENTINEL MQTT broker. The SENTINEL risk-scoring engine subscribes to these MQTT topics, digesting them as primary inputs to the Perceive layer. In this architecture, the ESP32-S3 handles the WiFi CSI, the ESP32-CAM handles the visual feed, and the Pi Hub fuses both. **Security Note**: The `sensing-server` HTTP endpoint binds to the loopback interface by default. To expose this dashboard on the LAN, it must be routed through a reverse proxy, as the native server contains no authentication or TLS layer.

## Section 9: SENTINEL Feature Mapping
RuView's semantic states map directly to core SENTINEL automations:
*   `someone-sleeping` → Triggers the **Sleep Mode Transition** automation, dimming lights and arming perimeters.
*   `possible-distress` → Feeds the **Escalation** layer, cross-checked instantly with Sentinel Band vitals.
*   `elderly-inactivity-anomaly` → Acts as the primary trigger for the **Elderly Mode** alert.
*   `fall-risk-elevated` → Provides a pre-fall warning to caregivers *before* a catastrophic fall occurs.
*   `bed-exit` → Integrates with **Sentinel Cradle** for infant or toddler monitoring.
*   `room-active` / `no-movement` → Drives **Zone-Based Mapping** and **Occupancy-Based Climate** control.
*   `multi-room-transition` → Serves as the primary input for the **Behavioral Learning** layer.
*   `bathroom` / `meeting` → Automatically adjusts Zone sensitivity to respect privacy or minimize interruptions.

## Section 10: Deployment Configuration
1.  **Flash Nodes**: Flash the ESP32-S3 firmware directly from the RuView GitHub repository.
2.  **Configure Router**: Ensure the router is broadcasting a 2.4GHz SSID and that **AP Isolation is explicitly disabled**, otherwise nodes cannot capture cross-traffic CSI.
3.  **Position Nodes**: For a single room, place one node centrally. For multi-room mapping, place 3 nodes at the extreme corners of the target footprint.
4.  **Run Server**: Execute the `sensing-server` binary on the Raspberry Pi.
5.  **Configure Bridge**: Connect the `sensing-server` to the SENTINEL MQTT broker.
6.  **Calibrate**: Vacate the room for exactly 60 seconds after boot to allow the system to establish a baseline empty-room signature.
7.  **Verify**: Access the HTTP dashboard on port 3000 to verify semantic state outputs and signal clarity.

## Section 11: Training Guide for 17-Keypoint Estimation
For Aaron's specific setup (RTX 3050, ESP32-S3 nodes, Raspberry Pi hub):
*   **Phase 1**: Deploy 3 ESP32-S3 nodes. Collect 2–3 hours of CSI data performing natural movements (sitting, standing, walking, lying, raising arms).
*   **Phase 2**: Pre-train on the MM-Fi dataset: `./target/release/sensing-server --train --dataset data/ --dataset-type mmfi --epochs 100`
*   **Phase 3**: Run SONA fine-tuning on the locally collected room data for 20-50 epochs.
*   **Phase 4**: Export to deployable format: `./target/release/sensing-server --train --dataset data/ --epochs 100 --save-rvf model.rvf`. Deploy this `.rvf` file to the Pi hub. 
Expect this process to take approximately one week end-to-end. Watch carefully for RF interference during Phase 1 collection.

## Section 12: Known Limitations and Mitigations
*   **Calibration Window**: The first 60 seconds after boot require a completely empty room to establish a baseline. *Mitigation*: If the baseline is corrupted, power-cycle the nodes while the room is vacant.
*   **RF Interference**: Microwaves, nearby AP power swings, or fans near the antenna cause false positives. *Mitigation*: Deploying a 3+ node mesh drastically reduces these errors via multi-angle corroboration.
*   **Single-Node Limits**: A single node provides presence only, not triangulated position, and its person-counting is a heuristic that can over-report. *Mitigation*: Track GitHub issue #509 and rely on multi-node setups for accurate counting.
*   **5GHz Incompatibility**: ESP32-S3 silicon does not support 5GHz. *Mitigation*: A dedicated 2.4GHz IoT network is strictly required.

## Section 13: Privacy Considerations
WiFi CSI sensing is ethically and practically superior to optical cameras for private spaces. At no stage does RuView capture images, meaning it is physically incapable of capturing faces or identifiable visual records. It operates purely on invisible radio waves, allowing SENTINEL to push its Perceive layer into bedrooms and bathrooms where cameras are an unacceptable privacy violation. In the context of a smart home, this enables the system owner to maintain high-fidelity safety monitoring (like fall detection in a shower) without subjecting themselves or their family to the massive privacy invasion of optical surveillance.