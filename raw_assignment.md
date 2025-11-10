Part 1: Short Answer Questions (30 points)

1 Problem Definition (6 points)
a. Define a hypothetical AI problem 
Predicting daily supermarket/ store sales demand for fresh bread.
b. List 3 objectives 
Minimize Food Waste- Accurately predict demand to prevent over-baking and reduce the amount of unsold bread.
Optimize Production- Improve the bakery's operational efficiency by creating more accurate schedules for staffing and raw ingredient orders.
Prevent stockouts- Ensure sufficient stock is available to meet customer demand, particularly during peak times, thereby protecting revenue and customer satisfaction.
Stakeholders:
Store Manager- deals with overall store profitability, which is directly impacted by minimizing waste (a cost) and preventing stockouts (a loss of revenue).
Bakery Department Head- needs the daily prediction number to plan labour, manage ingredient inventory and execute the baking schedule. 
c. Propose 1 Key Performance Indicator (KPI) to measure success.
Percentage reduction in daily bread waste.

2 Data Collection & Preprocessing (8 points)
a. Identify 2 data sources for your problem.
Historical sales data from cash registers (POS)- records from the store's cash registers Point-of-Sale system.
External data- local weather forecasts and holiday/event calendars.
b. Explain 1 potential bias in the data.
Stockout Bias-sales data might show low demand just because the store ran out of bread, leading the model to under-predict future demand.
c. Outline 3 preprocessing steps 
Handling Missing Data- fill gaps from register downtime with averages.
Feature Engineering- create new, meaningful columns from raw data, such as converting a date into a DayOfWeek (1-7).
Normalization- rescale numerical features like temperature to a 0-1 range, to ensure the model treats them equally.

3 Model Development (8 points)
a. Choose a model (e.g., Random Forest, Neural Network) and justify your choice.
Random Forest- because it effectively predicts numerical values (regression) using a mix of different feature types (categorical like holidays, numerical like no. of loaves).
b. Describe how you would split data into training/validation/test sets.
Training (70%) on older data.
Validation (15%) on recent data for tuning
Test (15%) on the most recent data for final evaluation.
c. Name 2 hyperparameters you would tune and why.
n_estimators (number of trees)- more trees generally make the model more accurate and stable but adding too many just makes it slow to train. 
max_depth- to control model complexity and prevent overfitting. If the trees are too deep, the model "memorizes" the old sales data instead of learning the real underlying pattern. Making it very bad at predicting sales on new, future days.

4 Evaluation & Deployment (8 points)
a. Select 2 evaluation metrics and explain their relevance.
Mean Absolute Error (MAE) to measure the average error in loaves per day.
This is an important metric especially for the store manager. An MAE of 6 provides a simple, direct answer: on average, our forecast will be wrong by 6 loaves. This number can be directly translated into the cost of waste or the risk of stockouts.

R-squared to see how much sales variance the model explains.
Shows how well your features like weather or holiday predict the sales. A high R squared score means your features are very good at predicting the sales patterns. A low R squared means the features are bad at explaining sales, and the model is mostly just guessing.
b. What is concept drift? How would you monitor it post-deployment?
Concept drift is what happens when the real-world patterns your model was trained on change over time, making its predictions less accurate for example an outdated model/concept.
For example, if a new rival supermarket with its own bakery opens nearby. This steals a portion of your customers. The AI model, trained on old data (before the rival existed), still expects high sales. It will consistently over-predict demand, leading to significant and continuous food waste.
To monitor it, continuously track the model's daily error.
Automatically log the model's prediction (e.g. Predict 150) versus the actual sales (e.g. Sold 120). If the Mean Average Error gets much worse and stays worse for several days, it triggers an alert. This alert tells you it's time to retrain the model on this new data so it can learn the new, lower sales pattern.
c. Describe 1 technical challenge during deployment 
Ensuring the automated system successfully pulls the sales logs of each day, weather forecasts of the next day, and the stores promotion calendar schedules every single night without failure, could be a challenge for example if the weather API is down, the whole prediction fails.

