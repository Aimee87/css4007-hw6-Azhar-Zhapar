# HW6 submission

## AI-use declaration

I used Microsoft Copilot to better understand the assignment requirements, get a grasp of value iteration, and verify the steps in my calculations.

### Setup and reproduction

Install dependencies:

```bash
pip install gymnasium pandas
```

Gymnasium version used:

```text
gymnasium 1.4.0
```

Run Part B:

```bash
jupyter notebook "Part B.ipynb"
```

Run Part C:

```bash
jupyter notebook "Part C.ipynb"
```

Outputs are saved in the `outputs/` folder.

## Part A — Paper calculations

The handwritten calculations for Part A can be found in the "PartA" folder. It contains V1, V2, arrows, an analysis of draws, and an explanation regarding the two iterations.

## Part B — FrozenLake

### Fixed policy, written before the experiments

The fixed policy was designed to follow a safe path to the goal while avoiding holes whenever possible.

State 0 → Right

State 1 → Right

State 2 → Down

State 3 → Left

State 4 → Left

State 6 → Down

State 8 → Up

State 9 → Right

State 10 → Down

State 13 → Right

State 14 → Right

The main path is:

0 → 1 → 2 → 6 → 10 → 14 → 15 (Goal)

Actions for the remaining nonterminal states were chosen to move the agent toward the goal and away from holes.

### Results

| Slippery? | Policy | Batch seed | Goals /50 | Holes /50 | Time limits /50 | Success % | Mean steps |
|-----------|---------|------------|-----------|-----------|-----------------|-----------|------------|
| False | random | 11 | 0 | 50 | 0 | 0.0 | 6.84 |
| False | random | 22 | 0 | 50 | 0 | 0.0 | 8.80 |
| False | random | 33 | 2 | 48 | 0 | 4.0 | 8.64 |
| False | fixed | 11 | 50 | 0 | 0 | 100.0 | 6.00 |
| False | fixed | 22 | 50 | 0 | 0 | 100.0 | 6.00 |
| False | fixed | 33 | 50 | 0 | 0 | 100.0 | 6.00 |
| True | random | 11 | 1 | 49 | 0 | 2.0 | 8.04 |
| True | random | 22 | 0 | 50 | 0 | 0.0 | 6.98 |
| True | random | 33 | 0 | 50 | 0 | 0.0 | 7.04 |
| True | fixed | 11 | 3 | 47 | 0 | 6.0 | 8.76 |
| True | fixed | 22 | 3 | 47 | 0 | 6.0 | 7.60 |
| True | fixed | 33 | 2 | 48 | 0 | 4.0 | 8.62 |

### Analysis

When is_slippery=False, the fixed policy worked very well. It reached the goal in every episode. This happened because the agent always moved in the chosen direction, so it could follow the planned path to the goal without making mistakes.

The random policy had much worse results. Most episodes ended in holes because the actions were chosen randomly. Sometimes the agent moved closer to the goal, but most of the time it could not find a safe path.

When is_slippery=True, the results became worse for both policies. The agent could slip and move in a different direction than expected. Because of this, even the fixed policy often left the planned path and fell into a hole before reaching the goal.

The results from the three batches were similar. There were some small differences, but the overall pattern stayed the same. This shows that the results are fairly consistent and not caused by just one lucky or unlucky batch.

One successful episode is not enough to show that a policy is good. A policy can succeed once because of luck. Running many episodes gives a more reliable picture of how the policy usually performs.

These experiments are not training. The policies stay the same during all runs. The agent does not learn from rewards or past episodes. We only test and compare the performance of the policies.

Successful trace example:

0 → 1 → 2 → 6 → 10 → 14 → 15 (Goal)

Failed trace example:

0 → 4 → 5 (Hole)

## Part C — Custom solver

### Compare code with paper

The solver matched my paper calculations.

V1:
| -1 | -1 | 10 | G |
|----|----|----|----|
| -1 | # | -1 | H |
| -1 | -1 | -1 | -1 |

Requested V2 values:

- V2(1,2) = 8.00
- V2(1,3) = 10.00
- V2(2,3) = 5.21

No discrepancies were found between the code results and the paper calculations.

### Values, policies and convergence
The resulting value maps and extracted policies for both environments are shown below.

Original map:
- Iterations: 12
- Final value change: 2.1652931048166124e-07

Extended map:
- Iterations: 11
- Final value change: 2.405881232014906e-07

Tie-breaking rule:
If multiple actions had the same value, the first action encountered in the order U, R, D, L was selected.

**Value Map (Original)**

```text
 4.20   8.00  10.00   0.00
 2.78   #####  5.82   0.00
 1.54   2.82   4.24   2.82
```

**Policy Map (Original)**

```text
→   →   →   G
↑   #   ↑   H
→   →   ↑   ←
```

**Value Map (Extended)**

```text
 4.20   8.00  10.00   0.00
 2.78   #####  5.82   0.00
 1.50   2.82   4.24   2.82
```

**Policy Map (Extended)**

```text
→   →   →   G
↑   #   ↑   H
↑   →   ↑   ←
```

### Simulation Results

| Map | Batch seed | Goals /50 | Holes /50 | Time limits /50 | Mean steps | Mean discounted return |
|------|------|------|------|------|------|------|
| Original | 11 | 50 | 0 | 0 | 5.00 | 1.50 |
| Original | 22 | 50 | 0 | 0 | 5.00 | 1.50 |
| Original | 33 | 50 | 0 | 0 | 5.00 | 1.50 |
| Extended | 11 | 50 | 0 | 0 | 5.00 | 1.50 |
| Extended | 22 | 50 | 0 | 0 | 5.00 | 1.50 |
| Extended | 33 | 50 | 0 | 0 | 5.00 | 1.50 |

### Explain your results

The policy reached the goal in all episodes on both maps. There were no holes and no time limits.

Adding sand and water changed the values only a little. The value of one state changed from 1.54 to 1.50 because the new tiles have extra cost.

The policy also changed at one position. In the original map the agent moved right, but in the extended map it moved up. This shows that tile costs can affect the best action.

The ice tile is important because movement is not always predictable. The agent can slip and move in a different direction.

The simulation results are similar to the value iteration results, but they are not exactly the same. Value iteration calculates expected future rewards, while simulations show what happened in a limited number of episodes.

### Rule Checks

**Boundary behavior**

- Moving Up from (0,0) keeps the agent at (0,0).
- Moving Left from (0,0) keeps the agent at (0,0).

**Wall behavior**

- Moving Right from (1,0) tries to enter the wall and the agent stays in the same state.

**Ice probabilities**

- Forward = 0.8
- Side direction = 0.1
- Side direction = 0.1
- Total probability = 1.0

**Floor rewards**

- Ordinary floor = -1
- Mud = -3
- Sand = -2
- Water = -5
- Goal = +10
- Hole = -10

**Terminal handling**

- Goal and Hole are terminal states.
- No additional rewards are received after reaching Goal or Hole.
