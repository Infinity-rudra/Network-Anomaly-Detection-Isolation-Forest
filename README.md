# Network Anomaly Detection Using Isolation Forest

### A Dataset-Based Machine Learning Study Using CSE-CIC-IDS2018

## Overview

This research explores the application of the **Isolation Forest machine learning algorithm** for detecting anomalous network traffic using the CSE-CIC-IDS2018 dataset.

The model is trained using benign network traffic and evaluated against benign and attack traffic to investigate its ability to identify anomalies. The study focuses on threshold calibration, detection performance, attack-wise evaluation, and the trade-off between attack detection and false alarms.

This is an offline, dataset-based experimental study. No live attacks were generated, and no real cloud environment was deployed.

---

## Research Objectives

- Develop a baseline network anomaly detection model using Isolation Forest.
- Train the model using benign network traffic.
- Evaluate its ability to distinguish benign traffic from attack traffic.
- Calibrate the anomaly detection threshold using benign validation data.
- Analyze the effect of different thresholds on detection performance.
- Evaluate detection rates across different attack categories.
- Examine anomaly score distributions and classification errors.

---

## Dataset

**Dataset:** CSE-CIC-IDS2018

The study uses network traffic data from the following two files:

- `Friday-16-02-2018_TrafficForML_CICFlowMeter.csv`
- `Wednesday-21-02-2018_TrafficForML_CICFlowMeter.csv`

The dataset contains benign traffic and multiple attack categories.

### Attack Categories Evaluated

- DoS attacks-SlowHTTPTest
- DDoS attack-HOIC
- DDoS attack-LOIC-UDP

The dataset was used for offline model training and evaluation.

---

## Methodology

The experimental workflow consisted of the following steps:

### 1. Data Preparation

- Combined the selected dataset files.
- Removed records with missing or malformed labels.
- Removed exact duplicate records.
- Separated labels from input features.
- Examined and removed constant features.
- Processed numerical features and protocol information.
- Prepared the feature matrix for model training.

### 2. Binary Classification Setup

The original traffic labels were converted into binary labels:

| Label | Meaning |
|---|---|
| 0 | Benign Traffic |
| 1 | Attack Traffic |

The original attack categories were retained for attack-wise evaluation.

### 3. Data Splitting

The dataset was divided into training, validation, and testing subsets.

| Subset | Samples | Description |
|---|---:|---|
| Training | 1,379 | Benign samples used for training |
| Validation | 460 | Benign samples used for threshold calibration |
| Testing | 7,218 | Benign and attack samples used for final evaluation |

The model was trained exclusively on benign traffic. All attack samples were reserved for testing.

### 4. Model Training

The Isolation Forest algorithm was used for anomaly detection.

Model configuration:

| Parameter | Value |
|---|---|
| Algorithm | Isolation Forest |
| Number of Estimators | 200 |
| Max Samples | Auto |
| Contamination | Auto |
| Random State | 42 |
| Training Data | Benign traffic only |

### 5. Threshold Calibration

The model's anomaly scores were calculated on the benign validation set.

Different thresholds were evaluated based on target validation False Positive Rates:

- 1%
- 3%
- 5%
- 7%
- 10%
- 15%
- 20%

The 10% target validation FPR threshold was selected as the primary operating point for the main evaluation.

**Selected Threshold:** `-0.500511`

Samples with anomaly scores below the selected threshold were classified as anomalies.

---

## Experimental Analysis

The study included the following analyses:

### Experiment 1 — Baseline Isolation Forest

Evaluated the baseline model using the initial threshold calibrated at a 5% target validation FPR.

### Experiment 2 — Threshold Calibration

Evaluated a threshold calibrated at a 10% target validation FPR to examine the effect on attack detection performance.

### Experiment 3 — Threshold Sensitivity Analysis

Compared model performance at multiple threshold settings to analyze the trade-off between attack detection and false positives.

### Experiment 4 — Attack-Wise Detection Analysis

Evaluated detection performance separately for:

- DoS attacks-SlowHTTPTest
- DDoS attack-HOIC
- DDoS attack-LOIC-UDP

### Experiment 5 — Anomaly Score Distribution Analysis

Examined anomaly score distributions across benign traffic and different attack categories.

### Experiment 6 — False Positive and False Negative Analysis

Analyzed correctly and incorrectly classified samples using the confusion matrix.

### Experiment 7 — Final Performance Comparison

Consolidated results from the baseline and threshold sensitivity analyses into a comparative table.

Experiment 7 is a results consolidation step rather than a separate model experiment.

---

## Results

### 1. Baseline and Selected Operating Point

| Metric | Baseline (5% Target Validation FPR) | Selected (10% Target Validation FPR) |
|---|---:|---:|
| Threshold | -0.567216 | -0.500511 |
| Test FPR | 3.70% | 7.61% |
| Precision | 98.47% | 98.86% |
| Recall | 16.19% | 44.88% |
| F1-Score | 0.2781 | 0.6173 |
| ROC-AUC | 0.9024 | 0.9024 |

The selected threshold improved recall and F1-score compared with the baseline while increasing the test False Positive Rate.