# PART 2 :Case Study Application — Predicting 30-Day Patient Readmission Risk
Context : build and deploy an AI decision-support system that predicts the probability a discharged patient will be readmitted within 30 days, and provide actionable risk explanations to care teams so they can target interventions and reduce preventable readmissions.

## 1. Problem Scope 
Problem definition: Many hospitals face high 30-day readmission rates that harm patients and increase costs. The goal is an AI system that, at or shortly before discharge, produces a calibrated risk score for 30-day unplanned readmission and surface the top contributing factors to guide tailored interventions.

## Detailed objectives:

Accurate risk scoring: Provide a calibrated probability (0–100%) per patient, enabling consistent thresholding for interventions. Explanation: calibrated probabilities let clinicians translate risk into actions (e.g., >20% → care manager follow-up).

Actionable interpretability: Return the top 3–5 factors (e.g., high creatinine trend, >3 prior admissions in 12 months, no follow-up scheduled) with human-readable explanations to aid clinician decision making.

Prioritized interventions: Enable care teams to triage scarce resources (home visits, early outpatient appointments, medication reconciliation) by predicted risk and intervention cost/benefit.

Equity of care: Ensure comparable model performance across protected and vulnerable subgroups (age, sex, race, insurance), and surface group-level performance metrics.

Continuous monitoring & retraining: Establish drift detection and retraining procedures so model performance remains reliable as treatment patterns/patient mix change.

Operational feasibility & safety: Integrate smoothly into discharge workflows with low latency, human override, logging, and fail-safe fallbacks (manual workflows if the system is unavailable).

## Primary stakeholders :
Patients & families — recipients of interventions; their outcomes and privacy are paramount.
Discharging clinicians (physicians, nurse practitioners) — need clear, trustworthy guidance to decide post-discharge care.
Care coordinators / case managers — operationalize follow-ups, arrange home services, and track outcomes.
Hospital quality & finance teams — monitor readmission rates, reimbursement, and value-based care metrics.
Health IT & Privacy Officers — ensure secure integration, compliance (HIPAA), and auditability.
Payers & health systems — interested in reduced avoidable admissions and cost savings, may fund pilots.
Why this matters (brief): reducing preventable readmissions improves patient safety, reduces unnecessary costs, and often aligns with regulatory or payer incentives; but any system must be accurate, fair, and integrated to be clinically useful.

## 2. Data Strategy 
### A. Proposed data sources 
Electronic Health Record (EHR) structured fields: demographics, problem lists, ICD/ CPT codes, discharge disposition, admitting service, length of stay, vitals. Why: foundational clinical snapshot.

Laboratory & vital sign time series: last values, abnormalities, recent trends (e.g., creatinine slope, hemoglobin). Why: acute physiology predicts instability post-discharge.

Medication orders & reconciliation: inpatient meds, discharge prescriptions, counts of high-risk drugs (anticoagulants, opiates). Why: medication complexity and access issues drive readmissions.

Utilization history: prior inpatient admissions, ED visits, outpatient visit frequency in last 30/90/365 days. Why: past utilization is one of the strongest predictors.

Discharge process data: presence/absence of scheduled follow-up within 7 days, home health referral, discharge instructions completeness. Why: process failures predict readmission.

Administrative & social determinants (where available): insurance/payer, primary language, coded social needs (housing, food insecurity), distance to care. Why: social vulnerabilities strongly influence post-discharge risk.

Clinical free text (optional): discharge summary, nursing notes, problem list narratives (use NLP cautiously). Why: contains nuanced clinical context not in structured fields; use with PHI protections.

### B. Two ethical concerns
1) Patient privacy & data security
Risk: Sensitive PHI exposure during model development, storage, or inference.
Mitigation: Use de-identified datasets for model development when possible; implement encryption (TLS for transit, AES-256 for rest), strict RBAC, audit logs, and BAAs for third-party vendors. Limit logging of PHI; use secure enclaves for re-identifiable work.

