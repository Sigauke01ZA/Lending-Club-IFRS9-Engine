# Model Validation Report: Lending Club IFRS 9 PD & ECL Engine

**Document Control**
*   **Model Name:** Lending Club IFRS 9 Probability of Default (PD) & Expected Credit Loss (ECL) Engine
*   **Model Owner:** Raymond Sibanda (Quantitative Analyst)
*   **Version:** 1.0 (Production)
*   **Status:** Approved for Portfolio

---

## 1. Executive Summary
This report documents the independent validation of the Lending Club IFRS 9 Engine. The model is designed to predict the 12-Month Probability of Default (PD) for unsecured consumer loans and translate this into Expected Credit Loss (ECL) under IFRS 9. 

**Validation Conclusion:** The model demonstrates strong discriminatory power (Gini: 0.4144) and excellent probability calibration. The business threshold optimization proves the model can save the bank significant capital. **The model is approved for deployment**, subject to the limitations noted in Section 6.

---

## 2. Data Validation & Preparation
*   **Data Source:** Lending Club loan-level data (2007–2018).
*   **Sampling:** A stratified sample of 150,000 loans was extracted. Stratification was applied by *Origination Year* and *Target* to ensure the sample preserved historical vintage bad rates.
*   **Data Quality:** 100% of critical financial variables (FICO, DTI, Income) were present. Missing `emp_length` values (approx. 6%) were imputed using the median to preserve volume.
*   **Audit Trail:** A full data cleaning audit trail is maintained in `data_cleaning_audit_log.csv`.

---

## 3. Methodology Assessment
*   **Champion Model:** Logistic Regression. Chosen for interpretability and regulatory acceptance.
*   **Challenger Model:** XGBoost. Chosen to capture complex, non-linear interactions between macroeconomic and behavioral variables.
*   **Calibration:** The raw XGBoost output exhibited over-confidence (inflated PDs). **Platt Scaling (Sigmoid Calibration)** was applied to align predicted probabilities with empirical default rates, preventing over-provisioning.
*   **Threshold Optimization:** A Cost Matrix was applied assuming a False Negative (missed default) costs the bank R200,000 and a False Positive (lost profit) costs R50,000. The mathematical optimum was found at a **2.0% strict risk appetite threshold**.

---

## 4. Performance & Calibration Results
*   **Discriminatory Power:** The Challenger XGBoost model achieved an AUC of 0.707 (Gini 0.4144), outperforming the Logistic baseline.
*   **Calibration:** The Reliability Diagram confirms the calibrated probabilities track the perfect 45-degree line, proving the PDs are mathematically sound for ECL calculation.
*   **Confusion Matrix (2.0% Threshold):** The model successfully captures **93% of actual defaults** (Recall), effectively sacrificing high precision (24%) to minimize the much more expensive False Negatives.

---

## 5. IFRS 9 ECL Results (Test Set)
*   **Total Exposure at Default (EAD):** R 649,919,100.00
*   **Total Expected Credit Loss (ECL):** R 15,473,827.81
*   **Provision Coverage Ratio:** **2.38%**

---

## 6. Limitations & Assumptions
1.  **Static LGD:** The ECL engine assumes a constant historical LGD of 93.72%. In a production environment, a dynamic LGD model (factoring in workout costs and recovery timelines) should be integrated.
2.  **Macroeconomic Overlays:** While the base model is strong, it does not include forward-looking macroeconomic scenarios (GDP, Unemployment). A post-model adjustment (PMA) is required to satisfy full IFRS 9 Stage 2 requirements.
3.  **Dataset Vintage:** The model is trained on 2007–2018 data, which includes the 2008 crisis. Performance should be monitored for Concept Drift as consumer behavior shifts in a post-COVID high-inflation environment.

---
**Sign-off:** Raymond Sibanda | [LinkedIn](https://www.linkedin.com/in/raymond-sibanda)
