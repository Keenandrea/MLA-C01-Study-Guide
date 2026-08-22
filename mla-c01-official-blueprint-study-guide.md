# MLA-C01 Study Guide — Complete, Plain-English Edition

This guide covers everything the official AWS exam guide lists as testable, explained in plain language with the "why" behind each idea. It has four parts:

1. **How the exam works**
2. **The four domains** — walked through task statement by task statement, covering every "Knowledge of" and "Skills in" item AWS lists
3. **Glossary** — every term, defined simply
4. **Service reference** — every in-scope AWS service: what it is, and when you'd reach for it

A note on how to read this: the exam almost never asks "what is X?" Instead it describes a situation and asks "which tool/setting is the best fit?" So as you read, keep asking yourself *"when would I actually pick this?"* — that's the muscle the exam tests.

---

# Part 1 — How the Exam Works

**What it's really testing.** This is a machine-learning *engineering* exam. It assumes you can take a model someone hands you (or that you build from a standard recipe) and get it running reliably, securely, and cheaply on AWS — collect the data, train it, deploy it, watch it, and lock it down. It is *not* a data-science research exam. You won't be asked to invent algorithms or do heavy math by hand.

**Who it's aimed at.** Roughly a year of hands-on SageMaker plus a year in a nearby role (backend dev, DevOps, data engineer, or data scientist). If you already do infrastructure, containers, CI/CD, and cloud security, you're most of the way there for two of the four domains.

**The mechanics.**
- **65 questions, 130 minutes.** Only **50 count**; **15 are unscored experiments** AWS is trialing, and you can't tell which is which — so treat every question as real.
- **Score 100–1,000; you need 720 to pass.** It's **compensatory**, meaning only your *total* has to clear 720. You can be weak in one domain and still pass if you're strong elsewhere — there's no minimum per section.
- **No penalty for guessing.** A blank counts the same as a wrong answer, so **never leave anything blank** — always put something down, even a guess, before time runs out.

**The question formats you'll see.**
- **Multiple choice** — one right answer, three wrong "distractors."
- **Multiple response** — pick *all* the correct ones out of five or more; you get it right only if you select every correct option and no wrong ones.
- **Ordering** — put 3–5 steps in the correct sequence (think: the stages of an ML pipeline).
- **Matching** — pair items on the left with items on the right (e.g., a metric to its use case).
- **Case study** — one scenario with several questions hanging off it; each question is scored on its own, so a wrong answer on one doesn't sink the rest.

**The four domains and their weight** (how many questions roughly come from each):
- **Domain 1 — Data Preparation: 28%** (the biggest slice)
- **Domain 2 — Model Development: 26%**
- **Domain 3 — Deployment & Orchestration: 22%**
- **Domain 4 — Monitoring, Maintenance & Security: 24%**

They're all close in size, so you can't safely skip any of them.

**Explicitly NOT tested** (AWS says so directly): designing entire architectures from a blank page, setting company-wide ML strategy, wiring up lots of brand-new tools, going deep in two different ML fields at once (say, both language *and* vision), and model "quantization" (shrinking models by lowering numeric precision) and its effect on accuracy. If an answer choice leans on one of these, it's probably a distractor.

---

# Part 2 — The Four Domains, Explained

## Domain 1 — Data Preparation for ML (28%)

The theme here: get raw data into AWS, clean it up, turn it into useful inputs (features), and make sure it's trustworthy and compliant before any model touches it. Real-world ML is mostly this — "garbage in, garbage out" is the whole reason it's the heaviest domain.

### Task 1.1 — Ingest and store data

**Data formats — and why the choice matters.** How you store data on disk changes how fast and how cheaply you can read it later.
- **CSV and JSON** are *row-based* and human-readable. Easy to eyeball, but bulky and slow when you only need a few columns out of many — the computer still has to read every row top to bottom.
- **Parquet and ORC** are *columnar* — they store all the values of one column together. If your training job only needs 5 of 200 columns, it reads just those 5. That makes them dramatically faster and cheaper for analytics and ML. **For large training datasets, Parquet is almost always the right answer over CSV.**
- **Avro** is row-based but carries its schema with it, which makes it good for streaming data whose shape may change over time.
- **RecordIO** is a compact binary format SageMaker likes for high-speed feeding of data into training.
- "Validated vs non-validated" just means whether the format enforces a defined schema (structure/types) or lets anything through.

**Core places to store data.**
- **Amazon S3** — object storage; think of it as an infinitely large, cheap folder in the cloud. This is the **default home for training data** and the center of almost every ML setup on AWS.
- **Amazon EFS** — a shared network drive (NFS) many machines can mount at once.
- **Amazon FSx** (especially **FSx for Lustre**) — a very high-speed shared file system that links to S3. When a training job is starving for data because reading from S3 is too slow, **FSx for Lustre is the classic fix**. (The guide also names FSx for NetApp ONTAP, another FSx flavor for enterprise file workloads.)
- **Amazon EBS** — a virtual hard disk attached to a single machine. Its **Provisioned IOPS** option buys guaranteed high disk speed for I/O-hungry work.

