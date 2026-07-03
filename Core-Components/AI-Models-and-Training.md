# AI Models and Training

Explains the TinyML and Edge AI models utilized across the perception nodes, focusing on on-device person/animal classification and acoustic event detection.

Integrates RuView (MIT License) by ruvnet — github.com/ruvnet/RuView.

## SENTINEL AI Stack Division of Labor
SENTINEL explicitly divides its local AI reasoning between the far edge (microcontrollers) and the local hub (Raspberry Pi), ensuring no data is ever offloaded to the cloud.

### Edge Inference: TFLite Micro (ESP32-S3 / ESP32-CAM)
*   **Visual Modality**: Runs highly optimized TensorFlow Lite Micro models directly on ESP32-CAM nodes for visual person detection and facial recognition.
*   **Acoustic Modality**: Runs lightweight CNNs for local audio classification (glass breaking, baby crying) on ESP32-S3 nodes.

### Hub Inference: WiFlow (Raspberry Pi 4/5)
*   **WiFi CSI Modality**: The heavy lifting for RuView's 17-keypoint skeleton tracking is handled by the **WiFlow** architecture running on the central Raspberry Pi hub. WiFlow is a state-of-the-art 1.8 million parameter model based on the CMU DensePose From WiFi research. While the ESP32-S3 nodes simply capture the raw Channel State Information (CSI), the Pi hub runs the `sensing-server` binary to compress the tensors, perform cross-attention mapping, and output semantic states and vitals. This strict division ensures that the heavy matrix math of RF tracking doesn't cripple the low-power edge nodes.