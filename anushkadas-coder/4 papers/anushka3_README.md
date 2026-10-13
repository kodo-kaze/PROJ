# anushka3 --- Multi-Agent Inverse Reinforcement Learning in Real-World Unstructured Pedestrian Crowds

> **Purpose of this README:** a research note for the
> crowd-exit/bottleneck project. It separates what the paper reports
> from how its ideas might be adapted to a simulation study of
> pedestrian clogging and robot/obstacle placement.

## 1. Metadata

  -----------------------------------------------------------------------
  Field                               Details
  ----------------------------------- -----------------------------------
  Local reference name                `anushka3`

  Paper title                         *Multi-Agent Inverse Reinforcement
                                      Learning in Real-World Unstructured
                                      Pedestrian Crowds*

  Authors                             Rohan Chandra, Haresh Karnan, Negar
                                      Mehr, Peter Stone, Joydeep Biswas

  Version/date shown in PDF           arXiv:2405.16439v3, 26 March 2025

  Research area                       Multi-agent inverse reinforcement
                                      learning (IRL), pedestrian
                                      trajectory prediction, social robot
                                      navigation

  Main contribution                   A multi-agent maximum-entropy IRL
                                      approach for learning
                                      agent-specific cost/reward
                                      functions in dense, unstructured
                                      pedestrian crowds

  Code/data                           The paper points to
                                      `mairl-uva.github.io`

  Role in our project                 **Supporting behavioural-modelling
                                      reference.** It can motivate richer
                                      pedestrian interaction models, but
                                      it does not directly establish that
                                      robots or pillars prevent
                                      bottleneck clogging.
  -----------------------------------------------------------------------

## 2. TL;DR

The paper addresses a limitation of conventional single-agent inverse
reinforcement learning: modelling each pedestrian as if their motion can
be explained independently becomes inadequate when people interact
closely in dense, unstructured crowds.

The authors introduce **multi-agent maximum-entropy inverse
reinforcement learning (MAIRL)**. It learns individual agents'
cost/reward functions from observed trajectories and uses a dynamic
game-theoretic solver to derive policies. A key device is the
**tractability--rationality trade-off**: simplify the coupled
multi-agent problem so it can be solved, accepting a modest reduction in
accuracy.

The evaluation includes ETH, UCY, SCAND, JRDB and a newly collected
**Speedway** dataset from a busy university-campus intersection. The
paper reports that MAIRL performs particularly well against compatible
baselines on Speedway and is competitive on ETH/UCY. Reported prediction
errors are in metres and should be interpreted as trajectory-prediction
metrics---not as measures of congestion, evacuation time, or clogging.

### Why it may matter to our exit project

A learned behavioural model could help represent differences in how
pedestrians move, react to neighbours, avoid collisions, or approach a
goal. That could be useful if our project eventually needs a more
realistic interaction model than a basic Social Force Model (SFM).

However, this paper is **not a direct robot-placement or
evacuation-clogging study**. Any claim that a robot, pillar, or other
intervention reduces clogging must be tested separately in our own
simulation.

## 3. Important definitions

  -----------------------------------------------------------------------------
  Term                          Meaning in this context Why it matters
  ----------------------------- ----------------------- -----------------------
  **Inverse Reinforcement       Infers the objective or Lets a model learn what
  Learning (IRL)**              reward/cost function    motion patterns appear
                                that could explain      to be optimising rather
                                observed expert         than only copying
                                behaviour.              trajectories.

  **Maximum-Entropy IRL (MaxEnt A formulation that      Avoids assuming there
  IRL)**                        matches expected        is only one exact
                                trajectory features     trajectory for a given
                                while retaining         situation.
                                uncertainty over        
                                possible behaviours.    

  **Multi-Agent IRL (MAIRL)**   Extends IRL to multiple More suitable for
                                agents whose actions    tightly coupled
                                and outcomes depend on  pedestrian interactions
                                one another.            than treating every
                                                        person independently.

  **Trajectory**                A time-ordered sequence The central data unit
                                of an agent's observed  for learning and
                                or predicted            evaluation.
                                states/positions.       

  **Feature function**          Maps a trajectory or    Features can represent
                                state/action sequence   motion and interaction
                                to quantities used by   properties, depending
                                the cost/reward model.  on the model's
                                                        specification.

  **Cost/reward function**      Scores states, actions, Different agents can
                                or trajectories         place different
                                according to the        importance on goals,
                                agent's inferred        risk, or other modelled
                                objective.              factors.

  **Bounded rationality**       Assumes observed agents Helps acknowledge
                                need not act as         variability in real
                                perfectly optimal       human movement.
                                planners in every       
                                instance.               

  **Dynamic game-theoretic      Computes policies while Used to derive policies
  solver**                      accounting for          that optimise the
                                strategic interaction   learned cost/reward
                                among agents under the  functions.
                                model.                  

  **Tractability--rationality   The paper's technique   A practical reminder
  trade-off**                   for making multi-agent  that a more coupled
                                MaxEnt IRL              model may require
                                computationally         simplifying
                                manageable, at some     assumptions.
                                cost in accuracy.       

  **ADE (Average Displacement   Average positional      Lower is better for
  Error)**                      distance between        trajectory prediction;
                                predicted and           it is not a crowd-flow
                                ground-truth trajectory metric.
                                points, under the       
                                evaluation protocol.    

  **FDE (Final Displacement     Distance between the    Lower is better for
  Error)**                      predicted and           endpoint prediction; it
                                ground-truth final      does not measure exit
                                positions, under the    throughput.
                                evaluation protocol.    

  **End-to-End Forecasting      A trajectory            Keep it distinct from
  Error (EFE)**                 forecasting metric used evacuation or clogging
                                by the JRDB benchmark   metrics.
                                in the paper.           
  -----------------------------------------------------------------------------

