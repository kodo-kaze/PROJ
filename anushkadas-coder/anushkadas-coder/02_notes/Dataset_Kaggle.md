# Benchmark Dataset Review: Pedestrian Crowd Estimation

**Dataset Name:** ShanghaiTech Crowd Counting Dataset  
**Platform / Source:** Kaggle (`https://www.kaggle.com/datasets/tthien/shanghaitech-with-people-density-map`)  
**Target Application:** Density map regression, crowd counting, and bottleneck flow analysis.

---

### 1. Dataset Breakdown & Class Balance

* **Part A (Extreme Dense Crowds):**
  * **Images:** 482 images sampled randomly from internet sources with high-density crowd scenarios.
  * **Annotated Heads:** ~241,677 labeled head center coordinates.
  * **Density Range:** High variation (up to several thousand individuals per frame), exhibiting severe occlusion and perspective distortion.
* **Part B (Sparse / Regular Urban Flow):**
  * **Images:** 716 images captured from fixed surveillance viewpoints on busy urban streets (Shanghai).
  * **Annotated Heads:** ~88,488 labeled head coordinates.
  * **Density Range:** Moderate to low, reflecting standard pedestrian movement and open-air flow.

---

### 2. Feature Engineering & Preprocessing Pipeline

* **Ground Truth Density Generation:** Head point annotations are converted into continuous density maps using geometry-adaptive or fixed Gaussian kernels:
  
  $$D(x) = \sum_{i=1}^N \delta(x - x_i) * G_{\sigma_i}(x)$$
  
  where the spread parameter $\sigma_i$ is determined by the average Euclidean distance to the $k$-nearest neighbor heads: $\sigma_i = \beta \bar{d}_i^k$.
* **Handling Extreme Density Imbalance:** Part A addresses high-density crowd crush scenarios, while Part B validates models against false positives in sparse backgrounds.

---

### 3. Model Compatibility

* **Target Architectures:** Fully Convolutional Networks (FCNs), Dilated CNNs (CSRNet), and Multi-Column CNNs (MCNN).
* **Evaluation Metrics:** Mean Absolute Error (MAE) and Root Mean Squared Error (RMSE) against ground truth integral counts:
  
  $$\text{MAE} = \frac{1}{N} \sum_{i=1}^N |C_i - \hat{C}_i|$$