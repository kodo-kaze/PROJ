The key finding is that crowd behavior changes with density: below about **0.75 persons/m²** the crowd is in the free regime, between **0.75 and 1.80 persons/m²** it is slow-moving, and above **1.80 persons/m²** it becomes jammed. The paper also finds that speed is much more strongly related to **step length** than step frequency, and that synchronization becomes most likely around the jamming/maximum-flow density.

## 1. Algorithm 
**Density-Aware Asynchronous Social Force Crowd Algorithm (DA-ASFC)**

The simulation maintains, for every pedestrian:

```text
position
velocity
mass
body_radius
desired_direction
next_step_time
step_cycle
step_duration
```

Instead of updating every pedestrian with the exact same `dt`, each pedestrian gets a **density-dependent step cycle**. This is one of the most important improvements suggested by the paper: real pedestrians do not necessarily begin their steps simultaneously, and their step timing changes with the environment.

---

# 2. Overall architecture

```text
                   ┌───────────────────┐
                   │  Initialize Crowd │
                   └─────────┬─────────┘
                             │
                             ▼
                ┌─────────────────────────┐
                │ Spatial Neighbor Search │
                │   + Local Density       │
                └────────────┬────────────┘
                             │
                             ▼
                 ┌──────────────────────┐
                 │ Determine Regime     │
                 │ FREE / SLOW / JAMMED │
                 └───────────┬──────────┘
                             │
             ┌───────────────┼────────────────┐
             ▼               ▼                ▼
       Desired speed    Step parameters   Direction
       from density     from density      from target
             │               │                │
             └───────────────┼────────────────┘
                             ▼
                  ┌────────────────────┐
                  │ Social Force Model │
                  │                    │
                  │ target force       │
                  │ pedestrian force   │
                  │ wall force         │
                  └──────────┬─────────┘
                             │
                             ▼
                 ┌───────────────────────┐
                 │ Asynchronous Step     │
                 │ Position Update       │
                 └───────────┬───────────┘
                             │
                             ▼
                 ┌───────────────────────┐
                 │ Collision / Safety    │
                 │ / Bottleneck Handling │
                 └───────────┬───────────┘
                             │
                             ▼
                       Next event
```

---

# 3. Step 1 — Calculate local density

For pedestrian `p`, don't use the **global** crowd density.

Calculate a **local density** around that pedestrian.

The paper uses a Voronoi-based representation to identify nearby pedestrians and their spatial relationships.

A practical implementation is:

```text
density[p] = number of pedestrians in local region
             / area of local region
```

An even better implementation is Voronoi density:

```text
ρp = 1 / VoronoiArea(p)
```

Then:

```text
ηp = 1 / ρp
```

where `η` is the **specific volume**.

This is important because the paper expresses several behavioral relationships using specific volume.

---

# 4. Step 2 — Determine crowd regime

Use the experimentally observed thresholds:

```text
ρ < 0.75              → FREE

0.75 ≤ ρ < 1.80       → SLOW MOVING

ρ ≥ 1.80              → JAMMED
```

These are directly supported by the paper.

The interpretation is:

|Regime|Behavior|
|---|---|
|Free|speed and direction essentially unconstrained|
|Slow-moving|speed decreases but direction remains largely unaffected|
|Jammed|both speed and direction become constrained|

This gives our algorithm an explicit **state machine**.

---

# 5. Step 3 — Density-dependent desired speed

This is one of the strongest pieces of the paper.

The paper obtained:

[  
v_0 =  
1.16\left[1-e^{-4.45(\eta-0.23)}\right]  
]

where

[  
\eta=\frac{1}{\rho}  
]

So:

```python
eta = 1.0 / density

desired_speed = 1.16 * (
    1 - exp(-4.45 * (eta - 0.23))
)
```

The paper reports an excellent fit for this relationship (`R² = 0.99`).

At low density, this approaches the free walking speed of about:

```text
1.16 m/s
```

As density increases, desired speed decreases.

This is much better than giving everyone a fixed desired velocity.

