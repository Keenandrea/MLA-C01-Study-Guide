# MLA-C01 Glossary of Terms & Definitions

A comprehensive reference of terms you may encounter on the AWS Certified Machine Learning Engineer – Associate exam. Organized by category. Terms marked ★ are high-frequency exam material.

---

## 1. Core Machine Learning Concepts

**Supervised learning** — Training on labeled data (input → known output). Covers classification and regression.

**Unsupervised learning** — Finding structure in unlabeled data (clustering, dimensionality reduction, anomaly detection).

**Semi-supervised learning** — Mix of a small labeled set and a large unlabeled set (e.g., Ground Truth active learning).

**Reinforcement learning** — An agent learns by taking actions in an environment to maximize cumulative reward.

**Classification** — Predicting a discrete class/label (binary or multiclass).

**Regression** — Predicting a continuous numeric value.

**Clustering** — Grouping similar data points without labels (e.g., K-Means).

**Forecasting** — Predicting future values in a time series (e.g., DeepAR, Amazon Forecast).

**Anomaly detection** — Identifying rare/outlier data points (e.g., Random Cut Forest).

**Recommendation** — Predicting user preferences/items (e.g., Factorization Machines, Amazon Personalize).

**Feature** — An input variable/column used by the model.

**Label / target** — The output variable the model predicts.

**★ Overfitting** — Model memorizes training data (incl. noise) and generalizes poorly. Fixes: regularization, dropout, early stopping, more data, simpler model.

**★ Underfitting** — Model is too simple to capture the pattern; poor on both training and test data. Fixes: more features, more complex model, train longer.

**Bias–variance tradeoff** — High bias = underfitting; high variance = overfitting. Goal is the balance between them.

**★ Regularization** — Penalizing model complexity to reduce overfitting. **L1 (Lasso)** drives some weights to zero (feature selection); **L2 (Ridge)** shrinks weights smoothly.

**Dropout** — Randomly disabling neurons during training to prevent co-adaptation (neural-net regularization).

**Early stopping** — Halting training when validation performance stops improving.

**Epoch** — One full pass through the training dataset.

**Batch size** — Number of samples processed before the model updates weights.

**Learning rate** — Step size for weight updates. Too high → diverges; too low → slow/stuck.

**Gradient descent** — Optimization that moves weights in the direction that reduces loss. Variants: batch, stochastic (SGD), mini-batch.

**Loss / cost function** — The quantity the model minimizes during training (e.g., cross-entropy, MSE).

