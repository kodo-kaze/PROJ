# Pedestrian Crowd Detection and Segmentation using Multi-Source Feature Descriptors

## 1. Introduction

The research paper **“Pedestrian Crowd Detection and Segmentation using Multi-Source Feature Descriptors”** was written by **Saleh Basalamah** and **Sultan Daud Khan** and published in the _International Journal of Advanced Computer Science and Applications (IJACSA), Volume 11, No. 1, 2020_. The paper addresses the problem of automatically detecting and segmenting crowded regions in images using computer-vision techniques.

The authors motivate the work by the increasing importance of crowd analysis for **public safety and security**. Large gatherings at sports events, festivals, concerts, carnivals, and other events can contain thousands of people. When large numbers of people gather in constrained environments, the resulting high density can contribute to crowd disasters. Surveillance cameras can provide coverage of such scenes, but relying entirely on security personnel to manually observe and identify abnormal situations is tiring and can lead to human errors. Therefore, the authors identify a need for automated systems capable of analyzing crowd scenes.

The paper specifically focuses on **crowd detection and segmentation**. According to the authors, these operations are important preprocessing steps for several crowd-analysis applications, including **crowd tracking, behavior understanding, anomaly detection, crowd counting, and density estimation**.

---

## 2. Problem Statement

The main problem addressed by the paper is the difficulty of automatically identifying **which regions of an image contain crowds and which regions belong to the background**.

Crowd detection and segmentation become particularly difficult in high-density scenes. When pedestrians are standing close together, individual people may partially or completely obscure one another. This creates severe **occlusion**. In addition, clutter within a scene can make it difficult for a computer-vision system to distinguish crowd regions from the surrounding background.

The authors also point out that many existing methods rely on assumptions that may not hold in real-world environments. Another challenge is the limited availability of suitable datasets, which makes it difficult to train some learning-based systems effectively. Crowd simulation models have therefore also been used to generate synthetic data for training and validation of computer-vision algorithms.

---

## 3. Limitations of Motion-Based Approaches

One possible way to identify a crowd is to use **motion segmentation**. If a surveillance camera records a sequence of frames, moving objects can be identified by comparing their motion across frames.

However, the authors identify important limitations with this approach.

A significant portion of a crowd may remain **stationary**. Therefore, motion-based segmentation may fail to identify stationary crowd regions. Furthermore, motion segmentation can detect movement from objects that do not belong to the crowd, producing **false positives**.

For this reason, the proposed approach does not depend on background subtraction or motion information. Instead, it uses **appearance-based low-level features** extracted directly from the image.

---

# 4. Proposed Approach

The authors propose a framework called a **multi-source feature descriptor approach**. The central idea is to extract different types of appearance and texture information from an image and combine them before classification.

The three feature sources used are:

1. **Local Binary Pattern (LBP)**
    
2. **Fourier Analysis**
    
3. **Gray-Level Co-occurrence Matrix (GLCM)**
    

The resulting features are concatenated into a feature vector and given to a **linear Support Vector Machine (SVM)** classifier. Each image cell is classified as either a **crowd** or **non-crowd** region. Finally, an **11 × 11 two-dimensional Gaussian kernel** is applied to smooth the classification output.

The overall process can be represented as:

```text
                    Input Image
                         │
                         ▼
                 Divide into Blocks
                         │
                         ▼
                  Divide into Cells
                         │
              ┌──────────┼──────────┐
              │          │          │
              ▼          ▼          ▼
             LBP      Fourier      GLCM
                      Analysis
              │          │          │
              └──────────┼──────────┘
                         ▼
                 Feature Fusion
                         │
                         ▼
               Feature Vector
                 (128 elements)
                         │
                         ▼
                  Linear SVM
                         │
                         ▼
              Crowd / Non-Crowd
                         │
                         ▼
                Gaussian Smoothing
                         │
                         ▼
              Crowd Segmentation
```

The paper describes the same general pipeline in **Figure 1 on page 3**, where training and testing images are divided into blocks and cells, features are extracted, and the resulting features are supplied to the SVM classifier.

---

# 5. Training and Testing Process

