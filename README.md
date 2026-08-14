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
