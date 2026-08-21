# AWS Certified Machine Learning Engineer – Associate (MLA-C01) Study Guide

## Exam Overview

| Item | Detail |
|---|---|
| Questions | 65 (50 scored, 15 unscored) |
| Time | 130 minutes |
| Passing score | 720 (scaled 100–1000, compensatory — no per-domain minimum) |
| Cost | $150 USD |
| Delivery | Pearson VUE test center or online proctored |
| Question types | Multiple choice, multiple response, ordering, matching, case study |

**Domain weightings:**

1. Data Preparation for ML — **28%**
2. ML Model Development — **26%**
3. Deployment and Orchestration of ML Workflows — **22%**
4. ML Solution Monitoring, Maintenance, and Security — **24%**

The weighting is nearly flat — you can't skip a domain. The exam follows one ML system from raw data to healthy production model: prepare → develop → deploy → monitor/secure. Most questions ask "given this scenario, which service/configuration is the *best* (cheapest / lowest-effort / most secure) fit?"

**Out of scope:** designing full end-to-end architectures from scratch, deep expertise in multiple ML domains (NLP + CV), model quantization tradeoffs, setting org-wide ML strategy. This is an *engineering/MLOps* exam, not a data science exam.

---

## Domain 1: Data Preparation for ML (28%)

### 1.1 Ingest and Store Data

**Storage services — know when to use each:**
- **S3** — default landing zone for training data. Know storage classes (Standard, Intelligent-Tiering, Glacier), lifecycle policies, S3 Transfer Acceleration, multipart upload, and **S3 Express One Zone** for low-latency training reads.
- **EFS / FSx for Lustre** — shared POSIX file systems for training. FSx for Lustre links to S3 and gives high-throughput access for large distributed training jobs (common exam answer for "fastest data loading for SageMaker training").
- **EBS** — instance-attached storage; know gp3 vs io2 at a high level.

**Ingestion:**
- **Kinesis Data Streams** vs **Data Firehose** — Streams = real-time custom consumers; Firehose = fully managed delivery to S3/Redshift/OpenSearch with optional Lambda transform. Classic exam distinction.
- **AWS Glue** — serverless Spark ETL; Glue Data Catalog; Glue crawlers; Glue DataBrew (no-code visual prep).
- **Amazon MSK** (managed Kafka), **AWS DMS** (database migration/CDC), **AppFlow** (SaaS ingestion).
- **Data formats:** Parquet/ORC (columnar, analytics/training), Avro/JSON/CSV, RecordIO-protobuf (SageMaker Pipe mode). Know why Parquet beats CSV for cost and speed.

### 1.2 Transform Data and Perform Feature Engineering

- **SageMaker Data Wrangler** — visual data prep, 300+ transforms, export to pipelines/Feature Store.
- **SageMaker Processing** — run sklearn/Spark preprocessing jobs at scale.
- **SageMaker Feature Store** — online store (low-latency inference lookups) vs offline store (S3, training). Know when a feature store is the right answer (feature reuse/consistency between training and inference — avoiding training/serving skew).
- **Amazon EMR** — big-data Spark/Hadoop when Glue isn't enough.
- **Core feature engineering:** one-hot vs label encoding, scaling/normalization (StandardScaler vs MinMax), binning, log transforms, handling missing data (imputation strategies), outlier treatment, handling class imbalance (SMOTE, class weights, resampling), tokenization/embeddings for text.
- **Ground Truth** — human labeling workflows, active learning, Mechanical Turk vs private workforce.

### 1.3 Data Integrity and Prep for Modeling

- **Bias detection pre-training:** SageMaker Clarify — class imbalance (CI), difference in proportions of labels (DPL). Know Clarify does *both* bias detection and explainability (SHAP).
- **Data quality:** Glue Data Quality, Deequ concepts.
- **Data leakage** — recognize it in scenarios (target leakage, leaking test data into preprocessing).
- Train/validation/test splits, stratified sampling, time-series splits (no random shuffling of temporal data).
- **Encryption & compliance during prep:** KMS on S3, Lake Formation permissions, Macie for PII discovery.

---

## Domain 2: ML Model Development (26%)

### 2.1 Choose a Modeling Approach

- **Problem framing:** classification vs regression vs clustering vs forecasting vs anomaly detection vs recommendation. Many questions are "which algorithm/service fits this business problem?"
- **SageMaker built-in algorithms** (know the headline use case): XGBoost (tabular), Linear Learner, K-Means, PCA, Random Cut Forest (anomaly detection), DeepAR (forecasting), BlazingText, Object Detection, Image Classification, Factorization Machines, K-NN, IP Insights.
- **AI services (use before building custom):** Rekognition (images/video), Comprehend (NLP/sentiment/PII), Transcribe, Translate, Polly, Textract (document extraction), Lex (chatbots), Kendra (enterprise search), Personalize (recommendations), Forecast, Fraud Detector. Exam pattern: "least operational overhead" → managed AI service, not custom model.
- **Amazon Bedrock** — foundation models via API, model selection, **fine-tuning vs RAG vs prompt engineering** decision-making, Knowledge Bases (RAG), Agents, Guardrails. Newer exam content leans into GenAI — know when RAG (fresh/proprietary knowledge, no training) beats fine-tuning (style/format/domain adaptation) beats prompt engineering (cheapest, try first).
- **SageMaker JumpStart** — pre-trained models and solution templates.
- **Amazon Q** — high-level awareness.