The proposed framework operates in two major stages: **training** and **testing**.

### Training stage

During training, a set of images is provided to the system. Each image is divided into a grid of cells. Low-level appearance features are then extracted from each cell.

The features obtained from the different sources are concatenated into a single feature vector. A linear classifier is subsequently trained using these feature vectors.

The paper states that the resulting feature vector has a size of **128**.

### Testing stage

During testing, a new input image goes through the same process. The image is divided into cells, and the same feature descriptors are calculated.

The extracted feature vector is passed to the already-trained classifier. The classifier generates a **confidence score for each cell**. A Gaussian kernel is then applied to smooth the final output.

Thus:

```text
TRAINING

Training Images
      ↓
Divide into Cells
      ↓
Extract LBP + Fourier + GLCM
      ↓
Combine Features
      ↓
128-element Feature Vector
      ↓
Train Linear SVM
      ↓
Learned Classifier


TESTING

Input Image
      ↓
Divide into Cells
      ↓
Extract LBP + Fourier + GLCM
      ↓
Combine Features
      ↓
Feature Vector
      ↓
Trained SVM
      ↓
Confidence Score
      ↓
Gaussian Smoothing
      ↓
Crowd Segmentation
```

---

# 6. Local Binary Pattern (LBP)

The first feature descriptor used by the authors is **Local Binary Pattern (LBP)**.

The paper describes LBP as a suitable choice for classification based on **texture and appearance**. For each pixel, the method compares its intensity with those of its neighboring pixels. The paper uses **8 neighboring pixels** with an angular quantization of 8 and a spatial resolution of 1.

The comparison produces binary values depending on whether the center pixel has lower or higher intensity relative to its neighbors. The process is applied throughout the image, producing a normalized histogram representation.

The paper then uses GLCM to obtain additional texture information from intensity relationships between pixels.

---

# 7. Gray-Level Co-occurrence Matrix (GLCM)

The second important source of information is the **Gray-Level Co-occurrence Matrix**.

GLCM is used to represent the distribution of gray levels of neighboring pixels in relation to one another. The authors adapt this type of texture information to classify image patches into **crowd and non-crowd** categories.

The GLCM is calculated using pixel pairs separated by a distance parameter and different orientations:

- 0°
    
- 45°
    
- 90°
    
- 135°
    

The resulting counts are converted into a joint conditional probability representation.

From the GLCM, the authors extract four features:

- **Entropy**
    
- **Energy**
    
- **Contrast**
    
- **Homogeneity**
    

These are the same statistical properties represented by equations in the paper.

Therefore, GLCM provides information about the **texture structure** of the image region.

---

# 8. Fourier Analysis

The third feature source is **Fourier Analysis**.

The authors observed that dense crowds viewed from a distant camera have a distinctive appearance. Due to perspective distortion, pedestrians farther from the camera may occupy only a small number of pixels. At the same time, large crowds contain repetitive structures because many pedestrians appear visually similar from a distant viewpoint.

The authors use the **Fourier Transform** to represent these repetitive structures in the frequency domain. In particular, repetitive patterns associated with pedestrian heads can produce peaks in the frequency domain.

To deal with scale differences caused by perspective distortion, the image is divided into patches. The authors assume that the density within a patch is approximately the same.

For each patch, the paper describes the following processing:

```text
Image Patch
     │
     ▼
Convert to Gradient
     │
     ▼
Fourier Transform
     │
     ▼
Low-Pass Filter
     │
     ▼
Remove Low-Amplitude Components
     │
     ▼
Inverse Fourier Transform
     │
     ▼
Non-Maxima Suppression
     │
     ▼
Statistical Features
```

The low-pass filtering step is used to remove high-frequency components associated with edges. The threshold for removing low-amplitude components is set to **0.4**. The image is then reconstructed using the inverse Fourier Transform and non-maxima suppression.

After reconstruction, the authors calculate four statistical properties:

- **Mean**
    
- **Variance**
    
- **Skewness**
    
- **Kurtosis**
    

These features describe the reconstructed image patch and contribute to the final feature representation.

---

# 9. Feature Fusion

The important aspect of the proposed method is that the three feature sources are **combined rather than used independently**.