**Hyperparameter** — A setting configured *before* training (learning rate, tree depth, # layers). Contrast with parameters (weights) learned *during* training.

**★ Transfer learning** — Reusing a pretrained model and adapting it to a new task with less data/compute.

**Fine-tuning** — Continuing training of a pretrained model on task-specific data.

**Ensemble** — Combining multiple models (bagging, boosting, stacking) for better performance.

**Boosting** — Sequentially training models where each corrects the last (e.g., XGBoost).

**★ Data leakage** — Information from outside the training set (or from the target) contaminating training, inflating performance. Includes target leakage and leaking test data into preprocessing.

**Training / validation / test split** — Partitioning data to train, tune, and finally evaluate.

**★ Cross-validation (k-fold)** — Splitting data into k folds, training/validating k times for robust performance estimates.

**Stratified sampling** — Splitting so class proportions are preserved (important for imbalanced data).

**★ Class imbalance** — One class vastly outnumbers others. Handle with resampling, **SMOTE** (synthetic minority oversampling), class weights, or choosing the right metric.

**Curse of dimensionality** — Too many features relative to samples degrades performance; motivates dimensionality reduction.

**Dimensionality reduction** — Reducing feature count while preserving signal (e.g., PCA).

---

## 2. Data Preparation & Feature Engineering

**★ ETL / ELT** — Extract-Transform-Load (transform before load) vs Extract-Load-Transform (load then transform).

**Data ingestion** — Bringing data into AWS (batch or streaming).

**Batch vs streaming** — Bulk processing at intervals vs continuous real-time processing.

**One-hot encoding** — Converting a categorical value into binary indicator columns.

**Label encoding** — Mapping categories to integers (implies ordinality — use with care).

**★ Normalization / scaling** — Rescaling features. **Min-Max** → [0,1]; **Standardization (Z-score)** → mean 0, std 1.

**Binning / bucketing** — Grouping continuous values into discrete ranges.

**Imputation** — Filling missing values (mean/median/mode, model-based, forward-fill).

**Outlier** — An extreme value; may be removed, capped (winsorized), or transformed.

**Log transform** — Compressing skewed distributions.

**Tokenization** — Splitting text into tokens (words/subwords) for NLP.

**Embedding** — Dense vector representation of tokens/items capturing semantic meaning.

**Vectorization** — Converting data (text/categorical) into numeric vectors.

**TF-IDF** — Term Frequency–Inverse Document Frequency; weights words by importance in a document vs corpus.

**★ Feature engineering** — Creating/transforming features to improve model performance.

**Feature selection** — Choosing the most relevant features (reduces overfitting, cost).

**★ Training/serving skew** — Inconsistency between features computed at training vs inference. Feature Store exists to prevent this.

**Data catalog** — Metadata repository describing datasets (e.g., Glue Data Catalog).

**Schema** — Structure/definition of a dataset's columns and types.

**★ Columnar formats (Parquet/ORC)** — Store data by column; faster and cheaper for analytics/ML than row formats (CSV/JSON).

**RecordIO-protobuf** — Efficient binary format used by SageMaker Pipe mode.

**Data quality** — Validity, completeness, consistency of data (Glue Data Quality, Deequ).

---

## 3. AWS Data Services

**★ Amazon S3** — Object storage; the default data lake / training data store. Storage classes: Standard, Intelligent-Tiering, Glacier (archival).

**S3 Express One Zone** — Single-AZ, ultra-low-latency S3 for high-performance workloads.

**S3 lifecycle policy** — Rules to transition/expire objects to save cost.

**S3 Transfer Acceleration** — Faster uploads over long distances via edge locations.

**★ AWS Glue** — Serverless Spark ETL; includes **Glue Data Catalog**, **crawlers** (auto-infer schema), and **DataBrew** (no-code prep).

**Amazon EMR** — Managed Hadoop/Spark clusters for big-data processing when Glue isn't enough.

**Amazon Athena** — Serverless SQL queries directly on S3 data.

**★ Kinesis Data Streams** — Real-time streaming ingestion with custom consumers; you manage shards/scaling.

**★ Kinesis Data Firehose** — Fully managed streaming *delivery* to S3/Redshift/OpenSearch, with optional Lambda transform.

**Amazon MSK** — Managed Apache Kafka.

**AWS DMS** — Database Migration Service; supports change data capture (CDC).

**Amazon AppFlow** — Managed data transfer from SaaS apps (Salesforce, etc.) to AWS.

**★ FSx for Lustre** — High-throughput parallel file system linked to S3; speeds up SageMaker training data loading.

**Amazon EFS** — Managed elastic NFS file system, shareable across instances.

**Amazon EBS** — Block storage attached to EC2/SageMaker instances (gp3, io2).

**AWS Lake Formation** — Central governance/permissions for a data lake.

**Amazon Redshift** — Managed data warehouse; Redshift ML lets you train from SQL.

**Amazon Macie** — Discovers/classifies PII and sensitive data in S3.

---

## 4. Amazon SageMaker (the core of the exam)

**★ SageMaker Studio** — Web-based IDE for the full ML lifecycle.

**SageMaker notebooks** — Managed Jupyter environments.

**★ SageMaker Data Wrangler** — Visual data prep with 300+ built-in transforms.

**SageMaker Processing** — Run preprocessing/postprocessing/evaluation jobs at scale (sklearn, Spark).

**★ SageMaker Feature Store** — Central feature repository. **Online store** = low-latency inference lookups; **Offline store** = S3-backed for training.

**SageMaker Ground Truth** — Managed data labeling with human workforces + active learning.

**★ SageMaker built-in algorithms** — Prebuilt algorithms: XGBoost, Linear Learner, K-Means, PCA, DeepAR, Random Cut Forest, BlazingText, Factorization Machines, Object Detection, IP Insights, K-NN, etc.

**Script mode** — Bring your own training script into a SageMaker-managed framework container.

**★ BYOC (Bring Your Own Container)** — Package a custom Docker image for training/inference in SageMaker.

**Input modes** — **File** (copy all data first), **Pipe** (stream during training), **FastFile** (stream but access like a file). Pipe/FastFile speed up large datasets.

**★ Managed Spot Training** — Use spare EC2 capacity (up to ~90% cheaper) with checkpointing to survive interruptions.

**Distributed training** — **Data parallel** (split data across GPUs) vs **model parallel** (split the model across GPUs).

**★ Automatic Model Tuning (AMT)** — Hyperparameter optimization. Strategies: grid, random, **Bayesian**, **Hyperband**. Warm start reuses prior tuning jobs.

**SageMaker Autopilot** — AutoML: auto-builds/tunes models with visibility into the process.

**SageMaker Canvas** — No-code ML for business analysts.

**★ SageMaker JumpStart** — Hub of pretrained models and prebuilt solution templates (incl. foundation models).

**★ SageMaker Clarify** — Detects **bias** (pre- and post-training) and provides **explainability** via SHAP.

**SageMaker Debugger** — Captures/inspects training tensors to catch overfitting, vanishing gradients, bottlenecks.

**SageMaker Experiments** — Tracks and compares training runs, parameters, metrics.

**★ SageMaker Model Registry** — Catalog of model versions with approval status and lineage.

**★ SageMaker Pipelines** — Native CI/CD workflow orchestration for ML (Processing → Training → Evaluate → Condition → Register steps); supports step caching.

**SageMaker Projects** — MLOps templates wiring up repos, pipelines, and CI/CD.

**★ SageMaker Model Monitor** — Detects production drift. Four types: **data quality**, **model quality** (needs ground truth), **bias drift**, **feature attribution drift**.

**SageMaker Inference Recommender** — Load tests to recommend the right instance type/size for an endpoint.

**★ SageMaker Neo** — Compiles/optimizes models to run efficiently on specific hardware/edge.

**SageMaker Model Cards** — Documented record of a model's intent, training, and performance (governance).

**SageMaker Model Dashboard** — Central view of models, endpoints, and monitoring status.

**SageMaker Role Manager** — Simplifies creating scoped IAM roles for ML personas.

---

## 5. Model Deployment & Inference

**★ Real-time endpoint** — Persistent HTTPS endpoint for low-latency, synchronous predictions (payload <6 MB, <60s).

**★ Serverless inference** — Auto-provisions/scales (incl. scale to zero) for intermittent traffic; tolerates cold starts; no GPU.

**★ Asynchronous inference** — Queues requests; handles large payloads (up to 1 GB) and long runtimes (up to 1 hr); scales to zero.

**★ Batch transform** — Offline scoring of a whole dataset without a persistent endpoint.

**★ Multi-model endpoint (MME)** — Host many models behind one endpoint/container to save cost; loads models on demand.

**Multi-container endpoint** — Multiple distinct containers on one endpoint (direct or as a serial inference pipeline).

**Inference components** — Fine-grained way to pack/scale multiple models on shared endpoint compute.

**★ Production variants** — Multiple model versions behind one endpoint with weighted traffic (A/B testing).

**★ Shadow testing** — Send a copy of production traffic to a new variant without returning its responses to users.

**★ Deployment guardrails** — Safe rollout strategies: **blue/green**, **canary**, **linear** traffic shifting, with auto-rollback on CloudWatch alarms.

**Auto scaling** — Adjust endpoint instance count via target tracking (e.g., InvocationsPerInstance).

**Cold start** — Latency spike when a scaled-to-zero/serverless resource spins up.

**★ Inferentia (inf1/inf2)** — AWS custom chips for cost-efficient inference.

**★ Trainium (trn1)** — AWS custom chips for cost-efficient training.

**AWS Graviton** — ARM-based CPUs offering better price/performance.

**Elastic Inference** — (Legacy) attach fractional GPU acceleration to instances.

**AWS IoT Greengrass** — Deploy/run ML models at the edge.

**Model latency vs throughput** — Latency = per-request response time; throughput = requests handled per unit time.

---

## 6. Orchestration, IaC & CI/CD

**★ AWS CloudFormation** — Declarative infrastructure-as-code via templates.

**AWS CDK** — Define infrastructure using programming languages; synthesizes to CloudFormation.

**★ Amazon ECR** — Container registry for Docker images used by SageMaker.

**★ AWS Step Functions** — Serverless workflow orchestration (state machines) across many services, including non-ML steps.

**★ Amazon EventBridge** — Event bus; trigger pipelines on schedules or events (e.g., drift alarm → retrain).

**Amazon MWAA** — Managed Apache Airflow for complex DAG orchestration.

**AWS CodePipeline** — Managed CI/CD pipeline orchestration.

**AWS CodeBuild** — Managed build/test service.

**AWS Lambda** — Serverless functions (<15 min); lightweight inference, glue logic, triggers.

**CI/CD** — Continuous Integration / Continuous Delivery: automated build, test, and deploy.

**★ Automated retraining loop** — Drift detected → EventBridge → pipeline retrains → evaluate → register → approve → deploy.

**Model lineage** — Tracked history of data, code, and steps that produced a model.

---

## 7. Monitoring, Security & Governance

**★ Amazon CloudWatch** — Metrics, logs, alarms, dashboards for AWS resources and endpoints.

**★ AWS CloudTrail** — Records API calls for audit/governance.

**AWS X-Ray** — Distributed tracing for request analysis.

**★ Data drift** — Input feature distribution changes over time.

**★ Concept drift** — The relationship between inputs and target changes over time.

**Feature attribution drift** — Shift in which features drive predictions (detected by Clarify/Model Monitor).

**Baseline** — Reference statistics (from training data) that monitoring compares production against.

**★ IAM** — Identity and Access Management: users, roles, policies. **Execution role** = the role SageMaker assumes to access resources.

**Least privilege** — Grant only the permissions needed.

**Identity-based vs resource-based policy** — Attached to a principal vs attached to a resource (e.g., S3 bucket policy).

**★ VPC (Virtual Private Cloud)** — Isolated network. Running SageMaker in **VPC mode** removes direct internet access.

**★ VPC endpoint / PrivateLink** — Private connectivity to AWS services (S3, SageMaker API/Runtime) without traversing the internet — common exam answer for "traffic must stay private."

**Security group / NACL** — Instance-level firewall vs subnet-level firewall.

**★ AWS KMS** — Key Management Service for encryption (S3, EBS, inter-container traffic).

**Encryption at rest vs in transit** — Protecting stored data vs data moving over the network.

**Network isolation** — Training-job flag that disables all outbound network access.

**AWS Secrets Manager** — Stores/rotates secrets (DB credentials, API keys).

**AWS Systems Manager Parameter Store** — Stores config/secrets (cheaper, no auto-rotation).

**★ Responsible AI** — Fairness, explainability, transparency, governance across the ML lifecycle (Clarify, Model Cards, Bedrock Guardrails).

**Compensatory scoring** — Exam scoring where strength in some domains offsets weakness in others (no per-domain pass bar).

---

## 8. Evaluation Metrics

**★ Confusion matrix** — Table of True/False Positives/Negatives.

**★ Accuracy** — Correct predictions ÷ total. Misleading on imbalanced data.

**★ Precision** — TP ÷ (TP + FP). "Of predicted positives, how many were right?" Prioritize when false positives are costly.

**★ Recall (Sensitivity / TPR)** — TP ÷ (TP + FN). "Of actual positives, how many did we catch?" Prioritize when false negatives are costly (fraud, disease).

**★ F1 score** — Harmonic mean of precision and recall; good for imbalanced classes.

**Specificity (TNR)** — TN ÷ (TN + FP).

**★ ROC curve / AUC** — TPR vs FPR across thresholds; AUC summarizes ranking ability (1.0 = perfect, 0.5 = random).

**★ PR-AUC** — Precision-Recall AUC; preferred over ROC-AUC for heavily imbalanced data.

**Threshold** — Cutoff that converts a probability into a class; tunes the precision/recall tradeoff.

**★ RMSE** — Root Mean Squared Error; penalizes large errors (regression).

**MAE** — Mean Absolute Error; robust to outliers (regression).

**MAPE** — Mean Absolute Percentage Error.

**R² (coefficient of determination)** — Proportion of variance explained (regression).

**Log loss / cross-entropy** — Penalizes confident wrong probabilistic predictions.

**SHAP** — Shapley Additive Explanations; attributes a prediction to each feature (used by Clarify).

---

## 9. Generative AI & Foundation Models

**★ Foundation model (FM)** — Large model pretrained on broad data, adaptable to many tasks.

**LLM** — Large Language Model; a text-focused foundation model.

**★ Amazon Bedrock** — Managed API access to foundation models (Anthropic, Meta, Amazon, etc.) for GenAI apps.

**★ Prompt engineering** — Crafting inputs to steer model output; cheapest customization — try first.

**★ RAG (Retrieval-Augmented Generation)** — Retrieve relevant documents and inject them into the prompt so the model uses fresh/proprietary knowledge *without retraining*. Bedrock **Knowledge Bases** implement this.

**★ Fine-tuning (GenAI)** — Further training an FM on your data to adapt style/format/domain. Choose over RAG when you need behavioral/format adaptation, not just facts.

**Vector database / vector store** — Stores embeddings for similarity search (backs RAG). AWS options: OpenSearch, Aurora pgvector, Kendra.

**Bedrock Agents** — Orchestrate multi-step tasks and tool/API calls with an FM.

**★ Bedrock Guardrails** — Policy controls to filter harmful content, block topics, and redact PII.

**Amazon Q** — AWS's generative AI assistant (business and developer variants).

**Token** — Unit of text an LLM processes; pricing/context limits are token-based.

**Context window** — Max tokens a model can consider at once.

**Hallucination** — Model produces confident but false output; RAG and guardrails mitigate it.

**Temperature** — Sampling parameter controlling output randomness (low = deterministic, high = creative).

**Model distillation** — Training a smaller "student" model to mimic a larger "teacher" for efficiency.

---

## 10. AWS AI/ML Managed Services (use before building custom)

**★ Amazon Rekognition** — Image/video analysis (objects, faces, moderation).

**★ Amazon Comprehend** — NLP: sentiment, entities, key phrases, PII detection.

**Amazon Textract** — Extract text/forms/tables from documents.

**Amazon Transcribe** — Speech-to-text.

**Amazon Polly** — Text-to-speech.

**Amazon Translate** — Language translation.

**Amazon Lex** — Conversational chatbots (powers Alexa-style bots).

**Amazon Kendra** — Intelligent enterprise search.

**★ Amazon Personalize** — Real-time recommendations as a managed service.

**Amazon Forecast** — Managed time-series forecasting.

**Amazon Fraud Detector** — Managed fraud detection.

**Amazon Augmented AI (A2I)** — Adds human review to ML predictions.

> **Exam pattern:** when a question stresses *"least operational overhead"* or *"no ML expertise available,"* the answer is usually a managed AI service above rather than a custom SageMaker model.

---

*Tip: The exam rarely asks "define X" directly — it embeds these terms in scenarios and asks which one fits. When you review, practice mapping each ★ term to the situation where it's the correct answer.*
