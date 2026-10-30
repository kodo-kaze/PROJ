# Mathematicians uncover the logic behind how people walk in crowds
### Pedestrian Flow, Lane Formation, and the Transition from Order to Disorder

MIT instructor Karol Bacik and his collaborators studied how the movement of human crowds changes between organized and disordered patterns. Their research focuses on common situations such as crowded plazas, crosswalks, and airport concourses, where people move toward different destinations while trying to avoid collisions. The researchers wanted to determine when pedestrians naturally form orderly lanes and when their paths instead become tangled and disorganized.

The study was published in the _Proceedings of the National Academy of Sciences_. Its authors include Karol Bacik, Grzegorz Sobota and Bogdan Bacik of the Academy of Physical Education in Katowice, Poland, and Tim Rogers of the University of Bath in the United Kingdom. The work was supported in part by the Engineering and Physical Sciences Research Council of UK Research and Innovation.

### Lane Formation in Crowds

A major phenomenon studied by the researchers is **lane formation**. When pedestrians travel through the same area in opposite directions, they can spontaneously organize themselves into lanes. Instead of every person taking an independent path and repeatedly encountering people moving in the opposite direction, people traveling in the same direction tend to group together.

For example, in a crosswalk where one group is moving in one direction and another group is moving in the opposite direction, the crowd can naturally develop into parallel streams:

```text
→ → → → → → → →
→ → → → → → → →

← ← ← ← ← ← ← ←
← ← ← ← ← ← ← ←

→ → → → → → → →
→ → → → → → → →
```

The important point is that these lanes do not necessarily have to be deliberately organized. They can emerge from the individual decisions people make while attempting to avoid collisions.

In earlier research conducted in 2023, Bacik and his collaborators investigated this lane-formation mechanism. They identified an important factor involving an imbalance between turning left and turning right. Once something in a crowd begins to resemble a lane, nearby individuals may join that developing lane or be forced to either side of it. They then move parallel to the original lane, creating a pattern that additional pedestrians can follow. In this way, an initially small pattern can develop into a larger, organized structure.

The new study asked how robust this mechanism is. In particular, the researchers wanted to know whether lanes would still form if pedestrians did not walk perfectly straight toward their destinations.

### Angular Spread

The researchers identified **angular spread** as the key quantity that determines whether pedestrian movement remains organized. Angular spread describes the range of different directions in which people in a crowd are walking.

When the angular spread is relatively small, most pedestrians are traveling in relatively similar or opposite directions. A crosswalk is an example of this situation: people generally cross from one side toward the other and encounter people moving in the opposite direction.

When the angular spread becomes larger, pedestrians can be traveling toward destinations located at many different angles. This creates more complicated interactions because people are no longer moving along a small number of dominant directions.

The difference can be represented approximately as:

```text
Small angular spread
────────────────────

→ → → → → → →
← ← ← ← ← ← ←
→ → → → → → →

Most people move in a few dominant directions.
              ↓
        Lane formation likely


Large angular spread
────────────────────

↗    →      ↘
   ↑     ↙
←      ↓      →
    ↖      ↘

People move in many different directions.
              ↓
       Lane formation weakens
```

### The 13-Degree Transition

The researchers used mathematical analysis to determine when the transition from organized to disordered movement should occur. Their calculations predicted that the transition would occur at an angular spread of approximately **13 degrees**.

This means that when pedestrians begin veering sufficiently far away from straight paths, the organized lane structure can become unstable. Around this transition, the crowd can change from a state in which clear lanes form to one in which there are few or no recognizable lanes.

The 13-degree value is therefore a measure of the boundary between two different types of crowd behavior:

```text
          ANGULAR SPREAD

              increases
                 ↓

     ORDER                     DISORDER
┌───────────────┐          ┌───────────────┐
│ Clear lanes   │          │ No clear lanes│
│               │          │               │
│ → → → → →     │          │ ↗ → ↘ ↓ ← ↖   │
│ ← ← ← ← ←     │          │   ↙ ↑ →       │
└───────────────┘          └───────────────┘
        │                         │
        └──────── ~13° ───────────┘
              transition
```

