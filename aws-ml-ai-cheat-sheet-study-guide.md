# AWS Machine Learning & AI Services — Cheat Sheet Study Guide

Based on the service list in the Tutorials Dojo *AWS Machine Learning and AI* cheat sheets section. Each entry gives what the service is, the key points worth memorizing, and when it's the right answer.

**Relevance key for MLA-C01:**
- 🟢 **Core** — commonly tested, know it well
- 🟡 **Supporting** — know what it does and its one-line use case
- ⚪ **Peripheral** — newer/niche (often GenAI-developer or specialty scope); recognize the name, don't over-invest

---

## 1. Amazon SageMaker Family (the heart of the exam)

### 🟢 Amazon SageMaker (AI)
Fully managed platform for the entire ML lifecycle: build, train, tune, deploy, monitor.
- **Studio** — web IDE for everything ML.
- **Training jobs** — managed compute for training; built-in algorithms, script mode, or bring-your-own-container (BYOC). Supports **Managed Spot Training** (up to ~90% cheaper, use checkpointing) and **distributed training** (data-parallel vs model-parallel).
- **Automatic Model Tuning (AMT)** — hyperparameter optimization (Bayesian, Hyperband, random, grid).
- **Inference options (know all four):** real-time endpoint (low latency, steady traffic), serverless (intermittent, scale-to-zero, cold starts), asynchronous (large payloads up to 1 GB, long runtime), batch transform (offline scoring, no persistent endpoint).
- **Multi-model / multi-container endpoints** — host many models cheaply.
- **Production variants & shadow variants** — A/B and shadow testing.
- **Deployment guardrails** — blue/green, canary, linear with auto-rollback.
- **Built-in algorithms to recognize:** XGBoost, Linear Learner, K-Means, PCA, DeepAR (forecasting), Random Cut Forest (anomaly), BlazingText, Object Detection, Factorization Machines, IP Insights, K-NN.
- Supporting tools: **Experiments** (run tracking), **Debugger** (training issue detection), **Model Registry** (versioning/approval), **Pipelines** (ML workflow CI/CD), **Model Monitor** (drift), **Inference Recommender** (right-sizing), **Neo** (compile for edge/hardware), **JumpStart** (pretrained models/templates), **Autopilot** (AutoML), **Canvas** (no-code), **Ground Truth** (labeling), **Model Cards / Model Dashboard** (governance).

### 🟢 SageMaker Feature Store
Central repository for ML features.
- **Online store** = low-latency lookups for real-time inference; **Offline store** = S3-backed for training and batch.
- Solves **training/serving skew** and enables feature reuse across teams/models. Exam answer whenever a scenario needs consistent features between training and inference.

### 🟢 SageMaker Data Wrangler
Visual, low-code data prep — 300+ built-in transforms, data import from many sources, quick analysis/visualization, exports to Pipelines or Feature Store. Right answer for "prepare/clean data with minimal code."

### 🟢 SageMaker Clarify
**Bias detection** (pre-training and post-training) + **explainability** via SHAP feature attributions. Integrates with Model Monitor for bias/feature-attribution **drift**. Right answer for "detect bias" or "explain predictions."

---

## 2. Foundation Models & Generative AI

### 🟢 Amazon Bedrock
Fully managed access to foundation models (Anthropic, Meta, Mistral, Amazon Titan, etc.) via a single API. No infrastructure to manage.
- Customize with **prompt engineering** (cheapest, try first) → **RAG / Knowledge Bases** (inject fresh/proprietary data, no retraining) → **fine-tuning** (adapt style/format/domain).
- **Guardrails** — filter harmful content, block topics, redact PII.
- **Agents** — multi-step task orchestration with tool/API calls.
- Serverless, pay-per-token. Newer MLA-C01 content leans into Bedrock — know the RAG vs fine-tune vs prompt decision cold.

### 🟡 Amazon Bedrock Knowledge Bases
Managed **RAG** implementation: ingests your documents, chunks + embeds them into a vector store, and retrieves relevant context at query time. Answer for "give the model access to private/current data without training it."

### 🟡 Amazon Titan
Amazon's own family of foundation models available through Bedrock — text generation, embeddings (for RAG/semantic search), and image generation. Titan Embeddings is the common pick for vectorizing text in a RAG pipeline.

### 🟡 Amazon Q
AWS's generative AI assistant. **Q Business** (enterprise Q&A over your data), **Q Developer** (coding/AWS help). Answer for "managed GenAI assistant with least setup."

### ⚪ Amazon Bedrock — Data Automation / Flows / Prompt Management
Peripheral tooling around Bedrock: **Data Automation** (extract insights from unstructured docs/images/audio/video), **Flows** (visually chain prompts, models, and logic into workflows), **Prompt Management** (version and manage prompt templates). Recognize them; more relevant to the GenAI Developer specialty than MLA-C01.

### ⚪ Amazon Bedrock AgentCore (+ Runtime, Browser Tools, Identity, Observability, Memory, Gateway, Code Interpreter)
A newer suite for building and operating production AI agents (secure runtime, tool/browser access, identity, memory, observability, gateways, sandboxed code execution). Enterprise agent infrastructure — largely **out of MLA-C01 scope**. Check the linked cheat sheet for current detail if you encounter it.

