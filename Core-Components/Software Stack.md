# Software Stack

*   **Inference:** Edge Impulse / TensorFlow Lite Micro for on-device ML ([[On-Device Classification]]).
*   **Backend Core:** Python running on the central hub, utilizing an MQTT broker (Eclipse Mosquitto).
*   **Database:** SQLite (local, encrypted) for event logs, behavior learning, and the [[Incident Memory Graph]].
*   **Mobile App:** Flutter or React Native for a cross-platform, unified household dashboard.

See also: [[Hardware Build Requirements]]