The researchers emphasize that the important question is not simply whether people walk in perfectly straight lines. Instead, they wanted to determine how much variation in walking direction a crowd can tolerate before the lane-forming mechanism stops working effectively.

### Mathematical Modeling Using Fluid Flow

To study the problem mathematically, Bacik and his collaborators used equations normally associated with fluid flow. The researchers treated the crowd as a collective flow rather than attempting to describe every individual pedestrian separately.

Bacik explains that a sufficiently large crowd can be described using fluid-like equations because individual differences can be averaged out. Although people differ in how assertively they walk and how they avoid other pedestrians, these individual effects can become less important when considering global characteristics of a large crowd.

The researchers therefore focused on large-scale properties, particularly whether lanes form or not.

Their mathematical model considered a situation in which pedestrians flow across a crosswalk. They varied parameters such as the width of the channel through which the crowd was moving, the angles at which people crossed, and the different directions in which pedestrians could move when dodging one another to avoid collisions.

The calculations showed that pedestrians are more likely to form lanes when they travel relatively straight across the crosswalk from opposite directions. As the range of crossing angles becomes larger, the lane structure becomes less stable and the crowd becomes increasingly disordered.

### Controlled Crowd Experiments

The researchers then tested whether the mathematical prediction matched actual human behavior.

They conducted controlled experiments in a gymnasium. An overhead camera recorded the movements of participants. Each participant wore a paper hat containing a unique barcode, allowing the camera system to track individual movements.

The participants were assigned different starting and ending positions on opposite sides of a simulated crosswalk. They were instructed to walk simultaneously toward their assigned destinations while avoiding collisions with other participants.

The researchers repeated the experiment many times. By changing the starting and ending positions, they produced crowd flows in which pedestrians crossed the area at many different angles.

This allowed the researchers to collect movement data from many different crowd configurations and examine when organized lanes formed and when they failed to form.

### Experimental Confirmation

When the researchers analyzed the experimental data, they found that angular spread was strongly related to whether lanes appeared.

The experiments supported the mathematical prediction. The transition between organized and disordered pedestrian flow occurred at approximately the predicted **13-degree** value.

When the average pedestrian's direction deviated sufficiently far from a straight path, the crowd could move from an organized state with recognizable lanes into a disordered state with little lane formation.

The experiments therefore provided evidence that the mathematical model captured an important feature of real pedestrian behavior.

### Disorder and Efficiency

The researchers also found that greater disorder in a crowd is associated with less efficient movement.

When people are organized into lanes, pedestrians traveling in the same direction can move together without constantly crossing paths with people traveling in other directions. This allows the crowd to maintain a more organized flow.

In a disordered crowd, pedestrians have to perform more dodging and avoidance movements because their paths intersect in more complicated ways. This reduces the efficiency of the overall movement.

The study therefore connects the formation of lanes not only with organization but also with the efficiency of pedestrian flow.

### Possible Applications to Public Spaces

The researchers believe that their findings could be useful for people designing public spaces. Places such as airports, crosswalks, and other pedestrian thoroughfares have to accommodate large numbers of people moving toward different destinations.

The study provides a quantitative way of thinking about when pedestrians are likely to form organized lanes and when their movement may become disordered. If public spaces are designed to encourage safer and more efficient pedestrian flow, the researchers suggest that their findings could provide simpler guidelines or rules of thumb.

The team plans to test the predictions further using footage of real-world crowds and pedestrian thoroughfares. They want to compare naturally occurring pedestrian movement with the theory developed in their research.

### Overall Conclusion

The research shows that pedestrian crowds can spontaneously organize into lanes when people move in relatively constrained directions, particularly when they travel in opposite directions across a shared space. However, this organization has a limit. As the variation in pedestrians' walking directions increases, the lane-forming mechanism becomes less effective.

The researchers identified **angular spread** as the key measure of this behavior and found a transition at approximately **13 degrees**. Their mathematical analysis, based on fluid-flow equations, predicted this transition, and controlled experiments with human participants produced results consistent with the prediction.

The study therefore provides a mathematical and experimental explanation for why some crowds move in clear, organized lanes while others become tangled and disordered. It also shows how individual pedestrian decisions, such as changing direction to avoid collisions, can collectively produce large-scale patterns of organization or disorder.