### 2.2 Train and Refine Models

- **SageMaker training jobs:** script mode, built-in containers, bring-your-own-container (BYOC), Spot training with checkpointing (cost optimization — frequent exam answer), distributed training (data parallel vs model parallel), Pipe vs File vs FastFile input modes.
- **Hyperparameter tuning:** SageMaker Automatic Model Tuning (AMT) — random, Bayesian, Hyperband, grid. Know Bayesian/Hyperband are the efficient choices. Warm start tuning.
- **Core ML concepts:** overfitting vs underfitting and remedies (regularization L1/L2, dropout, early stopping, more data), learning rate effects, epochs/batch size, gradient descent variants, transfer learning/fine-tuning.
- **SageMaker Autopilot / Canvas** — AutoML and no-code.
- **SageMaker Experiments** — tracking runs; **SageMaker Debugger** — detect vanishing gradients, overfitting, hardware bottlenecks during training.

### 2.3 Analyze Model Performance

- **Classification metrics:** accuracy, precision, recall, F1, ROC-AUC, PR-AUC, confusion matrix. Know *when to prefer which* (imbalanced data → precision/recall/F1/PR-AUC, not accuracy; fraud → recall vs precision tradeoff).
- **Regression metrics:** RMSE, MAE, MAPE, R².
- **Baselines and validation:** k-fold cross-validation, holdout, comparing against a simple baseline.
- **SageMaker Clarify** post-training: SHAP feature attributions, bias metrics on predictions.
- **SageMaker Model Registry** — versioning, approval status, lineage.

---

## Domain 3: Deployment and Orchestration (22%)

### 3.1 Select Deployment Infrastructure

**The four SageMaker inference options — this is heavily tested:**

| Option | Use when |
|---|---|
| **Real-time endpoint** | Low-latency, sustained traffic, payload <6 MB, <60 s |
| **Serverless inference** | Intermittent/unpredictable traffic, tolerate cold starts, no GPU |
| **Asynchronous inference** | Large payloads (up to 1 GB), long processing (up to 1 hr), queue + scale-to-zero |
| **Batch transform** | No persistent endpoint needed; score whole datasets offline |

- **Multi-model endpoints** (many models, one container, shared instance — cost savings) vs **multi-container endpoints** vs **inference components**.
- **Production variants** — A/B testing traffic splits; **shadow variants** — test new model on copied traffic without affecting responses.
- **Deployment guardrails:** blue/green with all-at-once, canary, and linear traffic shifting; auto-rollback on alarms.
- **Instance selection:** GPU (p/g families) vs CPU (m/c), **Inferentia (inf1/inf2)** and **Trainium (trn1)** for cost-efficient inference/training, **Elastic Inference** (legacy), **SageMaker Neo** (compile/optimize for edge), **IoT Greengrass** for edge deployment.
- **Auto scaling endpoints** — target tracking on InvocationsPerInstance.
- Alternatives: ECS/EKS/Lambda for hosting models outside SageMaker (Lambda for lightweight, spiky, <15 min).

### 3.2 Infrastructure as Code and Containers

- **CloudFormation** and **AWS CDK** basics — what they are, when to use.
- **ECR** — container registry for SageMaker images; extending prebuilt containers vs BYOC.
- **Docker fundamentals** as they apply to SageMaker (ENTRYPOINT, /opt/ml paths conceptually).

### 3.3 Orchestrate ML Workflows and CI/CD

- **SageMaker Pipelines** — native ML pipeline: Processing → Training → Evaluation → Condition → RegisterModel steps; caching; the default exam answer for "orchestrate ML workflow."
- **Step Functions** — general orchestration incl. non-ML steps; **MWAA (Airflow)** — when a team already uses Airflow.
- **EventBridge** — trigger retraining on schedule or on events (e.g., drift alarm → retrain pipeline).
- **CI/CD:** CodePipeline, CodeBuild, (CodeCommit deprecated for new customers — GitHub/GitLab integration), SageMaker Projects (MLOps templates). Understand automated retraining loops: drift detected → EventBridge → pipeline → evaluate → register → approve → deploy.

---

## Domain 4: Monitoring, Maintenance, and Security (24%)

### 4.1 Monitor Model Inference

- **SageMaker Model Monitor** — the big one. Four monitor types: **data quality**, **model quality** (needs ground truth labels), **bias drift**, **feature attribution drift**. Baseline from training data, scheduled monitoring jobs, violations → CloudWatch → alarm → retrain.
- **Concept drift vs data drift** — know the difference (relationship between X and y changes vs input distribution changes).
- **A/B testing / shadow testing** for validating new models in production.

### 4.2 Monitor and Optimize Infrastructure and Cost

