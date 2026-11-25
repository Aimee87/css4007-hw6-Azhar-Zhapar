# HW6 submission

## AI-use declaration

I used Microsoft Copilot to better understand the assignment requirements, get a grasp of value iteration, and verify the steps in my calculations.

## Setup and reproduction



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

## Part C — Custom solver

### Compare code with paper

Show V1 and the requested V2 values. Explain discrepancies.

### Values, policies and convergence

Link both value/policy maps. Record iteration counts, final value changes and tie-breaking rule.

### Simulation results

| Map | Batch seed | Goals /50 | Holes /50 | Time limits /50 | Mean steps | Mean discounted return |
| --- | --- | --- | --- | --- | --- | --- |

### Explain your results

Which values/actions changed with sand and water? Where did cost and risk matter? How do simulations relate to the computed values?

### Rule checks

Show boundary/wall behavior, slip probabilities, floor rewards and terminal handling.

