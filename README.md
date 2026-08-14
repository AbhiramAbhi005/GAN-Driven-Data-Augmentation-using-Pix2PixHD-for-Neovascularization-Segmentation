# GAN-Driven Data Augmentation using Pix2PixHD for the Segmentation of Neovascularization

## Overview

Diabetic Retinopathy (DR) is a major retinal disorder that can progress to Proliferative Diabetic Retinopathy (PDR), where abnormal blood vessels known as neovascularization develop on the retina and around the optic disc.

Accurate localization and segmentation of neovascularization from retinal fundus images is challenging because these pathological vessels are often thin, irregular, low-contrast, and sparsely distributed. Another major challenge is the limited availability of expertly annotated medical images required for training deep learning segmentation models.

This project presents a GAN-driven data augmentation framework that integrates Pix2PixHD and U-Net to improve neovascularization segmentation in diabetic retinopathy fundus images.

The framework uses retinal vessel masks as conditional inputs to Pix2PixHD to generate realistic synthetic fundus images. These synthetic images are then combined with real training images to increase the size and diversity of the training dataset. A U-Net segmentation model is subsequently trained to localize neovascularization regions.

---

## Objectives

The main objectives of this project are:

1. To preprocess retinal fundus images for improved visualization of vascular structures.
2. To generate retinal vessel masks using image processing techniques.
3. To use Pix2PixHD for generating realistic synthetic fundus images from vessel masks.
4. To augment the training dataset using GAN-generated synthetic images.
5. To train a U-Net segmentation model for neovascularization localization.
6. To evaluate segmentation performance using Accuracy, Dice, IoU, Sensitivity, and Specificity.
7. To compare conventional U-Net training with GAN-augmented training.
8. To investigate the effectiveness of GAN-based augmentation under limited-data conditions.

---

## Methodology

The proposed framework consists of two major stages.

### Stage 1: Image Processing and Annotation

The fundus images undergo preprocessing and annotation operations including:

- Green channel extraction
- CLAHE enhancement
- Image resizing to 512 × 512
- Expert annotation using CVAT
- Ground-truth mask generation
- Retinal vessel mask extraction

### Stage 2: GAN-Based Augmentation and Segmentation

The processed vessel masks are provided as conditional inputs to Pix2PixHD to generate synthetic fundus images.

The generated synthetic images are combined with the real training images to form a GAN-augmented dataset. The augmented dataset is then used to train a U-Net segmentation model for neovascularization localization.

---

## Overall Pipeline

```text
Retinal Fundus Images
        |
        v
Green Channel Extraction
        |
        v
CLAHE Enhancement
        |
        v
Image Preprocessing
        |
        +----------------------+
        |                      |
        v                      v
Expert Annotation       Vessel Mask Extraction
        |                      |
        v                      v
Ground Truth Mask       Pix2PixHD GAN
                               |
                               v
                     Synthetic Fundus Images
                               |
                               v
                 GAN-Augmented Training Dataset
                               |
                               v
                         U-Net Segmentation
                               |
                               v
                   Neovascularization Mask
                               |
                               v
                    Performance Evaluation
```
---

## Dataset

The project uses retinal fundus images containing neovascularization associated with Proliferative Diabetic Retinopathy (PDR).

The dataset used in the study consists of:

- **105 real fundus images** used for training
- **100 synthetic fundus images** generated using Pix2PixHD
- **205 images** in the GAN-augmented training dataset
- **30 independent real fundus images** used for final testing

The test images were kept separate from the training and synthesis processes to provide an unbiased evaluation of the segmentation model.

---

## Image Preprocessing

Several preprocessing techniques were applied to improve the visibility of retinal vascular structures.

### Green Channel Extraction

The green channel of the RGB fundus image was extracted because retinal vessels generally provide better contrast in the green channel compared with the red and blue channels.

### CLAHE

Contrast Limited Adaptive Histogram Equalization (CLAHE) was applied to enhance local contrast and improve the visibility of fine vascular structures, particularly low-contrast vessels.

### Image Resizing

Images were resized to:

**512 × 512 pixels**

This provided a consistent input size for the deep learning models.

---

