# Amazon Transcribe and Amazon Polly

## Amazon Transcribe — Speech to Text

### What Is Transcribe?
- **Amazon Transcribe** — Automatic Speech Recognition (ASR) service that converts **audio speech to text**.
- Supports **100+ languages** (English, Chinese, Arabic, French, Korean, Hindi, German, and more).

### Key Features
- **Automatic Language Identification** — detects the spoken language automatically.
- **Multiple speaker diarization** — identifies and labels different speakers in a recording.
- **PII Redaction** — automatically removes Personally Identifiable Information from transcripts.
- **Custom Vocabulary** — add domain-specific terms (medical, legal, technical jargon).
- **Multi-channel transcription** — transcribe separate audio channels (e.g., agent + customer) independently.
- **Amazon Transcribe Medical** — specialized model for converting clinical conversations into health records.

### Use Cases
- Transcribe customer service calls for quality assurance
- Generate closed captions and subtitles for video content
- Create searchable media archives (transcribe all videos → index text → search)
- Detect toxic/inappropriate language in audio (e.g., social media moderation)
- Convert doctor-patient conversations to structured clinical notes (Transcribe Medical)

---

## Amazon Polly — Text to Speech

### What Is Polly?
- **Amazon Polly** — converts **text to lifelike speech**.
- Create applications that "talk" to increase engagement and accessibility.

### Key Features
- **Multiple voices and languages** — choose from a variety of lifelike voices per language/locale.
- **SSML (Speech Synthesis Markup Language)** — control speech with markup for pauses, emphasis, pronunciation, and speed.
- **Neural TTS** — highly natural-sounding speech (Neural voices).
- **Custom lexicons** — define how specific words or phrases are pronounced.

### Use Cases
- Add voice to RSS feeds, websites, and blog posts for global accessibility
- Automated voice response systems (IVR) — "Your account balance is $XX"
- E-learning and content accessibility for visually impaired users
- Real-time voice output for IoT devices ("Press button to turn on AC")

---

## Transcribe vs Polly Comparison

| Feature | Amazon Transcribe | Amazon Polly |
|---|---|---|
| Direction | Audio → Text | Text → Audio |
| Purpose | Speech recognition | Speech synthesis |
| Use case | Transcription, captioning, search indexing | Voice apps, accessibility, IVR |

---

## Key Points / Exam Tips

- **Trigger:** "convert audio/speech to text" → **Amazon Transcribe**
- **Trigger:** "convert text to speech, voice applications, IVR" → **Amazon Polly**
- **Trigger:** "remove PII from call transcripts automatically" → **Transcribe PII Redaction**
- **Trigger:** "clinical conversations to health records" → **Amazon Transcribe Medical**
- Transcribe uses **ASR (Automatic Speech Recognition)** — it is NOT an NLP service
- Polly supports **SSML** — allows fine-grained control over speech output (pauses, emphasis)
