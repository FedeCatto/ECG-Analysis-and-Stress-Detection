# ECG Analysis & Stress Detection

> Binary stress classification from physiological signals using classical ML and HRV feature engineering

A full data science pipeline that transforms raw ECG recordings into reliable stress/non-stress predictions. The project covers every stage from signal acquisition to model evaluation — with deliberate design choices at each step to handle the real constraints of physiological data: inter-subject variability, noisy labels, class imbalance, and the risk of data leakage.

---

## Dataset

15 subjects from a public **PhysioNet** dataset, recorded via chest sensors at **700 Hz**. Binary target: `stress` vs. `non-stress`.

---

## Pipeline

### 1. Signal Preprocessing

Raw ECG signals require careful cleaning before any feature can be trusted.

- **Z-score normalisation** — removes amplitude differences between subjects, preventing the model from learning individual physiology rather than stress states
- **Band-pass filter (0.5–40 Hz)** — attenuates baseline wander and high-frequency noise while preserving the diagnostically relevant signal
- **Zero-phase (forward-backward) filtering** — eliminates phase distortion, keeping the temporal structure of cardiac events intact

### 2. Segmentation & Labelling

The continuous signal is cut into **10-second sliding windows with 50% overlap**. Each window is labelled by majority vote — assigned "stress" only if more than half its samples fall in that state. This makes labels more robust to transient noise at state boundaries.

### 3. Feature Extraction

16 features extracted per window across three domains:

**Time domain**
- Mean, standard deviation, RMS
- Hjorth parameters (activity, mobility, complexity)
- Zero-crossing rate

**Frequency domain**
- Power spectral density via Welch's method
- Band power in LF (0.04–0.15 Hz) and HF (0.15–0.40 Hz)
- LF/HF ratio — a standard autonomic nervous system marker

**Heart Rate Variability (HRV)**
- R-peak detection
- SDNN, RMSSD, pNN50

---

## Modelling

### Train/test split strategy

Split is **subject-based**: 12 subjects for training, 3 held out for testing. This is a deliberate choice — a random window-level split would leak temporal context from the same individual into both sets, inflating performance and hiding the model's true ability to generalise to unseen people.

### Handling class imbalance

Four strategies evaluated:

| Strategy | Description |
|----------|-------------|
| SMOTE | Synthetic oversampling of the minority class |
| Random undersampling | Downsample the majority class |
| NearMiss | Distance-based undersampling |
| Class-weighted SVC | Penalise misclassification of minority class at training time |

### Results

Best configuration: **SMOTE + RBF SVC** (hyperparameters tuned via grid search).

| Metric | Score |
|--------|-------|
| Accuracy | 0.81 |
| Macro F1 | 0.67 |
| Recall (stress class) | 0.66 |

Macro F1 is the primary metric here — accuracy alone is misleading when the stress class is underrepresented and the cost of a missed detection is higher than a false alarm.

---

## Key Design Decisions

A few choices that shaped the results and are worth calling out explicitly:

- **Subject-based split** prevents data leakage and gives a realistic estimate of generalisation
- **Majority voting** on window labels reduces sensitivity to noisy state transitions
- **LF/HF ratio** directly encodes autonomic nervous system dynamics — a physiologically grounded feature, not just a statistical artefact
- **Macro F1 as primary metric** correctly penalises ignoring the minority stress class

---

## Limitations & Next Steps

Classical ML with hand-crafted features delivers interpretable, lightweight models — but has a ceiling. Planned extensions:

- **Deep learning on raw signals** — CNNs or transformers that learn representations directly from waveforms, bypassing manual feature design
- **Multimodal fusion** — combine ECG with additional signals (EDA, respiration, PPG) for richer stress characterisation
- **Personalised models** — subject-adaptive calibration to reduce inter-individual variability

---

## Stack

`Python` `scikit-learn` `SciPy` `SMOTE` `HRV analysis` `signal processing` `ECG` `feature engineering` `binary classification`