## Ground Truth Generation

Expert annotations were created to identify neovascularization regions in the retinal fundus images.

**CVAT (Computer Vision Annotation Tool)** was used for annotation.

The annotations were converted into binary segmentation masks representing:

- **0:** Non-neovascular region
- **1:** Neovascularization region

These masks were used as ground truth for training and evaluating the U-Net segmentation model.

---

## Vessel Mask Extraction

Retinal vessel masks were generated from the preprocessed fundus images using digital image processing techniques.

The extracted vessel structures provide the conditional information required by Pix2PixHD for generating corresponding fundus images.

This step connects the conventional image processing stage with the GAN-based data augmentation stage.

---

## Pix2PixHD-Based Data Augmentation

### Why Pix2PixHD?

Medical image datasets often contain a limited number of expert-annotated images. Training a segmentation model using only a small dataset can result in:

- Overfitting
- Poor generalization
- Limited representation of pathological variations
- Difficulty learning rare neovascularization patterns

To address this limitation, **Pix2PixHD** was used to generate additional synthetic fundus images.

### Conditional Image Generation

Pix2PixHD learns the relationship between vessel masks and retinal fundus images.

The vessel mask is provided as the conditional input, and the generator produces a corresponding synthetic fundus image.

```text
Vessel Mask
     |
     v
Pix2PixHD Generator
     |
     v
Synthetic Fundus Image
```
## Pix2PixHD Configuration

The Pix2PixHD implementation was configured for 512 × 512 retinal fundus images.

The implementation used a simplified Pix2PixHD architecture consisting of:

- Global Generator
- Multi-scale Discriminator
- L1 reconstruction loss
- Adversarial GAN loss
- Adam optimizer

The Local Enhancer component of the original Pix2PixHD architecture was omitted because the target image resolution was 512 × 512.

The main training configuration was:

| **Parameter** | **Value** |
|---|---|
| Image Resolution | 512 × 512 |
| Generator | Global Generator |
| Discriminator | Multi-scale Discriminator |
| GAN Training Epochs | 60 |
| Optimizer | Adam |
| Learning Rate | 0.0002 |
| Beta 1 | 0.5 |
| Beta 2 | 0.999 |
| L1 Loss Weight (λ) | 10.0 |
| Mask Noise (σ) | 0.05 |

The trained generator was subsequently used to produce 100 synthetic fundus images for dataset augmentation.

---
# GAN-Augmented Dataset

A total of **100 synthetic fundus images** were generated using **Pix2PixHD**.

These synthetic images were combined with the **105 real training images**, resulting in a total training dataset of **205 images**.

```text
105 Real Images
       |
       +------------------+
                          |
                          v
                 GAN-Augmented Dataset
                          ^
                          |
       +------------------+
       |
100 Synthetic Images
       |
       v
    Pix2PixHD
```

The synthetic images were used to increase the diversity of retinal appearance and provide additional examples of vascular structures without requiring additional manual annotations.

---

# U-Net Segmentation

The GAN-augmented dataset was used to train a **U-Net semantic segmentation model** for neovascularization localization.

U-Net was selected because its encoder-decoder architecture is well suited for biomedical image segmentation and can preserve fine spatial details through **skip connections**.

The model takes a preprocessed retinal fundus image as input and produces a **pixel-level segmentation mask** identifying neovascularization regions.

```text
Fundus Image
     |
     v
   U-Net
     |
     v
Predicted Segmentation Mask
     |
     v
Neovascularization Region
```

The U-Net model was trained for **20 epochs** using the GAN-augmented dataset.

The segmentation model was evaluated on **30 independent real fundus images** that were not used during training or synthetic image generation.

---

## U-Net Configuration

The segmentation model uses a U-Net encoder-decoder architecture with skip connections for preserving spatial information.

The configured U-Net contains approximately **31 million trainable parameters**.

The encoder consists of four downsampling stages with:

- 64 filters
- 128 filters
- 256 filters
- 512 filters

Each encoder block uses convolutional layers followed by Batch Normalization and ReLU activation, with 2 × 2 max pooling used for downsampling.

