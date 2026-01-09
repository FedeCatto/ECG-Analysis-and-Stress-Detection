# ECG-Analysis-and-Stress-Detection

This project focuses on the analysis of **electrocardiographic (ECG) signals** and the classification of stress using **classical machine learning** techniques.  
The main goal is to transform raw physiological signals into a reliable binary classification between **"stress"** and **"non-stress"** states.

---

## 📌 Project Overview

The work analyzes data from **15 subjects** from a public [PhysioNet](https://physionet.org/) dataset, acquired through chest sensors with a **sampling rate of 700 Hz**.  

The analysis covers the full lifecycle of a **Data Science project**: from raw data exploration, preprocessing, class balancing, to model evaluation.

---

## 🛠️ Technical Workflow

### 1. Signal Preprocessing
To ensure data quality and reduce inter-subject variability, a cleaning pipeline was implemented:

- **Standardization:** Z-score normalization to mitigate amplitude differences between subjects.  
- **Filtering:** Band-pass filter (0.5 Hz - 40 Hz) to remove noise and baseline wander.  
- **Zero-phase filtering:** Forward-backward filtering to preserve temporal structure without phase distortions.  

### 2. Segmentation and Labeling
The continuous signal was divided into fixed-length windows:

- **Sliding Window:** 10-second windows with 50% overlap.  
- **Majority Voting:** Each window is labeled as "stress" if more than 50% of its samples are in that state, increasing robustness against noisy labels.  

### 3. Feature Extraction
**16 key features** were extracted from the signal:

- **Time Domain:** Mean, standard deviation, RMS, Hjorth parameters (activity, mobility, complexity), zero-crossing rate.  
- **Frequency Domain:** Power spectral density (Welch’s method), power in LF (0.04–0.15 Hz) and HF (0.15–0.40 Hz) bands, LF/HF ratio.  
- **Heart Rate Variability (HRV):** R-peak detection and metrics such as SDNN, RMSSD, pNN50.  

---

## 🧪 Model Training and Evaluation

- **Dataset split:** Subject-based (12 training, 3 testing) to avoid **data leakage** and evaluate generalization to new individuals.  
- **Handling class imbalance:**  
  - SMOTE (Synthetic Minority Over-sampling Technique)  
  - Undersampling (Random and NearMiss)  
  - Class-weighted SVC  
  - Bagging Classifier  

**Key results:**  
The combination of **SMOTE** and **RBF SVC** (optimized via grid search) achieved the best trade-off:

- **Macro F1 Score:** 0.67  
- **Accuracy:** 0.81  
- **Recall (Stress class):** 0.66  

---

## 📈 Conclusions and Future Work

This project demonstrates that **classical machine learning models**, supported by solid feature engineering, can effectively detect stress from ECG signals.  

Future directions could include:

- Applying **Deep Learning models** to learn directly from raw signals.  
- Integrating **multimodal approaches** with additional physiological signals.  

---
