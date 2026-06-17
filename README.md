# A Dual-Channel Deep Learning Model for Prokaryotic sgRNA Cleavage Activity Prediction

> **A Prokaryotic sgRNA Cleavage Activity Prediction Model Integrating Local Multi-Scale Convolution and Global RNA Semantic Representations.**

CRISPR-FMC is a robust, dual-channel deep learning framework designed to predict the cleavage activity of long-sequence (43-nt) sgRNAs in prokaryotic CRISPR-Cas systems (Cas9, eSpCas9, and knoRecA_Cas9). By integrating local sequence specificities via multi-scale CNNs and global RNA folding semantics via the RNA-FM foundation model, CRISPR-FMC efficiently screens out low-activity sgRNAs to reduce high-throughput experimental costs.

---

## 🌟 Key Features

* **Dual-Feature Encoding:** Combines continuous nucleotide embeddings with pre-trained RNA foundation model (RNA-FM) representations.
* **Multi-Scale Convolution:** Captures local binding motifs using parallel 1D-CNNs with distinct receptive fields (K=3, 5, 7).
* **Biological Interpretability:** Uncovers the dependency on the PAM-proximal seed region and nonlinear synergistic epistasis within the core target sequence using DeepSHAP and pairwise masking.
* **Ensemble Inference:** Utilizes a soft-voting mechanism across 5 independently trained models to minimize prediction variance and enhance generalization against intrinsic biological noise.

---

## 📐 Model Architecture

The model architecture consists of two parallel feature extraction branches that are concatenated before the final classification head.

### 1. Local Sequence Branch (Multi-Scale CNN)
The input sequence is mapped from a discrete one-hot space to a continuous 16-dimensional embedding space. To capture distinct local motif lengths, the model employs three parallel 1D-Convolutional Neural Networks with kernel sizes of 3, 5, and 7. The outputs are processed through max and average pooling layers and flattened to form the comprehensive local feature representation.

### 2. Global Semantic Branch (RNA-FM)
The sequence is processed through the pre-trained RNA-FM foundation model to extract 640-dimensional high-level semantic features. These features are then passed through an information bottleneck (a Dense layer with 64 units) followed by Batch Normalization and Dropout. This compression filters out redundant background noise and retains the core folding and evolutionary semantics.

### 3. Fusion & Optimization
The local sequence features and global semantic features are concatenated and fed into a Multi-Layer Perceptron (MLP) classification head to yield the final predicted probability. 
* **Loss Function:** The network is optimized using standard Binary Cross-Entropy (BCE) loss combined with L2 regularization to prevent overfitting.
* **Inference Strategy:** During inference, a soft-voting ensemble strategy is applied. The final score is the arithmetic mean of the predicted probabilities from 5 independently trained sub-models, ensuring robust performance.

---

## 📊 Performance Benchmark

Evaluated on independent test sets (split 8:2), CRISPR-FMC demonstrates superior discriminative ability compared to traditional machine learning and single-channel deep learning baselines.

| Model / Dataset | Cas9 (AUC) | eSpCas9 (AUC) | knoRecA_Cas9 (AUC) |
| :--- | :--- | :--- | :--- |
| GBDT | 0.7312 | 0.7782 | 0.6264 |
| LightGBM | 0.7674 | 0.8302 | 0.6403 |
| XGBoost | 0.7651 | 0.8352 | 0.6281 |
| RNASwan-seq | 0.7643 | 0.8392 | 0.6307 |
| **CRISPR-FMC (Ours)** | **0.7951** | **0.8582** | **0.6682** |

---

## 🛠️ Installation & Setup

### Prerequisites
* Python >= 3.8
* PyTorch >= 1.12.0
* TensorFlow >= 2.0 (if using Keras backend)
* [RNA-FM](https://github.com/ml4bio/RNA-FM) (Pre-trained weights required)

### Clone the Repository
```bash
git clone [https://github.com/YourUsername/CRISPR-FMC.git](https://github.com/YourUsername/CRISPR-FMC.git)
cd CRISPR-FMC
pip install -r requirements.txt
