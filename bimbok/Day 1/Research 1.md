The pedestrian crowd problem involves safety risks, movement slowdowns, and bottlenecks that happen when too many people or social groups move together in tight urban spaces, transit hubs, or during evacuations



# [[void 1]]
## Pedestrian crowd dynamics in merging sections: Revisiting the “faster-is-slower” phenomenon

https://www.sciencedirect.com/science/article/abs/pii/S0378437117308956

- **Premise vs. Reality:** The classic _“Faster-is-Slower” (FIS)_ theory claims higher escape speed causes clogging and delays, but high-density human experiments ($150$ subjects) showed **"Faster-is-Faster" (FIF)** in merging corridors.
- **Geometry Impact:** While corridor merging angles affect flow rates, higher speeds consistently improved discharge rates across all tested layouts. 
- **Boundary Conditions:** FIS is **not universal**; it only triggers in extreme physical bottlenecks where exits are excessively narrow relative to body dimensions.
- **Simulation / Modeling Layer:** Do not hardcode universal speed penalties at all junctions; apply clogging/friction delays only at severe physical pinch points.
- **Monitoring & Alert Logic:** High movement speed in wide merging corridors is an efficient flow indicator, whereas high density combined with near-zero velocity indicates a critical bottleneck/stampede risk.
- **Architectural Insights:** Corridors with optimized merging angles clear crowds faster during evacuations, informing layout planning and crowd-routing algorithms.


# void 2
## Pedestrians & Crowds- Crowd safety and pedestrian traffic: Applications of artificial intelligence, computer vision, physics and econometric methods

https://www.sciencedirect.com/special-issue/100PZRGHRL6

Pedestrian traffic is an inherent and important part of urban mobility, and with the increasing urban populations, the ability to manage crowds of pedestrians and guarantee their safety and acceptability of level of service is going to become of even more importance. The underlying problems of crowd dynamics are multifaceted and require integration of expertise and collaboration between scholars of various domains. This Special Issues seeks innovative applications of Computer Vision (CV), Artificial Intelligence (AI) and Machine Learning (ML) techniques in crowd management. Other innovative methodologies of pedestrian traffic flow modelling and simulation are also considered, including those using physics-based or econometric models. One of the primary aims of this Special Issue is to encourage studies that make a bridge between the modelling/experimental sector of crowd dynamics and the CV and AI sector. Some broad areas of interest are listed below, although the scope is not necessarily limited to these: 
 Applications of CV and AI methods in field pedestrian data collection, crowd monitoring, anomaly detection and crowd control intervention. 
 Applications of CV and AI methods in calibration and validation of pedestrian models. 
 Data driven pedestrian and crowd models that utilise AI, ML or CV methods to obtain real-time feedback 
 Application of AI and/or ML methods in optimisation of pedestrian traffic under normal operations or emergency conditions. 
 Applications of advanced statistical mechanics or econometrics modelling methods in pedestrian traffic simulation.

# [[void 3]]
## Mathematicians uncover the logic behind how people walk in crowds

https://news.mit.edu/2025/mathematicians-uncover-logic-behind-how-crowds-walk-0324

Researchers at MIT and their collaborators studied how pedestrian crowds transition between organized and disordered movement. They found that people walking in opposite directions often spontaneously form clear lanes, allowing the crowd to move more efficiently and safely.

The study identified **angular spread**—the variation in the directions pedestrians take—as a key factor in this process. When pedestrians move mostly straight toward their destinations, lane formation is likely. However, when people begin walking at increasingly different angles, the organized lanes become unstable. Mathematical modeling predicted a transition at approximately **13 degrees**, which was also supported by controlled experiments involving participants crossing a simulated crosswalk.

The researchers used fluid-flow mathematics to model the overall movement of a crowd rather than tracking every individual separately. Their experiments showed that greater disorder leads to less efficient movement because pedestrians must make more frequent dodging and avoidance maneuvers. These findings could help in designing airports, crosswalks, stadiums, and other public spaces to encourage safer and more efficient pedestrian movement.

**Basic concept:**

```text
Small angular spread                    Large angular spread
        ↓                                       ↓
  → → → → →                              ↗   →   ↘
  → → → → →                                 ↖
  ← ← ← ← ←                              ←      ↓
  ← ← ← ← ←                                 ↙
        ↓                                       ↓
  Organized lanes                         Disordered flow

       < ~13°                               > ~13°
```

Overall, the research demonstrates how simple individual walking behaviors can produce large-scale patterns of order or disorder in crowds, and provides a quantitative way to predict when this transition is likely to occur.


# [[void 4]]
## Pedestrian Crowd Detection and Segmentation using Multi-Source Feature Descriptors
[[Paper_87-Pedestrian_Crowd_Detection_and_Segmentation.pdf]]
The research paper **“Pedestrian Crowd Detection and Segmentation using Multi-Source Feature Descriptors”** by Saleh Basalamah and Sultan Daud Khan presents a computer-vision-based approach for detecting and segmenting crowded regions in images. The authors focus on crowd detection as an important preprocessing step for applications such as crowd counting, tracking, density estimation, and behavior analysis. The paper identifies **occlusion and cluttered backgrounds** as major challenges in accurately detecting crowded regions.

The proposed method uses multiple appearance and texture descriptors rather than relying on motion information. The input image is divided into smaller cells, from which three types of features are extracted: **Local Binary Pattern (LBP), Fourier Analysis, and Gray-Level Co-occurrence Matrix (GLCM)**. These complementary features are combined to form a **128-element feature vector**, which is then classified using a **linear Support Vector Machine (SVM)** to distinguish crowd regions from non-crowd regions.

The researchers evaluated their approach using the **UCF CC 50 dataset**, which contains 50 crowd images with significant variation in crowd density, scene, resolution, and viewpoint. The proposed method was compared with several existing approaches, including GMM, MEOF, HOG+SVM, SIFT+SVM, Fourier Analysis, and LBP-based texture analysis. The proposed multi-source feature approach achieved the highest reported **AUC of 0.63** among the compared methods.

The overall methodology can be summarized as follows:

```text
                Input Image
                     │
                     ▼
             Divide into Cells
                     │
          ┌──────────┼──────────┐
          ▼          ▼          ▼
         LBP      Fourier      GLCM
          │       Analysis      │
          └──────────┼──────────┘
                     ▼
             Feature Combination
                     │
                     ▼
              128-Element Vector
                     │
                     ▼
              Linear SVM Classifier
                     │
              ┌──────┴──────┐
              ▼             ▼
            Crowd        Non-Crowd
              │
              ▼
        Crowd Segmentation
```

Overall, the paper demonstrates that combining **LBP, Fourier, and GLCM features** can improve the detection and segmentation of crowded regions compared with the individual or alternative methods evaluated in the study. The resulting segmentation identifies the regions of an image that correspond to crowds, providing a basis for subsequent crowd-analysis tasks.



