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