**Streaming data (data that arrives continuously, like clickstreams or sensor feeds).**
- **Kinesis Data Streams** — real-time pipe where *you* write the code that consumes the data. Use it when you need custom, low-latency processing.
- **Kinesis Data Firehose** — the "set it and forget it" cousin: fully managed, it just delivers the stream into S3/Redshift/OpenSearch, optionally running a Lambda to tweak records on the way. Use it when you want no servers and no custom consumer code.
- **Managed Service for Apache Flink** — for doing real computation *on* the stream as it flows (aggregations, windows).
- **Apache Kafka / Amazon MSK** — shows up when a team already runs Kafka.

**Skills the exam expects here.**
- **Pulling data out of various stores** (S3, EBS, EFS, RDS, DynamoDB) using the right accelerator — e.g. **S3 Transfer Acceleration** to speed big uploads across long distances, **EBS Provisioned IOPS** for disk-speed-bound jobs.
- **Picking a format based on how you'll read the data** — columnar (Parquet/ORC) for analytics/training, row formats for record-by-record use.
- **Getting data into SageMaker Data Wrangler and Feature Store** as part of the prep pipeline.
- **Merging data from multiple sources** with plain code, **AWS Glue**, or **Apache Spark**.
- **Troubleshooting ingestion/storage problems** that come down to running out of capacity or not scaling.
- **Making the first storage call** by weighing cost, performance, and the shape of the data.

*Typical exam scenario:* "A training job is slow because it spends most of its time reading data from S3 — what fixes it?" → FSx for Lustre, or a streaming input mode like Pipe/FastFile.

### Task 1.2 — Transform data and perform feature engineering

"Feature engineering" just means turning raw data into the numeric inputs a model can learn from, and doing it well enough that the model actually finds the signal.

**Cleaning the data.**
- **Outliers** — freakishly large/small values that can throw a model off. You detect them and then treat them (remove, cap them at a sane limit, or transform them).
- **Imputation** — filling in missing values instead of dropping the row, e.g. with the column's average or median, or a small model that predicts the missing value.
- **Combining and deduplication** — joining datasets together and removing duplicate rows so the model doesn't over-count.

**Engineering the features.**
- **Scaling / standardization** (Z-score: rescale so a column has average 0 and spread 1) and **normalization** (Min-Max: squeeze values into a 0-to-1 range). Why bother? Many algorithms treat "big numbers" as "more important." If income is in the thousands and age is under 100, the model over-weights income unless you put them on the same scale.
- **Feature splitting** — breaking one messy field into useful pieces (a timestamp → separate day, month, hour columns).
- **Binning** — grouping a continuous number into buckets (ages 0–17, 18–34, 35+).
- **Log transformation** — compressing data that's badly skewed (a few enormous values dominating) so the model can see the pattern in the bulk of it.

**Encoding (turning categories/text into numbers, since models only do math).**
- **One-hot encoding** — one yes/no column per category (Red/Green/Blue → three 0/1 columns). Safe default because it implies no false ordering.
- **Binary encoding** — a more compact scheme when a category has *tons* of possible values.
- **Label encoding** — map categories to integers (Red=0, Green=1…). Careful: it implies an order that may not exist, so it's mainly for genuinely ordered categories or tree models.
- **Tokenization** — chopping text into pieces (words/sub-words) so language data can be fed to a model.

**Tools for transforming.**
- **SageMaker Data Wrangler** — point-and-click data prep with 300+ built-in transforms; the go-to when you want to prep data with little or no code.
- **AWS Glue** and **Glue DataBrew** — serverless ETL and a no-code visual cleaner, respectively.
- **Spark on Amazon EMR** — for heavy, large-scale transformations.
- **AWS Lambda / Spark** — for transforming *streaming* data on the fly.

**Labeling (creating the "answer key" for supervised learning).**
- **SageMaker Ground Truth** — managed labeling workflows, including "active learning" where the machine labels the easy cases and humans handle the hard ones.
- **Amazon Mechanical Turk** — a marketplace of human workers to do the labeling.

**Managing features so training and production agree.**
- **SageMaker Feature Store** — a central library of features. Its **online store** serves features fast at prediction time; its **offline store** (in S3) feeds training. The big reason it exists: it stops **training/serving skew** — the nasty bug where a feature is calculated one way during training and a slightly different way in production, quietly wrecking accuracy.

### Task 1.3 — Ensure data integrity and prepare data for modeling

This is about trust and compliance: is the data fair, is it legal to use this way, and is it clean enough to model on.

**Spotting bias before you train.**
- **Class Imbalance (CI)** — one outcome hugely outnumbers another (e.g. 99% "not fraud," 1% "fraud"). A lazy model can score 99% "accurate" by always guessing the majority — and be useless.
- **Difference in Proportions of Labels (DPL)** — checks whether a positive outcome is unfairly more common for one group than another.
- Both are measured with **SageMaker Clarify** *before* training.
- **Fixing imbalance:** generate synthetic examples of the rare class (**SMOTE**), or **resample** (duplicate the rare class / trim the common one), or weight the rare class more heavily.

**Security and compliance while handling data.**
- **Encryption** — protect data at rest (with **KMS** keys) and in transit (TLS). Know that this is expected everywhere sensitive data lives.
- **Classification, anonymization, and masking** — tagging data by sensitivity, stripping identities, and hiding fields (e.g. showing only the last 4 digits).
- **Compliance implications** — **PII** (personal info like names/SSNs), **PHI** (health info, governed by HIPAA), and **data residency** (laws requiring data stay in a certain country/region). These constraints often decide which region, encryption, or service is "allowed" in a scenario.
- **Amazon Macie** automatically finds PII/PHI sitting in S3; **Lake Formation** applies fine-grained permissions across a data lake.