### ⚪ AWS Agent Squad / AWS Strands Agents
Frameworks/SDKs for multi-agent orchestration and agent development. Developer tooling, peripheral to this exam.

---

## 3. AI Application Services (managed, task-specific — "least ML expertise / lowest effort")

> **Exam pattern:** when a question stresses *no ML team*, *least operational overhead*, or *fastest to build*, the answer is usually one of these managed services rather than a custom SageMaker model.

### 🟢 Amazon Comprehend
Natural language processing: sentiment, entities, key phrases, language detection, topic modeling, and **PII detection/redaction**. Custom classification and entity recognition supported.

### 🟡 Amazon Comprehend Medical
NLP specialized for medical text — extracts conditions, medications, dosages, PHI from clinical notes. HIPAA-eligible.

### 🟢 Amazon Rekognition
Image and video analysis: object/scene detection, facial analysis and comparison, celebrity recognition, text-in-image, and content moderation. Custom Labels for domain-specific objects.

### 🟢 Amazon Textract
Extracts text, forms, tables, and key-value pairs from scanned documents (goes beyond plain OCR). Answer for "digitize forms/invoices/documents."

### 🟡 Amazon Transcribe
Automatic speech-to-text. Supports custom vocabularies, speaker diarization, and PII redaction. **Transcribe Medical** for clinical speech.

### 🟡 Amazon Polly
Text-to-speech with natural/neural voices; **Speech Synthesis Markup Language (SSML)** for fine control.

### 🟡 Amazon Translate
Neural machine translation across many languages; supports custom terminology.

### 🟡 Amazon Lex
Build conversational chatbots (same tech behind Alexa) — automatic speech recognition + natural language understanding. Integrates with Lambda for fulfillment.

### 🟡 Amazon Kendra
Intelligent enterprise search — natural-language queries over your documents with ML ranking. Often paired with GenAI as a retriever for RAG.

### 🟢 Amazon Personalize
Real-time personalized recommendations as a managed service (same tech lineage as Amazon.com). You bring interaction data; it builds and hosts the recommender. Answer for "add recommendations without building a model."

### 🟡 Amazon Fraud Detector
Managed fraud detection — builds custom fraud models from your historical data plus AWS fraud expertise. Answer for "detect online fraud/fake accounts with minimal ML work."

### 🟡 Amazon Augmented AI (A2I)
Adds **human review** to ML predictions when confidence is low or for auditing/compliance. Plugs into Rekognition, Textract, or custom models. Answer for "human-in-the-loop review workflow."

---

## 4. Inference Acceleration & Edge

### 🟡 Amazon Elastic Inference (legacy)
Attach fractional GPU acceleration to EC2/SageMaker instances to cut inference cost. Being superseded by **Inferentia** (inf1/inf2) chips — if a question asks for the most cost-effective *current* inference acceleration, lean toward Inferentia/Neo rather than Elastic Inference.

### ⚪ AWS DeepLens
Deep-learning-enabled video camera for hands-on learning/edge CV demos. Legacy/educational; rarely more than name recognition.

---

## 5. AI for Developers & Operations

### 🟡 Amazon CodeGuru (Reviewer / Security / Profiler)
ML-powered code tooling: **Reviewer** flags code-quality issues and bugs in pull requests; **Security** finds security vulnerabilities; **Profiler** identifies runtime performance/cost inefficiencies.

### 🟡 Amazon DevOps Guru
ML-powered operational insights — detects anomalous application/infra behavior and surfaces likely causes and remediation, using CloudWatch/other signals. Answer for "ML-based ops anomaly detection with no model-building."

---

## 6. Industry & Emerging (mostly name-recognition for MLA-C01)

### ⚪ AWS HealthLake
Managed data lake for health data in FHIR format, with integrated NLP to structure clinical text for analytics/ML.

### ⚪ AWS HealthScribe
Generative-AI clinical documentation — turns patient–clinician conversations into structured clinical notes.

### ⚪ AWS AI Factories / AWS Transform
Newer enterprise offerings (large-scale AI infrastructure / AI-assisted modernization & migration). Peripheral to MLA-C01 — recognize the names only.

---

## How to Use This With the Real Cheat Sheets

Work top-down by relevance: master everything under **SageMaker** (Section 1) and **Bedrock** (Section 2), then learn the one-line "when to use" for each **AI application service** (Section 3) — those two skills cover the large majority of ML/AI service questions on MLA-C01. Skim Sections 4–6 for name recognition so an unfamiliar service in an answer choice doesn't throw you.

For deeper per-service detail, open the individual Tutorials Dojo cheat sheet for any service you feel shaky on — this guide mirrors that section's service list so you can go one-for-one.

### Highest-yield "which service?" discriminators to drill
- **Comprehend vs Kendra vs Lex** — analyze text vs search documents vs build a chatbot.
- **Rekognition vs Textract** — general images/video vs documents/forms.
- **Personalize vs Forecast vs Fraud Detector** — recommendations vs time-series forecasting vs fraud.
- **RAG (Knowledge Bases) vs fine-tuning vs prompt engineering** — fresh/proprietary facts vs behavior/format adaptation vs cheapest first attempt.
- **Custom SageMaker model vs managed AI service** — when the scenario says "no ML expertise / least effort," pick the managed service.
- **Elastic Inference vs Inferentia/Neo** — prefer the current AWS silicon (Inferentia + Neo) for cost-effective inference.
