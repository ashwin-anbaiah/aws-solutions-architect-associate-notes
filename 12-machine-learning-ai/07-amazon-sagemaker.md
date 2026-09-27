# Amazon SageMaker AI

## What Is SageMaker?

- **Amazon SageMaker AI** — fully managed ML platform for data scientists and developers to **build, train, tune, and deploy machine learning models** at scale.
- Reduces the heavy lifting of ML infrastructure — you focus on the model; AWS manages the compute.
- Part of AWS's middle layer in the three-tier AI/ML approach.

## The ML Workflow SageMaker Supports

```
Data collection → Data labeling → Feature engineering → Model training
  → Model evaluation → Hyperparameter tuning → Model deployment → Monitoring
```

## Key SageMaker Features

| Feature | Description |
|---|---|
| **SageMaker Studio** | Web-based IDE for building, training, and deploying models — all in one interface |
| **SageMaker Autopilot** | AutoML — automatically explores and creates the best ML model for your data |
| **Built-in Algorithms** | Pre-optimized algorithms (XGBoost, linear learner, K-means, etc.) for common ML tasks |
| **Notebook Instances** | Managed Jupyter notebooks with pre-installed libraries, scalable compute |
| **Training and Tuning** | Manages distributed training across multiple GPUs/instances; automatic hyperparameter tuning |
| **Model Deployment** | One-click deployment to production endpoints; supports multi-model endpoints |
| **SageMaker Ground Truth** | Data labeling service for text, images, and video (human-in-the-loop labeling) |
| **SageMaker Model Monitor** | Continuously monitors model quality in production; detects data drift and model degradation |
| **SageMaker Pipelines** | ML workflow orchestration and automation (MLOps); CI/CD for ML models |
| **SageMaker Canvas** | No-code ML model building for business analysts (no coding required) |
| **Geospatial ML** | Train and deploy models using geospatial data |

## Additional ML/AI Services (Exam Relevant)

### Amazon Personalize
- Fully managed real-time **personalization and recommendation** service.
- Uses the same ML technology as Amazon.com's recommendation engine.
- No ML expertise required — provide user interaction data; Personalize handles model training and serving.
- Use cases: product recommendations, personalized emails, content recommendations, search ranking.

### Amazon Forecast
- Fully managed **time-series forecasting** service using ML.
- Based on the same technology used by Amazon.com for supply chain forecasting.
- Combines historical data with additional variables (promotions, weather, holidays) for accurate predictions.
- Use cases: demand forecasting, financial planning, staffing forecasts, inventory optimization.
- Note: Different from Timestream — Timestream **stores** time-series data; Forecast **predicts** future values.

### Amazon Bedrock
- Fully managed service to **build Generative AI applications** using **Foundation Models (FMs)**.
- Access FMs from Amazon (Titan), Anthropic (Claude), Meta (Llama), Mistral, and others via a single API.
- **No infrastructure to manage** — serverless; no model training required.
- Features: Retrieval-Augmented Generation (RAG), agents, knowledge bases, fine-tuning.
- Use cases: chatbots, content generation, code generation, document summarization, customer service automation.
- Integrates with **Aurora ML** — call Bedrock models from SQL queries in Aurora.

---

## SageMaker vs AI Services vs Bedrock

| Service | Use When |
|---|---|
| **AI Services** (Rekognition, Transcribe, etc.) | Need a pre-built AI capability — no model training or ML knowledge |
| **Amazon Bedrock** | Need GenAI / LLM capabilities using foundation models — no training needed |
| **Amazon SageMaker** | Need to build, train, and deploy a **custom ML model** with full control |

---

## Key Points / Exam Tips

- **Trigger:** "build and train a custom ML model" → **Amazon SageMaker**
- **Trigger:** "AutoML — automatically find the best model" → **SageMaker Autopilot**
- **Trigger:** "monitor ML model in production for drift" → **SageMaker Model Monitor**
- **Trigger:** "label training data for ML" → **SageMaker Ground Truth**
- **Trigger:** "Generative AI, foundation models, LLMs, RAG" → **Amazon Bedrock**
- **Trigger:** "personalized recommendations, e-commerce recommendations" → **Amazon Personalize**
- **Trigger:** "ML-based time-series forecasting, demand planning" → **Amazon Forecast**
- Bedrock is **NOT SageMaker** — Bedrock uses pre-built foundation models; SageMaker is for building custom models
- Avoid: "use SageMaker for simple text analysis" — that's Comprehend; SageMaker is for custom model building
