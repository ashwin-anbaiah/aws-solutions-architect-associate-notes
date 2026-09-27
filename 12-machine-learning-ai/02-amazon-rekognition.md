# Amazon Rekognition

## What Is Rekognition?

- **Amazon Rekognition** — computer vision AI service to identify and analyze people, text, objects, and scenes in **images and videos**.
- API-based — no ML expertise required; simply pass an image/video and receive structured results.

## Key Capabilities

| Capability | Description |
|---|---|
| **Face Detection and Analysis** | Detect faces in an image; analyze attributes (age range, emotion, gender, smile, glasses) |
| **Face Compare and Search** | Compare a face against a collection of known faces to find matches |
| **Celebrity Recognition** | Identify known celebrities in images and video |
| **Object and Scene Detection** | Detect objects, scenes, and activities (e.g., "outdoor," "sports," "dog") |
| **Text Detection** | Extract printed and handwritten text from images |
| **Content Moderation** | Detect explicit, suggestive, or otherwise unsafe content (images and video) |
| **Video Segment Detection** | Detect specific segments in video — blank frames, black frames, credits, slates |
| **Custom Labels** | Train Rekognition to detect custom objects specific to your business (e.g., defective parts on a factory line) |

## Common Architecture Patterns

```
Image/Video uploaded to S3
  → Lambda triggers Rekognition API
  → Rekognition returns labels/faces/text
  → Store results in DynamoDB / trigger SNS notification
```

For video: submit a job → poll for completion → retrieve results (async).

## Use Cases

- Workplace safety verification (PPE detection with Custom Labels)
- Social media content moderation at scale
- Identity verification (face compare for user onboarding)
- Media archive search (find specific scenes/people in video archives)
- Automated video categorization and metadata generation
- Surveillance and security (intruder detection)

---

## Key Points / Exam Tips

- **Trigger:** "detect faces, objects, text in images/videos" → **Amazon Rekognition**
- **Trigger:** "content moderation — detect inappropriate content" → **Rekognition**
- **Trigger:** "face recognition, celebrity detection" → **Rekognition**
- **Trigger:** "computer vision + custom objects" → **Rekognition Custom Labels**
- Rekognition is for **images and video** (visual data); use Transcribe for audio, Textract for documents
- Rekognition Video processes asynchronously — submit job, then poll or use SNS for completion notification
- Works with **Kinesis Video Streams** for real-time video frame analysis
