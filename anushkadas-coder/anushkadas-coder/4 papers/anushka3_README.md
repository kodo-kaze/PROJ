# anushka3 --- Multi-Agent Inverse Reinforcement Learning in Real-World Unstructured Pedestrian Crowds

  ----------------------------------- -----------------------------------
  **Citation**                        Rohan Chandra, Haresh Karnan, Negar
                                      Mehr, Peter Stone, Joydeep Biswas.
                                      *Multi-Agent Inverse Reinforcement
                                      Learning in Real World Unstructured
                                      Pedestrian Crowds.*
                                      arXiv:2405.16439v3, 26 March 2025.

  **File**                            `anushka3.pdf`

  **Type**                            Multi-agent inverse reinforcement
                                      learning + real-world pedestrian
                                      trajectory dataset +
                                      trajectory-prediction experiments

  **Code/data**                       `mairl-uva.github.io` (linked by
                                      the paper; check current
                                      availability, dependencies and
                                      licence before reuse)

  **Role in our project**             A supporting reference for
                                      modelling **pedestrian--pedestrian
                                      interactions**. It may motivate
                                      richer behaviour than a
                                      single-agent model, but it does not
                                      directly test bottleneck clogging
                                      or robots placed at exits.
  ----------------------------------- -----------------------------------

------------------------------------------------------------------------

## 1. TL;DR

1.  **Single-agent MaxEnt IRL has trouble with dense, tightly coupled
    interactions.** Treating each pedestrian independently can fail when
    people pass, weave, yield, or squeeze past each other.

2.  The authors propose **multi-agent maximum-entropy inverse
    reinforcement learning (MAIRL)** to infer agents' cost/reward
    functions from observed trajectories and generate interaction-aware
    motion predictions.

3.  Their key technique is the **tractability--rationality trade-off**:
    a mathematical simplification makes the multi-agent optimisation
    tractable, at the cost of some accuracy / reduced uncertainty.

4.  On the paper's dense **Speedway** campus-intersection dataset, MAIRL
    reports **ADE/FDE = 0.26/0.60 m**, compared with **0.40/0.80 m** for
    single-agent MaxEnt IRL. On the ETH/UCY average, MAIRL reports
    **0.23/0.39 m**, compared with **0.50/1.11 m** for single-agent
    MaxEnt IRL.

5.  For our exit project, this is a **behaviour-modelling reference**,
    not evidence that a robot or pillar prevents an arch. We still need
    to evaluate our own clogging metrics.

------------------------------------------------------------------------

## 2. Definitions we will reuse

  -----------------------------------------------------------------------------
  Term                          Meaning in the paper    Our exit meaning
  ----------------------------- ----------------------- -----------------------
  **IRL**                       Inverse reinforcement   A possible way to learn
                                learning: infer a       pedestrian movement
                                cost/reward function    objectives from
                                that can explain        trajectory data, if
                                demonstrated behaviour. suitable data are
                                                        available.

  **MaxEnt IRL**                Maximum-entropy IRL;    A single-agent baseline
                                matches expected        for comparison, not a
                                trajectory features     clogging metric.
                                while representing      
                                uncertainty over        
                                possible behaviour.     

  **MAIRL**                     Multi-agent IRL; learns A candidate for
                                behaviour objectives    representing
                                while accounting for    interactions near the
                                interactions among      exit, if we implement
                                multiple agents.        or adapt it.

  **Trajectory**                A time-ordered sequence A pedestrian's
                                of states and controls  simulated positions /
                                for an agent.           states across time.

  **State**                     The agent/environment   Position and any
                                state at a time step;   additional variables
                                the paper uses          our model actually
                                continuous states and   records.
                                joint multi-agent       
                                states.                 

  **Control / action**          The input selected by   The movement command or
                                an agent at a time      acceleration in our
                                step.                   chosen simulation
                                                        model.

  **Cost function**             Scores states and       Candidate behavioural
                                controls; the paper     objective only if
                                describes terms related explicitly represented
                                to inter-agent          in our implementation.
                                distance, deviation     
                                from a reference path   
                                and control changes.    

  **Bounded rationality**       Agents do not always    A possible modelling
                                behave as perfectly     assumption for
                                deterministic,          variability in
                                perfectly optimal       pedestrian decisions.
                                planners.               

  **Tractability--rationality   The paper's             A reminder that
  trade-off**                   simplification to make  computational
                                multi-agent MaxEnt IRL  simplifications can
                                solvable, reducing      change the behaviour
                                uncertainty / bounded   produced by the model.
                                rationality and         
                                slightly reducing       
                                accuracy.               

  **ADE**                       Average displacement    A trajectory-prediction
                                error between generated metric; **not** an
                                and demonstrated        exit-flow metric.
                                trajectory positions,   
                                under the paper's       
                                evaluation protocol.    

  **FDE**                       Final displacement      Endpoint-prediction
                                error between generated metric; **not**
                                and demonstrated final  evacuation time or clog
                                positions.              duration.

  **EFE**                       End-to-end forecasting  Keep it separate from
                                error used for the JRDB our exit and clogging
                                benchmark comparison in metrics.
                                the paper.              
  -----------------------------------------------------------------------------