2) Algorithmic bias / inequitable outcomes
Risk: Model trained on historical data that encodes disparities (e.g., marginalized groups have different utilization patterns), resulting in biased predictions and unequal interventions.
Mitigation: Perform subgroup performance audits (disaggregate recall/precision/AUROC by race, age, payer), apply fairness-aware techniques (reweighting, stratified sampling), consult stakeholders from affected communities, and monitor outcomes post-deployment.

Additional ethical considerations :
Automation bias: clinicians may over-trust or under-trust the model — respond with clear UI cues, uncertainty, and mandatory clinician review.
Intervention equity: ensure flagged patients have real, available interventions to avoid harm through "flagging without capacity."
Informed governance: ensure transparency to patients about secondary data usage and governance oversight.

### C. Preprocessing pipeline (full, stepwise with feature engineering — 10+ steps)
1. Secure ingestion & validation
Pull extracts from EHR via secure pipelines (FHIR/HL7 or database extracts), validate schema and field ranges, log missingness.

2. Patient-level linking & deduplication
Map encounters to unique patient IDs; deduplicate repeated or merged records; ensure consistent patient identifiers.

3. Define prediction index/time window
Index time = discharge timestamp. Define look-back windows for features (e.g., labs/vitals last 72 hrs; utilization: 30/90/365 days). Ensure feature generation uses only data available before discharge.

4. Label engineering
Label = unplanned inpatient readmission within 30 days of discharge (exclude planned readmissions like scheduled chemo or staged procedures). Create flags for transfers and death.

5. Missing data strategy
Classify missingness: (a) informative (e.g., no scheduled follow-up) vs (b) random. For (a) keep as indicator variable; for (b) impute using median, or model-based imputation. Always include missingness indicator features.

6. Feature engineering — clinical aggregates
Comorbidity scores: compute Charlson/Elixhauser indices from ICD history.
Lab feature set: last value, mean over last 72 hours, slope (trend), binary abnormal flags.
Vital trends: variability and last recorded vitals (HR, BP, O2 sat).
Medication complexity: number of unique meds, number of high-risk meds, presence of new discharge prescriptions.
Utilization metrics: counts of ED visits, admissions in prior 30/90/365 days; time since last discharge.
Discharge process features: follow-up scheduled (yes/no), discharge to home vs SNF, home health referral, documented caregiver presence.
Social risk proxies: insurance type, language, distance to hospital; if free-text indicates social instability, create binary flags via NLP.

7. Categorical encoding & scaling
One-hot encode low-cardinality fields (discharge disposition), target/ordinal encoding for high-cardinality fields (diagnosis groups). Scale continuous variables (robust scaling) to manage outliers.

8. NLP processing (if used)
De-identify text, extract clinically meaningful tokens/phrases (problem summaries, social risk phrases) using a validated clinical NLP pipeline; represent as sparse features or topic embeddings.

9. Leakage checks
Ensure no post-discharge data or future events are included in features; validate feature timestamps carefully to avoid label leakage.

10. Splitting strategy
Use patient-grouped chronologic split: train on earlier dates, validate on later period(s); ensure patients do not appear in both train/val/test sets to avoid optimistic bias.

11. Feature selection & dimensionality reduction
Use domain knowledge (clinician review) plus statistical selection (regularized models, permutation importance) to remove noisy or non-generalizable features.

12. Pipelines & reproducibility
Implement preprocessing steps as a deterministic pipeline (e.g., scikit-learn Pipeline, or a dedicated preprocessing microservice) so production inference uses exact same transforms.


