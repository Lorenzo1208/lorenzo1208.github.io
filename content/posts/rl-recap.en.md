---
title: "Reinforcement Learning: the essentials"
date: 2026-03-26
draft: false
tags: ["AI", "reinforcement learning"]
description: "Dense summary of Reinforcement Learning: Q-learning, SARSA, epsilon decay and results on Taxi-v3."
---

## What is RL

An agent interacts with an environment. After each action, it receives a score. It learns to maximize that score. That's it.

Three elements are enough to define any RL problem:

- **States**: every possible situation
- **Actions**: everything the agent can do
- **Rewards**: the score received after each action

## Taxi-v3

5x5 grid, one taxi, one passenger, one destination.

**500 states** = 25 taxi positions x 5 passenger positions x 4 destinations

**6 actions**: North, South, East, West, Pickup, Dropoff

**Rewards**:

| Event | Score |
|---|---|
| Drop off at correct location | +20 |
| Each movement | -1 |
| Wrong pickup/dropoff | -10 |

The -1 per step is as important as the +20: without it, the agent has no reason to be fast.

## Random agent (baseline)

No memory, random actions. Result: ~350 steps on average. This is the ceiling of pure chance, the starting point to measure progress.

## Q-Table

A 500 x 6 = **3000 values** table. Each cell holds the estimated quality of an action from a given state (Q-value). Everything starts at zero. The agent fills it in by playing.

## The Q-learning formula

```
Q(s,a) <- Q(s,a) + α × [r + γ × max Q(s',a') - Q(s,a)]
```

In plain English: new opinion = old opinion + correction proportional to the error.

The bracket `[r + γ × max Q(s',a') - Q(s,a)]` is the TD error: gap between what was expected and what actually happened.

## The 3 hyperparameters

**α (alpha): learning rate**
Speed of belief update. Too high: unstable. Too low: slow learning. Typical value: 0.1

**γ (gamma): discount factor**
Importance of future rewards. γ=0: short-sighted agent. γ=0.99: forward-planning agent. For Taxi-v3: 0.95+

**ε (epsilon): exploration rate**
Probability of taking a random action instead of the best known one. Starts at 1.0, decreases progressively.

## Epsilon Decay

```
ε <- max(ε_min, ε × decay_rate)
```

Start by exploring everything (ε=1.0), then exploit what was learned more and more. Keep a minimum exploration rate (0.01-0.05) to never miss a better strategy.

## SARSA vs Q-Learning

The only difference: how the future value is estimated.

| | Q-Learning | SARSA |
|---|---|---|
| Update uses | `max Q(s', a')` | `Q(s', a')` |
| Type | Off-policy | On-policy |
| Speed | Faster | Slower |
| Stability | Can oscillate | More stable |

Q-Learning always assumes the best future choice. SARSA accounts for what the agent will actually do, including its exploration mistakes.

## Results on Taxi-v3

| Algorithm | Avg steps | Reward |
|---|---|---|
| Random | ~350 | -400 |
| SARSA | ~16 | +7 |
| Q-Learning | ~13 | +8 |
| Theoretical optimal | ~10 | - |

27x more efficient than chance. The only difference: a memory of 3000 numbers updated by a single formula.
