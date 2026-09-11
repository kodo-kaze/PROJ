```
Algorithm DA-ASFC

1. Initialize pedestrians P₁...Pₙ
2. Build Spatial Grid / KD-Tree
3. Assign each pedestrian an initial event time

4. while simulation_time < T:

      a. Pop pedestrian i with the earliest event time

      b. Find nearby pedestrians using Spatial Grid / KD-Tree

      c. Calculate local density:
             Aᵢ = VoronoiArea(i)
             ρᵢ = 1 / Aᵢ
             ηᵢ = Aᵢ

      d. Determine crowd regime:
             if ρᵢ < 0.75:
                    Regime = FREE
             else if ρᵢ < 1.80:
                    Regime = SLOW
             else:
                    Regime = JAMMED

      e. Calculate desired speed:
             v₀ᵢ = 1.16 ×
                   [1 − exp(−4.45(ηᵢ − 0.23))]

      f. Calculate step timing:
             Tsᵢ  = 0.62 ×
                    [1 − exp(−7.79(ηᵢ − 0.16))]

             Tscᵢ = 3.19 × exp(−6.49ηᵢ) + 0.56

             dtᵢ = min(Tsᵢ, Tscᵢ)

      g. Compute social force:
             Fᵢ = Fdesired
                 + Frepulsion
                 + Fcontact
                 + Fwall

      h. Update pedestrian:
             aᵢ = Fᵢ / mᵢ
             Vᵢ = Vᵢ + aᵢ × dtᵢ
             Pᵢ = Pᵢ + Vᵢ × dtᵢ

      i. Apply collision and boundary constraints

      j. Rebuild/update local spatial information

      k. Schedule pedestrian i's next event:
             event_timeᵢ =
                 event_timeᵢ + dtᵢ

5. Return pedestrian trajectories,
   density and crowd-regime information.
```

Absolutely bro 😄 Let's make the whole **DA-ASFC algorithm** super simple.

## 🧠 What is DA-ASFC doing?

Imagine **100 people walking through a corridor**.

Our algorithm repeatedly asks:

> **"How crowded is this person's area, how fast should they walk, when should they take their next step, and are other people/walls pushing them?"**

Then we move that person and repeat.

---

## Step-by-step

### 1. Start with people

We have:

```text
P1   P2   P3   P4   P5
→    →    →    →    →
```

For every person, we know:

- Position
- Velocity
- Mass
- Walking direction

---

### 2. Find nearby people

We don't compare every person with everyone.

Instead:

```text
        Spatial Grid
┌─────┬─────┬─────┐
│     │ P1  │     │
├─────┼─────┼─────┤
│ P2  │ P3  │ P4  │
├─────┼─────┼─────┤
│     │ P5  │     │
└─────┴─────┴─────┘
```

We only look at **nearby pedestrians**.

This makes the simulation faster.

---

### 3. Calculate local density

For person `P3`, calculate their nearby space using the **Voronoi area**.

```text
        P2
      ┌─────┐
      │     │
 P1   │ P3  │   P4
      │     │
      └─────┘
```

If P3 has a **large area**, they have lots of space.

If P3 has a **small area**, they are crowded.

Formula:

```text
ρ = 1 / VoronoiArea
```

So:

```text
Large area → Low density
Small area → High density
```

---

### 4. Decide the crowd condition

Now we check the density.

```text
ρ < 0.75       → FREE
0.75 ≤ ρ < 1.80 → SLOW
ρ ≥ 1.80       → JAMMED
```

Think of it like a traffic light:

```text
FREE       → 🟢 People move normally
SLOW       → 🟡 People slow down
JAMMED     → 🔴 Movement becomes restricted
```

---

### 5. Calculate walking speed

Now we calculate how fast that person **wants** to walk.

```text
η = 1 / ρ

v₀ = 1.16 × [1 − e⁻⁴·⁴⁵(η−0.23)]
```

You don't need to memorize what the equation is doing.

The important idea is:

```text
More space
   ↓
Higher desired speed

Less space
   ↓
Lower desired speed
```

---

### 6. Decide when the person takes their next step ⭐

This is the special part.

Normally, a simulation might do:

```text
Update everyone
      ↓
   wait 0.1s
      ↓
Update everyone
      ↓
   wait 0.1s
```

Everyone moves together.

DA-ASFC instead gives people **individual step timings**.

For example:

```text
P1 → next step at 0.43 s
P2 → next step at 0.51 s
P3 → next step at 0.39 s
P4 → next step at 0.63 s
```

So:

```text
0.39s → P3 moves
0.43s → P1 moves
0.51s → P2 moves
0.63s → P4 moves
```

That's **asynchronous movement**.

---

### 7. Calculate forces

When P3 moves, we calculate the forces acting on them.

```text
              Wall
               ↓
        ↗  P3  ↖
       P2       P4
               ↓
            Direction
```

Forces include:

```text
Desired walking force
        +
Repulsion from people
        +
Contact/friction
        +
Wall force
```

So:

```text
F = Fdesired + Frepulsion + Fcontact + Fwall
```

---

### 8. Update the person's movement

From the total force:

```text
acceleration = F / mass
```

Then:

```text
velocity = velocity + acceleration × dt

position = position + velocity × dt
```

So basically:

```text
Forces
  ↓
Acceleration
  ↓
Velocity
  ↓
Position
```

---

### 9. Check collisions and walls

After moving the person, we check:

```text
Did they hit another person?
Did they cross the wall?
Did they leave the corridor?
```

If yes → correct their movement.

---

### 10. Schedule their next step

Finally:

```text
P3 moved
 ↓
Calculate next step time
 ↓
Put P3 back into event queue
```

Then the algorithm picks whoever has the **earliest next event**.

---

# 🔥 Very simple example

Suppose we have:

```text
P1 → P2 → P3
```

Initially:

```text
P1 next step = 0.50s
P2 next step = 0.35s
P3 next step = 0.70s
```

The algorithm chooses:

```text
0.35s → P2
```

Calculate P2's:

```text
Density
   ↓
Regime
   ↓
Speed
   ↓
Forces
   ↓
New position
```

Then schedule P2 again:

```text
P2 next step = 0.82s
```

Queue now:

```text
P1 → 0.50
P3 → 0.70
P2 → 0.82
```

Next:

```text
0.50 → P1
```

Then:

```text
0.70 → P3
```

And so on.

---

# 🎯 In one super-simple diagram

```text
        PEOPLE
           ↓
    Find nearby people
           ↓
    Calculate density
           ↓
    FREE / SLOW / JAMMED
           ↓
    Calculate desired speed
           ↓
    Calculate step timing
           ↓
     Calculate forces
           ↓
   Move ONE pedestrian
           ↓
 Check collision / walls
           ↓
 Schedule next step
           ↓
      Event Queue
           ↓
   Pick next pedestrian
           ↺
```

### The main idea to remember

**DA-ASFC is basically a crowd simulator where each person:**

> **looks at their local crowd → decides how fast to move → decides when to step → feels forces from people/walls → moves → schedules their next step.**

And the **⭐ key novelty** is that pedestrians don't all update at the same time; their movement is handled through an **asynchronous event queue**.