## 4. Methodology at a glance

1.  **Collect demonstrations.** Use recorded pedestrian trajectories
    from real-world datasets.
2.  **Represent behaviour.** Describe states, actions and trajectory
    features under the model.
3.  **Learn agent costs/rewards.** Adjust the model so the generated
    behaviour aligns with expert demonstrations.
4.  **Account for interactions.** Use a multi-agent formulation rather
    than assuming all pedestrians behave independently.
5.  **Make the optimisation tractable.** Apply the paper's
    tractability--rationality trade-off.
6.  **Compute policies.** Use a dynamic game-theoretic solver for
    policies that optimise the learned cost/reward functions.
7.  **Evaluate trajectories.** Compare predictions with ground truth and
    baselines using metrics such as ADE/FDE (and EFE on the relevant
    benchmark).

### Reported trajectory-prediction results

The following values are reported in the paper's comparisons and are
included here as reference points from the PDF. They are **not directly
comparable to our exit simulation metrics**.

  ------------------------------------------------------------------------------
  Dataset /    Method                  ADE (m)          FDE (m) Interpretation
  comparison                                                    
  ------------ -------------- ---------------- ---------------- ----------------
  Speedway     Single-agent               0.40             0.80 Baseline
               MaxEnt IRL                                       reported in the
                                                                paper

  Speedway     MAIRL                      0.26             0.60 Lower error than
                                                                the single-agent
                                                                MaxEnt IRL
                                                                baseline

  ETH/UCY      MAIRL                      0.23             0.39 Reported average
  average                                                       across the
                                                                evaluated
                                                                ETH/UCY setting

  ETH/UCY      Single-agent               0.50             1.11 Reported
  average      MaxEnt IRL                                       comparison
                                                                baseline
  ------------------------------------------------------------------------------

**Reading these numbers carefully**

-   Lower ADE/FDE indicates closer trajectory predictions under the
    paper's evaluation protocol.
-   Dataset splits, input formats and baseline compatibility affect
    which methods can be compared on each dataset.
-   The paper also reports evaluation on SCAND and JRDB; JRDB uses EFE
    in the comparisons described by the authors.
-   These values do **not** show that a method reduces clog duration,
    increases exit throughput, prevents arches, or improves evacuation
    safety.

## 5. Connection to our bottleneck / exit project

Our project studies crowd movement through an exit bottleneck, including
arch formation and clogging, and considers whether placing robots or
obstacles near the exit can change the outcome. The MAIRL paper can
inform the **pedestrian-behaviour component**, but it does not supply a
validated intervention for our scenario.

### Possible modelling pipeline

``` text
Real or simulated pedestrian trajectories
                 |
                 v
      Behaviour representation
                 |
                 v
   SFM baseline / learned MAIRL model
                 |
                 v
   Bottleneck and intervention scenarios
   (no robot, robot/pillar configurations)
                 |
                 v
        Repeated simulation runs
                 |
                 v
 Clogging metrics + trajectory metrics
                 |
                 v
 Compare conditions with uncertainty
```

### Potential uses

#### U1 --- Behaviour-model comparison

Use the existing SFM as a baseline and investigate whether a learned or
MAIRL-inspired model reproduces observed interactions more
realistically. This is a separate model-validation question from whether
an intervention works.

#### U2 --- Heterogeneous pedestrian behaviour

The paper motivates allowing different agents to have different inferred
objectives. In a simulation, this could inspire controlled
heterogeneity---for example, varying the relative importance of goal
progress and collision avoidance. Any chosen feature set and parameter
values must be documented as project assumptions unless calibrated from
data.

