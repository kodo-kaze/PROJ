## What is the problem?
A pedestrian crowd problem occurs when a large number of people occupy or move through the same area and their movement becomes difficult to manage. This can happen in railway stations, metro stations, stadiums, markets, festivals, narrow corridors and other public places.
A crowd is not automatically dangerous just because it is large. The situation also depends on density, available space, movement speed, direction and interaction between pedestrians.


# References:

## Research papers:
### (2026)
https://www.sciencedirect.com/science/article/abs/pii/S0001457526000916?via%3Dihub
Published in Accident Analysis & Prevention (2026), this article focuses on the safety and risk management aspects of pedestrian traffic and crowd dynamics. In the context of the overarching pedestrian crowd problem, studies in this journal typically address how infrastructure design, human behavior, and traffic flow intersect to create collision risks or evacuation failures.
While the exact granular findings are enclosed within the journal's specific safety modeling scope, the research contributes to the field by translating behavioral theories into actionable accident prevention frameworks. It emphasizes understanding the environmental and psychological triggers that lead to pedestrian accidents, ultimately aiding in the development of safer crosswalks, transit intersections, and crowd control protocols.


### (2025)
https://doi.org/10.1126/sciadv.adw2688
This research paper from Science Advances investigates pedestrian crowd dynamics through a novel bottom-up foot-tracking methodology, moving away from traditional head-tracking techniques. By utilizing a large-scale experimental platform equipped with a transparent floor, the researchers precisely captured individual walking speeds, directions, step lengths, and step frequencies in natural gaits.
The findings reveal that crowds exhibit three distinct dynamical regimes depending on their density: a free regime where movement is unconstrained, a slow-moving regime where speed drops but step direction remains independent, and a highly constrained jammed regime. Crucially, the paper demonstrates that spontaneous movement synchronization (where pedestrians walk in phase with the same step frequency) is most likely to occur right at the onset of the jammed regime, offering a critical predictive indicator for crowd crushing and evacuation modeling.


### (2024)
https://academic.oup.com/pnasnexus/article/3/4/pgae120/7632022
This paper from PNAS Nexus takes a unique, physics-inspired approach to classifying crowd behavior by introducing dimensionless numbers, akin to how the Reynolds number is used to classify fluid mechanics. The authors argue that traditional density-based metrics fail to encompass the diverse behaviors and risk profiles observed in real-world pedestrian streams.
To solve this, they introduced two new variables: the Intrusion Number, which quantifies a pedestrian's psychological desire to maintain personal space, and the Avoidance Number, which measures the anticipation of physical collisions. By calculating these numbers based on empirical datasets, the researchers successfully delineated distinct crowd flow regimes mathematically. This allows for highly accurate perturbative expansions of individual pedestrian dynamics, providing engineers with a robust way to validate simulation models for specific crowd scenarios.


## Dataset:
https://www.kaggle.com/datasets/menhari/crowd-human-crowd-detection
This Kaggle dataset provides a practical, data-driven foundation for addressing the pedestrian crowd problem through computer vision. It is a cleaned, beginner-friendly variation of the well-known "CrowdHuman" dataset, consisting of approximately 15,000 images specifically curated for training machine learning models to detect humans in highly congested environments.
The dataset comes pre-annotated with bounding boxes in YOLO (You Only Look Once) format, focusing on a single "person" class. This is a vital resource for researchers and developers building real-time crowd monitoring systems, as it trains AI to accurately count and track individuals even when they are heavily occluded, blurred, or packed tightly together. Applying models trained on this data allows authorities to monitor live camera feeds and automatically detect when a crowd is approaching the dangerous density thresholds identified in the theoretical research above.