# Neuroscience-Informed Analysis of Model Behavior

> This document interprets the segmentation model's predictions, errors, and learned features through the lens of tumor biology and neuroanatomy. The goal is to bridge the gap between what the model *does* and what it *means* clinically.

## 1. Biological Context of Tumor Sub-Regions

### Enhancing Tumor (ET)
*To be completed after training — will analyze why ET boundaries are the hardest to segment, relating to blood-brain barrier dynamics and angiogenesis patterns in high-grade gliomas.*

### Necrotic Core
*To be completed — will explore the relationship between necrosis extent and model confidence, and how necrotic heterogeneity in GBM affects predictions.*

### Peritumoral Edema
*To be completed — will examine the fundamental biological ambiguity of the edema boundary, where infiltrative tumor cells create a gradient rather than a boundary.*

## 2. Modality-Specific Attention Analysis

*Using Grad-CAM to determine which MRI sequences the model attends to for each sub-region, and whether this aligns with established radiology:*
- T1ce → expected primary signal for enhancing tumor
- FLAIR → expected primary signal for edema
- T2 → complementary information for fluid-filled regions

## 3. Failure Mode Analysis

*Neuroanatomical categorization of cases where the model fails:*
- Periventricular tumors
- Tumors with significant midline shift
- Post-surgical cavities mimicking necrosis
- Low-grade gliomas with minimal enhancement

## 4. Clinical Implications

*Discussion of how segmentation accuracy maps to surgical decision-making — what error tolerance is acceptable for different tumor regions and surgical approaches.*
