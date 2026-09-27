# Machine Learning and AI Overview — Three-Layer Approach

## AWS's Three-Layer AI/ML Architecture

AWS organizes its AI/ML capabilities into three layers based on the expertise required:

| Layer | Audience | What it provides |
|---|---|---|
| **AI Services** (top) | No ML experience needed | Pre-built fully managed AI APIs — just call the API |
| **ML Platform (SageMaker)** (middle) | Data scientists and developers | Tools to build, train, tune, and deploy custom models |
| **Infrastructure & Frameworks** (bottom) | ML experts and practitioners | GPU/CPU compute, Deep Learning AMIs, EFA, Inferentia chips |

---

## Layer 1: AI Services — Pre-Built APIs

Call the API with your data → receive an AI-powered response. No model training required.

| Domain | Service | What It Does |
|---|---|---|
| **Vision** | Amazon Rekognition | Detect faces, objects, text, labels in images and video |
| **Speech → Text** | Amazon Transcribe | Convert audio speech to text (ASR) |
| **Text → Speech** | Amazon Polly | Convert text to lifelike speech |
| **Document** | Amazon Textract | Extract text and data from scanned documents and forms |
| **Translation** | Amazon Translate | Translate text between 75+ languages |
| **NLP / Text** | Amazon Comprehend | Understand text — entities, sentiment, key phrases, topics |
| **Search** | Amazon Kendra | Intelligent enterprise search across documents and content repos |
| **Contact Center** | Amazon Connect | AI-powered cloud contact center |
| **Conversational AI** | Amazon Lex | Build voice/text chatbots (powers Alexa) |
| **Personalization** | Amazon Personalize | Real-time personalized recommendations |
| **Forecasting** | Amazon Forecast | Time-series forecasting using ML |
| **Generative AI** | Amazon Bedrock | Access to foundation models (FMs) for GenAI applications |

## Layer 2: ML Platform

- **Amazon SageMaker AI** — fully managed service for data scientists to build, train, tune, and deploy ML models at any scale.

## Layer 3: Infrastructure

- High-performance GPU/CPU EC2 instances (P4, P5, Trn1)
- **EFA (Elastic Fabric Adapter)** for low-latency inter-node communication in distributed training
- **AWS Inferentia** — custom chip for low-cost, high-throughput ML inference
- **Deep Learning AMIs** and containers

---

## Key Points / Exam Tips

- The SAA-C03 exam focuses primarily on **Layer 1 (AI Services)** and **SageMaker** — knowing which service fits which use case is the key skill
- **Trigger:** "no ML experience, just call an API" → any of the AI Services above
- **Trigger:** "build, train, deploy custom ML model" → **Amazon SageMaker**
- **Trigger:** "foundation models, generative AI, LLMs" → **Amazon Bedrock**
- All AI services follow the same pattern: **API call → send data → receive AI-powered result**
