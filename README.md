# HW6 — Grid World: calculate, simulate, solve

**CSS-4007 · Artificial Intelligence · Fall 2026**

Fork [ardakshalkar/css4007-hw6](https://github.com/ardakshalkar/css4007-hw6), keep your fork public as in earlier homeworks, and work in your fork. Do not open a pull request to the starter repository.

Reference: [Berkeley CS188 Lecture 8 — MDPs I](https://inst.eecs.berkeley.edu/~cs188/fa26/assets/lectures/cs188-fa26-lec08.pdf), discussed in class.

| Part | What you do |
| --- | --- |
| **A — Easy** | Calculate Grid World values on paper. |
| **B — Medium** | Run repeated FrozenLake simulations in Gymnasium. |
| **C — Hard** | Code a solver for Part A, then add more floor types. |

As in earlier homeworks, explain your decisions and use your own results. The starter supplies tasks, maps and an empty `SUBMISSION.md`; you write the programs. Finish Part A before coding. Save a photo/scan and commit it before your first program. Write the other answers in `SUBMISSION.md`, save run results in `outputs/`, and include setup/run commands. Declare which AI tools you used and for what, following HW4.

## Part A — Easy: Calculate on paper

This is a constructed map for this assignment. Rows and columns start at 1.

```text
             Column
          1   2   3   4
        +---+---+---+---+
Row 1   | . | M | . | G |
        +---+---+---+---+
Row 2   | . | # | I | H |
        +---+---+---+---+
Row 3   | S | . | . | . |
        +---+---+---+---+
```

| Tile | Meaning | Reward when a move ends here |
| --- | --- | ---: |
| `S` | Start; otherwise ordinary floor | -1 |
| `.` | Ordinary floor | -1 |
| `M` | Mud; expensive to enter | -3 |
| `I` | Ice; movement from here is uncertain | -1 |
| `G` | Goal; ends the episode | +10 |
| `H` | Hole; ends the episode | -10 |
| `#` | Wall; cannot be entered | — |

**Rules**

- Choose up, right, down or left at any nonterminal tile. On `S`, `.` and `M`, the chosen direction happens with probability 1.
- When **starting a move on ice**, move in the chosen direction with probability 0.8, or in either perpendicular direction with probability 0.1 each. Slipping depends on the tile you leave. These are our map's probabilities, not FrozenLake's defaults.
- Moving into a wall or outside the map leaves you in place. You still receive the reward of the tile where the move ends. Apply this separately to each possible slip direction.
- Entering `G` gives +10 without an additional floor cost. Goal and hole values stay zero and give no further rewards after termination. A wall is not a state.
- Use discount factor **0.9**, starting with **V0 = 0** everywhere.

**What to write**

1. Identify the states, actions, transitions and rewards in your own words. What is the agent trying to maximize?
2. Calculate **V1 for every nonterminal tile** and put the values into a copy of the map.
3. Using only V1, calculate **V2 at (1,2), (1,3) and (2,3)**. Show all four action calculations at each position, then choose the largest. At the ice tile, show the probability-weighted sum.
4. Draw a best-action arrow at those three positions; list all tied actions. Why do two rounds alone not prove that you have the final optimal policy?

Use the lecture's update:

`V(k+1,s) = max over a of sum over s' [P(s'|s,a) × (R(s,a,s') + 0.9 × V(k,s'))]`.

Use the previous round's values throughout a round. Show your calculations; a filled-in map alone is insufficient.

## Part B — Medium: Simulate FrozenLake with Gymnasium

Read the official [FrozenLake documentation](https://gymnasium.farama.org/environments/toy_text/frozen_lake/) and [environment API](https://gymnasium.farama.org/api/env/). Install Gymnasium in a virtual environment and record the versions you actually use. No API key is needed.

Use `FrozenLake-v1`, `map_name="4x4"`, and its default rewards:

```text
SFFF
FHFH
FFFH
HFFG
```

Here `F` is frozen floor, `H` a hole, and `G` the goal. This is a separate environment from Part A: keep its default rules and rewards.

1. Write an episode runner using `reset()` and `step()`. Stop on `terminated` or `truncated`, with a **100-step limit**. Save one episode using text rendering.
2. Compare **uniformly random actions** with **a fixed policy you design from the map**. Write one action for every nonterminal state before running your fixed policy. Keep it unchanged across experiments; it can react to the current state.
3. Run both policies with **`is_slippery=False`** and **`is_slippery=True`**: four conditions. Do not customize rewards or slip probabilities.
4. For each condition, run **three batches of 50 episodes**, with batch seeds **11, 22, 33**. Reset episode `i` with seed `1000 × batch_seed + i`, for `i = 0..49`. Seed your random action generator too. Use the same episode seed lists in all four conditions.
5. Save one row per episode: condition, batch seed, episode seed, policy, steps, total reward, and outcome (goal, hole or time limit). Use the final state to distinguish a goal from a hole. If termination occurs on the last allowed step, record the terminal outcome.

Summarize each batch separately:

| Slippery? | Policy | Batch seed | Goals /50 | Holes /50 | Time limits /50 | Success % | Mean steps |
| --- | --- | --- | --- | --- | --- | --- | --- |

Explain what slipping changed, how consistent the batches were, and why one successful episode is insufficient evidence. Include one successful and one failed trace if observed; if an outcome never occurred, say so. Explain why these fixed-policy simulations are not training.

## Part C — Hard: Write a solver and add floors

1. Implement **the exact Part A map and rules first**. Write value iteration yourself using the known transition model. Print V1 and the three requested V2 values. Compare with your paper work; explain and correct discrepancies.
2. Continue until the largest absolute change in a full update is below **0.000001**, with a safeguard of **10,000 iterations**. Report the iteration count and final change; report nonconvergence if the safeguard is reached. Extract a policy by comparing actions using the final values. Print value maps and policy arrows; state how you break ties.
3. Then solve this extended map, keeping the same start, goal, discount and original tile rules:

```text
.M.G
.#IH
SD.W
```

New floors: **`D` = sand, reward -2**, and **`W` = shallow water, reward -5**. Both are traversable; moves from them are deterministic. Store floor rules separately from the solver so adding a floor does not require rewriting value iteration.

4. Simulate the computed policy on each map for **three batches of 50 episodes**, with the Part B seed scheme and a 100-step limit. Report goals, holes, time limits, mean steps, and mean discounted return. Use `r0 + 0.9*r1 + 0.9^2*r2 + ...`. Explain how finite simulations relate to infinite-horizon values rather than treating them as identical.
5. Compare your maps: which values/actions changed, where did floor costs matter, and how did the policy handle ice? Use printed maps and saved runs as evidence. A custom simulator is sufficient; a Gymnasium-compatible environment is optional. Arbitrary new floor letters are not standard FrozenLake tile types.

Before trusting your solver, demonstrate wall/boundary behavior, ice probabilities summing to 1, the specified mud/sand/water rewards, and no rewards after termination. Save these checks with your results.

## What to hand in

- Part A scan/photo, including value maps and the requested action calculations.
- Your code, dependency versions and commands to reproduce Parts B and C.
- `SUBMISSION.md` with results, explanations and AI-use declaration.
- Episode results and value/policy maps in `outputs/`.

**Recorded assessment total:** 4 marks. **Course weight:** 4%.

The deadline, submission destination and allocation of marks between parts have not yet been specified.