During training, the authors divide images into **dense overlapping patches** and extract appearance features using the different feature sources. The resulting features are combined into a longer feature vector.

The general concept is:

```text
             Image Patch
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
      LBP      Fourier      GLCM
       │       Features     Features
       │          │          │
       └──────────┼──────────┘
                  ▼
          Feature Concatenation
                  │
                  ▼
            Final Feature Vector
                  │
                  ▼
               SVM
```

The purpose of combining the sources is to use different types of information about the same image region.

The paper reports that **texture and appearance features work well in high-density crowds because they capture the regular and repetitive structure of crowds**.

---

# 10. SVM Classification

After feature extraction and fusion, the resulting feature vector is supplied to a **linear Support Vector Machine classifier**.

The classifier is trained using patches representing:

- **crowd**
    
- **non-crowd**
    

During testing, the classifier produces a confidence score for each cell, indicating how the cell should be classified.

The classification is therefore performed at the **patch/cell level**, rather than attempting to detect and track every individual pedestrian.

This is one of the important characteristics of the proposed framework.

---

# 11. Gaussian Smoothing

After the SVM produces the classification output, the authors apply an **11 × 11 two-dimensional Gaussian kernel**.

The purpose described in the methodology is to **smooth the final output**.

Conceptually:

```text
SVM Cell Scores
      │
      ▼
┌───┬───┬───┬───┐
│ + │ + │ - │ - │
├───┼───┼───┼───┤
│ + │ + │ + │ - │
├───┼───┼───┼───┤
│ + │ + │ + │ + │
└───┴───┴───┴───┘
      │
      ▼
Gaussian Smoothing
      │
      ▼
Smoothed Crowd Region
```

This produces the final crowd segmentation.

---

# 12. Dataset Used

The proposed framework was evaluated using the **UCF CC 50 dataset**.

The dataset contains **50 images captured from 50 different scenes**. The paper describes substantial variation in:

- image resolution
    
- camera viewpoints
    
- crowd densities
    

The number of people represented in the images ranges from **94 persons per image to 4,543 persons per image**.

During training, the authors cropped multiple patches from the images and divided them into **crowd and non-crowd patches**. These patches were then used to train the classifier.

The paper also states that the patch size was kept at **32 × 32 pixels** throughout the experiments.

---

# 13. Experimental Evaluation

The authors evaluated the method using **ROC curves** and **Area Under the Curve (AUC)**.

They compared the proposed approach against several other techniques:

- Gaussian Mixture Model (**GMM**) for background subtraction
    
- Motion Extraction using Optical Flow (**MEOF**)
    
- **HOG + SVM**
    
- **SIFT + SVM**
    
- **Fourier Analysis**
    
- **Texture Analysis using LBP**
    
- the **proposed method**
    

The ROC curves are presented in **Figure 4 on page 4**, while the numerical AUC comparison is provided in Table I on page 5.

The reported AUC values are:

|Method|AUC|
|---|--:|
|GMM|0.27|
|MEOF|0.15|
|HOG + SVM|0.45|
|SIFT + SVM|0.56|
|Fourier Analysis|0.37|
|Texture Analysis (LBP)|0.58|
|**Proposed Method**|**0.63**|

The proposed approach achieved the highest AUC among the methods listed in the paper.

---

# 14. Segmentation Results

The paper also provides qualitative segmentation results.

**Figure 5 on page 7** presents several crowd scenes with the predicted segmentation mask overlaid on the original images. In these results:

- **green** represents the background/non-crowd region
    
- **pink** represents the detected crowded region
    

The examples cover different crowd scenes and demonstrate that the proposed framework can identify crowded areas within complex images.

The authors report that the proposed framework can effectively discriminate between crowd and non-crowd regions and precisely segment crowded areas. They also report a small number of false positives.

One source of false positives identified by the authors is **leafy areas**, which the framework sometimes treats as crowded regions.

---

# 15. Comparison with Existing Methods

The paper's related-work section explains that previous crowd-related research has concentrated heavily on:

- crowd counting
    
- density estimation
    
- tracking
    
- anomaly detection
    
