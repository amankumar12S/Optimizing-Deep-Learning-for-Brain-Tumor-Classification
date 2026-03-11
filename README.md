# Optimizing Deep Learning for Brain Tumor Classification

### **A Comparative Ablation Study of Preprocessing & Augmentation Strategies using VGG16**

![Python](https://img.shields.io/badge/Python-3.10-blue)
![TensorFlow](https://img.shields.io/badge/TensorFlow-2.x-orange)

---

##  Project Overview

This project investigates how **image preprocessing and data augmentation strategies** affect the performance of deep learning models for **brain tumor classification** (Glioma, Meningioma, Pituitary).

Most prior works rely on **slice-wise random splitting**, which introduces **patient-level data leakage** and leads to inflated performance estimates.
We address this issue by enforcing a strict **Patient-Level Split**, ensuring realistic clinical generalization.

A controlled **8-experiment ablation study** was conducted using a fixed **VGG16** architecture to isolate the effects of preprocessing and augmentation.

---

##  Full Jupyter Notebook

If GitHub cannot render the full notebook due to its size, the complete ablation study (with logs, plots, and outputs) is available on Google Colab:

Link : [(https://colab.research.google.com/drive/1_LDk5Kr7Fo2emU5qR0cuDmMw01Q13K39?usp=sharing)](https://colab.research.google.com/drive/1_LDk5Kr7Fo2emU5qR0cuDmMw01Q13K39?usp=sharing )]

---

## Dataset Link : [Brain Tumor Dataset on Figshare](https://figshare.com/articles/dataset/brain_tumor_dataset/1512427)

---

##  Key Findings

1. **Preprocessing alone is insufficient** — accuracy saturates at ~88%.
2. **Aggressive (“destructive”) preprocessing harms performance** — Skull-stripping and CLAHE combined with augmentation lead to underfitting (~82% accuracy).
3. **Denoising combined with augmentation yields the highest overall accuracy**, but with important statistical nuance:

   * **94.00% Accuracy**
   * **0.991 AUC**
   * However, **McNemar’s test indicates that the improvement over augmentation alone is not statistically significant**, suggesting a global accuracy gain rather than a consistent per-sample advantage.

> **Key takeaway:** Data augmentation is the dominant factor for generalization, while denoising provides a modest average improvement but does not uniformly outperform augmentation alone on all test samples.

---

##  Methodology

### 1. Dataset & Splitting

* **Source:** Figshare Brain Tumor Dataset (Cheng et al.)
* **Data Size:** 3,064 T1-weighted contrast-enhanced MRI images from 233 patients
* **Tumor Classes:** Glioma, Meningioma, Pituitary
* **Leakage Prevention:** Custom **patient-level splitting algorithm**

  * **Training:** 70% (163 patients)
  * **Validation:** 15% (35 patients)
  * **Testing:** 15% (35 patients)
* **Result:** **Zero patient overlap** across splits

#### Duplicate Verification

A perceptual hash (`imagehash`) check identified 26 visually identical images.
All duplicates were confined **within the same split partition**, confirming **no cross-split leakage**.

---

###  Patient-Level Split Summary

| Set        | Patients | Percentage |
| ---------- | -------- | ---------- |
| Train      | 163      | 70%        |
| Validation | 35       | 15%        |
| Test       | 35       | 15%        |

 Zero patient overlap.

---

##  Model Architecture (VGG16)

* Pretrained **VGG16** (ImageNet), frozen convolutional base
* Custom classification head:

  * Flatten → Dense(512) → Dropout(0.6)
  * Dense(256) → Dropout(0.5)
  * Softmax (3 classes)
* Regularization: Dropout + EarlyStopping
* Identical architecture and hyperparameters across all experiments

---

##  Experimental Setup

**Preprocessing strategies tested:**

* Skull-Stripping (Otsu thresholding + morphological operations)
* CLAHE (contrast enhancement)
* Bilateral Filtering (edge-preserving denoising)
* Raw images (baseline)

Each configuration was evaluated **with and without data augmentation** under identical conditions.

---

##  Results Summary

| Exp | Strategy          | Augmentation | Accuracy   | AUC       | Outcome             |
| --- | ----------------- | ------------ | ---------- | --------- | ------------------- |
| 1   | Baseline          | No           | 87.92%     | 0.970     | Weak generalization |
| 2   | **Aug Only**      | Yes          | 92.16%     | 0.992     | Major improvement   |
| 3   | Skull-Stripping   | No           | 88.77%     | 0.968     | Minor change        |
| 4   | CLAHE             | No           | 88.98%     | 0.977     | Minor change        |
| 5   | Denoising         | No           | 88.14%     | 0.973     | No improvement      |
| 6   | Skull-Strip + Aug | Yes          | 81.78%     | 0.960     | Underfitting        |
| 7   | CLAHE + Aug       | Yes          | 82.63%     | 0.955     | Underfitting        |
| 8   | **Denoise + Aug** | Yes          | **94.00%** | **0.991** | Highest accuracy    |

> **Statistical note:** McNemar’s test compared the Experiments and did indicated a statistically significant difference p = 0.05

---

##  Visualizations

* Training & validation curves
* Confusion matrices
* ROC curves
* Grad-CAM visual explanations

All outputs are available in the **master Jupyter notebook** included in this repository.

---

##  Installation

```bash
pip install tensorflow opencv-python-headless scikit-learn pandas seaborn matplotlib tqdm mat73
```

---

##  Acknowledgments

Special thanks to:

* **Mr. Himangshu Pal** — Supervision, guidance, and feedback

---

##  Authors & Contributions

### **Latchan Chhetri**

 LinkedIn: [https://www.linkedin.com/in/latchan-chhetri-704380297/](https://www.linkedin.com/in/latchan-chhetri-704380297/)

**Contributions:**

* Conceptualization
* Methodology & experimental design
* Patient-level data splitting
* Preprocessing pipeline
* Model training & ablation study
* Analysis, visualization, and writing

---

### **Aman Kumar**

**Contributions:**

* Compute resource support
* Manuscript approval
* Administrative assistance

---

### **Himangshu Pal**

**Contributions:**

* Supervision
* Review & editing
* Conceptual oversight

---
