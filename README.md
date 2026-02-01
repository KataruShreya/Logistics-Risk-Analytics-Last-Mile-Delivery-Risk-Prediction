# 🚚 Last-Mile Delivery Risk Prediction using Databricks Lakehouse

## 1. Problem Definition & AI Framing

### Business Problem

Last-mile delivery operations often suffer from delays, missed pickup windows, and poor GPS tracking, leading to SLA breaches, higher costs, and customer dissatisfaction. These risks are not caused by a single factor but by a combination of operational signals.

### Objective

Build an end-to-end AI-powered system to **predict and identify high-risk last-mile deliveries** so that operations teams can proactively intervene.

### Why AI (instead of rule-based logic)?

* Delivery risk is influenced by **multiple interacting factors** (time delays, GPS issues, courier behavior)
* Static rules fail to capture **probabilistic and nuanced risk patterns**
* AI enables **risk scoring**, not just binary alerts

### ML Task Type

* **Binary classification** (High-risk vs Not high-risk)
* **Risk probability scoring** for operational decision-making

### Inputs & Outputs

**Inputs (Features):**

* Accept-to-pickup duration (minutes)
* Pickup delay beyond time window (minutes)
* Missing pickup GPS flag

**Outputs:**

* Risk probability (0–1)
* Risk bucket: LOW / MEDIUM / HIGH

### Success Metric

* ROC-AUC score to evaluate model discrimination ability

---

## 2. Data Architecture (Lakehouse Design)

This project follows the **Medallion Architecture** using Databricks Lakehouse:

**Bronze Layer**

* Raw parquet files ingested from volume storage
* Minimal transformation
* Ingestion timestamp added for auditability

**Silver Layer**

* Cleaned and standardized data
* Timestamp parsing and validation
* Feature engineering (durations, delay metrics, GPS flags)
* Initial and refined high-risk labeling

**Gold Layer**

* Aggregated analytics tables for business insights
* Final ML feature table
* Prediction output tables for dashboards and Genie

Delta Lake is used throughout to ensure **ACID transactions, schema enforcement, and scalability**.

---

## 3. Data Understanding & Feature Engineering

### Dataset Overview

The dataset represents pickup-level operational data across multiple cities, including timestamps, courier identifiers, and GPS information.

### Key Feature Engineering Steps

* Fixed missing year in timestamp columns to enable accurate parsing
* Derived **accept_to_pickup_minutes** to measure courier responsiveness
* Calculated **pickup_delay_minutes** to capture SLA violations
* Created **missing_pickup_gps** flag as an operational risk indicator

### Risk Label Creation

Initial risk labels were created using conservative thresholds. These thresholds were later refined using **distribution analysis (median, P95, P99)** to ensure realistic and business-aligned risk definitions.

This iterative approach improved label quality and model reliability.

---

## 4. Model Selection & Technical Reasoning

### Model Chosen: Logistic Regression

Logistic Regression was selected as a **strong baseline model** due to:

* High interpretability (clear feature influence)
* Stability and fast training
* Easier adoption and trust in operational environments

### Features Used

* Accept-to-pickup duration
* Pickup delay duration
* Missing GPS indicator

### Limitations & Future Scope

* Assumes linear relationships
* Future improvements may include tree-based models (Random Forest, Gradient Boosting) for capturing non-linear patterns

---

## 5. Training, Evaluation & Experiment Tracking

* Data split into train and test sets (80/20)
* Model evaluated using **ROC-AUC**
* MLflow used to log:

  * Parameters
  * Metrics
  * Model artifacts
  * Input examples

This ensures **reproducibility and traceability** of experiments.

---

## 6. AI Innovation & Insight Generation ⭐

This project goes beyond model training by **turning predictions into decisions**:

### Risk Bucketing Strategy

* **HIGH (≥ 0.8):** Immediate operational intervention
* **MEDIUM (0.5–0.8):** Monitor closely
* **LOW (< 0.5):** No action required

### Business Insights Generated

* City-level risk hotspots
* Consistently risky couriers
* Overall health of last-mile operations

### Analytics & Exploration

* SQL-based dashboards for monitoring trends
* Databricks Genie used for natural-language queries and ad-hoc insights

Predictions are persisted back into Delta Lake, enabling **continuous analytics and decision-making**.

---

## 7. Database ↔ AI Workflow

This project demonstrates a complete database-to-AI loop:

1. Data ingestion into Delta tables
2. Feature extraction from Silver/Gold layers
3. ML training using Gold feature tables
4. Predictions written back to Gold Delta tables
5. Dashboards and Genie consuming prediction outputs

This ensures a **clear, end-to-end production-style workflow**.

---

## 8. Orchestration & Governance

### Orchestration

* Databricks Jobs automate the pipeline
* Notebook dependencies ensure correct execution order

### Governance (Unity Catalog)

* Centralized catalog and schema management
* Role-based access control:

  * Data Engineers: Bronze & Silver (read/write)
  * Data Scientists: ML feature tables (read)
  * Business Analysts: Prediction tables (read-only)

This setup ensures **secure and scalable AI operations**.

---

## 9. Business Impact & Practical Use

This system enables operations teams to:

* Proactively intervene in high-risk deliveries
* Identify problematic cities and couriers
* Reduce SLA breaches and operational costs
* Make data-driven decisions instead of reactive firefighting

---

## 11. Conclusion

This project demonstrates how Databricks Lakehouse can be used to build an **end-to-end, governed, and production-aligned AI system** that delivers real business value using simple yet effective machine learning techniques.

---

📌 **Domain:** Transportation & Logistics </br>
📌 **Tools Used:** Databricks, Delta Lake, PySpark, MLflow, SQL, Databricks Genie
