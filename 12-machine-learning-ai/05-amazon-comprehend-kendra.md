# Amazon Comprehend and Amazon Kendra

## Amazon Comprehend — NLP Text Analysis

### What Is Comprehend?
- **Amazon Comprehend** — uses **Natural Language Processing (NLP)** to extract insights from text documents.
- Analyzes text to find relationships, entities, sentiment, key phrases, and topics.

### Key Capabilities

| Capability | Description |
|---|---|
| **Language detection** | Identify the language of the text |
| **Entity recognition** | Extract people, places, brands, dates, events |
| **Key phrase extraction** | Identify important phrases and concepts in text |
| **Sentiment analysis** | Determine if text is Positive, Negative, Neutral, or Mixed |
| **Topic modeling** | Automatically group a collection of documents by topic |
| **PII detection** | Find and redact personally identifiable information |
| **Custom classification** | Train Comprehend to classify documents into custom categories |
| **Custom entity recognition** | Train to detect domain-specific entities (e.g., medical codes) |

### Amazon Comprehend Medical
- Specialized version for **medical text**.
- Extracts medical information: conditions, medications, dosages, procedures, anatomy.
- Input: doctor's notes, clinical trial reports, radiology reports.

### Use Cases
- Analyze customer reviews/feedback to identify pain points and satisfaction drivers
- Automatically categorize support tickets by topic
- Detect PII in documents for compliance
- Sentiment analysis of social media mentions and product reviews

---

## Amazon Kendra — Intelligent Enterprise Search

### What Is Kendra?
- **Amazon Kendra** — ML-powered **intelligent enterprise search** service.
- Allows employees and customers to find information across multiple content repositories using natural language questions.
- Returns **direct answers**, not just a list of links.

### Key Features
- **Natural language search** — search by asking questions: "How do I raise an IT ticket for a new laptop?"
- Indexes 180+ document types: HTML, PowerPoint, Word, PDF, SharePoint, Confluence, Jira, OneDrive, Slack, Dropbox.
- **Incremental learning** — learns from user feedback (thumbs up/down, click-through) to improve search relevance.
- **Over 40 data source connectors** for common enterprise content repositories.

### Use Cases
- Employee self-service portals — search company wikis, HR policies, IT documentation
- Customer-facing help centers
- Research and knowledge management across large document collections
- FAQ and troubleshooting guides

---

## Comprehend vs Kendra — Comparison

| Feature | Comprehend | Kendra |
|---|---|---|
| Purpose | Analyze and extract insights from text | Search across enterprise documents |
| Input | Raw text | Documents from connected repositories |
| Output | Entities, sentiment, topics, key phrases | Direct answers to questions |
| Use case | Text analytics, sentiment, classification | Enterprise search, FAQ, document search |
| ML approach | Pre-trained NLP models | ML-powered information retrieval |

---

## Key Points / Exam Tips

- **Trigger:** "NLP, extract entities, sentiment analysis, key phrases from text" → **Amazon Comprehend**
- **Trigger:** "analyze customer feedback, reviews, call transcripts for insights" → **Comprehend**
- **Trigger:** "intelligent enterprise search, natural language questions across company documents" → **Amazon Kendra**
- **Trigger:** "extract medical information from clinical notes, lab reports" → **Comprehend Medical**
- Comprehend ANALYZES text; Kendra SEARCHES through documents
- Kendra gives **direct answers** (question answering), not just document lists
- Avoid: "use Kendra for NLP analysis" or "use Comprehend for document search" — wrong direction