## 3. Model Development (10 points — detailed; includes model selection + confusion matrix)
### A. Model selection and justification 
Primary model: Gradient Boosted Trees (LightGBM / XGBoost / CatBoost).
Why: Excellent performance on tabular EHR data; handles missing values natively; fast to train; often outperforms linear models when interactions/nonlinearities matter.
Interpretability support: Use SHAP (SHapley Additive exPlanations) to present per-patient feature contributions; this combination balances accuracy and explainability.
Calibration needs: Post-training calibrate probabilities (isotonic regression or Platt) because interventions depend on trustworthy risk probabilities.
Alternative / simpler models: Regularized logistic regression for maximum interpretability and easier clinical acceptance; can be used as a baseline. Generalized Additive Models (GAMs / EBM) can offer a middle ground (interpretable nonlinear effects).
Sequence models (optional): If rich longitudinal EHR sequences are available, consider time-aware models (RNN, Transformer) to capture temporal patterns — but these require more data and rigorous validation.
Ensembling & robustness: Consider ensembling multiple models to reduce variance. Provide model versioning and explainability artifacts for each ensemble member as needed.
### B. Training & validation strategy (concise)
Use patient-grouped k-fold cross-validation or time-based splits. Optimize hyperparameters via cross-validation with metric appropriate to class imbalance (e.g., AUROC, average precision, or a utility function capturing intervention cost/benefit).
### C. Hypothetical confusion matrix & metrics (with calculations)
Assumptions: test set = 1,000 discharges; true positives (actual readmissions within 30 days) = 150 (15% prevalence). A chosen threshold yields:
	Pred = Positive	Pred = Negative	Total
Actual Positive (P)	TP = 100	FN = 50	150
Actual Negative (N)	FP = 150	TN = 700	850
Total	250	750	1000

Metric calculations:
Precision (PPV) = TP / (TP + FP) = 100 / (100 + 150) = 100 / 250 = 0.40 (40%).
Interpretation: Of patients flagged high risk, 40% are true readmissions. Precision costs matter if interventions are expensive.
Recall (Sensitivity) = TP / (TP + FN) = 100 / (100 + 50) = 100 / 150 = 0.667 (66.7%).
Interpretation: The model detects two-thirds of actual readmissions.
Specificity = TN / (TN + FP) = 700 / (700 + 150) = 700 / 850 = 0.824 (82.4%).
Negative Predictive Value (NPV) = TN / (TN + FN) = 700 / (700 + 50) = 700 / 750 = 0.933 (93.3%).
F1 Score = 2 * (Precision * Recall) / (Precision + Recall) = 2*(0.4*0.6667)/(0.4+0.6667) ≈ 0.50 (50%).
AUROC & PR-AUC: compute both; AUROC for discrimination across thresholds, PR-AUC useful due to class imbalance.

Threshold trade-offs:
If interventions are low cost (e.g., automated SMS), prefer higher recall (lower threshold).
If interventions are resource-intensive (home visit), prefer higher precision (raise threshold). Use expected utility analysis to pick threshold.
Calibration check: Ensure predicted probability bins match observed readmission rates (calibration plot / Brier score).


