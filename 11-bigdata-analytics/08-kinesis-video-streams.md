# Amazon Kinesis Video Streams

## What Is Kinesis Video Streams?

- **Amazon Kinesis Video Streams** — managed service to securely **ingest, store, and process real-time video and media streams** from devices and cameras.
- Designed for continuous video, audio, and other time-serialized data.

## Key Features

- **Ingestion** — connect cameras, IoT devices, drones, smartphones to stream video to AWS.
- **Secure storage** — video data durably stored and encrypted in the cloud.
- **Playback** — access stored video through the AWS console or via APIs.
- **Integration** — connect to **Amazon Rekognition Video** for computer vision analysis, and other ML services.
- **Time-indexed data** — access video frames by timestamp.
- **WebRTC support** — real-time peer-to-peer video and audio communication (two-way streaming).

## Use Cases

| Use Case | Description |
|---|---|
| **Home security cameras** | Motion detection, face recognition via Rekognition |
| **Smart traffic cameras** | Vehicle detection, license plate recognition |
| **Industrial monitoring** | Surveillance of manufacturing floor, equipment |
| **Connected vehicles** | Dashcam video ingestion for analysis |
| **Telehealth** | Real-time patient video feeds |

## Kinesis Video Streams vs Kinesis Data Streams

| Feature | Kinesis Video Streams | Kinesis Data Streams |
|---|---|---|
| Data type | Video, audio, media | Any event/log/metric data |
| Purpose | Media ingestion and playback | Real-time event streaming |
| Processing | Rekognition Video, custom ML | Lambda, Flink, custom KCL |
| Use when | Camera feeds, video analytics | Clickstream, IoT metrics, logs |

---

## Key Points / Exam Tips

- **Trigger:** "video streams from cameras," "real-time video ingestion," "video analytics" → **Kinesis Video Streams**
- **Trigger:** "live video + computer vision analysis" → **Kinesis Video Streams + Amazon Rekognition Video**
- Kinesis Video Streams is for **media/video data**; Kinesis Data Streams is for **event/log data**
- Supports **WebRTC** for two-way peer-to-peer real-time video communication
- Integrated with **Amazon Rekognition** for automatic face detection, object detection in video
