# RuView WiFlow Training Pipeline

Integrates RuView (MIT License) by ruvnet — github.com/ruvnet/RuView.

This document serves as a reproducible, step-by-step training guide for the 1.8M parameter WiFlow model, specifically tailored for SENTINEL's deployment on an RTX 3050 GPU, ESP32-S3 nodes, and a Raspberry Pi hub.

## Prerequisites & Hardware Setup
*   **Compute**: NVIDIA RTX 3050 (6GB VRAM is perfectly sufficient for the 1.8M parameter model).
*   **Network**: A dedicated 2.4GHz WiFi network with **AP Isolation explicitly disabled**.
*   **Nodes**: Minimum of 3 ESP32-S3 nodes flashed with the latest RuView firmware, positioned at the extremes of the target room.
*   **Hub**: Raspberry Pi 4/5 running the `sensing-server` Rust binary.

## Phase 1: CSI Data Collection
Before training, you must collect room-specific baseline data.
1.  Boot the ESP32-S3 nodes and the `sensing-server`.
2.  Vacate the room for exactly 60 seconds to allow the system to establish a baseline calibration.
3.  Re-enter the room and spend 2 to 3 hours performing natural, continuous movements. Ensure you cover all major semantic states: sitting, standing, walking, lying down on the floor, lying on the bed, and raising arms.
4.  The `sensing-server` will automatically log the raw CSI streams to local storage.

## Phase 2: MM-Fi Pre-Training
The WiFlow model relies on self-supervised learning, utilizing the public MM-Fi dataset to understand basic human kinematics via CSI before it ever looks at your specific room.
1.  Download the MM-Fi dataset to the `data/` directory.
2.  Execute the pre-training command on the RTX 3050 machine:
    ```bash
    ./target/release/sensing-server --train --dataset data/ --dataset-type mmfi --epochs 100
    ```
3.  This phase establishes the foundational 5-keypoint proxy and interpolates the 17-keypoint skeleton using strict bone-length constraints.

## Phase 3: SONA Fine-Tuning
With the base model trained, you must adapt it to the unique RF multipath geometry of your specific room using the SONA/MERIDIAN adaptation layer and the data collected in Phase 1.
1.  Execute the fine-tuning command against your local dataset:
    ```bash
    ./target/release/sensing-server --train --dataset local_room_data/ --epochs 50
    ```
2.  This prevents the model from needing to relearn basic human physics, instead focusing purely on resolving your room's specific reflections and interference patterns.

## Phase 4: Export and Deployment
1.  Once fine-tuning converges, export the model to a single, deployable `.rvf` file:
    ```bash
    ./target/release/sensing-server --train --dataset local_room_data/ --epochs 100 --save-rvf model.rvf
    ```
2.  Transfer `model.rvf` to the Raspberry Pi hub.
3.  Restart the `sensing-server` on the Pi, pointing it to the new `.rvf` file.
4.  Verify semantic state outputs and the 17-keypoint skeleton via the HTTP dashboard on port 3000.

## Troubleshooting
*   **Calibration Errors**: If the baseline is wrong, power-cycle the ESP32-S3 nodes in an entirely empty room.
*   **No Data Received**: Confirm AP Isolation is disabled on the router. ESP32-S3 nodes must be able to see cross-traffic.
*   **Expectations**: This end-to-end process requires approximately one week. While reliable lying/standing/falling detection is achievable rapidly, pixel-perfect 17-keypoint precision requires extensive data volume and optimally placed nodes.