The model was trained using a hybrid **Binary Cross-Entropy (BCE) + Dice loss** to address the severe class imbalance between neovascular and non-neovascular pixels.

---

# Baseline and GAN-Augmented Comparison

To determine the effectiveness of Pix2PixHD-based augmentation, two U-Net models were compared.

## Baseline U-Net

The baseline model was trained using only the **105 real fundus images**.

## GAN-Augmented U-Net

The augmented model was trained using:

* **105** real fundus images
* **100** Pix2PixHD-generated synthetic images
* **Total: 205 training images**

Both models used the same:

* U-Net architecture
* Training procedure
* Loss functions
* Evaluation test set

This controlled comparison was performed to determine whether the addition of GAN-generated images improved segmentation performance.

---

# Evaluation Metrics

The segmentation models were evaluated using the following metrics:

| **Metric** | **Description** |
|---|---|
| **Accuracy** | Measures the overall percentage of correctly classified pixels. |
| **Dice Coefficient** | Measures the spatial overlap between the predicted segmentation and the ground-truth mask. |
| **Intersection over Union (IoU)** | Measures the intersection between predicted and ground-truth regions relative to their union. |
| **Sensitivity** | Measures the ability of the model to correctly detect neovascularization pixels. |
| **Specificity** | Measures the ability of the model to correctly identify non-neovascular pixels. |
| **ROC-AUC** | Measures the model's ability to distinguish between neovascular and non-neovascular pixels across different classification thresholds. |
| **Precision-Recall / Average Precision** | Evaluates performance on the minority neovascularization class, particularly under severe class imbalance. |

---

# Results

The Pix2PixHD generator produced synthetic fundus images with good structural and perceptual similarity.

The main synthesis quality results were:

| **Metric** | **Result** |
|---|---:|
| **PSNR** | 29.40 ± 1.73 dB |
| **SSIM** | 0.7948 ± 0.0217 |
| **MSE** | 81.49 ± 40.09 |
| **MAE** | 6.16 ± 1.39 |
| **LPIPS** | 0.1575 ± 0.0398 |
| **FID** | 57.45 |
These results indicate that the generated images maintained anatomical structure and realistic visual characteristics while introducing additional variation into the training data.

---

# U-Net Segmentation Results

The U-Net model trained using the GAN-augmented dataset achieved the following results on the independent test set:

| **Metric** | **Mean ± Standard Deviation** |
|---|---:|
| **Accuracy** | 0.9743 ± 0.0089 |
| **Dice Coefficient** | 0.7144 ± 0.0912 |
| **IoU** | 0.5635 ± 0.1023 |
| **Sensitivity** | 0.8370 ± 0.0734 |
| **Specificity** | 0.9819 ± 0.0048 |
| **ROC-AUC** | 0.9540 |
| **Average Precision** | 0.7534 |

The results demonstrate that the model achieved strong pixel-level classification and reliable detection of neovascularization while maintaining very high specificity.

---

# Baseline vs GAN-Augmented Performance

The comparison between the baseline U-Net and GAN-augmented U-Net demonstrated improvements across all major segmentation metrics.

| **Metric** | **Baseline U-Net** | **U-Net + Pix2PixHD** | **Relative Improvement** |
|---|---:|---:|---:|
| **Accuracy** | 0.9704 | 0.9743 | +0.4% |
| **Dice Coefficient** | 0.6733 | 0.7144 | +6.1% |
| **IoU** | 0.5127 | 0.5635 | +9.9% |
| **Sensitivity** | 0.8321 | 0.8370 | +0.6% |
| **Specificity** | 0.9784 | 0.9819 | +0.4% |

The most significant improvements were observed in **Dice and IoU**, indicating better spatial localization and boundary delineation of neovascularization regions after GAN-based augmentation.

---

# Confusion Matrix Analysis

The pixel-level confusion matrix of the GAN-augmented U-Net contained:

| **Classification** | **Count** |
|---|---:|
| **True Negatives (TN)** | 7,432,447 |
| **False Positives (FP)** | 134,927 |
| **False Negatives (FN)** | 67,181 |
| **True Positives (TP)** | 229,765 |