#### U3 --- Interaction-aware approach to the exit

Test whether interaction modelling changes how pedestrians approach,
queue at, or move around the bottleneck. Do not assume it will improve
flow; compare it empirically against the SFM baseline.

#### U4 --- Test interventions only after validation

Once the behavioural model is validated, compare the same scenarios with
and without a robot or pillar. Keep population, exit width, initial
conditions, simulation duration and random seeds controlled wherever
possible. MAIRL itself should not be cited as evidence that the
intervention works.

## 6. Metrics: keep the two evaluation layers separate

  -----------------------------------------------------------------------
  Layer                   Example metrics         Question answered
  ----------------------- ----------------------- -----------------------
  **Behaviour /           ADE, FDE, EFE (where    Does the model predict
  trajectory prediction** applicable)             pedestrian movement
                                                  accurately?

  **Bottleneck /          Throughput, total       Does the simulated
  evacuation dynamics**   evacuation time, clog   crowd flow or clog
                          duration/fraction,      differently?
                          burst-size              
                          distribution, tail      
                          exponent, flowing       
                          parameter (W), density  
                          near exit               

  **Intervention          Difference in the above Does the tested
  comparison**            metrics between         intervention change the
                          baseline and            outcome under the
                          robot/pillar scenarios, chosen conditions?
                          with variability across 
                          runs                    
  -----------------------------------------------------------------------

A good trajectory score does not automatically imply good clogging
statistics. A model can predict individual positions reasonably well
while still misrepresenting rare collective events such as stable arches
or long-lasting clogs.

## 7. Implementation notes for our project

-   **Do not replace SFM immediately.** Preserve the current SFM setup
    as a reproducible baseline.
-   **Validate before intervention claims.** First compare the model's
    trajectories and crowd-level behaviour against suitable reference
    data or known patterns.
-   **Record assumptions explicitly.** Document the chosen state,
    action, feature and cost definitions, as well as any simplifications
    needed for computation.
-   **Separate calibration and evaluation.** Avoid tuning the model on
    the same runs used to claim that a robot/pillar intervention works.
-   **Use repeated runs.** Crowd simulations can be sensitive to initial
    positions and stochasticity. Report distributions or uncertainty,
    not only one run.
-   **Keep metrics dimensionally correct.** ADE/FDE are spatial
    prediction errors in metres; throughput is a flow rate; clogging
    metrics describe crowd dynamics.
-   **Check implementation availability.** The paper links code and data
    through `mairl-uva.github.io`; confirm the repository's current
    status, dependencies, data access and licence before attempting
    reuse.

## 8. What not to claim

Do **not** write that:

-   MAIRL prevents clogging or prevents arch formation at exits.
-   The paper proves that robots should be placed at a bottleneck.
-   Lower ADE/FDE necessarily means faster evacuation or safer crowd
    movement.
-   The reported benchmark numbers are results from our own simulation.
-   The paper's real-world pedestrian datasets directly validate our
    specific exit geometry or robot behaviour.
-   A MAIRL-inspired implementation is equivalent to the paper's full
    method unless its formulation, solver and evaluation have actually
    been reproduced.

Safer wording:

> "This paper motivates modelling pedestrian--pedestrian interactions
> with a multi-agent inverse reinforcement learning framework. We use it
> as a behavioural-modelling reference; the effect of robots or
> obstacles on bottleneck clogging must be evaluated independently."

## 9. TODO checklist

-   [ ] Read the paper's problem formulation and
    tractability--rationality trade-off carefully.
-   [ ] Confirm the exact table rows and evaluation protocols before
    quoting benchmark numbers in a report.
-   [ ] Inspect the linked code/data repository and record dependencies,
    licence and reproducibility status.
-   [ ] Define whether the project needs trajectory realism, crowd-level
    clogging realism, or both.
-   [ ] Preserve the current SFM baseline and document its parameters.
-   [ ] Choose a validation dataset or reference behaviour appropriate
    to the simulated exit scenario.
-   [ ] Define crowd-level metrics before running intervention
    experiments.
-   [ ] Run baseline and intervention conditions with controlled
    settings and multiple seeds.
-   [ ] Report uncertainty and limitations; distinguish source results
    from our own experimental results.

## 10. Final assessment

**Best role for `anushka3`: supporting reference for pedestrian
behaviour and multi-agent interaction modelling.**

For the current bottleneck project, use this paper to justify
investigating richer interaction-aware behaviour models---not as direct
evidence for any particular robot or pillar placement. The main research
question about clogging remains empirical: under a validated model, does
the intervention consistently change throughput, clog statistics, or
evacuation outcomes?