---

# 6. Step 4 — Calculate natural step parameters

This is where I would make your algorithm different from a basic Social Force Model.

The paper models:

### Step duration

[  
T_s =  
0.62[1-e^{-7.79(\eta-0.16)}]  
]

### Step-cycle duration

[  
T_{sc}=3.19e^{-6.49\eta}+0.56  
]

These relationships were fitted from the experimental footstep data.

Therefore:

```python
Ts  = 0.62 * (1 - exp(-7.79 * (eta - 0.16)))
Tsc = 3.19 * exp(-6.49 * eta) + 0.56
```

Then:

```text
step_frequency = 1 / Tsc
```

The paper explicitly distinguishes **step duration** from **step-cycle duration**; they are not the same quantity.

---

# 7. Step 5 — Pure foot-motion duration

The paper doesn't always allow the pedestrian to move for the whole step cycle.

It uses:

[  
\Delta t =  
\begin{cases}  
T_s & \eta>0.16,;T_s\le T_{sc}\  
T_{sc} & \eta>0.16,;T_s>T_{sc}\  
0.01 & \eta\le0.16  
\end{cases}  
]

The last case prevents the simulation from becoming completely deadlocked.

Implementation:

```python
if eta > 0.16:
    dt_step = min(Ts, Tsc)
else:
    dt_step = 0.01
```

---

# 8. Step 6 — Social Force calculation

Now we use the Social Force Model.

For pedestrian `p`:

# [  
m_p\frac{dv_p}{dt}

m_p\frac{v^0_p e^0_p-v_p}{\tau_p}  
+  
\sum_q f_{pq}  
+  
\sum_w f_{pw}  
]

The first term drives the pedestrian toward their desired velocity.

The second handles other pedestrians.

The third handles walls/boundaries.

### Desired-motion force

```text
F_desired =
m * (desired_velocity - velocity) / tau
```

---

# 9. Pedestrian-pedestrian interaction

The paper uses three components:

```text
psychological repulsion
+
body/contact force
+
sliding friction
```

The interaction force is:

# [  
f_{pq}

A_p e^{(r_{pq}-d_{pq})/B_p}n_{pq}  
+  
kg(r_{pq}-d_{pq})n_{pq}  
+  
\kappa g(r_{pq}-d_{pq})  
\Delta v^t_{qp}t_{pq}  
]

where:

```text
rpq = rp + rq
dpq = distance between pedestrian centers
npq = normalized vector between them
```

The contact terms activate only when:

[  
d_{pq}<r_{pq}  
]

according to the paper.

The calibrated parameters reported by the study are:

```text
A  = 42.17 N
B  = 0.073 m
k  = 95129.84 kg/s²
κ  = 285066.61 kg/(m·s)
τ  = 0.31 s
```

These were calibrated using differential evolution against the experimental trajectories.

---

# 10. Wall/boundary force

For every nearby wall:

```text
distance_to_wall
normal_direction
tangential_direction
```

calculate:

# [  
f_{pw}

A_p e^{(r_p-d_{pw})/B_p}n_{pw}  
+  
kg(r_p-d_{pw})n_{pw}  
+  
\kappa g(r_p-d_{pw})  
(v_p\cdot t_{pw})t_{pw}  
]

This prevents pedestrians from walking through walls and models physical contact with the boundary.

---

# 11. The important improvement: asynchronous updates

A conventional implementation would do:

```text
for every pedestrian:
    update position

wait dt

repeat
```

I would **not** do this.

Instead:

```text
P1 next step = 0.43 s
P2 next step = 0.51 s
P3 next step = 0.39 s
P4 next step = 0.63 s
...
```

Use a priority queue:

```text
event = pedestrian whose next_step_time is smallest
```

Then update only that pedestrian.

The paper explicitly argues that asynchronous position updates and dynamic time steps are more representative of real pedestrian movement than synchronized updates.

This is probably the **single most important algorithmic improvement** you should include in your project.

---

# 12. Position update

For pedestrian `p`:

# [  
r_p(t_{n+1,s})

r_p(t_{n,s})  
+  
\Delta t,v_p(t_{n,s})  
]

from the paper's Eq. 13.

So:

```python
new_position = position + velocity * dt_step
```

Then:

```python
next_step_time = current_step_time + Tsc
```

according to Eq. 14.

---

# 13. Add synchronization near jamming

This is another interesting feature from the paper.

At approximately:

[  
\rho = 1.80;persons/m^2  
]

pedestrian synchronization is most likely.

The paper also finds synchronization is more likely with **pedestrians in front** than those beside the pedestrian.

So introduce a small step-phase coupling:

```python
if 1.6 <= density <= 2.0:

    front_neighbor = find_front_neighbor(p)

    if front_neighbor exists:

        phase_error = (
            front_neighbor.next_step_time
            - p.next_step_time
        )

        p.next_step_time +=
            synchronization_gain * phase_error
```

Don't make this too strong.

The goal isn't:

```text
EVERYONE WALK IN SYNCHRONY
```

It is:

```text
crowd naturally becomes more synchronized
around the onset of jamming
```

That is what the paper actually observes.

The physical motivation is particularly useful: at approximately `1.80 persons/m²`, the measured buffer distance reaches a minimum of roughly `0.22 m`, increasing collision risk. Synchronizing steps can simultaneously preserve stepping motion and reduce collision risk.

---

# 14. Complete algorithm

Here is the version I would actually implement.

```text
ALGORITHM DA-ASFC

INPUT:
    environment
    walls
    target / exit
    N pedestrians

FOR each pedestrian p:

    initialize:
        position[p]
        velocity[p]
        mass[p]
        radius[p]
        desired_direction[p]
        random step phase

        next_step_time[p] = random()

INSERT all pedestrians into event priority queue


WHILE pedestrians remain in environment:

    p = pedestrian with smallest next_step_time

    t = next_step_time[p]


    ------------------------------------------------
    1. LOCAL PERCEPTION
    ------------------------------------------------

    neighbors = spatial_query(position[p])

    density[p] = estimate_local_density(p, neighbors)

    eta = 1 / density[p]


    ------------------------------------------------
    2. DETERMINE CROWD REGIME
    ------------------------------------------------

    IF density[p] < 0.75:
        regime = FREE

    ELSE IF density[p] < 1.80:
        regime = SLOW_MOVING

    ELSE:
        regime = JAMMED


    ------------------------------------------------
    3. DESIRED SPEED
    ------------------------------------------------

    desired_speed =
        1.16 * (
            1 - exp(-4.45 * (eta - 0.23))
        )

    desired_velocity =
        desired_speed * desired_direction[p]


    ------------------------------------------------
    4. STEP PARAMETERS
    ------------------------------------------------

    Ts =
        0.62 * (
            1 - exp(-7.79 * (eta - 0.16))
        )

    Tsc =
        3.19 * exp(-6.49 * eta) + 0.56

    dt_step =
        min(Ts, Tsc)

    IF eta <= 0.16:
        dt_step = 0.01


    ------------------------------------------------
    5. SOCIAL FORCE
    ------------------------------------------------

    F =

        m[p] *
        (
            desired_velocity - velocity[p]
        ) / 0.31


    FOR each neighbor q:

        d = distance(p, q)

        r = radius[p] + radius[q]

        n = direction(q -> p)

        psychological_force =
            42.17 *
            exp((r-d)/0.073) *
            n

        IF d < r:

            contact_force =
                95129.84 *
                (r-d) *
                n

            friction_force =
                285066.61 *
                (r-d) *
                tangential_velocity_difference *
                tangent(n)

        ELSE:

            contact_force = 0
            friction_force = 0

        F += psychological_force
        F += contact_force
        F += friction_force


    ------------------------------------------------
    6. WALL FORCES
    ------------------------------------------------

    FOR each nearby wall:

        calculate wall distance

        calculate normal force

        calculate tangential friction

        F += wall_force


    ------------------------------------------------
    7. UPDATE VELOCITY
    ------------------------------------------------

    acceleration = F / mass[p]

    velocity[p] =
        velocity[p] +
        acceleration * dt_step


    ------------------------------------------------
    8. OPTIONAL SYNCHRONIZATION
    ------------------------------------------------

    IF density[p] approximately 1.80:

        q = nearest_front_neighbor(p)

        IF q exists:

            phase_error =
                next_step_time[q]
                - next_step_time[p]

            next_step_time[p] +=
                synchronization_gain
                * phase_error


    ------------------------------------------------
    9. UPDATE POSITION
    ------------------------------------------------

    position[p] =
        position[p] +
        velocity[p] * dt_step


    ------------------------------------------------
    10. COLLISION CORRECTION
    ------------------------------------------------

    resolve_remaining_overlaps()

    enforce_boundary_constraints()


    ------------------------------------------------
    11. SCHEDULE NEXT STEP
    ------------------------------------------------

    next_step_time[p] =
        t + Tsc

    push(p, next_step_time[p])


    ------------------------------------------------
    12. EXIT / BOTTLENECK
    ------------------------------------------------

    IF pedestrian reaches target:

        remove pedestrian


OUTPUT:
    pedestrian trajectories
    density map
    velocity map
    flow rate
    congestion regions
    synchronization level
```

