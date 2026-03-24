# 🧠 Brain Tumor Segmentation from MRI using Deep Learning

> A deep learning pipeline for automated brain tumor segmentation from multi-modal MRI scans, combining state-of-the-art computer vision with neuroscience-informed analysis.

[![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)](https://python.org)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-ee4c2c.svg)](https://pytorch.org)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

---

## Overview

Brain tumors are among the most challenging pathologies in neurosurgery. Accurate segmentation of tumor regions from MRI is critical for surgical planning, treatment monitoring, and prognostic assessment — yet manual segmentation is time-consuming and subject to inter-rater variability.

This project implements a **U-Net-based segmentation pipeline** trained on the [BraTS (Brain Tumor Segmentation) Challenge](https://www.synapse.org/brats) dataset, capable of delineating three clinically meaningful tumor sub-regions:

- **Enhancing Tumor (ET)** — the active, contrast-enhancing core visible on T1-weighted post-gadolinium MRI
- **Tumor Core (TC)** — includes the enhancing tumor, necrotic core, and non-enhancing solid tumor
- **Whole Tumor (WT)** — the complete tumor extent including peritumoral edema

### Why These Sub-Regions Matter (A Neuroscience Perspective)

From a neurosurgical standpoint, each sub-region carries distinct clinical significance:

- **Enhancing tumor** reflects regions of blood-brain barrier breakdown, indicating active angiogenesis and high-grade malignancy. These are typically the primary targets for maximal safe resection.
- **The necrotic core** represents tissue that has outgrown its blood supply — a hallmark of glioblastoma (WHO Grade IV) and a key factor in prognosis.
- **Peritumoral edema** (FLAIR-hyperintense regions) often contains infiltrative tumor cells in high-grade gliomas, making the boundary between "tumor" and "brain" biologically ambiguous — one of the fundamental challenges in neurosurgical oncology.

Understanding these biological distinctions is what separates a meaningful segmentation model from a purely technical exercise.

---

## Features

- **Multi-modal MRI input**: Processes T1, T1-contrast enhanced (T1ce), T2, and FLAIR sequences simultaneously
- **U-Net architecture** with ResNet34 encoder (transfer learning from ImageNet)
- **Custom loss functions**: Combined Dice + Focal loss optimized for class imbalance
- **Comprehensive evaluation**: Dice coefficient, Hausdorff distance, sensitivity, specificity
- **Model interpretability**: Grad-CAM visualizations showing which MRI regions drive predictions
- **Full training pipeline**: Data loading, augmentation, training, validation, and inference
- **Neuroscience-informed analysis**: Biological context for model behavior and failure modes

---

## Project Structure

```
brain-tumor-segmentation/
│
├── README.md
├── requirements.txt
├── setup.py
├── .gitignore
│
├── configs/
│   └── config.yaml              # Hyperparameters, paths, training settings
│
├── data/
│   ├── raw/                     # Raw BraTS data (not tracked by git)
│   └── processed/               # Preprocessed numpy arrays
│
├── src/
│   ├── __init__.py
│   ├── dataset.py               # Custom PyTorch Dataset for BraTS MRI data
│   ├── model.py                 # U-Net architecture definition
│   ├── losses.py                # Dice loss, focal loss, combined loss
│   ├── metrics.py               # Dice coefficient, Hausdorff distance, etc.
│   ├── train.py                 # Training loop with validation
│   ├── predict.py               # Inference on new MRI scans
│   ├── transforms.py            # Data augmentation pipeline
│   └── visualization.py         # Grad-CAM, overlay plots, metric dashboards
│
├── notebooks/
│   ├── 01_data_exploration.ipynb      # EDA: visualize MRI modalities & labels
│   ├── 02_preprocessing.ipynb         # Normalization, cropping, patching
│   ├── 03_training_experiments.ipynb   # Training runs & hyperparameter tuning
│   └── 04_results_analysis.ipynb      # Final evaluation & neuroscience discussion
│
├── results/
│   ├── figures/                 # Generated plots and visualizations
│   ├── models/                  # Saved model checkpoints
│   └── logs/                    # TensorBoard training logs
│
└── docs/
    └── neuroscience_analysis.md # Biological interpretation of model behavior
```

---

## Getting Started

### Prerequisites

- Python 3.10+
- CUDA-capable GPU (recommended: 8GB+ VRAM)
- ~20GB disk space for BraTS dataset

### Installation

```bash
git clone https://github.com/YOUR_USERNAME/brain-tumor-segmentation.git
cd brain-tumor-segmentation
pip install -r requirements.txt
```

### Dataset

1. Register for the [BraTS Challenge](https://www.synapse.org/brats) on Synapse
2. Download the training dataset
3. Place files in `data/raw/`

```bash
python src/dataset.py --preprocess --data_dir data/raw/ --output_dir data/processed/
```

### Training

```bash
python src/train.py --config configs/config.yaml
```

Monitor training with TensorBoard:
```bash
tensorboard --logdir results/logs/
```

### Inference

```bash
python src/predict.py --model results/models/best_model.pth --input path/to/mri_scan/
```

---

## Results

> *Results will be updated as training experiments are completed.*

| Tumor Region     | Dice Score | Hausdorff95 (mm) | Sensitivity | Specificity |
|------------------|-----------|-------------------|-------------|-------------|
| Whole Tumor      | —         | —                 | —           | —           |
| Tumor Core       | —         | —                 | —           | —           |
| Enhancing Tumor  | —         | —                 | —           | —           |

---

## Neuroscience-Informed Analysis

One of the unique aspects of this project is interpreting model behavior through a neuroscience lens. Key questions explored in `docs/neuroscience_analysis.md`:

- **Why does the model struggle with enhancing tumor boundaries?** — The biological reality of infiltrative glioma growth means there is no "true" boundary; tumor cells extend beyond contrast enhancement.
- **What MRI modalities does the model rely on most?** — Grad-CAM analysis reveals modality-specific attention patterns that align with known radiological signatures (e.g., FLAIR for edema, T1ce for enhancement).
- **How do different tumor grades affect segmentation accuracy?** — WHO grade correlates with tumor heterogeneity, directly impacting model performance.
- **Clinical failure modes** — Analysis of cases where the model fails and the neuroanatomical reasons why (e.g., tumors near the ventricles, midline shift, post-surgical cavities).

---

## Technical Deep Dive

### Architecture
The model uses a **U-Net** with a **ResNet34 encoder** pretrained on ImageNet. The encoder-decoder structure with skip connections is particularly well-suited for medical image segmentation because it preserves fine spatial details needed for precise tumor boundary delineation while leveraging deep semantic features for region classification.

### Loss Function
Standard cross-entropy loss fails for tumor segmentation due to extreme class imbalance (tumor voxels represent <2% of the brain volume). This project uses a combined **Dice Loss + Focal Loss**:
- **Dice Loss** directly optimizes the overlap metric we care about
- **Focal Loss** down-weights easy-to-classify background voxels, focusing learning on the hard boundary cases

### Data Augmentation
Medical imaging datasets are small. The augmentation pipeline includes random rotations, elastic deformations, intensity shifts, and random cropping — all applied consistently across the four MRI modalities to maintain spatial alignment.

---

## Roadmap

- [x] Project structure and documentation
- [ ] Data loading and preprocessing pipeline
- [ ] U-Net model implementation
- [ ] Training pipeline with Dice + Focal loss
- [ ] Evaluation metrics and visualization
- [ ] Grad-CAM interpretability analysis
- [ ] Neuroscience analysis write-up
- [ ] Hyperparameter optimization
- [ ] Model comparison (U-Net vs. Attention U-Net vs. nnU-Net)

---

## References

- Ronneberger, O., Fischer, P., & Brox, T. (2015). U-Net: Convolutional Networks for Biomedical Image Segmentation.
- Menze, B. H., et al. (2015). The Multimodal Brain Tumor Image Segmentation Benchmark (BRATS).
- Isensee, F., et al. (2021). nnU-Net: a self-configuring method for deep learning-based biomedical image segmentation.

---

## License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.

---

## Author

**Julian Munoz** — CS (AI/ML) · Neuroscience · Aspiring Neurosurgeon

*Building at the intersection of artificial intelligence and neurosurgery.*