### 2. Threshold Sensitivity Analysis

| Target Validation FPR | Threshold | Test FPR | Precision | Recall | F1-Score |
|---|---:|---:|---:|---:|---:|
| 1% | -0.618601 | 1.09% | 0.0000 | 0.0000 | 0.0000 |
| 3% | -0.586267 | 3.04% | 0.9874 | 0.1619 | 0.2782 |
| 5% | -0.567216 | 3.70% | 0.9847 | 0.1619 | 0.2781 |
| 7% | -0.533062 | 5.00% | 0.9794 | 0.1619 | 0.2778 |
| 10% | -0.500511 | 7.61% | 0.9886 | 0.4488 | 0.6173 |
| 15% | -0.481320 | 11.52% | 0.9885 | 0.6727 | 0.8006 |
| 20% | -0.437319 | 19.35% | 0.9847 | 0.8458 | 0.9100 |

The sensitivity analysis indicates that increasing the threshold resulted in higher attack recall but also increased the test False Positive Rate.

The 20% target validation FPR setting achieved the highest F1-score among the evaluated thresholds. However, the primary operating point remained the threshold selected using the validation set.

### 3. Attack-Wise Detection Results

At the selected threshold:

| Attack Category | Total Samples | Detected | Missed | Detection Rate |
|---|---:|---:|---:|---:|
| DoS attacks-SlowHTTPTest | 2,865 | 1,476 | 1,389 | 51.52% |
| DDoS attack-HOIC | 2,163 | 1,094 | 1,069 | 50.58% |
| DDoS attack-LOIC-UDP | 1,730 | 463 | 1,267 | 26.76% |

The detection performance varied across attack categories. SlowHTTPTest and HOIC achieved approximately 51% detection rates, while LOIC-UDP showed a lower detection rate at the selected operating point.

### 4. Confusion Matrix

At the selected threshold:

| | Predicted Benign | Predicted Attack |
|---|---:|---:|
| Actual Benign | 425 | 35 |
| Actual Attack | 3,725 | 3,033 |

### Error Analysis

| Metric | Value |
|---|---:|
| True Negatives | 425 |
| False Positives | 35 |
| False Negatives | 3,725 |
| True Positives | 3,033 |
| False Positive Rate | 7.61% |
| False Negative Rate | 55.12% |

The model correctly identified most benign samples, but it missed a substantial proportion of attack samples at the selected threshold.

---

## Key Findings

- The Isolation Forest model achieved a ROC-AUC of **0.9024**.
- The selected threshold achieved a precision of **98.86%**.
- The attack recall at the selected threshold was **44.88%**.
- Threshold adjustment substantially affected recall and F1-score.
- Higher attack detection rates were accompanied by higher false-positive rates.
- Detection performance varied across attack categories.
- LOIC-UDP showed comparatively lower detection performance at the selected threshold.

---

## Limitations

- The study was conducted using a publicly available dataset.
- No live attacks were generated.
- No real cloud environment was deployed.
- The model was evaluated offline.
- The model was trained using benign samples only.
- Threshold calibration was performed using benign validation samples; attack recall could not be optimized using the validation set.
- The evaluation used selected files and attack categories from CSE-CIC-IDS2018.
- Exact duplicate removal may affect the representation of repeated traffic observations.
- The random data split does not establish generalization to other days, environments, or networks.
- The observed results should not be interpreted as real-world cloud security performance.

---

## Conclusion

This study evaluated Isolation Forest for dataset-based network anomaly detection using CSE-CIC-IDS2018 traffic.

The model was trained exclusively on benign traffic and evaluated against benign and attack samples. Threshold calibration and sensitivity analysis demonstrated that the detection threshold significantly affected attack recall and false-positive rates.

At the selected operating point, the model achieved high precision but moderate recall, highlighting the challenge of detecting all attack traffic while maintaining a manageable false-alarm rate.

The study provides a practical baseline for understanding unsupervised network anomaly detection and its evaluation using machine learning.

---

## Future Scope

Potential future directions include:

- Evaluating the model on additional days of network traffic.
- Investigating other anomaly detection algorithms.
- Exploring feature selection and feature engineering.
- Evaluating the model on additional datasets.
- Investigating methods to improve detection of under-detected attack categories.
- Studying deployment considerations for cloud-based network monitoring environments.

These directions are proposed as future work and were not implemented in the current study.

---

## Technologies Used

- Python
- Google Colab
- Pandas
- NumPy
- Scikit-learn
- Matplotlib
- Seaborn

---

## Research Scope

This project is an offline, dataset-based experimental study of machine learning for network anomaly detection.

It does not represent a deployed cloud security system, live intrusion detection service, attack mitigation system, or real-world attack experiment.

---

## Author

**Rudra Nayak**

Bachelor of Arts (Honours) in Computer Applications  
Dyal Singh Evening College, University of Delhi  
Department of Computer Science  
New Delhi, India

---

## Disclaimer

This project is intended for academic research and educational purposes. The experiments use publicly available network traffic data and do not involve live attack generation or deployment in a real cloud environment.