## 4. Deployment (integration steps + compliance)
### A. Steps to integrate the model into hospital systems (8+ steps, descriptive)
Model artifactization & CI/CD: Package model artifacts (model file, preprocessing code, feature schema) and store in a model registry (with versioning). Automate tests and deployments with CI/CD pipelines.
Containerize & secure runtime: Build a Docker image containing the model server and preprocessing pipeline; use Kubernetes or a secure VM for hosting with private network access.
Deploy an API layer: Expose a secured REST or FHIR-compatible endpoint that the EHR can call at discharge; include strict input validation and feature checks.
EHR integration & UI design: Work with the EHR vendor/clinical informatics to embed the risk score and top contributing factors into clinician workflows (e.g., discharge summary screen, best practice advisory), ensuring minimal disruption.
Access control & logging: Enforce OAuth2 / mutual TLS and RBAC; log requests and responses for audit but limit PHI in logs; retain logs per policy.
Pilot phase (silent & controlled rollout): Start in silent mode (predictions recorded but not shown/used) for 1–3 months to gather labels and calibrate; then pilot with a small clinician group with feedback loops.
Human-in-the-loop & workflows: Define stepwise interventions for different risk tiers (e.g., high risk → care manager call within 48 hrs; medium risk → nurse phone check).
Monitoring & feedback: Implement dashboards tracking model performance (AUROC, precision, recall, calibration), data drift (PSI/per-feature distribution), and operational metrics (latency, error rates).
Governance & change control: Create an ML governance board (clinicians, IT, privacy, legal) to approve updates, review performance, and oversee retraining cadence.
Rollback & resiliency plans: Implement quick rollback procedures and fallback manual decision support if model service fails.
### B. Ensuring compliance with HIPAA & healthcare regulations (6+ concrete measures)
Data minimization & need-to-know: Only the minimum necessary PHI/fields are used for inference; restrict development and logs to de-identified or limited datasets.
BAAs with vendors: Ensure written Business Associate Agreements (BAAs) with any cloud or vendor handling PHI, specifying responsibilities and breach notification timelines.
Encryption & secure transmission: Use TLS 1.2+ for all network traffic and AES-256 (or equivalent) encryption for stored PHI; manage keys with enterprise key management.
RBAC & strong authentication: Enforce least privilege, MFA for admin interfaces, and separate service accounts for automated processes.
Audit logging & monitoring: Immutable audit trails of who accessed what data, when, and for what purpose; retain logs per policy for audits.
Privacy & impact assessments: Conduct a Data Protection Impact Assessment (DPIA) / Privacy Impact Assessment (PIA) before deployment; involve legal and privacy teams.
IRB & patient consent (if applicable): For research or prospective trials, obtain Institutional Review Board (IRB) approvals and informed consent where required.
Incident response & breach plan: Defined procedures for suspected breaches, including notification timelines and remediation steps.
Periodic compliance audits: Regular security penetration testing and privacy audits to ensure continued compliance.


## 5. Optimization (propose 1 method to address overfitting, with detailed steps and rationale)
Primary method (recommended): Early stopping with patient-grouped cross-validation + regularization hyperparameters (applies to gradient boosting).
Why this method?
Early stopping prevents the model from continuing to fit noise after validation performance plateaus. Patient-grouped CV ensures validation performance reflects real generalization across patients (prevents optimistic leakage). Regularization (L1/L2/leaf penalties / max depth) controls model complexity directly.
Implementation steps (detailed):
Patient-grouped k-fold CV: split by patient (not encounter) to build folds, or use time-based holdout (train on earlier dates, validate on later dates) to mimic deployment.
Hyperparameter grid / Bayesian search: tune learning_rate, num_trees, max_depth / num_leaves, min_child_weight, L1/L2 regularization (alpha, lambda), and subsample fraction. Use CV performance (AUROC or utility) to pick settings.
Early stopping rule: during training, monitor validation loss (or AUROC); stop training if no improvement after N rounds (e.g., 50 rounds) to avoid overfitting.
Use subsampling & column sampling: set subsample and colsample_bytree to <1.0 to reduce variance (bagging behavior) and improve generalization.
Calibration & ensembling: after training, calibrate with isotonic regression on holdout; optionally ensemble several models trained with different seeds/ folds to reduce variance.
Post-hoc evaluation: perform a robustness evaluation on later temporal holdout sets; evaluate subgroup performance to ensure regularization didn’t disproportionately hurt minority groups.
Other complementary techniques (brief):
Prune features via domain knowledge and regularized models (LASSO) to remove noisy predictors.
If using deep models, apply dropout, weight decay, and early stopping similarly.
Monitor generalization gap (training vs validation metrics) and trigger retraining with more data if needed.


## Final notes & recommended next steps 
Build a reproducible prototype using de-identified EHR extracts and baseline logistic regression + LightGBM.
Run a silent pilot (predictions logged but not shown) for 3 months to collect ground-truth labels and calibration data.
Conduct fairness audits on the pilot data; adjust sampling or model to address disparities.
Develop clinical workflows for actioning risk scores, including resource mapping for interventions.
Complete DPIA & security assessments and obtain governance sign-offs before any live patient–facing deployment.
Design monitoring dashboards for performance, drift, and operational metrics; schedule regular retraining cadences (e.g., quarterly or triggered by drift alerts).
