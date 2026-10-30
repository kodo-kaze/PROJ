
# Our final goal for V1

We want:

```text
IMAGE / VIDEO
      │
      ▼
   YOLO MODEL
      │
      │ detects people
      ▼
YOLO OUTPUT
      │
      ▼
FEATURE EXTRACTION
      │
      ▼
    CSV
      │
      ▼
  ML CLASSIFIER
      │
      ▼
┌──────────────┐
│ CROWD        │
│ NOT CROWD    │
└──────────────┘
```

---

# STEP 1 — Get a proper YOLO training dataset

We need:

**Images + bounding boxes around every person.**

For example:

```text
image001.jpg

      ┌────┐
      │ 👤 │
      └────┘

               ┌────┐
               │ 👤 │
               └────┘
```

The label says where those people are.

### Use CrowdHuman

[CrowdHuman official dataset](https://www.crowdhuman.org/?utm_source=chatgpt.com)

Why?

Because it is specifically designed for **human detection in crowded scenes**, rather than ordinary object detection.

### DO NOT use your Pedestrian Planet CSV for this step.

Those are already YOLO-generated detections.

---

# STEP 2 — Convert CrowdHuman annotations to YOLO format

CrowdHuman doesn't necessarily arrive in the exact YOLO format we want.

We convert:

```text
CrowdHuman annotation
        ↓
YOLO annotation
```

Eventually we want:

```text
dataset/
│
├── images/
│   ├── 001.jpg
│   ├── 002.jpg
│   └── ...
│
├── labels/
│   ├── 001.txt
│   ├── 002.txt
│   └── ...
│
└── data.yaml
```

A label might look like:

```text
0 0.45 0.52 0.12 0.40
0 0.72 0.48 0.10 0.35
```

`0` = person.

---

# STEP 3 — Get a pretrained YOLO

We don't train YOLO from zero.

Start with a pretrained detection model.

I'd start with:

```text
YOLO26m
```

Then:

```python
from ultralytics import YOLO

model = YOLO("yolo26m.pt")
```

It starts with general object-detection knowledge.

---

# STEP 4 — Fine-tune YOLO on CrowdHuman

Now we train:

```text
Pretrained YOLO
       +
CrowdHuman
       ↓
Fine-tuning
       ↓
pedestrian_model.pt
```

For example:

```python
model.train(
    data="data.yaml",
    epochs=50,
    imgsz=640,
    batch=16
)
```

The exact batch size depends on your GPU/RAM.

---

# STEP 5 — Test YOLO

Before doing ANY crowd classification, test the detector.

Give it:

```text
crowded image
```

and check:

```text
👤 ← detected
👤 ← detected
👤 ← detected
👤 ← detected
```

If YOLO is missing lots of people, **we fix YOLO first**.

Don't move forward until this works reasonably well.

---

# STEP 6 — Run YOLO on your crowd dataset

Now we start the second half.

Give YOLO lots of images/videos.

For every image:

```text
image
 ↓
YOLO
 ↓
person 1 → bbox + confidence
person 2 → bbox + confidence
person 3 → bbox + confidence
...
```

---

# STEP 7 — Extract useful features

This is where **we write our own Python code**.

From YOLO's output, calculate things like:

### 1. Number of people

```text
person_count = 47
```

### 2. Average confidence

```text
avg_confidence = 0.89
```

### 3. Bounding-box occupancy

How much of the image is occupied by detected people.

```text
occupancy = 0.63
```

### 4. Spatial density

How concentrated are the people?

```text
density = 0.71
```

### 5. Average distance between people

Potentially useful for distinguishing:

```text
👤              👤

from

👤👤👤👤
```

### 6. Number of people per region

Divide the image into grids:

```text
┌──────┬──────┬──────┐
│  2   │  8   │  1   │
├──────┼──────┼──────┤
│  15  │  22  │  9   │
├──────┼──────┼──────┤
│  3   │  7   │  4   │
└──────┴──────┴──────┘
```

This can give the ML model much more information.

---

# STEP 8 — Create OUR CSV

Now our Python script generates something like:

```text
image,person_count,avg_confidence,occupancy,density,avg_distance,label
001.jpg,3,0.92,0.08,0.05,0.41,0
002.jpg,7,0.89,0.16,0.11,0.29,0
003.jpg,32,0.91,0.54,0.61,0.13,1
004.jpg,67,0.87,0.79,0.84,0.07,1
```

This is **our dataset for the second model**.

---

# STEP 9 — Add the actual CROWD / NOT CROWD label

This is critical.

YOLO does **not** produce:

```text
CROWD
NOT CROWD
```

We have to establish that ground truth.

For each image:

```text
001.jpg → NOT_CROWD
002.jpg → NOT_CROWD
003.jpg → CROWD
004.jpg → CROWD
```

```text
                 IMAGE
                   │
             ┌─────┴─────┐
             │           │
             ▼           ▼
           YOLO       HUMAN LABEL
             │           │
             ▼           ▼
        YOLO features   CROWD
             │           │ 
             │           │ 
             └────┬──────┘
                  ▼
              training
                  │
                  ▼
             ML classifier
```
We can do this through a labeling process based on the definition of crowd that we establish for your project.

**Don't just randomly say `>10 people = crowd`.**

We'll define the criterion properly.

---

# STEP 10 — Split the CSV

We'll split:

```text
70% → training
15% → validation
15% → testing
```

For example:

```text
crowd_dataset.csv
       │
       ├── train.csv
       ├── val.csv
       └── test.csv
```

---

# STEP 11 — Train the ML model

Now the ML model receives:

```text
person_count
avg_confidence
occupancy
density
avg_distance
...
```

and learns:

```text
                    FEATURES
                       │
                       ▼
                ┌─────────────┐
                │ Random      │
                │ Forest      │
                └──────┬──────┘
                       │
                 ┌─────┴─────┐
                 ▼           ▼
              CROWD      NOT CROWD
```

I'd start with **Random Forest** because it's simple, interpretable, and gives us a strong baseline.

Then we can compare it against:

- XGBoost
- SVM
- Logistic Regression
- MLP
    

---

# STEP 12 — Evaluate

We don't just say:

> "It works."

We'll calculate:

```text
Accuracy
Precision
Recall
F1-score
Confusion Matrix
ROC-AUC
```

For our project report, this becomes a proper ML experiment.

---

# STEP 13 — Connect everything

Finally:

```text
             IMAGE
               │
               ▼
        ┌─────────────┐
        │    YOLO     │
        │   PERSON    │
        │  DETECTOR   │
        └──────┬──────┘
               │
               ▼
       PERSON DETECTIONS
               │
               ▼
      FEATURE EXTRACTION
               │
               ▼
          FEATURES
               │
               ▼
       ┌─────────────┐
       │   RANDOM    │
       │   FOREST    │
       └──────┬──────┘
              │
       ┌──────┴──────┐
       ▼             ▼
    CROWD       NOT-CROWD
```

For video, we can additionally put **ByteTrack/BoT-SORT** between YOLO and feature extraction so that people can be tracked across frames.

---

# 🛑 Where (bimbok's hunted) current Pedestrian Planet dataset fits


[Pedestrian Planet Dataset](https://www.kaggle.com/datasets/shaadalam9/pedestrian-planet-crowd-dataset)

Currently:

```text
Pedestrian Planet
       │
       ▼
YOLO-generated CSV
```

It can potentially help us with **real-world testing/analysis**, but **it is NOT the first dataset we're going to use to train YOLO.**

---

# So your actual work order

This is the checklist we follow:

```text
☐ 1. Get CrowdHuman
        ↓
☐ 2. Convert annotations to YOLO
        ↓
☐ 3. Get pretrained YOLO
        ↓
☐ 4. Fine-tune YOLO
        ↓
☐ 5. Test YOLO
        ↓
☐ 6. Run YOLO on crowd images
        ↓
☐ 7. Extract features
        ↓
☐ 8. Create CSV
        ↓
☐ 9. Add CROWD / NOT-CROWD labels
        ↓
☐ 10. Split CSV
        ↓
☐ 11. Train Random Forest
        ↓
☐ 12. Evaluate
        ↓
☐ 13. Connect YOLO + ML
        ↓
       DONE
```