**Skills the exam expects here.**
- **Validating data quality** with **Glue DataBrew** and **Glue Data Quality** (rules that flag bad/missing values).
- **Finding and reducing bias** — recognizing **selection bias** (your data isn't representative) and **measurement bias** (your data was collected inaccurately), using **Clarify** to detect them.
- **Reducing prediction bias** through good **splitting, shuffling, and augmentation** — but *never* shuffle time-series data across the time boundary, because letting the model peek at the future is a form of cheating called leakage.
- **Configuring data to load efficiently into training** via **EFS / FSx**.

## Domain 2 — ML Model Development (26%)

The theme: pick the right approach for the problem, train the model well, and honestly measure how good it is.

### Task 2.1 — Choose a modeling approach

**Match the problem to the method.** First figure out what kind of problem you have — predicting a category (classification), a number (regression), grouping similar things (clustering), predicting the future (forecasting), spotting weird events (anomaly detection), or suggesting items (recommendation) — then pick a tool that fits.

**SageMaker built-in algorithms** (ready-made recipes; know the headline use for each): **XGBoost** (the workhorse for tabular/spreadsheet data), **Linear Learner**, **K-Means** (clustering), **PCA** (shrinking many columns into a few), **DeepAR** (forecasting), **Random Cut Forest** (anomaly detection), **BlazingText** (text), **Object Detection / Image Classification** (vision), **Factorization Machines / K-NN** (recommendations).

**Managed AI services — the "don't build it if you can rent it" rule.** AWS has pre-trained services that solve common problems with zero model-building: **Translate, Transcribe** (speech→text), **Rekognition** (images/video), **Comprehend** (text analysis), **Textract** (documents), **Polly** (text→speech), **Lex** (chatbots), **Kendra** (search), **Personalize** (recommendations), **Forecast**, **Fraud Detector**. And **Amazon Bedrock** for generative AI. **Whenever a question stresses "least operational overhead" or "the team has no ML expertise," the answer is usually one of these — not a custom model.**

**Interpretability matters in the choice.** Simple models (linear, single trees) are easy to explain; deep neural nets are black boxes. If a scenario demands you *explain why* a decision was made (regulated industries, loan approvals), that pushes you toward simpler models or toward **Clarify** (which explains predictions with SHAP).

**Foundation models and templates.** **SageMaker JumpStart** gives you pre-trained models and ready-made solution templates so you don't start from scratch; **Bedrock** gives API access to large foundation models. Also weigh **cost** — the fanciest model isn't always worth it.

### Task 2.2 — Train and refine models

**The nuts and bolts of training.**
- **Epoch** = one complete pass through all your training data. **Step/iteration** = one small batch processed. **Batch size** = how many examples the model looks at before nudging its internal settings.
- **Learning rate** = how big each nudge is. Too big and training bounces around and never settles; too small and it crawls.

**Making training faster/cheaper.**
- **Early stopping** — quit once the model stops improving on validation data, so you don't waste time (and money) overtraining.
- **Distributed training** — spread the work across multiple GPUs. "Data-parallel" splits the *data* across machines; "model-parallel" splits a huge *model* across machines.
- **Managed Spot Training** — use AWS's spare capacity at up to ~90% off; because AWS can reclaim it anytime, you use **checkpointing** (saving progress periodically) so an interruption doesn't lose everything. This pairing is a favorite "how do I cut training cost?" answer.

**Stopping the model from memorizing (overfitting).** **Overfitting** is when a model aces the training data but flunks new data because it memorized instead of learning. Tools to prevent it — collectively "regularization":
- **Dropout** — randomly ignore some neurons during training so the network can't lean on any one path.
- **Weight decay / L2** — gently shrink the model's internal numbers so none dominate.
- **L1** — shrink some numbers all the way to zero, effectively dropping useless features.
- Plus **early stopping** and **feature selection**.
The opposite problem, **underfitting** (too simple, bad on everything), is fixed by adding complexity, more features, or training longer.

**Tuning hyperparameters (the dials you set before training).** **SageMaker Automatic Model Tuning (AMT)** searches for the best dial settings. Methods: **grid** (try every combo — thorough but slow), **random** (sample combos), **Bayesian** (learn from past tries to search smartly — efficient), and **Hyperband** (kill bad runs early). Know that different models have different key dials: number/depth of trees for tree models, number of layers/learning rate for neural nets.

**Skills the exam expects here.** Train with **built-in algorithms** or **script mode** (bring your own TensorFlow/PyTorch script into a managed container) or **BYOC** (bring your own Docker container for full control); **fine-tune** pre-trained models via **Bedrock/JumpStart**; run **AMT**; prevent **overfitting, underfitting, and catastrophic forgetting** (when fine-tuning makes a model forget what it already knew); **combine models** via ensembling/stacking/boosting for extra accuracy; **shrink models** (change data types, prune, compress) so they run cheaper/faster; and **version every model** in the **SageMaker Model Registry** so you can reproduce and audit results.

### Task 2.3 — Analyze model performance

**Classification metrics — and which to trust when.**
- **Confusion matrix** — the 2×2 table of right/wrong predictions (true/false positives and negatives) everything else is built from.
- **Accuracy** — % correct overall. **Misleading when data is imbalanced** (the 99%-not-fraud trap).
- **Precision** — of the things the model *flagged*, how many were truly positive? Care about this when a **false alarm is costly** (e.g. blocking a legit transaction).
- **Recall** — of the things that *were* positive, how many did the model *catch*? Care about this when a **miss is costly** (e.g. failing to catch fraud or a disease).
- **F1 score** — balances precision and recall into one number; the go-to for imbalanced problems.
- **ROC curve / AUC** — how well the model ranks positives above negatives across all thresholds; AUC of 1.0 is perfect, 0.5 is a coin flip.
- **Heat maps** — a visual way to read a confusion matrix or feature relationships.

**Regression metrics** (for predicting numbers): **RMSE** punishes big misses hardest; MAE treats all misses equally; R² says how much of the variation the model explains.

**Judging fit and health.**
- **Baselines** — always compare against something dumb-but-simple; if a complex model barely beats "guess the average," it isn't earning its keep.
- **Overfitting vs underfitting** — spot it by comparing training vs validation scores (great on train, poor on validation = overfit).
- **Convergence issues** — when training loss won't go down or blows up; usually a learning-rate or data problem.

**SageMaker tools for this.** **Clarify** shows bias in predictions and explains them (SHAP); **Model Debugger** catches training problems like vanishing gradients or bottlenecks; **Experiments** tracks and compares runs so results are reproducible; and comparing a **shadow variant** (a new model quietly fed real traffic, responses discarded) against the live **production variant** lets you validate a model before trusting it.

## Domain 3 — Deployment & Orchestration (22%)

The theme: get the trained model serving predictions in a way that fits the traffic pattern, define your infrastructure as repeatable code, and automate the whole build-test-deploy-retrain loop. This domain overlaps heavily with normal DevOps.

### Task 3.1 — Select deployment infrastructure

**The four ways SageMaker serves predictions — memorize which fits which situation:**
- **Real-time endpoint** — an always-on server for instant, one-at-a-time predictions. Use for steady traffic needing low latency (payloads under 6 MB, responses under 60 seconds).
- **Serverless inference** — spins up on demand and scales to zero when idle. Use for spiky/occasional traffic where you don't want to pay for an idle server and can tolerate a brief "cold start" delay. No GPU.
- **Asynchronous inference** — queues requests and processes them in the background. Use for **big inputs** (up to 1 GB) or **long-running** predictions (up to an hour); it can also scale to zero.
- **Batch transform** — no server at all; you point it at a whole dataset and it scores everything at once. Use when you don't need live predictions, just results on a pile of data.

**Other knowledge:** deployment best practices (**versioning** models, having a **rollback** plan); serving in **real time vs batch**; provisioning the right compute for **prod vs test** (**CPU** for light work, **GPU** for heavy models); choosing a **provided container** vs a **custom** one; and **SageMaker Neo** to compile a model to run efficiently on small/edge devices.

**Skills the exam expects here.** Weigh **performance vs cost vs latency**; pick the **compute** (GPU vs CPU, processor family, network bandwidth); choose the **orchestrator** (**SageMaker Pipelines** for ML-native workflows, **Airflow/MWAA** if the team already uses Airflow); decide **multi-model** (many models sharing one endpoint to save money) vs **multi-container** endpoints; and choose the **deployment target** — a SageMaker endpoint, or **ECS/EKS/Kubernetes**, or **Lambda** (great for lightweight, spiky work under 15 minutes).

### Task 3.2 — Create and script infrastructure

**Knowledge.** The difference between **on-demand** (pay as you go, instant) and **provisioned** (reserved ahead of time) resources; how to compare **scaling policies**; the trade-offs of **Infrastructure as Code** — **CloudFormation** (describe your infra in a template) vs **CDK** (describe it in a real programming language); **container** basics and AWS's container services; and **SageMaker endpoint auto scaling**, which adds/removes capacity based on demand or a schedule.

**Skills the exam expects here.** Apply best practices for cheap, scalable serving — **auto scaling** endpoints, adding **Spot Instances**, putting **Lambda behind an endpoint**. **Automate provisioning** and let stacks talk to each other via **CloudFormation/CDK**. Build and maintain **containers** with **ECR** (image storage), **ECS/EKS**, and **BYOC**. Put **endpoints inside a VPC** for network isolation. **Deploy via the SageMaker SDK**. And choose the right **auto-scaling trigger metric** — model latency, CPU utilization, or invocations per instance.

### Task 3.3 — CI/CD orchestration

**Knowledge.** What **CodePipeline** (orchestrates the pipeline), **CodeBuild** (builds/tests), and **CodeDeploy** (deploys) do and their limits; how to fold **data ingestion** into orchestration; **Git** basics; how **CI/CD** ideas apply to ML; **rollout strategies** — **blue/green** (stand up the new version alongside the old, then switch), **canary** (send a little traffic to the new version first), **linear** (shift traffic gradually) — and how code repos and pipelines connect.

**Skills the exam expects here.** Configure and troubleshoot the Code* services and their stages; apply branching workflows (**Gitflow, GitHub Flow**); automate model build and deploy; **trigger training/inference jobs** with **EventBridge rules, SageMaker Pipelines, or CodePipeline**; add **unit, integration, and end-to-end tests** to the pipeline; and **build automated retraining** — the loop where drift is detected, an event kicks off a pipeline, the model retrains, gets evaluated and registered, and (once approved) redeploys.

## Domain 4 — Monitoring, Maintenance & Security (24%)

The theme: once the model is live, watch it for decay, keep the infrastructure healthy and cheap, and lock everything down. Again, lots of overlap with DevSecOps.

### Task 4.1 — Monitor model inference

**Knowledge.** **Drift** — the slow rot of model accuracy over time. **Data drift** = the incoming data starts looking different from the training data (a new customer demographic shows up). **Concept drift** = the *relationship* the model learned changes (what counted as fraud last year isn't what counts now). You also should know the AWS **ML Lens** (Well-Architected) monitoring principles.

**Skills the exam expects here.** Monitor live models with **SageMaker Model Monitor**, which watches four things: **data quality**, **model quality** (needs the real answers/labels to compare against), **bias drift**, and **feature-attribution drift** (the reasons behind predictions shifting). It baselines against your training data and raises CloudWatch alarms when things move. Use **Clarify** to detect distribution changes; watch **workflows** for errors in processing or inference; and use **A/B testing** to compare a new model against the current one on real traffic.

### Task 4.2 — Monitor and optimize infrastructure and costs

**Knowledge.** The **KPIs** for ML infrastructure — utilization, throughput, availability, scalability, fault tolerance. Observability tools: **X-Ray** (traces a request across services to find slow spots), **CloudWatch Lambda Insights** and **Logs Insights**. **CloudTrail** to log activity and even trigger retraining. The **instance-type families** — memory-optimized, compute-optimized, general-purpose, and inference-optimized (**Inferentia**) — and how they affect speed and cost. Cost tools — **Cost Explorer**, **Billing and Cost Management**, **Trusted Advisor** — and cost **tracking via tags**.

**Skills the exam expects here.** Troubleshoot with **CloudWatch Logs and alarms**; create **CloudTrail trails**; build dashboards (**QuickSight**, **CloudWatch**); monitor infra with **EventBridge**; **right-size** instances using **SageMaker Inference Recommender** (load-tests to suggest the best instance) and **Compute Optimizer**; resolve latency/scaling issues; apply a **tagging strategy** so costs are attributable; handle capacity via **provisioned concurrency, service quotas, and auto scaling**; set budgets/quotas with **Cost Explorer, Trusted Advisor, and Budgets**; and choose **purchasing options** — **Spot** (cheapest, interruptible), **On-Demand** (flexible), **Reserved Instances** and **SageMaker Savings Plans** (commit for a discount).

### Task 4.3 — Secure AWS resources

**Knowledge.** **IAM** roles, policies, and groups (plus **bucket policies** and **SageMaker Role Manager**) that control who can do what; SageMaker's own security/compliance features; controls for **network access** to ML resources; and **CI/CD security** best practices.

**Skills the exam expects here.**
- **Least-privilege access to ML artifacts** — grant only what's needed. The subtle-but-important lever: scope **`iam:PassRole`** so a user can hand SageMaker *only one specific execution role*, not any role — this blocks a common privilege-escalation path.
- **Configure IAM for both users and applications** — and understand the two-identity model: the **principal** (the user or app *calling* SageMaker) is separate from the **execution role** (the role SageMaker *assumes on your behalf* to reach S3, ECR, etc.). Most "the job can't read S3" problems are a gap in the execution role or a **KMS key policy**.
- **Monitor/audit/log** for ongoing compliance (CloudTrail + CloudWatch).
- **Troubleshoot security issues** — usually permissions or encryption-key access.
- **Build VPCs, subnets, and security groups** to isolate ML systems — run SageMaker in **VPC mode** (no direct internet) and use **VPC endpoints / PrivateLink** so traffic to S3 and the SageMaker APIs never leaves the AWS network. Encrypt with **KMS**; keep credentials in **Secrets Manager**.

---

# Part 3 — Glossary (Plain-English)

## Machine-learning basics
- **Supervised learning** — learning from examples that come with the right answers (labels).
- **Unsupervised learning** — finding patterns in data that has no answer key (grouping, anomaly spotting).
- **Semi-supervised learning** — a little labeled data plus a lot of unlabeled data.
- **Reinforcement learning** — learning by trial and error to maximize a reward.
- **Classification / Regression** — predicting a category vs predicting a number.
- **Clustering** — grouping similar items together with no labels.
- **Forecasting** — predicting future values over time.
- **Anomaly detection** — flagging rare, unusual data points.
- **Recommendation** — suggesting items a user will likely want.
- **Feature / Label** — an input column vs the thing you're trying to predict.
- **Overfitting** — the model memorized the training data and does poorly on new data.
- **Underfitting** — the model is too simple and does poorly even on training data.
- **Bias–variance tradeoff** — the balancing act between too-simple (bias/underfit) and too-complex (variance/overfit).
- **Regularization** — techniques that keep a model from overfitting by discouraging complexity.
- **L1 vs L2** — L1 can zero out useless features; L2 gently shrinks all the model's numbers.
- **Dropout** — randomly switching off neurons during training so the network generalizes better.
- **Early stopping** — halting training once it stops improving.
- **Epoch / Batch size / Learning rate** — one full pass through the data / examples per update / how big each learning step is.
- **Gradient descent** — the core method models use to gradually reduce their error.
- **Loss function** — the number the model tries to make as small as possible.
- **Hyperparameter vs parameter** — a dial you set beforehand vs a value the model learns during training.
- **Transfer learning / Fine-tuning** — reusing a pre-trained model / continuing to train it on your own data.
- **Ensemble / Boosting** — combining several models for better results / building models in sequence where each fixes the last (XGBoost).
- **Data leakage** — accidentally letting the model see information it wouldn't have in real life, giving falsely great scores.
- **Train/validation/test split** — carving data into a part to learn from, a part to tune on, and a part to grade on.
- **Cross-validation** — rotating which slice is the test slice to get a more reliable score.
- **Class imbalance** — one outcome vastly outnumbers the others.
- **SMOTE** — a technique that invents synthetic examples of the rare class to balance data.
- **Dimensionality reduction / PCA** — shrinking many columns down to a few that still capture most of the information.

## Data preparation
- **ETL / ELT** — extract-transform-load vs extract-load-transform (transform before vs after loading).
- **Batch vs streaming** — processing data in chunks on a schedule vs continuously in real time.
- **One-hot / Label encoding** — turning categories into yes/no columns vs into numbers.
- **Normalization / Standardization** — squeezing values into 0–1 vs re-centering to average 0.
- **Binning / Log transform** — bucketing numbers into ranges / taming extreme skew.
- **Imputation** — filling in missing values.
- **Outlier** — an extreme value that can distort a model.
- **Tokenization / Embedding** — splitting text into pieces / turning words into meaning-carrying number vectors.
- **TF-IDF** — a way of scoring how important a word is to a document.
- **Feature engineering / selection** — crafting useful inputs / keeping only the inputs that help.
- **Training/serving skew** — features computed differently in training vs production, silently hurting accuracy.
- **Columnar formats (Parquet/ORC)** — file layouts that make reading specific columns fast and cheap.
- **Data quality** — how complete, valid, and consistent your data is.

## Metrics
- **Confusion matrix** — the grid of correct/incorrect predictions.
- **Accuracy** — overall percent correct (unreliable on imbalanced data).
- **Precision** — how many of the flagged items were truly positive (watch when false alarms are costly).
- **Recall** — how many of the true positives you caught (watch when misses are costly).
- **F1** — a single balance of precision and recall.
- **ROC / AUC** — how well the model separates positives from negatives overall.
- **RMSE / MAE / R²** — regression error measures (RMSE punishes big misses; MAE is even-handed; R² is variance explained).
- **SHAP** — a method that explains how much each feature pushed a prediction.

## Deployment & operations
- **Real-time / Serverless / Async / Batch** — the four inference styles (see Domain 3).
- **Multi-model endpoint** — many models behind one endpoint to save money.
- **Production vs shadow variant** — the live model vs a hidden copy tested on real traffic without affecting users.
- **Blue/green, canary, linear** — safe ways to roll out a new version gradually with a fallback.
- **Auto scaling / Cold start** — adding capacity as demand rises / the first-request delay when starting from zero.
- **Inferentia / Trainium / Graviton** — AWS chips for cheap inference / cheap training / better-value general compute.
- **CloudFormation / CDK** — infrastructure defined as templates / as real code.
- **CI/CD** — automated build, test, and deployment.
- **Automated retraining loop** — drift detected → pipeline retrains → evaluates → deploys, with little human effort.

## Monitoring & security
- **Data drift vs concept drift** — inputs change shape vs the input→answer relationship changes.
- **Baseline** — the reference "normal" that monitoring compares against.
- **IAM / Execution role** — who's allowed to do what / the role SageMaker uses on your behalf.
- **Least privilege** — give only the permissions actually needed.
- **VPC / VPC endpoint (PrivateLink)** — a private network / a private doorway to AWS services that avoids the public internet.
- **Security group / NACL** — a firewall around an instance / around a subnet.
- **KMS** — the service that manages encryption keys.
- **Responsible AI** — keeping models fair, explainable, and governed.

## Generative AI
- **Foundation model / LLM** — a big general-purpose pre-trained model / a text-focused one.
- **Prompt engineering** — getting better answers by wording the request well (cheapest, try first).
- **RAG** — feeding the model relevant documents at query time so it can use fresh or private info without retraining.
- **Fine-tuning** — training a foundation model further on your data to change its behavior/style.
- **Vector database** — stores "meaning vectors" so you can search by similarity (powers RAG).
- **Guardrails** — filters that block harmful content and hide sensitive data.
- **Hallucination** — when a model states something false but confidently; RAG and guardrails reduce it.

---

# Part 4 — AWS Service Reference (what it is · when to use it)

## SageMaker and its parts (the exam's center of gravity)
- **Amazon SageMaker** — the all-in-one platform for building, training, deploying, and monitoring your own models. *Use whenever* you need a custom model rather than an off-the-shelf AI service.
- **Studio** — the web-based workbench/IDE for all of SageMaker. *Use as* your home base.
- **Data Wrangler** — visual, low-code data prep. *Use to* clean and engineer features fast.
- **Processing** — runs prep or evaluation jobs at scale. *Use for* big preprocessing or scoring steps in a pipeline.
- **Feature Store** — a shared library of features (online for serving, offline for training). *Use to* reuse features and stop training/serving skew.
- **Ground Truth** — managed data labeling. *Use to* create labeled datasets with humans + automation.
- **Built-in algorithms** — ready-made algorithms (XGBoost, etc.). *Use when* a standard approach fits, to skip custom code.
- **Script mode / BYOC** — your own training script in a managed container / your own full container. *Use for* custom frameworks / total control.
- **Managed Spot Training** — cheap interruptible training with checkpoints. *Use to* cut training cost dramatically.
- **Automatic Model Tuning (AMT)** — auto-searches for the best hyperparameters. *Use to* squeeze out accuracy without manual trial-and-error.
- **Autopilot / Canvas** — automated ML / no-code ML. *Use for* fast baselines / non-coders.
- **JumpStart** — pre-trained models and solution templates. *Use to* start from something instead of scratch.
- **Clarify** — detects bias and explains predictions. *Use to* prove fairness or explain "why."
- **Debugger** — catches training problems as they happen. *Use to* debug slow or non-converging training.
- **Experiments** — tracks and compares training runs. *Use for* reproducibility.
- **Model Registry** — versions and approves models with history. *Use to* manage versions for audits and CI/CD.
- **Pipelines** — SageMaker's native workflow orchestrator. *Use as* the default way to string ML steps together.
- **Model Monitor** — watches deployed models for drift. *Use to* catch accuracy decay in production.
- **Inference Recommender** — load-tests to size endpoints. *Use to* pick the right instance type/size.
- **Neo** — compiles models for specific/edge hardware. *Use for* edge devices or hardware speedups.
- **Role Manager / Model Cards / Model Dashboard** — scoped IAM roles / model documentation / a central model view. *Use for* governance and access setup.

## AI application services (rent, don't build)
- **Bedrock** — foundation models by API, plus RAG, agents, and guardrails. *Use for* generative-AI features with no model hosting.
- **Amazon Q** — a ready-made GenAI assistant. *Use for* a chat assistant with minimal setup.
- **Comprehend** — text analysis (sentiment, entities, PII). *Use to* understand text with no training.
- **Comprehend Medical** — the same but for clinical text. *Use for* medical NLP.
- **Rekognition** — image and video analysis. *Use for* vision tasks off the shelf.
- **Textract** — pulls text/tables/forms out of documents. *Use to* digitize invoices and forms.
- **Transcribe** — speech to text. *Use to* turn audio into text.
- **Polly** — text to speech. *Use to* generate spoken audio.
- **Translate** — language translation. *Use for* translating text.
- **Lex** — chatbot builder. *Use to* create conversational bots.
- **Kendra** — smart enterprise search; can feed RAG. *Use for* natural-language search over documents.
- **Personalize** — recommendations as a service. *Use to* add "you may also like" without building it.
- **Fraud Detector** — managed fraud models. *Use to* detect fraud with little ML effort.
- **Augmented AI (A2I)** — adds human review to predictions. *Use for* human-in-the-loop checks.
- **CodeGuru** — ML-powered code review and profiling. *Use to* improve code quality and performance.
- **DevOps Guru** — ML-powered ops anomaly detection. *Use for* automatic "something's wrong" alerts.
- **HealthLake** — health-data lake with NLP (niche). *Use for* health-data analytics.
- **Lookout for Equipment / Metrics / Vision** — anomaly detection for sensors / business metrics / images. *Use for* targeted anomaly detection (mostly name recognition).
- **Mechanical Turk** — human task workforce. *Use as* labelers.

## Analytics
- **Athena** — run SQL directly on files in S3. *Use for* quick queries without a database.
- **Data Firehose** — hands-off streaming delivery into storage. *Use to* load streams with no code.
- **Kinesis (Data Streams)** — real-time streaming you build on. *Use when* you need custom real-time processing.
- **Kinesis Video Streams** — stream video in. *Use for* video ingestion.
- **Managed Service for Apache Flink** — compute on streams in real time. *Use for* live aggregations.
- **EMR** — managed Spark/Hadoop clusters. *Use for* heavy big-data processing.
- **Glue** — serverless ETL plus a data catalog. *Use for* no-server data transformation and cataloging.
- **Glue DataBrew** — no-code data cleaning. *Use for* point-and-click prep.
- **Glue Data Quality** — automated data-quality checks. *Use to* validate datasets.
- **Lake Formation** — fine-grained data-lake permissions. *Use to* control who sees what in the lake.
- **OpenSearch** — search and log analytics; can store vectors. *Use for* search, observability, or RAG.
- **QuickSight** — business dashboards. *Use for* visualizations for stakeholders.
- **Redshift** — a data warehouse for big analytical queries. *Use for* large-scale reporting/analytics.

## Storage
- **S3** — cheap, huge object storage; the default data lake. *Use as* the home for training data.
- **S3 Glacier** — very cheap long-term archive. *Use for* data you rarely touch.
- **EBS** — a virtual disk attached to one instance. *Use for* instance storage (Provisioned IOPS for speed).
- **EFS** — a shared network drive many machines can mount. *Use for* shared file access.
- **FSx (incl. Lustre)** — high-speed shared file system linked to S3. *Use to* speed up training data loading.
- **Storage Gateway** — bridges on-prem storage to AWS (niche). *Use for* hybrid setups.

## Databases
- **DynamoDB** — serverless NoSQL, super-fast lookups. *Use for* low-latency reads (e.g. online features).
- **RDS** — managed relational databases. *Use for* traditional transactional data.
- **DocumentDB / Neptune / ElastiCache** — document DB / graph DB / in-memory cache. *Use for* those respective data shapes.

## Compute & containers
- **EC2** — virtual servers you manage. *Use for* custom or self-managed workloads.
- **Lambda** — run code with no servers (under 15 min). *Use for* lightweight inference, triggers, glue logic.
- **Batch** — run large batch compute jobs. *Use for* big offline processing.
- **Serverless Application Repository** — share/deploy serverless apps (niche).
- **ECR** — stores your Docker images. *Use to* hold containers for SageMaker/ECS/EKS.
- **ECS / EKS** — run containers (AWS-native / Kubernetes). *Use to* host models or services in containers.

## Developer tools & IaC
- **CloudFormation** — infrastructure as templates. *Use for* repeatable, version-controlled infra.
- **CDK** — infrastructure as real code. *Use when* teams prefer coding over YAML.
- **CodePipeline / CodeBuild / CodeDeploy** — orchestrate / build-and-test / deploy. *Use to* automate the release process.
- **CodeArtifact** — a package/dependency repository. *Use to* manage build dependencies.
- **X-Ray** — traces a request across services. *Use to* find where latency is coming from.

## Integration & orchestration
- **Step Functions** — visual workflow engine across any service. *Use for* orchestration that includes non-ML steps.
- **MWAA (Managed Airflow)** — managed Apache Airflow. *Use when* a team already runs Airflow.
- **EventBridge** — an event bus and scheduler. *Use to* trigger pipelines/retraining on events or a schedule.
- **SNS / SQS** — notifications (fan-out) / message queues (buffering). *Use for* alerts / decoupling systems.

## Management, governance & cost
- **CloudWatch (+ Logs)** — metrics, logs, alarms, dashboards. *Use to* monitor everything.
- **CloudTrail** — records every API call. *Use for* audit trails and security investigations.
- **Config** — tracks resource configuration/compliance. *Use to* enforce configuration rules.
- **Compute Optimizer** — recommends better instance sizes. *Use to* right-size and save money.
- **Auto Scaling** — scales resources with demand. *Use for* elastic capacity.
- **Systems Manager** — ops tooling + Parameter Store for config/secrets. *Use for* config storage and automation.
- **Organizations / Service Catalog / Trusted Advisor / Chatbot** — multi-account management / approved-product catalog / best-practice and cost checks / ChatOps alerts.
- **Cost Explorer / Budgets / Billing and Cost Management** — analyze spend / set alerts and limits / manage billing. *Use for* cost visibility and control.

## Networking & security
- **VPC (+ endpoints / PrivateLink)** — your private network, with private doorways to AWS services. *Use to* keep traffic off the public internet.
- **API Gateway** — a managed front door for APIs. *Use to* expose a model as an API.
- **CloudFront** — content delivery network. *Use for* fast global delivery.
- **Direct Connect** — a dedicated line from your data center to AWS (niche).
- **IAM** — controls who can do what. *Use for* all access control (users, execution roles, least privilege, PassRole).
- **KMS** — manages encryption keys. *Use to* encrypt S3, disks, and traffic.
- **Macie** — finds sensitive data (PII/PHI) in S3. *Use for* compliance discovery.
- **Secrets Manager** — stores and rotates secrets. *Use for* database passwords and API keys.

## Migration
- **DataSync** — fast bulk data transfer into AWS. *Use to* move big datasets into S3/EFS/FSx.

---

# Out-of-Scope Services — Don't Study, but Recognize as Distractors
These appear in the guide's out-of-scope list, so if you see them as an answer they're almost certainly wrong. Notable ML ones: **DeepRacer, HealthImaging, HealthOmics, Monitron, Panorama**. Also out of scope: AppFlow, SWF, MQ; App Runner, Elastic Beanstalk, Lightsail, Outposts; ROSA; CodeCatalyst, CloudShell, Application Composer, Fault Injection Service; the entire IoT family; most security services beyond IAM/KMS/Macie/Secrets Manager (Cognito, GuardDuty, Inspector, Security Hub, WAF, Shield, Detective, ACM, CloudHSM, Directory Service); the Elemental/Media family; Route 53 and most advanced networking; and all business/end-user apps. Learning this list is a fast way to eliminate wrong answers under time pressure.

---

# How to Study This
For every task statement, be able to answer two things: *what is it* and *when would I pick it*. The exam lives in the second. Train yourself on the qualifier words that signal the answer:
- **"least operational overhead" / "no ML expertise"** → a managed AI service or something serverless.
- **"most cost-effective"** → Spot training, serverless/async inference, batch transform, multi-model endpoints, Savings Plans.
- **"lowest latency"** → real-time endpoint, online Feature Store.
- **"must not use the public internet"** → VPC endpoints / PrivateLink.
- **"detect drift / accuracy dropping in production"** → Model Monitor.
- **"explain predictions / check for bias"** → Clarify.

Given a DevSecOps/infrastructure background, Domains 3 and 4 (deployment, IaC, containers, VPC, IAM, cost) are largely your day job wearing ML vocabulary — the least-privilege/`PassRole`, VPC-endpoint, and KMS material especially. Put your heaviest study into Domain 2's ML theory (metrics, choosing algorithms, regularization, tuning), since that's the part furthest from infrastructure work and where most surprises hide.