- motion-flow characterization
    
- crowd segmentation
    
- individual or group detection
    

The authors also discuss previous work on identifying specific crowd behaviors, including behaviors described as **blocking, lane, bottleneck, ring/arch, and fountainhead**.

The paper also discusses the use of **Convolutional Neural Networks (CNNs)** for crowd behavior analysis. However, the authors state that CNN-based approaches had not achieved the same level of performance seen in image classification and object detection, citing issues such as limited or noisy datasets, variations in motion, viewpoint and scale, and difficulties exploiting temporal information between consecutive video frames.

The proposed method instead uses low-level appearance features and does not require pedestrian detection or tracking. The authors therefore state that the approach can be applied to both **low-density and high-density crowds**.

---

# 16. Main Contributions Claimed by the Authors

The authors summarize several contributions of their approach.

First, the method does **not require pedestrian detection or tracking**. Instead, it uses low-level appearance features.

Second, it performs detection from a **single image rather than requiring an entire video sequence**, which the authors state reduces computational cost.

Third, the approach does **not use background subtraction or motion information**.

Fourth, because it relies on appearance features rather than pedestrian detection and tracking, the authors state that it can be applied to both low- and high-density crowd scenes.

Fifth, the method focuses the later crowd-analysis process on the **Region of Interest (ROI)** corresponding to the crowded area.

Finally, the experiments on different scenes showed that the proposed framework could localize crowded regions with the reported performance.

---

# 17. Complete Methodology at a Glance

The entire research method can therefore be summarized as:

```text
                         ┌─────────────────┐
                         │   Input Image   │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Divide Image    │
                         │ into Blocks     │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Divide Blocks   │
                         │ into Cells      │
                         └────────┬────────┘
                                  │
                  ┌───────────────┼───────────────┐
                  │               │               │
                  ▼               ▼               ▼
             ┌────────┐     ┌──────────┐    ┌────────┐
             │  LBP   │     │ Fourier  │    │  GLCM  │
             │Features│     │ Analysis │    │Features│
             └────┬───┘     └─────┬────┘    └───┬────┘
                  │               │               │
                  └───────────────┼───────────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Feature Fusion  │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ 128-Dimensional │
                         │ Feature Vector  │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │  Linear SVM     │
                         │   Classifier    │
                         └────────┬────────┘
                                  │
                                  ▼
                     ┌────────────────────────┐
                     │ Crowd / Non-Crowd     │
                     │ Confidence for Cells  │
                     └───────────┬────────────┘
                                 │
                                 ▼
                         ┌─────────────────┐
                         │ Gaussian Kernel │
                         │   11 × 11       │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │ Final Crowd     │
                         │ Segmentation    │
                         └─────────────────┘
```

The paper's figures illustrate this process visually: **Figure 1** shows the training/testing pipeline, **Figure 2** shows feature extraction and concatenation for image blocks, **Figure 3** shows the crowd and non-crowd patches used for training, **Figure 4** presents ROC curves, and **Figure 5** shows the final segmentation results.

---

# 18. Conclusion of the Paper

The authors conclude that they have proposed a method for **detecting and segmenting crowds using low-level appearance features**. The framework was tested on challenging images containing substantial variations in **crowd density, viewpoint, scale, and pedestrian appearance**. According to the reported experimental results, the method was able to detect and segment crowded regions in these scenes.

The authors state that their future work is to integrate the proposed framework with different **crowd-tracking and crowd-behavior-understanding applications**.

The paper also acknowledges support from **NVIDIA Corporation**, which provided a **Titan Xp GPU** for the research.

## In short

The research proposes a **single-image crowd detection and segmentation framework** that avoids dependence on motion, background subtraction, pedestrian detection, and tracking. Instead, it divides an image into regions, extracts **LBP, Fourier, and GLCM features**, combines them into a **128-element feature vector**, and uses a **linear SVM** to classify regions as crowd or non-crowd. The resulting classification is smoothed using an **11 × 11 Gaussian kernel**. Evaluated on the **UCF CC 50** dataset, the proposed method achieved a reported **AUC of 0.63**, higher than the comparison methods included in the paper.