## 15. Why this should perform well

The advantage is that we're not inventing arbitrary rules. We're translating the paper's experimentally observed behavior directly into the model:

**Density → behavior**

```text
density
   ↓
specific volume
   ↓
crowd regime
   ↓
desired speed
   ↓
step duration / step frequency
   ↓
social interactions
   ↓
asynchronous movement
```

The paper reports that this exact combination of a calibrated Social Force model plus the proposed position-update scheme reproduced the experimental **free, slow-moving, and jammed regimes**, and also reproduced the peak in synchronization near the jamming density.

That is considerably stronger than merely saying “we use a Social Force Model.”

---

## 16. One thing I would improve beyond the paper

For your project, I would add a **spatial grid / KD-tree** for neighbor search.

Naively:

```text
every pedestrian checks every other pedestrian
```

gives:

[  
O(N^2)  
]

per update.

Instead:

```text
environment
      ↓
uniform spatial grid
      ↓
only inspect nearby cells
      ↓
nearby pedestrians
```

This gives approximately:

[  
O(Nk)  
]

where `k` is the number of nearby pedestrians, which is much better for large crowds.

So the final architecture becomes:

```text
              ┌───────────────┐
              │ Spatial Grid  │
              └───────┬───────┘
                      │
                      ▼
┌───────────┐   ┌───────────────┐
│ Pedestrian│──►│ Local Density │
└───────────┘   └───────┬───────┘
                        │
                        ▼
                 ┌─────────────┐
                 │ Crowd State │
                 └──────┬──────┘
                        │
          ┌─────────────┼──────────────┐
          ▼             ▼              ▼
     Desired Speed   Step Timing   Direction
          │             │              │
          └─────────────┼──────────────┘
                        ▼
                ┌───────────────┐
                │ Social Forces │
                └───────┬───────┘
                        ▼
                ┌───────────────┐
                │ Async Update  │
                └───────┬───────┘
                        ▼
                ┌───────────────┐
                │ Event Queue   │
                └───────────────┘
```

### The important caveat

The paper itself says its experiment mainly studied **unidirectional** pedestrian motion and did not investigate several phenomena such as lane formation, stripe formation, arching, and clogging. It also notes that the participants were young, so the exact density thresholds may change for other populations.

So I would present your algorithm as:

> **A density-aware asynchronous pedestrian crowd model based on the experimentally calibrated Social Force Model and footstep dynamics proposed by Ma et al.**

rather than claiming that it is a universally accurate crowd model.

This gives you a very solid project: **Social Force Model + density-dependent behavior + asynchronous natural steps + bottleneck handling + synchronization + efficient spatial neighbor search.**