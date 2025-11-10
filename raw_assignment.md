# AI Development Workflow Assignment

**Course:** AI for Software Engineering  
**Duration:** 7 days  
**Total Points:** 100  
**Group Members:** [Your Group Members Names Here]  
**Date:** [Submission Date]

---

## Table of Contents

1. [Part 1: Short Answer Questions](#part-1-short-answer-questions)
2. [Part 2: Case Study Application](#part-2-case-study-application)
3. [Part 3: Critical Thinking](#part-3-critical-thinking)
4. [Part 4: Reflection & Workflow Diagram](#part-4-reflection--workflow-diagram)
5. [References](#references)

---

## Part 1: Short Answer Questions (30 points)

### 1. Problem Definition (6 points)

#### a. Define a hypothetical AI problem

**Problem Statement:**  
Predicting daily supermarket/store sales demand for fresh bread.

**Context:**  
Supermarkets face a constant challenge of determining how much bread to bake daily. Over-baking leads to waste and financial loss, while under-baking results in stockouts, disappointed customers, and lost revenue. This AI system aims to forecast daily bread demand using historical sales, weather patterns, and other relevant factors.

---

#### b. List 3 objectives and 2 stakeholders

**Objectives:**

1. **Minimize Food Waste**  
   Accurately predict demand to prevent over-baking and reduce unsold bread. Even a 10% reduction in waste can save thousands of dollars annually and reduce environmental impact.

2. **Optimize Production**  
   Improve operational efficiency by creating accurate schedules for staffing and raw ingredient orders. This allows better planning for flour, yeast procurement, and baker shift scheduling.

3. **Prevent Stockouts**  
   Ensure sufficient stock during peak times to protect revenue and customer satisfaction. Repeated stockouts damage customer loyalty and may drive customers to competitors.

**Stakeholders:**

1. **Store Manager**  
   Oversees overall store profitability. Needs the system to minimize waste costs and prevent revenue loss from stockouts. Uses predictions for strategic decisions on inventory, pricing, and promotions.

2. **Bakery Department Head**  
   Manages daily bakery operations. Requires daily predictions to plan production schedules, manage ingredient inventory, and schedule staff appropriately.

---

#### c. Propose 1 Key Performance Indicator (KPI) to measure success

**KPI:** Percentage reduction in daily bread waste.

**Rationale:**  
This metric is easily measurable by counting unsold loaves before and after AI implementation. It directly ties to business value, as each wasted loaf represents lost money and environmental impact. A clear target, such as 40% waste reduction within 3 months, provides a concrete success measure that stakeholders can understand and track.

---

### 2. Data Collection & Preprocessing (8 points)

#### a. Identify 2 data sources for your problem

**Data Source 1: Historical Sales Data (Point-of-Sale System)**

Contains transaction timestamps, quantities sold, bread types, prices, and stockout times. This data reveals actual customer demand patterns, seasonal trends, peak shopping hours, and provides 12-24 months of historical foundation for pattern recognition.

**Data Source 2: External Data (Weather & Calendar Information)**

Includes weather forecasts (temperature, precipitation), public holidays, school breaks, and local events. Weather affects shopping behavior, holidays change purchasing patterns, and local events impact foot traffic. Sources include weather APIs like OpenWeatherMap and public holiday databases.

---

#### b. Explain 1 potential bias in the data

**Stockout Bias (Censored Demand)**

Sales data shows low demand on days when bread sold out early, not because customers didn't want more, but because none was available. This "censored demand" means we only see sales until stock depleted, not true customer demand. For example, if 100 loaves sold out by 2 PM, actual demand might have been 150, but only 100 was recorded.

**Impact:** The model learns from recorded sales rather than actual demand, leading to systematic under-prediction on high-demand days. This perpetuates stockouts and defeats our objective of preventing them.

---

#### c. Outline 3 preprocessing steps

**1. Handling Missing Data**

Fill gaps in data caused by system downtime or unavailable information. For small gaps (1-2 hours), use averages of surrounding values. For complete missing days, use the average of the same weekday from surrounding weeks. For missing weather data, use historical averages or alternative APIs.

**2. Feature Engineering**

Create meaningful features from raw data. Transform dates into DayOfWeek (1-7), IsWeekend (0/1), Month (1-12), and IsHoliday (0/1). Convert weather into IsRainy, TemperatureCategory, and IsSevereWeather. Create sales-based features like SalesLast7Days, SalesSameDayLastWeek, and TrendDirection. This gives the model actionable patterns to learn from.

**3. Normalization (Feature Scaling)**

Rescale numerical features to a common range (0-1) so features with different units are treated fairly. Without normalization, features with larger numbers dominate learning, even if less important. Apply Min-Max scaling: divide the difference between each value and the minimum by the range (maximum minus minimum).

---

### 3. Model Development (8 points)

#### a. Choose a model and justify your choice

**Model:** Random Forest Regressor

**Justification:**  
Random Forest is an ensemble method that creates multiple decision trees and averages their predictions. It's ideal for this problem because it handles mixed data types (categorical like holidays, numerical like temperature) without complex transformations, captures non-linear relationships (sales don't change linearly with weather), and resists overfitting through its averaging approach. It also provides feature importance rankings and handles outliers well, making it robust and interpretable for stakeholders.

---

#### b. Describe how you would split data into training/validation/test sets

**Three-Way Time-Based Split:**

- **Training Set (70%)** - Older historical data (e.g., Jan 2022 - Dec 2023) for the model to learn patterns
- **Validation Set (15%)** - Recent data (e.g., Jan 2024 - Mar 2024) for tuning hyperparameters  
- **Test Set (15%)** - Most recent data (e.g., Apr 2024 - Jun 2024) for final evaluation

**Rationale:**  
Time-based splitting (not random shuffling) is crucial because it mimics real deployment where the model trained on past data predicts future sales. This chronological split ensures honest performance estimation.

---

#### c. Name 2 hyperparameters you would tune and why

**1. n_estimators (Number of Trees)**

Controls how many decision trees the Random Forest creates. Too few trees (e.g., 10) leads to high variance and less accurate predictions. Optimal range (100-200) balances accuracy and training speed. Too many trees (1000+) provides minimal accuracy improvement while significantly increasing training time.

**2. max_depth (Maximum Tree Depth)**

Controls how many questions each tree can ask before making a prediction. Shallow trees (depth 2-3) underfit and miss important patterns. Optimal depth (8-12) captures meaningful patterns without memorizing noise. Deep trees (20+) overfit by memorizing training data specifics, performing poorly on new data.

---

### 4. Evaluation & Deployment (8 points)

#### a. Select 2 evaluation metrics and explain their relevance

**1. Mean Absolute Error (MAE)**

Measures average prediction error in loaves of bread. It's easily understood by stakeholders - "on average, forecasts will be wrong by 6 loaves." This directly translates to business costs: if each loaf costs $2 and MAE is 6 loaves, the daily forecast error cost is $12. It's actionable for planning and treats over-predictions and under-predictions equally.

**2. R-squared (R²)**

Measures how much sales variation the model explains, scored between 0 and 1. A high R² (0.7-0.9) means features like weather and holidays genuinely predict sales. Low R² (0.2-0.4) indicates sales are mostly random or missing key features. It validates feature selection and communicates confidence to stakeholders about the model's predictive power.

---

#### b. What is concept drift? How would you monitor it post-deployment?

**Concept Drift:**  
Occurs when real-world patterns the model learned change over time, reducing prediction accuracy. Examples include new competitors opening nearby, consumer behavior shifts (low-carb diet trends), store changes (renovations), or neighborhood changes (new residential development).

**Monitoring Strategy:**

Implement daily automated logging of predictions versus actual sales, calculating error metrics. Track 7-day and 30-day rolling average errors. Set threshold-based alerts: Warning when error exceeds 1.5× baseline, Critical when it exceeds 2× baseline. Monitor for directional bias (consistent over-prediction or under-prediction). When critical alerts persist for 3+ days, trigger automated retraining with recent data. Maintain a visual dashboard showing performance trends and alert history.

---

#### c. Describe 1 technical challenge during deployment

**Challenge: Data Pipeline Reliability and Dependencies**

The prediction system must automatically pull data from multiple sources nightly. Failures at any point break the entire pipeline. The weather API might timeout or hit rate limits, the POS database might be under maintenance, network connectivity could fail, or data formats might change unexpectedly after system upgrades.

**Solutions:**  
Implement robust error handling with retry logic and exponential backoff. Use fallback data sources (multiple weather APIs, cached historical data). Run health checks before main processes. Validate data quality before use. Implement graceful degradation (make predictions with available data, noting reduced confidence). Log all processes comprehensively and send alerts on failures. Schedule redundant runs (10:30 PM health check, 11:00 PM main run, 11:30 PM backup, 6:00 AM final check).

---

## Part 2: Case Study Application (40 points)

### Scenario
A hospital wants an AI system to predict patient readmission risk within 30 days of discharge.

**Context:**  
Build and deploy an AI decision-support system that predicts the probability a discharged patient will be readmitted within 30 days, and provide actionable risk explanations to care teams so they can target interventions and reduce preventable readmissions.

---

### 1. Problem Scope (5 points)

#### Problem Definition

Many hospitals face high 30-day readmission rates that harm patients and increase costs. The goal is an AI system that, at or shortly before discharge, produces a calibrated risk score for 30-day unplanned readmission and surfaces the top contributing factors to guide tailored interventions.

#### Objectives

**1. Accurate Risk Scoring**  
Provide a calibrated probability (0–100%) per patient, enabling consistent thresholding for interventions. Calibrated probabilities let clinicians translate risk into actions (e.g., >20% risk → care manager follow-up).

**2. Actionable Interpretability**  
Return the top 3–5 risk factors (e.g., high creatinine trend, >3 prior admissions in 12 months, no follow-up scheduled) with human-readable explanations to aid clinician decision making.

**3. Prioritized Interventions**  
Enable care teams to triage scarce resources (home visits, early outpatient appointments, medication reconciliation) by predicted risk and intervention cost/benefit.

**4. Equity of Care**  
Ensure comparable model performance across protected and vulnerable subgroups (age, sex, race, insurance), and surface group-level performance metrics.

**5. Continuous Monitoring & Retraining**  
Establish drift detection and retraining procedures so model performance remains reliable as treatment patterns and patient mix change.

**6. Operational Feasibility & Safety**  
Integrate smoothly into discharge workflows with low latency, human override, logging, and fail-safe fallbacks if the system is unavailable.

#### Primary Stakeholders

| Stakeholder | Role & Interest |
|-------------|----------------|
| **Patients & Families** | Recipients of interventions; their outcomes and privacy are paramount |
| **Discharging Clinicians** | Physicians and nurse practitioners who need clear, trustworthy guidance to decide post-discharge care |
| **Care Coordinators** | Operationalize follow-ups, arrange home services, and track outcomes |
| **Hospital Quality & Finance Teams** | Monitor readmission rates, reimbursement, and value-based care metrics |
| **Health IT & Privacy Officers** | Ensure secure integration, HIPAA compliance, and auditability |
| **Payers & Health Systems** | Interested in reduced avoidable admissions and cost savings |

**Why This Matters:**  
Reducing preventable readmissions improves patient safety, reduces unnecessary costs, and aligns with regulatory and payer incentives. However, any system must be accurate, fair, and clinically integrated to be useful.

---

### 2. Data Strategy (10 points)

#### A. Proposed Data Sources

**1. Electronic Health Record (EHR) Structured Fields**  
Demographics, problem lists, ICD/CPT codes, discharge disposition, admitting service, length of stay, and vitals. Provides foundational clinical snapshot of the patient.

**2. Laboratory & Vital Sign Time Series**  
Last values, abnormalities, and recent trends (e.g., creatinine slope, hemoglobin levels). Acute physiology predicts post-discharge instability.

**3. Medication Orders & Reconciliation**  
Inpatient medications, discharge prescriptions, counts of high-risk drugs (anticoagulants, opiates). Medication complexity and access issues drive readmissions.

**4. Utilization History**  
Prior inpatient admissions, ED visits, outpatient visit frequency in last 30/90/365 days. Past utilization is one of the strongest predictors.

**5. Discharge Process Data**  
Presence/absence of scheduled follow-up within 7 days, home health referral, discharge instructions completeness. Process failures predict readmission.

**6. Administrative & Social Determinants**  
Insurance/payer, primary language, coded social needs (housing, food insecurity), distance to care. Social vulnerabilities strongly influence post-discharge risk.

**7. Clinical Free Text (Optional)**  
Discharge summaries, nursing notes, problem list narratives using Natural Language Processing. Contains nuanced clinical context not in structured fields; requires PHI protections.

---

#### B. Two Ethical Concerns

**1. Patient Privacy & Data Security**

**Risk:**  
Sensitive Protected Health Information (PHI) exposure during model development, storage, or inference.

**Mitigation:**
- Use de-identified datasets for model development when possible
- Implement encryption (TLS for transit, AES-256 for storage at rest)
- Apply strict Role-Based Access Control (RBAC)
- Maintain comprehensive audit logs
- Establish Business Associate Agreements (BAAs) for third-party vendors
- Limit PHI logging and use secure enclaves for re-identifiable work

**2. Algorithmic Bias & Inequitable Outcomes**

**Risk:**  
Model trained on historical data that encodes disparities (e.g., marginalized groups have different utilization patterns), resulting in biased predictions and unequal interventions.

**Mitigation:**
- Perform subgroup performance audits disaggregated by race, age, and payer
- Apply fairness-aware techniques (reweighting, stratified sampling)
- Consult stakeholders from affected communities
- Monitor outcomes post-deployment for equity
- Regularly review model performance across demographic groups

**Additional Ethical Considerations:**
- **Automation Bias:** Clinicians may over-trust or under-trust the model. Address with clear UI cues, uncertainty indicators, and mandatory clinician review.
- **Intervention Equity:** Ensure flagged patients have access to real interventions to avoid harm through "flagging without capacity."
- **Informed Governance:** Ensure transparency to patients about data usage and establish governance oversight.

---

#### C. Preprocessing Pipeline

**Step 1: Secure Ingestion & Validation**  
Pull data extracts from EHR via secure pipelines (FHIR/HL7 or database extracts), validate schema and field ranges, log missingness patterns.

**Step 2: Patient-Level Linking & Deduplication**  
Map encounters to unique patient IDs, deduplicate repeated or merged records, ensure consistent patient identifiers across systems.

**Step 3: Define Prediction Time Window**  
Set index time as discharge timestamp. Define look-back windows for features (e.g., labs/vitals last 72 hours; utilization: 30/90/365 days). Ensure features use only pre-discharge data.

**Step 4: Label Engineering**  
Define label as unplanned inpatient readmission within 30 days of discharge. Exclude planned readmissions (scheduled chemotherapy, staged procedures). Create flags for transfers and deaths.

**Step 5: Missing Data Strategy**  
Classify missingness as informative (e.g., no scheduled follow-up) versus random. For informative missingness, keep as indicator variable. For random missingness, apply median or model-based imputation. Always include missingness indicator features.

**Step 6: Feature Engineering - Clinical Aggregates**
- **Comorbidity scores:** Compute Charlson/Elixhauser indices from ICD history
- **Lab features:** Last value, mean over last 72 hours, slope (trend), binary abnormal flags
- **Vital trends:** Variability and last recorded vitals (heart rate, blood pressure, oxygen saturation)
- **Medication complexity:** Number of unique medications, high-risk medications, new discharge prescriptions
- **Utilization metrics:** Counts of ED visits and admissions in prior 30/90/365 days; time since last discharge
- **Discharge process features:** Follow-up scheduled (yes/no), discharge destination (home vs SNF), home health referral, documented caregiver presence
- **Social risk proxies:** Insurance type, language, distance to hospital; binary flags from NLP if available

**Step 7: Categorical Encoding & Scaling**  
One-hot encode low-cardinality fields (discharge disposition). Apply target/ordinal encoding for high-cardinality fields (diagnosis groups). Scale continuous variables using robust scaling to manage outliers.

**Step 8: NLP Processing (If Used)**  
De-identify text, extract clinically meaningful tokens/phrases using validated clinical NLP pipeline. Represent as sparse features or topic embeddings.

**Step 9: Leakage Checks**  
Ensure no post-discharge data or future events are included in features. Validate feature timestamps carefully to avoid label leakage.

**Step 10: Data Splitting Strategy**  
Use patient-grouped chronologic split: train on earlier dates, validate on later periods. Ensure patients do not appear in both train/validation/test sets to avoid optimistic bias.

**Step 11: Feature Selection**  
Use domain knowledge (clinician review) plus statistical selection (regularized models, permutation importance) to remove noisy or non-generalizable features.

**Step 12: Pipeline Reproducibility**  
Implement preprocessing steps as a deterministic pipeline so production inference uses exact same transformations as training.

---

### 3. Model Development (10 points)

#### A. Model Selection and Justification

**Primary Model: Gradient Boosted Trees (LightGBM / XGBoost / CatBoost)**

**Justification:**
- Excellent performance on tabular EHR data
- Handles missing values natively
- Fast to train and efficient for large datasets
- Often outperforms linear models when interactions and nonlinearities matter
- Supports interpretability through SHAP (Shapley Additive Explanations) values
- Can be calibrated post-training for trustworthy probability estimates

**Alternative Models:**
- **Regularized Logistic Regression:** Maximum interpretability and easier clinical acceptance; useful as baseline
- **Generalized Additive Models (GAMs/EBM):** Middle ground offering interpretable nonlinear effects
- **Sequence Models (Optional):** RNN or Transformer for rich longitudinal EHR sequences to capture temporal patterns

**Model Calibration:**  
Post-training calibration using isotonic regression or Platt scaling is essential because interventions depend on trustworthy risk probabilities.

---

#### B. Training & Validation Strategy

Use patient-grouped k-fold cross-validation or time-based splits to ensure patients don't appear in multiple sets. Optimize hyperparameters via cross-validation using metrics appropriate for class imbalance (AUROC, average precision, or utility function capturing intervention cost/benefit).

---

#### C. Confusion Matrix & Performance Metrics

**Test Set Assumptions:**
- Total discharges: 1,000
- Actual readmissions within 30 days: 150 (15% prevalence)
- Model predictions at chosen threshold:

|  | **Predicted Positive** | **Predicted Negative** | **Total** |
|---|---|---|---|
| **Actual Positive** | TP = 100 | FN = 50 | 150 |
| **Actual Negative** | FP = 150 | TN = 700 | 850 |
| **Total** | 250 | 750 | 1,000 |

**Metric Calculations:**

**Precision (Positive Predictive Value):**
Precision = TP / (TP + FP) = 100 / 250 = 0.40 (40%)


*Interpretation:* Of patients flagged as high risk, 40% are true readmissions. Important when interventions are expensive.

**Recall (Sensitivity):**
Recall = TP / (TP + FN) = 100 / 150 = 0.667 (66.7%)


*Interpretation:* The model detects two-thirds of actual readmissions.

**Specificity:**
Specificity = TN / (TN + FP) = 700 / 850 = 0.824 (82.4%)


*Interpretation:* The model correctly identifies 82.4% of patients who won't be readmitted.

**Negative Predictive Value (NPV):**
NPV = TN / (TN + FN) = 700 / 750 = 0.933 (93.3%)


*Interpretation:* 93.3% of patients predicted as low risk truly won't be readmitted.

**F1 Score:**
F1 = 2 × (Precision × Recall) / (Precision + Recall) = 2 × (0.4 × 0.667) / (1.067) ≈ 0.50 (50%)



**Threshold Trade-offs:**
- **Low-cost interventions** (automated SMS): Prefer higher recall (lower threshold)
- **Resource-intensive interventions** (home visits): Prefer higher precision (raise threshold)
- Use expected utility analysis to select optimal threshold
- Ensure calibration: predicted probability bins should match observed readmission rates

---

### 4. Deployment (10 points)

#### A. Integration Steps into Hospital Systems

**Step 1: Model Artifactization & CI/CD**  
Package model artifacts (model file, preprocessing code, feature schema) and store in a model registry with versioning. Automate testing and deployment with CI/CD pipelines.

**Step 2: Containerize & Secure Runtime**  
Build a Docker image containing the model server and preprocessing pipeline. Host using Kubernetes or secure VM with private network access.

**Step 3: Deploy API Layer**  
Expose a secured REST or FHIR-compatible endpoint that the EHR can call at discharge. Include strict input validation and feature checks.

**Step 4: EHR Integration & UI Design**  
Work with EHR vendor and clinical informatics to embed the risk score and top contributing factors into clinician workflows (discharge summary screen, best practice advisory) with minimal disruption.

**Step 5: Access Control & Logging**  
Enforce OAuth2/mutual TLS and RBAC. Log requests and responses for audit while limiting PHI in logs. Retain logs per organizational policy.

**Step 6: Pilot Phase**  
Start in silent mode (predictions recorded but not shown) for 1-3 months to gather labels and calibrate. Then pilot with a small clinician group with feedback loops.

**Step 7: Human-in-the-Loop Workflows**  
Define stepwise interventions for different risk tiers:
- High risk → Care manager call within 48 hours
- Medium risk → Nurse phone check
- Low risk → Standard discharge process

**Step 8: Monitoring & Feedback**  
Implement dashboards tracking model performance (AUROC, precision, recall, calibration), data drift (per-feature distribution), and operational metrics (latency, error rates).

**Step 9: Governance & Change Control**  
Create an ML governance board (clinicians, IT, privacy, legal) to approve updates, review performance, and oversee retraining cadence.

**Step 10: Rollback & Resiliency Plans**  
Implement quick rollback procedures and fallback manual decision support if model service fails.

---

#### B. Ensuring HIPAA Compliance

**1. Data Minimization**  
Use only the minimum necessary PHI for inference. Restrict development and logs to de-identified or limited datasets.

**2. Business Associate Agreements (BAAs)**  
Ensure written BAAs with any cloud provider or vendor handling PHI, specifying responsibilities and breach notification timelines.

**3. Encryption & Secure Transmission**  
Use TLS 1.2+ for all network traffic and AES-256 encryption for stored PHI. Manage keys with enterprise key management systems.

**4. Role-Based Access Control (RBAC)**  
Enforce least privilege access, multi-factor authentication (MFA) for admin interfaces, and separate service accounts for automated processes.

**5. Audit Logging & Monitoring**  
Maintain immutable audit trails of who accessed what data, when, and for what purpose. Retain logs per policy for compliance audits.

**6. Privacy Impact Assessments**  
Conduct Data Protection Impact Assessment (DPIA) or Privacy Impact Assessment (PIA) before deployment. Involve legal and privacy teams.

**7. Incident Response Plan**  
Define procedures for suspected breaches, including notification timelines and remediation steps.

**8. Regular Compliance Audits**  
Conduct periodic security penetration testing and privacy audits to ensure continued compliance.

**9. IRB & Patient Consent**  
For research or prospective trials, obtain Institutional Review Board (IRB) approvals and informed consent where required.

---

### 5. Optimization (5 points)

#### Method to Address Overfitting: Early Stopping with Cross-Validation & Regularization

**Why This Method:**  
Early stopping prevents the model from fitting noise after validation performance plateaus. Patient-grouped cross-validation ensures validation reflects real generalization across patients. Regularization parameters control model complexity directly.

**Implementation Steps:**

**Step 1: Patient-Grouped Cross-Validation**  
Split by patient (not encounter) to build folds, or use time-based holdout (train on earlier dates, validate on later) to mimic deployment conditions.

**Step 2: Hyperparameter Tuning**  
Tune key parameters using grid search or Bayesian optimization:
- Learning rate
- Number of trees
- Max depth / number of leaves
- Min child weight
- L1/L2 regularization (alpha, lambda)
- Subsample fraction

Use cross-validation performance (AUROC or utility metric) to select optimal settings.

**Step 3: Early Stopping Rule**  
During training, monitor validation loss or AUROC. Stop training if no improvement occurs after N rounds (e.g., 50 rounds) to prevent overfitting.

**Step 4: Subsampling & Column Sampling**  
Set subsample and colsample_bytree to <1.0 to reduce variance through bagging behavior and improve generalization.

**Step 5: Calibration & Ensembling**  
After training, calibrate probabilities using isotonic regression on holdout set. Optionally ensemble several models trained with different seeds/folds to reduce variance.

**Step 6: Post-hoc Evaluation**  
Perform robustness evaluation on later temporal holdout sets. Evaluate subgroup performance to ensure regularization didn't disproportionately harm minority groups.

**Complementary Techniques:**
- Prune features via domain knowledge and regularized models (LASSO) to remove noisy predictors
- For deep models, apply dropout and weight decay
- Monitor generalization gap (training vs validation metrics) and trigger retraining with more data if needed

---

### Recommended Next Steps

**1. Build Reproducible Prototype**  
Use de-identified EHR extracts with baseline logistic regression and LightGBM models.

**2. Silent Pilot Phase**  
Run predictions logged but not shown for 3 months to collect ground-truth labels and calibration data.

**3. Fairness Audits**  
Conduct fairness audits on pilot data and adjust sampling or model to address disparities.

**4. Develop Clinical Workflows**  
Create workflows for actioning risk scores, including resource mapping for interventions.

**5. Complete Compliance Assessments**  
Finish DPIA and security assessments. Obtain governance sign-offs before patient-facing deployment.

**6. Design Monitoring Systems**  
Build dashboards for performance, drift, and operational metrics. Schedule regular retraining cadences (quarterly or triggered by drift alerts).

---


## Part 3: Critical Thinking (20 points)

> **[TO BE FILLED]**
> 
> *This section will cover:*
> - *Ethics & Bias (10 points)*
> - *Trade-offs (10 points)*

---

## Part 4: Reflection & Workflow Diagram (10 points)

### Reflection (5 points)

#### What was the most challenging part of the workflow? Why?

The most challenging part of the AI development workflow was **data preprocessing and feature engineering**. This stage required careful consideration of multiple factors:

**Challenges Encountered:**

1. **Identifying and Handling Data Biases** - Recognizing stockout bias in the bread sales data was particularly challenging. We had to think critically about what the data truly represented versus what we needed it to represent. Missing sales due to stock unavailability don't indicate lack of demand, yet they appear as low sales in the data.

2. **Feature Engineering Decisions** - Determining which features would be most predictive required domain knowledge and experimentation. For instance, deciding whether to include day of week, weather conditions, holidays, or promotional events, and how to encode these features properly (categorical vs. numerical) was complex.

3. **Balancing Data Quality and Quantity** - We faced the dilemma of whether to discard incomplete records or impute missing values. Each approach has trade-offs that could impact model performance.

**Why This Was Challenging:**

- It required both technical skills (coding, statistical knowledge) and domain understanding (business context)
- Poor decisions at this stage compound through the entire pipeline, affecting model performance
- There's often no single "correct" answer, requiring judgment calls and iterative refinement
- The impact of preprocessing decisions isn't always immediately visible until model evaluation

---

#### How would you improve your approach with more time/resources?

With additional time and resources, I would implement the following improvements:

**1. Enhanced Data Collection**

- **Collect Additional Features:** Customer foot traffic data from sensors, competitor pricing and promotions, social media sentiment, local event information, and historical bread variety preferences
- **Extend Historical Data Range:** Gather 2-3 years of data to capture seasonal patterns, yearly trends, and anomalies

**2. Advanced Preprocessing Techniques**

- **Automated Feature Selection:** Implement feature importance analysis using SHAP values or permutation importance, validated by domain experts
- **Advanced Imputation Methods:** Use K-Nearest Neighbors (KNN) imputation or Multiple Imputation by Chained Equations (MICE) instead of simple averages

**3. Model Development Improvements**

- **Ensemble Approach:** Compare Random Forest with XGBoost, LightGBM, and LSTM for time series, then create an ensemble combining multiple algorithms
- **Automated Hyperparameter Tuning:** Implement grid search or Bayesian optimization with time series cross-validation

**4. Deployment and Monitoring Enhancements**

- **Real-Time Monitoring Dashboard:** Build dashboards showing predictions vs. actuals, performance metrics, and drift alerts
- **A/B Testing Framework:** Deploy in parallel with existing methods to compare outcomes before full rollout
- **Automated Retraining Pipeline:** Schedule regular retraining with automatic versioning and rollback capabilities

**5. Stakeholder Engagement**

- **Regular Feedback Loops:** Weekly meetings with store manager and bakery head for feedback on accuracy and usability
- **User-Friendly Interface:** Develop a mobile app or web interface for easy prediction viewing and manual corrections

**6. Ethical and Practical Considerations**

- **Bias Auditing:** Regular audits to ensure no unexpected biases develop over time
- **Explainability:** Implement LIME or SHAP tools so stakeholders understand prediction reasoning

**Resource Requirements:**

- **Technical Resources:** Cloud computing infrastructure, production-grade databases
- **Human Resources:** Data scientists, domain experts, software engineers
- **Time:** 3-6 months for comprehensive implementation and testing

---

### Workflow Diagram (5 points)

#### AI Development Workflow - Complete Process Flow

```mermaid
flowchart TD
    Start([AI DEVELOPMENT WORKFLOW]) --> Stage1
    
    Stage1[STAGE 1: PROBLEM DEFINITION] --> Stage2
    Stage2[STAGE 2: DATA COLLECTION] --> Stage3
    Stage3[STAGE 3: DATA PREPROCESSING & EDA] --> Stage4
    Stage4[STAGE 4: DATA SPLITTING] --> Stage5
    Stage5[STAGE 5: MODEL SELECTION] --> Stage6
    Stage6[STAGE 6: MODEL TRAINING & TUNING] --> Stage7
    Stage7[STAGE 7: MODEL EVALUATION] --> Decision
    
    Decision{Performance Satisfactory?} -->|NO| Stage6
    Decision -->|YES| Stage8
    
    Stage8[STAGE 8: MODEL DEPLOYMENT] --> Stage9
    Stage9[STAGE 9: MONITORING & MAINTENANCE] --> Stage10
    Stage10[STAGE 10: RETRAINING & OPTIMIZATION] --> Continuous
    Continuous[CONTINUOUS IMPROVEMENT] --> Stage9
    
    style Start fill:#e1f5ff,stroke:#0288d1,stroke-width:3px
    style Stage1 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage2 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage3 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage4 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage5 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage6 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage7 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage8 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage9 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Stage10 fill:#fff3e0,stroke:#f57c00,stroke-width:2px
    style Decision fill:#fff9c4,stroke:#f9a825,stroke-width:2px
    style Continuous fill:#e8f5e9,stroke:#43a047,stroke-width:2px
```

---

#### Detailed Workflow Stages

##### Stage 1: Problem Definition
- Define problem scope and objectives
- Identify stakeholders
- Establish success metrics (KPIs)
- Assess feasibility and constraints

##### Stage 2: Data Collection
- Identify data sources
- Gather relevant datasets
- Assess data quality and availability
- Address legal/ethical considerations

##### Stage 3: Data Preprocessing & EDA
- Clean data (handle missing values, outliers)
- Explore data patterns and distributions
- Feature engineering (create new features)
- Data transformation (normalization, encoding)
- Identify and mitigate biases

##### Stage 4: Data Splitting
- **Training Set (70%)** - Model learning
- **Validation Set (15%)** - Hyperparameter tuning
- **Test Set (15%)** - Final evaluation

##### Stage 5: Model Selection
- Choose appropriate algorithm(s)
- Train initial model
- Justify model choice based on problem type

##### Stage 6: Model Training & Tuning
- Train model on training data
- Tune hyperparameters using validation set
- Apply regularization techniques
- Prevent overfitting/underfitting

##### Stage 7: Model Evaluation
- Evaluate on test set using appropriate metrics
- Analyze confusion matrix, precision, recall, etc.
- Compare with baseline models
- Assess model interpretability

##### Stage 8: Model Deployment
- Integrate into production system
- Set up APIs and interfaces
- Ensure scalability and reliability
- Address technical challenges
- Comply with regulations

##### Stage 9: Monitoring & Maintenance
- Monitor model performance continuously
- Detect concept drift
- Track prediction accuracy
- Log errors and anomalies

##### Stage 10: Retraining & Optimization
- Retrain with new data periodically
- Update features and parameters
- Optimize for changing conditions
- Version control models

##### Continuous Improvement Cycle
- Iterate based on feedback
- Incorporate lessons learned
- Stay updated with new techniques
- Return to monitoring for ongoing improvement

---

#### Cross-Cutting Concerns Throughout All Stages

| Concern | Description |
|---------|-------------|
| **Ethics & Fairness** | Ensure the model doesn't discriminate or produce biased outcomes |
| **Privacy & Security** | Protect sensitive data and comply with data protection laws |
| **Transparency & Explainability** | Make model decisions interpretable and understandable |
| **Stakeholder Communication** | Keep all parties informed about progress and decisions |
| **Documentation** | Maintain comprehensive records of all processes and decisions |

---