------------------------------------------------------------------------

## 3. Key setups and numbers (so we do not have to re-open the PDF)

### Real-world pedestrian data

-   **Datasets evaluated:** ETH, UCY, SCAND, JRDB and the authors' new
    **Speedway** dataset.
-   **Speedway setting:** a busy university-campus intersection with
    tightly coupled interactions among pedestrians and other moving
    agents such as scooters and bicycles.
-   **Data collection:** the paper describes a BlueCity2 Velodyne LiDAR
    mounted at the intersection, with object tracking providing agent
    IDs and state information.
-   **Processed Speedway trajectories:** the paper describes 20
    pedestrian trajectories in four cardinal directions, then forms four
    interaction categories with (5\^3 = 125) trajectories per category,
    for **500 trajectories total**.
-   **Evaluation:** the paper reports ADE/FDE in metres on ETH/UCY and
    Speedway, and EFE results for JRDB and SCAND. Lower error is better.
-   **Training details reported for baseline comparison:** 60/40
    cross-validation, learning rate (3 `\times 10`{=tex}\^{-4}), batch
    size 64, dropout 0.2, AdamW and MSE loss; training was performed on
    an NVIDIA GeForce RTX 2080.

### Reported ADE/FDE results

  Dataset / comparison   Method                      ADE (m)   FDE (m)
  ---------------------- ------------------------- --------- ---------
  Speedway               Single-agent MaxEnt IRL        0.40      0.80
  Speedway               MAIRL                          0.26      0.60
  ETH/UCY average        Single-agent MaxEnt IRL        0.50      1.11
  ETH/UCY average        MAIRL                          0.23      0.39

The paper's Table I also reports MAIRL values for individual ETH/UCY
datasets: ETH 0.39/0.71, Hotel 0.13/0.18, Univ 0.23/0.32, Zara1
0.19/0.29, and Zara2 0.15/0.28 (ADE/FDE, metres).

### EFE results reported in Table II

  ------------------------------------------------------------------------
  Dataset                           MAIRL EFE result Comparison context
  --------------------- ---------------------------- ---------------------
  JRDB                                         2.640 Lower is better; the
                                                     table includes
                                                     methods with results
                                                     from about 2.506 to
                                                     4.249.

  SCAND                                  0.23 / 0.67 Reported as the pair
                                                     of errors in the
                                                     table; lower is
                                                     better.
  ------------------------------------------------------------------------

**Important:** these are trajectory-prediction results from the paper.
They are not measurements of exit throughput, evacuation time, arch
stability, clog duration, or robot effectiveness.

------------------------------------------------------------------------

## 4. The main method and its relation to our project

  -----------------------------------------------------------------------------
  Paper component               Meaning in the paper    Possible counterpart in
                                                        our project
  ----------------------------- ----------------------- -----------------------
  **Observed trajectories**     Demonstrations from     Logged trajectories
                                real-world pedestrian   from a suitable
                                datasets.               reference dataset or
                                                        from our simulation for
                                                        controlled experiments.

  **Agent-specific              Each agent can have its Could motivate
  costs/rewards**               own objective, rather   heterogeneity, but only
                                than forcing all agents if we define and
                                to share one objective. justify the
                                                        corresponding model
                                                        features.

  **Multi-agent interactions**  Agents' outcomes depend Pedestrians interacting
                                on other agents' states while approaching,
                                and actions.            queueing at, and
                                                        passing through an
                                                        exit.

  **Dynamic game-theoretic      Used to roll out        A potential
  solver**                      trajectories that       implementation
                                maximise learned reward component only if we
                                functions.              choose to
                                                        reproduce/adapt the
                                                        paper's approach.

  **Tractability--rationality   Makes the multi-agent   A design trade-off to
  trade-off**                   optimisation tractable  document if we
                                at some cost in         implement an
                                accuracy.               approximate interaction
                                                        model.

  **Trajectory error metrics**  ADE/FDE (and EFE in     Use for behaviour
                                relevant benchmarks)    validation if
                                evaluate predicted      ground-truth
                                movement.               trajectories are
                                                        available; pair with
                                                        separate clogging
                                                        metrics.
  -----------------------------------------------------------------------------