- **CloudWatch** (metrics, logs, alarms, dashboards), **CloudTrail** (API audit), **AWS X-Ray** (tracing), EventBridge.
- **SageMaker Inference Recommender** — right-size endpoint instances; load testing.
- **Cost tools:** Cost Explorer, Budgets, cost allocation tags, Compute Optimizer.
- **Cost levers (exam favorites):** Spot training + checkpoints, multi-model endpoints, serverless/async inference, Savings Plans, Inferentia/Graviton, S3 lifecycle policies, stopping idle Studio notebooks (lifecycle configs).

### 4.3 Secure AWS ML Resources

- **IAM:** SageMaker execution roles, least privilege, identity vs resource policies, condition keys. Common scenario: training job can't read S3 → execution role permissions or KMS key policy.
- **Network isolation:** SageMaker in **VPC mode**, no direct internet access, **VPC endpoints / PrivateLink** for S3, SageMaker API, and SageMaker Runtime (recurring exam answer for "traffic must not traverse the internet").
- **Encryption:** KMS for S3, EBS volumes on training instances, inter-container traffic encryption for distributed training.
- **Secrets Manager** vs Parameter Store.
- **SageMaker Role Manager**, network isolation flag for training jobs, Ground Truth private workforce security.
- **Governance/Responsible AI:** SageMaker Model Cards, Model Dashboard, Clarify, Bedrock Guardrails, Audit via CloudTrail; Macie for PII in S3.

---

## Service Cheat Sheet (one-liners to memorize)

- **SageMaker Studio** — IDE for everything ML
- **Data Wrangler** — visual data prep
- **Feature Store** — online + offline feature storage, fixes training/serving skew
- **Ground Truth** — data labeling
- **Clarify** — bias + explainability (SHAP)
- **Debugger** — training-time issue detection
- **Experiments** — run tracking
- **Autopilot** — AutoML
- **JumpStart** — pretrained models/templates
- **Model Registry** — version + approve models
- **Pipelines** — ML workflow orchestration
- **Model Monitor** — production drift detection
- **Inference Recommender** — endpoint right-sizing
- **Neo** — compile models for edge/hardware
- **Bedrock** — foundation models, RAG (Knowledge Bases), Agents, Guardrails
- **Kinesis Firehose** — managed streaming delivery to S3
- **Glue** — serverless ETL + Data Catalog
- **EMR** — managed Spark/Hadoop
- **Athena** — SQL on S3
- **Step Functions / EventBridge** — orchestration + event triggers
- **Macie** — PII discovery in S3

---

## 6-Week Study Plan

**Week 1 — Foundations + Domain 1.** Read the official exam guide end to end. Cover S3, Glue, Kinesis, data formats, feature engineering. Hands-on: Data Wrangler flow on a sample dataset, load to Feature Store.

**Week 2 — Domain 2 (modeling).** Built-in algorithms, metrics, tuning, over/underfitting. Hands-on: XGBoost training job with Spot + checkpointing, run an AMT tuning job.

**Week 3 — Domain 2 (GenAI) + Domain 3 start.** Bedrock (RAG vs fine-tune vs prompting), JumpStart. The four inference options — build a serverless and a real-time endpoint.

**Week 4 — Domain 3.** SageMaker Pipelines end to end (process → train → evaluate → register → deploy). Deployment guardrails, blue/green, shadow tests. CI/CD concepts.

**Week 5 — Domain 4.** Model Monitor all four types, drift scenarios, VPC/PrivateLink/KMS security patterns, cost optimization levers. Do the AWS Skill Builder official practice question set; review every miss.

**Week 6 — Practice exams + gaps.** Two or three full timed practice exams (Tutorials Dojo and/or Skill Builder). Target 80%+ before booking. Re-drill weak domains, reread the service cheat sheet daily.

Given your background (Docker, Ansible, CI/CD, AWS EC2), Domains 3 and 4 will feel familiar — the orchestration, IaC, VPC, and IAM material maps closely to what you already do. Budget extra time for Domain 2's ML theory (metrics, algorithm selection, tuning) since that's the least DevOps-shaped part of the exam.

---

## Resources

- **Official exam guide** — docs.aws.amazon.com (search "MLA-C01 exam guide") — the source of truth for scope
- **AWS Skill Builder** — free MLA-C01 exam prep course + official practice question set (20 free); the paid tier has a full practice exam
- **Tutorials Dojo** — MLA-C01 practice exams and cheat sheets (widely regarded as closest to real difficulty)
- **SageMaker Developer Guide** — skim the inference options, Pipelines, and Model Monitor chapters
- **AWS free tier / Workshops** (workshops.aws) — hands-on labs; hands-on time with SageMaker Studio matters more than videos

## Exam-Day Tactics

- ~2 minutes per question; flag and move on.
- Watch for qualifier words: **"least operational overhead"** → managed/serverless service; **"most cost-effective"** → Spot, serverless, batch, multi-model endpoints; **"lowest latency"** → real-time endpoint, online feature store, provisioned throughput.
- Eliminate answers that violate a stated constraint (e.g., "no internet access" kills anything without VPC endpoints).
- Ordering questions: think through the ML lifecycle arc (data → train → evaluate → register → deploy → monitor).
- New AWS features must be GA for 3+ months to appear — don't overthink bleeding-edge services.