The results demonstrate that the model correctly classified the majority of non-neovascular pixels while detecting a substantial proportion of neovascularization pixels.

---

# Training and Convergence Analysis

The U-Net model was trained for **20 epochs** to analyze convergence and generalization.

The training and validation curves showed stable convergence, with both accuracy and loss following similar trends.

The training and validation accuracy remained close throughout training, indicating that the model did not exhibit significant overfitting.

The validation IoU and Dice scores also showed an overall increasing trend during training, demonstrating progressive improvement in spatial segmentation performance.

---

# Qualitative Results

Qualitative analysis was performed by comparing:

1. Input fundus image
2. Ground-truth neovascularization mask
3. Predicted segmentation mask
4. Overlay of the prediction on the original fundus image

The predicted segmentation masks demonstrated the ability to identify irregular neovascular structures and maintain close spatial correspondence with expert annotations.

The qualitative results also showed that the model could detect small pathological regions while maintaining relatively low false-positive predictions in surrounding healthy retinal tissue.

---

# Statistical Validation

Statistical validation was performed using **10,000 bootstrap iterations** to calculate **95% confidence intervals**.

| **Metric** | **Mean** | **95% Confidence Interval** |
|---|---:|---:|
| **Accuracy** | 0.9743 | [0.9660, 0.9827] |
| **Dice Coefficient** | 0.7144 | [0.6802, 0.7486] |
| **IoU** | 0.5634 | [0.5210, 0.6058] |
| **Sensitivity** | 0.8369 | [0.7927, 0.8811] |
| **Specificity** | 0.9819 | [0.9777, 0.9861] |

The confidence intervals demonstrate that the model maintained consistent performance across the independent test set.

---

# Clinical Significance

The results demonstrate that **Pix2PixHD-based data augmentation** can improve neovascularization segmentation when only a limited number of annotated fundus images are available.

The improvement in Dice and IoU indicates better localization of pathological regions, while the high sensitivity and specificity demonstrate the model's ability to detect neovascularization while limiting false-positive predictions.

The proposed framework can potentially support applications such as:

* Automated diabetic retinopathy screening
* Neovascularization localization
* Disease monitoring
* Clinical decision support
* Treatment planning assistance

> **Note:** The system is intended as a clinical decision-support tool rather than a replacement for ophthalmologist evaluation.

---

# Conclusion

This project demonstrated a **GAN-driven data augmentation framework combining Pix2PixHD and U-Net** for neovascularization segmentation in proliferative diabetic retinopathy.

Pix2PixHD generated **100 synthetic fundus images** from retinal vessel masks, increasing the training dataset from **105 real images to 205 images**.

The GAN-augmented U-Net achieved:

* **Dice Coefficient:** 0.7144
* **IoU:** 0.5635
* **Dice Improvement:** 6.1%
* **IoU Improvement:** 9.9%

compared with baseline U-Net training using only real images.

The results demonstrate that GAN-generated synthetic images can provide useful additional training diversity and improve segmentation performance under limited-data conditions.

---

# Future Work

Future improvements will focus on:

* Exploring advanced architectures such as **U-Net++** and transformer-based segmentation models.
* Improving segmentation of extremely faint and low-contrast neovascularization.
* Integrating fundus photography with **Optical Coherence Tomography (OCT)** for multimodal analysis.
* Performing cross-dataset validation using external datasets from different geographic regions and imaging protocols.
* Applying **Explainable AI (XAI)** techniques to improve clinical interpretability.
* Conducting prospective clinical trials using real-world screening populations.
* Evaluating clinical outcomes, referral patterns, treatment support, and cost-effectiveness.

---

# Technologies Used

| **Category** | **Technologies** |
|---|---|
| **Programming** | Python |
| **Deep Learning** | PyTorch, U-Net, Pix2PixHD |
| **Computer Vision** | OpenCV |
| **Image Processing** | Green Channel Extraction, CLAHE |
| **Annotation** | CVAT |
| **Development Environment** | Google Colab |
| **Evaluation** | Accuracy, Dice, IoU, Sensitivity, Specificity, ROC-AUC, Precision-Recall, FID, SSIM, PSNR, LPIPS |