The paper's cost features include distance to goal, distance to other
agents, and control effort. Do not assume our current SFM implementation
uses these exact features or that MAIRL can be dropped into it without
additional implementation work.

------------------------------------------------------------------------

## 5. How we use it (concrete items)

**U1. Treat it as a behaviour-model reference.**\
Use the paper to motivate the importance of modelling interactions among
multiple pedestrians. Do not cite it as a direct study of clogging or
exit interventions.

**U2. Keep the current SFM baseline.**\
First compare the behaviour we already simulate against any alternative
interaction-aware model. A more complex model is not automatically more
realistic; it needs validation.

**U3. Separate trajectory accuracy from clogging.**\
ADE/FDE answer whether predicted trajectories align with demonstrations.
Our project still needs its own metrics: (`\alpha`{=tex}), (W), burst
size (`\langle `{=tex}s`\rangle`{=tex}), clog-time fraction, throughput
and evacuation time, as applicable. A lower ADE/FDE does not guarantee a
lower clogging rate.

**U4. Test interaction modelling before testing robots.**\
If feasible, compare SFM and an MAIRL-inspired or reproduced MAIRL model
under the same geometry, population and initial conditions. Record
whether the model changes the approach to the exit, queue structure, and
crowd-level outcomes. Treat this as a separate experiment.

**U5. Use real-world data cautiously.**\
Speedway is a busy campus intersection, not our specific exit
bottleneck. Validate transfer to the exit geometry instead of assuming
the learned behaviour generalises unchanged.

**U6. Keep robot effects as a hypothesis.**\
After validating the pedestrian model, compare baseline / pillars /
robots using controlled simulations and repeated runs. The paper
provides no robot-at-the-exit experiment and cannot support a claim that
a robot prevents arch formation.

**U7. Record the computational trade-off.**\
If implementing the approach, document the approximation, solver,
features and any accuracy loss. Do not call a simplified custom model
"the paper's MAIRL" unless the relevant formulation has actually been
reproduced.

------------------------------------------------------------------------

## 6. Tested code / implementation notes

The paper describes a full multi-agent learning and game-theoretic
planning pipeline, rather than a small standalone clog-statistics
function. It links code and data at `mairl-uva.github.io`.

Before attempting reuse:

``` text
1. Check whether the linked repository is currently available.
2. Read the repository README and licence.
3. Record Python / framework versions and required datasets.
4. Reproduce a paper benchmark before changing the environment.
5. Only then consider adapting the model to the exit simulation.
```

Do **not** present an untested pseudocode sketch as a working MAIRL
implementation. The following is only a high-level integration sketch:

``` text
observed or simulated trajectories
              |
              v
      prepare agent states
              |
              v
 learn / configure interaction model
              |
              v
 generate pedestrian trajectories
              |
              v
 validate trajectory behaviour
              |
              v
 run bottleneck scenarios
              |
              v
 calculate clogging metrics separately
```

For our project, `anushka1` remains the reference for operational
clogging statistics. `anushka3` may inform the behaviour model that
produces the trajectories those statistics are calculated from.

------------------------------------------------------------------------

## 7. What not to claim

-   The paper **does not test robots or pillars placed at exits**.
-   It does not establish that MAIRL prevents arches, reduces clog
    duration, or increases evacuation throughput.
-   The Speedway data come from a **busy campus intersection**, not an
    emergency evacuation bottleneck.
-   ADE/FDE/EFE are trajectory-prediction metrics; they are not
    interchangeable with (W), (`\alpha`{=tex}), burst size, throughput,
    or evacuation time.
-   Better trajectory prediction does not automatically imply better
    prediction of rare collective events such as stable arches or long
    clogs.
-   Do not claim that our SFM has been replaced by MAIRL unless we
    implement and validate that change.
-   Do not quote benchmark results as results from our own simulation.

------------------------------------------------------------------------

## 8. TODO

-   [ ] Read the paper's method section and document the exact
    tractability--rationality trade-off.
-   [ ] Check `mairl-uva.github.io` for the current code, datasets,
    dependencies and licence.
-   [ ] Decide whether our project needs improved individual trajectory
    prediction, crowd-level dynamics, or both.
-   [ ] Keep the current SFM configuration as a reproducible baseline.
-   [ ] Define how any alternative model will be validated before using
    it for intervention claims.
-   [ ] If data are available, evaluate trajectory predictions using
    appropriate metrics such as ADE/FDE.
-   [ ] Continue evaluating bottleneck outcomes with the project metrics
    from `anushka1`; do not substitute ADE/FDE for clogging statistics.
-   [ ] Compare baseline / pillars / robots with controlled settings and
    multiple simulation seeds.
-   [ ] Report the transfer limitation: campus-intersection behaviour is
    not the same as exit-bottleneck behaviour.
