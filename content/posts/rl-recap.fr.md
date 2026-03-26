---
title: "Reinforcement Learning : l'essentiel"
date: 2026-03-26
draft: false
tags: ["IA", "reinforcement learning"]
description: "Synthèse dense du Reinforcement Learning : Q-learning, SARSA, epsilon decay et résultats sur Taxi-v3."
---

## C'est quoi le RL

Un agent interagit avec un environnement. A chaque action, il reçoit un score. Il apprend à maximiser ce score. C'est tout.

Trois éléments suffisent à définir n'importe quel problème RL :

- **States** : toutes les situations possibles
- **Actions** : tout ce que l'agent peut faire
- **Rewards** : le score reçu après chaque action

## Taxi-v3

Grille 5x5, un taxi, un passager, une destination.

**500 états** = 25 positions taxi x 5 positions passager x 4 destinations

**6 actions** : Nord, Sud, Est, Ouest, Ramasser, Déposer

**Récompenses** :

| Événement | Score |
|---|---|
| Déposer au bon endroit | +20 |
| Chaque mouvement | -1 |
| Mauvais ramasser/déposer | -10 |

Le -1 par mouvement est aussi important que le +20 : sans lui, l'agent n'a aucune raison d'aller vite.

## Agent aléatoire (baseline)

Aucune mémoire, actions au hasard. Résultat : ~350 steps en moyenne. C'est le plafond du hasard pur, le point de départ pour mesurer les progrès.

## Q-Table

Tableau de 500 x 6 = **3000 valeurs**. Chaque case contient la qualité estimée d'une action depuis un état donné (Q-value). Au départ tout est à zéro. L'agent le remplit en jouant.

## La formule Q-learning

```
Q(s,a) <- Q(s,a) + α × [r + γ × max Q(s',a') - Q(s,a)]
```

En français : nouvelle opinion = ancienne opinion + correction proportionnelle à l'erreur.

Le crochet `[r + γ × max Q(s',a') - Q(s,a)]` est l'erreur TD : écart entre ce qu'on espérait et ce qu'on a eu.

## Les 3 hyperparamètres

**α (alpha) : taux d'apprentissage**
Vitesse de mise à jour des croyances. Trop élevé : instable. Trop bas : apprentissage lent. Valeur typique : 0.1

**γ (gamma) : facteur d'actualisation**
Importance des récompenses futures. γ=0 : agent myope. γ=0.99 : agent prévoyant. Pour Taxi-v3 : 0.95+

**ε (epsilon) : taux d'exploration**
Probabilité de faire une action aléatoire plutôt que la meilleure connue. Commence à 1.0, diminue progressivement.

## Epsilon Decay

```
ε <- max(ε_min, ε × decay_rate)
```

On commence à explorer tout (ε=1.0), puis on exploite de plus en plus ce qu'on a appris. On garde un minimum d'exploration (0.01-0.05) pour ne jamais rater une meilleure stratégie.

## SARSA vs Q-Learning

La seule différence : comment on estime la valeur future.

| | Q-Learning | SARSA |
|---|---|---|
| Mise à jour | `max Q(s', a')` | `Q(s', a')` |
| Type | Off-policy | On-policy |
| Vitesse | Plus rapide | Plus lent |
| Stabilité | Peut osciller | Plus stable |

Q-Learning imagine toujours le meilleur choix futur. SARSA tient compte de ce que l'agent va vraiment faire, y compris ses erreurs d'exploration.

## Résultats sur Taxi-v3

| Algorithme | Steps moyens | Reward |
|---|---|---|
| Aléatoire | ~350 | -400 |
| SARSA | ~16 | +7 |
| Q-Learning | ~13 | +8 |
| Optimal théorique | ~10 | - |

27x plus efficace que le hasard. La seule différence : une mémoire de 3000 nombres mise à jour par une seule formule.
