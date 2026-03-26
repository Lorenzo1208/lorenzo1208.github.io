---
title: "Reinforcement Learning Feynman"
date: 2026-03-26
draft: false
tags: ["IA", "reinforcement learning"]
description: "Du zéro absolu jusqu'à un taxi autonome. Q-Learning, SARSA, epsilon decay : expliqué comme si t'avais 10 ans, avec les maths quand même."
layout: "article"
---

<style>
  :root {
    --ink: #1a1a1a;
    --paper: #f5f2eb;
    --accent: #c84b2f;
    --accent2: #2563a8;
    --muted: #6b6560;
    --soft: #e8e4da;
    --formula-bg: #1e1e2e;
    --formula-fg: #cdd6f4;
    --formula-accent: #f9e2af;
    --formula-blue: #89b4fa;
    --formula-green: #a6e3a1;
    --formula-red: #f38ba8;
    --formula-purple: #cba6f7;
  }

  .rl-wrap * { box-sizing: border-box; }

  .rl-wrap {
    background: var(--paper);
    color: var(--ink);
    font-family: 'DM Sans', sans-serif;
    font-size: 17px;
    line-height: 1.8;
    margin: 0 calc(-1 * var(--gap, 24px));
  }

  @import url('https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,700;1,400&family=IBM+Plex+Mono:wght@400;500&family=DM+Sans:wght@300;400;500&display=swap');

  .rl-wrap .hero {
    background: var(--ink);
    color: var(--paper);
    padding: 80px 40px 60px;
    text-align: center;
    position: relative;
    overflow: hidden;
  }
  .rl-wrap .hero::before {
    content: '';
    position: absolute;
    top: -60px; left: -60px;
    width: 300px; height: 300px;
    border-radius: 50%;
    background: var(--accent);
    opacity: 0.08;
  }
  .rl-wrap .hero::after {
    content: '';
    position: absolute;
    bottom: -80px; right: -40px;
    width: 400px; height: 400px;
    border-radius: 50%;
    background: var(--accent2);
    opacity: 0.06;
  }
  .rl-wrap .hero-label {
    font-size: 11px;
    letter-spacing: 4px;
    text-transform: uppercase;
    color: var(--accent);
    margin-bottom: 20px;
    font-weight: 500;
  }
  .rl-wrap .hero h1 {
    font-family: 'Playfair Display', serif;
    font-size: clamp(36px, 6vw, 72px);
    font-weight: 700;
    line-height: 1.1;
    margin-bottom: 24px;
    color: var(--paper);
    border: none;
    padding: 0;
  }
  .rl-wrap .hero-sub {
    font-size: 18px;
    color: rgba(245,242,235,0.6);
    max-width: 560px;
    margin: 0 auto;
    font-weight: 300;
  }

  .rl-wrap .toc {
    background: var(--soft);
    border-bottom: 1px solid rgba(0,0,0,0.08);
    padding: 0 40px;
    display: flex;
    gap: 0;
    overflow-x: auto;
    scrollbar-width: none;
  }
  .rl-wrap .toc::-webkit-scrollbar { display: none; }
  .rl-wrap .toc a {
    font-size: 12px;
    font-weight: 500;
    letter-spacing: 0.5px;
    color: var(--muted);
    text-decoration: none;
    padding: 14px 16px;
    white-space: nowrap;
    border-bottom: 2px solid transparent;
    transition: all 0.2s;
  }
  .rl-wrap .toc a:hover { color: var(--ink); border-bottom-color: var(--accent); }

  .rl-wrap .container {
    max-width: 780px;
    margin: 0 auto;
    padding: 0 32px;
  }

  .rl-wrap .section {
    padding: 72px 0 48px;
    border-bottom: 1px solid rgba(0,0,0,0.07);
  }

  .rl-wrap .section-label {
    font-size: 11px;
    letter-spacing: 4px;
    text-transform: uppercase;
    color: var(--accent);
    font-weight: 500;
    margin-bottom: 12px;
  }

  .rl-wrap h2 {
    font-family: 'Playfair Display', serif;
    font-size: clamp(26px, 4vw, 38px);
    font-weight: 700;
    line-height: 1.2;
    margin-bottom: 28px;
    color: var(--ink);
    border: none;
    padding: 0;
  }

  .rl-wrap h3 {
    font-family: 'Playfair Display', serif;
    font-size: 22px;
    font-weight: 400;
    font-style: italic;
    margin: 40px 0 16px;
    color: var(--ink);
    border: none;
    padding: 0;
  }

  .rl-wrap p { margin-bottom: 20px; color: #2a2520; }

  .rl-wrap .pullquote {
    border-left: 4px solid var(--accent);
    padding: 16px 24px;
    margin: 36px 0;
    background: rgba(200,75,47,0.04);
  }
  .rl-wrap .pullquote p {
    font-family: 'Playfair Display', serif;
    font-size: 20px;
    font-style: italic;
    color: var(--ink);
    margin: 0;
    line-height: 1.6;
  }

  .rl-wrap .feynman-close {
    background: var(--ink);
    color: var(--paper);
    padding: 20px 28px;
    margin: 40px 0;
    border-radius: 4px;
  }
  .rl-wrap .feynman-close .label {
    font-size: 10px;
    letter-spacing: 3px;
    text-transform: uppercase;
    color: var(--accent);
    margin-bottom: 6px;
  }
  .rl-wrap .feynman-close p {
    color: var(--paper);
    margin: 0;
    font-weight: 500;
    font-size: 16px;
  }

  .rl-wrap .formula-block {
    background: var(--formula-bg);
    border-radius: 8px;
    padding: 32px;
    margin: 36px 0;
    overflow-x: auto;
  }
  .rl-wrap .formula-main {
    font-family: 'IBM Plex Mono', monospace;
    font-size: clamp(14px, 2.5vw, 20px);
    color: var(--formula-fg);
    text-align: center;
    margin-bottom: 28px;
    line-height: 2;
  }
  .rl-wrap .formula-main .fa { color: var(--formula-blue); font-style: normal; }
  .rl-wrap .formula-main .fg { color: var(--formula-green); font-style: normal; }
  .rl-wrap .formula-main .fr { color: var(--formula-red); font-style: normal; }
  .rl-wrap .formula-main .fp { color: var(--formula-purple); font-style: normal; }
  .rl-wrap .formula-main .fy { color: var(--formula-accent); font-style: normal; }

  .rl-wrap .formula-legend {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 12px;
    margin-top: 4px;
  }
  .rl-wrap .formula-item {
    display: flex;
    align-items: flex-start;
    gap: 12px;
    padding: 12px 16px;
    background: rgba(255,255,255,0.04);
    border-radius: 6px;
  }
  .rl-wrap .formula-symbol {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 15px;
    font-weight: 500;
    min-width: 60px;
    padding: 2px 8px;
    border-radius: 4px;
    text-align: center;
    background: rgba(255,255,255,0.06);
  }
  .rl-wrap .formula-symbol.fa { color: var(--formula-blue); }
  .rl-wrap .formula-symbol.fg { color: var(--formula-green); }
  .rl-wrap .formula-symbol.fr { color: var(--formula-red); }
  .rl-wrap .formula-symbol.fp { color: var(--formula-purple); }
  .rl-wrap .formula-symbol.fy { color: var(--formula-accent); }
  .rl-wrap .formula-desc { font-size: 13px; color: rgba(205,214,244,0.75); line-height: 1.5; }
  .rl-wrap .formula-desc strong { color: var(--formula-fg); font-weight: 500; display: block; margin-bottom: 2px; }

  .rl-wrap .cards {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
    gap: 16px;
    margin: 32px 0;
  }
  .rl-wrap .card {
    background: white;
    border: 1px solid rgba(0,0,0,0.08);
    border-radius: 8px;
    padding: 24px;
  }
  .rl-wrap .card-icon { font-size: 28px; margin-bottom: 12px; }
  .rl-wrap .card h4 { font-weight: 500; font-size: 15px; margin-bottom: 6px; color: var(--ink); }
  .rl-wrap .card p { font-size: 14px; color: var(--muted); margin: 0; line-height: 1.6; }

  .rl-wrap .reward-table { width: 100%; border-collapse: collapse; margin: 28px 0; font-size: 15px; }
  .rl-wrap .reward-table th { text-align: left; padding: 10px 16px; background: var(--ink); color: var(--paper); font-weight: 500; font-size: 13px; letter-spacing: 0.5px; }
  .rl-wrap .reward-table td { padding: 12px 16px; border-bottom: 1px solid rgba(0,0,0,0.06); }
  .rl-wrap .reward-table tr:nth-child(even) td { background: rgba(0,0,0,0.02); }
  .rl-wrap .reward-pos { color: #1a7a4a; font-family: 'IBM Plex Mono', monospace; font-weight: 500; }
  .rl-wrap .reward-neg { color: #c84b2f; font-family: 'IBM Plex Mono', monospace; font-weight: 500; }

  .rl-wrap .hyperparam { border: 1px solid rgba(0,0,0,0.08); border-radius: 8px; margin: 20px 0; overflow: hidden; }
  .rl-wrap .hyperparam-header { display: flex; align-items: center; gap: 20px; padding: 20px 24px; background: white; }
  .rl-wrap .hyperparam-symbol { font-family: 'IBM Plex Mono', monospace; font-size: 32px; font-weight: 500; min-width: 48px; text-align: center; }
  .rl-wrap .hyperparam-symbol.alpha { color: #2563a8; }
  .rl-wrap .hyperparam-symbol.gamma { color: #1a7a4a; }
  .rl-wrap .hyperparam-symbol.epsilon { color: #c84b2f; }
  .rl-wrap .hyperparam-info h4 { font-size: 16px; font-weight: 500; margin-bottom: 2px; }
  .rl-wrap .hyperparam-info p { font-size: 13px; color: var(--muted); margin: 0; }
  .rl-wrap .hyperparam-body { padding: 20px 24px; background: rgba(0,0,0,0.02); border-top: 1px solid rgba(0,0,0,0.06); font-size: 15px; }
  .rl-wrap .hyperparam-body .range { display: inline-block; font-family: 'IBM Plex Mono', monospace; font-size: 13px; background: var(--ink); color: var(--paper); padding: 2px 10px; border-radius: 4px; margin: 8px 0; }

  .rl-wrap .compare-table { width: 100%; border-collapse: collapse; margin: 28px 0; font-size: 15px; }
  .rl-wrap .compare-table th { text-align: center; padding: 14px 16px; font-weight: 500; font-size: 14px; border-bottom: 2px solid rgba(0,0,0,0.1); }
  .rl-wrap .compare-table th:first-child { text-align: left; }
  .rl-wrap .compare-table td { padding: 14px 16px; border-bottom: 1px solid rgba(0,0,0,0.06); text-align: center; font-size: 14px; }
  .rl-wrap .compare-table td:first-child { text-align: left; font-weight: 500; }
  .rl-wrap .compare-table .ql { background: rgba(37,99,168,0.05); }
  .rl-wrap .compare-table .sa { background: rgba(26,122,74,0.05); }
  .rl-wrap .compare-table th.ql { color: #2563a8; }
  .rl-wrap .compare-table th.sa { color: #1a7a4a; }
  .rl-wrap .check { color: #1a7a4a; font-size: 18px; }

  .rl-wrap .decay-viz { background: white; border: 1px solid rgba(0,0,0,0.08); border-radius: 8px; padding: 24px; margin: 28px 0; }
  .rl-wrap .decay-bar-container { display: flex; align-items: center; gap: 12px; margin: 12px 0; }
  .rl-wrap .decay-label { font-family: 'IBM Plex Mono', monospace; font-size: 12px; color: var(--muted); min-width: 90px; }
  .rl-wrap .decay-bar-wrap { flex: 1; height: 20px; background: rgba(0,0,0,0.04); border-radius: 4px; overflow: hidden; }
  .rl-wrap .decay-bar { height: 100%; border-radius: 4px; }
  .rl-wrap .decay-val { font-family: 'IBM Plex Mono', monospace; font-size: 12px; min-width: 36px; text-align: right; color: var(--ink); }

  .rl-wrap .qtable-viz { overflow-x: auto; margin: 28px 0; }
  .rl-wrap .qtable { border-collapse: collapse; font-size: 12px; font-family: 'IBM Plex Mono', monospace; min-width: 100%; }
  .rl-wrap .qtable th { background: var(--ink); color: var(--paper); padding: 8px 12px; text-align: center; font-weight: 500; font-size: 11px; letter-spacing: 0.5px; }
  .rl-wrap .qtable td { padding: 8px 12px; text-align: center; border: 1px solid rgba(0,0,0,0.06); font-size: 11px; }
  .rl-wrap .qtable tr:nth-child(even) td { background: rgba(0,0,0,0.02); }
  .rl-wrap .qtable .best { background: rgba(37,99,168,0.15); color: #2563a8; font-weight: 500; }
  .rl-wrap .qtable .neg { color: #c84b2f; }
  .rl-wrap .qtable .state-label { text-align: left; background: rgba(0,0,0,0.03); color: var(--muted); }

  .rl-wrap .step-compare { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; margin: 32px 0; }
  .rl-wrap .step-card { text-align: center; padding: 28px 20px; border-radius: 8px; border: 1px solid rgba(0,0,0,0.08); }
  .rl-wrap .step-number { font-family: 'Playfair Display', serif; font-size: 64px; font-weight: 700; line-height: 1; margin-bottom: 8px; }
  .rl-wrap .step-card.bad .step-number { color: #c84b2f; }
  .rl-wrap .step-card.good .step-number { color: #1a7a4a; }
  .rl-wrap .step-card .step-label { font-size: 14px; color: var(--muted); }
  .rl-wrap .step-card .step-method { font-weight: 500; font-size: 15px; margin-bottom: 4px; }

  .rl-wrap code { font-family: 'IBM Plex Mono', monospace; font-size: 13px; background: rgba(0,0,0,0.06); padding: 1px 6px; border-radius: 3px; color: #2563a8; }

  .rl-wrap .two-formulas { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; margin: 28px 0; }
  .rl-wrap .formula-card { background: var(--formula-bg); border-radius: 8px; padding: 20px; overflow-x: auto; }
  .rl-wrap .formula-card h5 { font-family: 'IBM Plex Mono', monospace; font-size: 12px; letter-spacing: 2px; text-transform: uppercase; margin-bottom: 14px; padding-bottom: 10px; border-bottom: 1px solid rgba(255,255,255,0.08); }
  .rl-wrap .formula-card.ql h5 { color: var(--formula-blue); }
  .rl-wrap .formula-card.sa h5 { color: var(--formula-green); }
  .rl-wrap .formula-card .fm { font-family: 'IBM Plex Mono', monospace; font-size: 12px; color: var(--formula-fg); line-height: 2; }
  .rl-wrap .formula-card .diff-ql { color: var(--formula-blue); font-weight: 500; }
  .rl-wrap .formula-card .diff-sa { color: var(--formula-green); font-weight: 500; }

  .rl-wrap .rl-footer { background: var(--ink); color: rgba(245,242,235,0.5); text-align: center; padding: 48px 40px; font-size: 14px; margin-top: 80px; }
  .rl-wrap .rl-footer strong { color: var(--paper); }

  @media (max-width: 600px) {
    .rl-wrap .two-formulas { grid-template-columns: 1fr; }
    .rl-wrap .step-compare { grid-template-columns: 1fr; }
    .rl-wrap .formula-legend { grid-template-columns: 1fr; }
    .rl-wrap .toc { padding: 0 16px; }
    .rl-wrap .container { padding: 0 20px; }
    .rl-wrap .hero { padding: 60px 24px 48px; }
  }
</style>

<div class="rl-wrap">

<div class="hero">
  <div class="hero-label">Feynman Style</div>
  <h1>Reinforcement<br>Learning</h1>
  <p class="hero-sub">Du zéro absolu jusqu'à un taxi autonome. Expliqué comme si t'avais 10 ans, avec les maths quand même.</p>
</div>

<nav class="toc">
  <a href="#rl">C'est quoi le RL</a>
  <a href="#taxi">Taxi-v3</a>
  <a href="#brute">Brute Force</a>
  <a href="#qtable">Q-Table</a>
  <a href="#formule">La Formule</a>
  <a href="#hyper">Hyperparamètres</a>
  <a href="#epsilon">Epsilon &amp; Decay</a>
  <a href="#sarsa">SARSA vs Q-Learning</a>
  <a href="#results">Résultats</a>
</nav>

<div id="rl" class="section">
<div class="container">
  <div class="section-label">Section 01</div>
  <h2>C'est quoi le Reinforcement Learning ?</h2>
  <p>La plupart des gens pensent que le Reinforcement Learning c'est une IA qui « apprend toute seule en jouant ». C'est pas faux. Mais c'est comme dire que la cuisine c'est « mettre des trucs dans une casserole ». T'as raté le truc essentiel.</p>
  <div class="pullquote"><p>Le RL c'est pas de la magie. C'est un agent qui reçoit des scores pour ses actions, et qui essaie progressivement d'en recevoir plus.</p></div>
  <p>Voilà l'image la plus précise qui existe. Tu as un chien. Il ne comprend pas ta langue. Impossible de lui dire « assieds-toi parce que c'est poli et ça me facilite la vie ». Par contre, si à chaque fois qu'il s'assoit tu lui donnes un biscuit, son cerveau note : <em>s'asseoir = biscuit = bien</em>. Avec le temps, sans jamais avoir compris la phrase, il s'assoit parfaitement.</p>
  <p>Ton taxi dans ce projet, c'est ce chien. Sauf qu'à la place d'un cerveau biologique, il a un tableau de nombres. Et à la place de biscuits, il reçoit des points.</p>
  <h3>Les 3 briques fondamentales</h3>
  <p>Tout projet de RL commence par définir exactement trois choses. Pas deux, pas quatre. Trois.</p>
  <div class="cards">
    <div class="card"><div class="card-icon">🗺️</div><h4>États (States)</h4><p>Toutes les situations possibles dans lesquelles l'agent peut se trouver. Une photo instantanée du monde à un instant T.</p></div>
    <div class="card"><div class="card-icon">🕹️</div><h4>Actions</h4><p>Tout ce que l'agent peut faire depuis n'importe quel état. Son vocabulaire de mouvements.</p></div>
    <div class="card"><div class="card-icon">🍪</div><h4>Récompenses (Rewards)</h4><p>Le score reçu après chaque action. Le seul signal que l'agent utilise pour apprendre.</p></div>
  </div>
  <div class="feynman-close">
    <div class="label">La chose que tu sais maintenant</div>
    <p>Le RL, c'est juste un système States → Actions → Rewards en boucle. Tout le reste (Q-learning, réseaux de neurones, SARSA) c'est juste des façons différentes de gérer cette boucle.</p>
  </div>
</div>
</div>

<div id="taxi" class="section">
<div class="container">
  <div class="section-label">Section 02</div>
  <h2>L'environnement Taxi-v3</h2>
  <p>Maintenant on applique ça à notre problème. Taxi-v3 c'est un environnement fourni par la bibliothèque <code>Gymnasium</code>. Un terrain de jeu 5×5 cases, un taxi, un passager, une destination. Simple en apparence. Riche en pratique.</p>
  <h3>Les états : 500, pas moins</h3>
  <p>Combien de situations différentes le taxi peut-il vivre ? On compte :</p>
  <div class="formula-block">
    <div class="formula-main">
      <span class="fy">25</span> positions taxi × <span class="fa">5</span> positions passager × <span class="fg">4</span> destinations = <span class="fp">500 états</span>
    </div>
    <div class="formula-legend">
      <div class="formula-item"><span class="formula-symbol fy">25</span><div class="formula-desc"><strong>Grille 5×5</strong>25 cases où le taxi peut se trouver</div></div>
      <div class="formula-item"><span class="formula-symbol fa">5</span><div class="formula-desc"><strong>Position du passager</strong>4 lieux (R,G,Y,B) + 1 cas "dans le taxi"</div></div>
      <div class="formula-item"><span class="formula-symbol fg">4</span><div class="formula-desc"><strong>Destinations</strong>R, G, Y, B : les 4 coins du terrain</div></div>
      <div class="formula-item"><span class="formula-symbol fp">500</span><div class="formula-desc"><strong>États totaux</strong>25 × 5 × 4 = 500 situations possibles</div></div>
    </div>
  </div>
  <h3>Les actions : 6 choix à chaque instant</h3>
  <div class="cards">
    <div class="card"><div class="card-icon">⬆️</div><h4>Nord</h4><p>Monter d'une case</p></div>
    <div class="card"><div class="card-icon">⬇️</div><h4>Sud</h4><p>Descendre d'une case</p></div>
    <div class="card"><div class="card-icon">➡️</div><h4>Est</h4><p>Aller à droite</p></div>
    <div class="card"><div class="card-icon">⬅️</div><h4>Ouest</h4><p>Aller à gauche</p></div>
    <div class="card"><div class="card-icon">🙋</div><h4>Ramasser</h4><p>Prendre le passager</p></div>
    <div class="card"><div class="card-icon">🏁</div><h4>Déposer</h4><p>Laisser le passager</p></div>
  </div>
  <h3>Les récompenses : le signal d'apprentissage</h3>
  <p>C'est <strong>ici</strong> que tout se joue. La définition des récompenses dicte entièrement ce que l'agent va apprendre. Mal défini → agent stupide. Bien défini → agent efficace.</p>
  <table class="reward-table">
    <thead><tr><th>Événement</th><th>Récompense</th><th>Pourquoi</th></tr></thead>
    <tbody>
      <tr><td>Déposer au bon endroit</td><td class="reward-pos">+20</td><td>C'est l'objectif final : grosse récompense</td></tr>
      <tr><td>Chaque step (mouvement)</td><td class="reward-neg">−1</td><td>Encourage à aller vite, pas à tourner en rond</td></tr>
      <tr><td>Ramasser/déposer au mauvais endroit</td><td class="reward-neg">−10</td><td>Pénalise les erreurs graves</td></tr>
    </tbody>
  </table>
  <div class="feynman-close">
    <div class="label">La chose que tu sais maintenant</div>
    <p>La récompense −1 par step est aussi importante que le +20. Sans elle, l'agent aurait aucune raison de se dépêcher. Il pourrait faire 10 000 steps et finir quand même avec +20.</p>
  </div>
</div>
</div>

<div id="brute" class="section">
<div class="container">
  <div class="section-label">Section 03</div>
  <h2>L'agent aléatoire : la baseline brute force</h2>
  <p>Avant d'apprendre quoi que ce soit, on teste un agent qui fait des actions complètement aléatoires. Pas pour être nul. Pour avoir un point de comparaison honnête.</p>
  <div class="pullquote"><p>Un agent sans mémoire ne peut jamais s'améliorer. Il refait les mêmes erreurs à l'infini, sans jamais remarquer qu'il les fait.</p></div>
  <p>À chaque état, il tire au hasard parmi les 6 actions. Il n'a aucune mémoire des coups précédents. Il ne sait pas ce qui a marché. Il ne sait pas ce qui a échoué. Il est aussi "intelligent" qu'un dé à six faces.</p>
  <div class="step-compare">
    <div class="step-card bad"><div class="step-method">Agent aléatoire</div><div class="step-number">~350</div><div class="step-label">steps en moyenne</div></div>
    <div class="step-card good"><div class="step-method">Q-Learning optimisé</div><div class="step-number">~13</div><div class="step-label">steps en moyenne</div></div>
  </div>
  <p>Ce ratio illustre bien la différence. L'agent aléatoire prend 350 steps là où Q-learning en prend 13. C'est 27x plus efficace. Et la seule différence, c'est la mémoire.</p>
  <div class="feynman-close">
    <div class="label">La chose que tu sais maintenant</div>
    <p>La brute force est pas "stupide", elle est optimale sans information. C'est le plafond du hasard pur. Tout ce qu'on fait après, c'est ajouter de la mémoire organisée.</p>
  </div>
</div>
</div>

<div id="qtable" class="section">
<div class="container">
  <div class="section-label">Section 04</div>
  <h2>La Q-Table : la mémoire du taxi</h2>
  <p>La Q-Table est l'idée centrale du Q-learning. Et c'est beaucoup plus simple que son nom le laisse croire.</p>
  <p>Imagine un tableau à deux axes. En lignes : tous les états (500). En colonnes : toutes les actions (6). Dans chaque case : un nombre. Ce nombre s'appelle une <strong>Q-value</strong>.</p>
  <div class="pullquote"><p>Le Q dans Q-value veut dire "Quality". C'est littéralement la qualité estimée d'une action depuis un état donné.</p></div>
  <p>Au départ, tout le tableau est à zéro. L'agent ne sait rien. Puis à chaque action, il met à jour la case correspondante. Plus il joue, plus le tableau se remplit de vraies valeurs. Et quand le tableau est bien rempli, le taxi sait exactement quoi faire dans chaque situation.</p>
  <h3>Exemple simplifié</h3>
  <p>Voici un extrait de Q-table après entraînement. La case en bleu = meilleure action depuis cet état.</p>
  <div class="qtable-viz">
    <table class="qtable">
      <thead><tr><th>État</th><th>⬆️ Nord</th><th>⬇️ Sud</th><th>➡️ Est</th><th>⬅️ Ouest</th><th>🙋 Ramasser</th><th>🏁 Déposer</th></tr></thead>
      <tbody>
        <tr><td class="state-label">État 42</td><td class="neg">−2.1</td><td class="best">+7.4</td><td>+1.2</td><td class="neg">−0.8</td><td class="neg">−9.5</td><td class="neg">−9.8</td></tr>
        <tr><td class="state-label">État 137</td><td>+2.3</td><td class="neg">−1.1</td><td class="best">+8.9</td><td>+0.7</td><td class="neg">−9.2</td><td class="neg">−9.9</td></tr>
        <tr><td class="state-label">État 298</td><td class="neg">−0.5</td><td>+1.4</td><td class="neg">−1.8</td><td>+3.2</td><td class="neg">−9.7</td><td class="best">+18.6</td></tr>
      </tbody>
    </table>
  </div>
  <p>À l'état 298, le taxi est pile au bon endroit pour déposer son passager. La Q-table lui dit : +18.6 pour "Déposer" vs −9.7 pour "Ramasser". Choix évident. C'est ça, la mémoire organisée.</p>
  <p>La taille totale de cette table : <code>500 × 6 = 3000 valeurs</code>. C'est tout. Pas de réseau de neurones, pas de calcul complexe. Juste 3000 nombres qui s'améliorent avec le temps.</p>
  <div class="feynman-close">
    <div class="label">La chose que tu sais maintenant</div>
    <p>La Q-table est la différence entre "faire au hasard" et "faire en ayant appris". C'est la cristallisation de toute l'expérience passée en un seul tableau de 3000 nombres.</p>
  </div>
</div>
</div>

<div id="formule" class="section">
<div class="container">
  <div class="section-label">Section 05</div>
  <h2>La formule de mise à jour : mot par mot</h2>
  <p>Voilà la formule centrale du Q-learning. Elle fait peur au premier coup d'œil. On va la décortiquer lettre par lettre jusqu'à ce qu'elle devienne évidente.</p>
  <div class="formula-block">
    <div class="formula-main">
      <span class="fy">Q(s, a)</span> ← <span class="fy">Q(s, a)</span> + <span class="fa">α</span> × [ <span class="fr">r</span> + <span class="fg">γ</span> × <span class="fp">max Q(s', a')</span> − <span class="fy">Q(s, a)</span> ]
    </div>
    <div class="formula-legend">
      <div class="formula-item"><span class="formula-symbol fy">Q(s,a)</span><div class="formula-desc"><strong>La Q-value actuelle</strong>Ce que le taxi pense de l'action a depuis l'état s en ce moment.</div></div>
      <div class="formula-item"><span class="formula-symbol fa">α</span><div class="formula-desc"><strong>Alpha : taux d'apprentissage</strong>À quelle vitesse on remplace l'ancien avis par le nouveau. Entre 0 et 1.</div></div>
      <div class="formula-item"><span class="formula-symbol fr">r</span><div class="formula-desc"><strong>Reward : récompense immédiate</strong>Le score reçu juste après avoir fait l'action. +20, −1, ou −10.</div></div>
      <div class="formula-item"><span class="formula-symbol fg">γ</span><div class="formula-desc"><strong>Gamma : facteur d'actualisation</strong>À quel point on valorise les récompenses futures par rapport à maintenant. Entre 0 et 1.</div></div>
      <div class="formula-item"><span class="formula-symbol fp">max Q(s',a')</span><div class="formula-desc"><strong>Meilleure Q-value future</strong>Le meilleur score que le taxi pense pouvoir obtenir depuis le prochain état s'.</div></div>
      <div class="formula-item"><span class="formula-symbol fy">[...]</span><div class="formula-desc"><strong>L'erreur TD (Temporal Difference)</strong>La différence entre ce qu'on espérait et ce qu'on a vraiment eu. Le moteur de la mise à jour.</div></div>
    </div>
  </div>
  <h3>La même formule, en français</h3>
  <div class="pullquote"><p>« Ma nouvelle opinion sur cette action = mon ancienne opinion + (un peu de) la différence entre ce que j'espérais et ce que j'ai vraiment eu. »</p></div>
  <p>Le crochet <code>[ r + γ·max Q(s',a') − Q(s,a) ]</code> c'est exactement ça : l'écart entre l'espoir et la réalité. Si t'as eu mieux que prévu → la Q-value monte. Si t'as eu moins bien que prévu → elle descend.</p>
  <p>Et <code>α</code> contrôle à quelle vitesse tu mets à jour ton opinion. <code>α = 1.0</code> → tu remplaces complètement ton ancien avis par le nouveau. <code>α = 0.1</code> → tu fais une petite correction de 10%. En pratique, on utilise une valeur basse pour stabiliser l'apprentissage.</p>
  <h3>L'analogie de la note scolaire</h3>
  <p>Imagine que ton prof note un devoir. Il avait une attente. Toi, tu as rendu quelque chose. La différence entre son attente et ta copie, c'est l'erreur. Il ajuste sa note en conséquence, mais pas d'un coup brutal. Un petit peu. C'est exactement α × erreur.</p>
  <div class="feynman-close">
    <div class="label">La chose que tu sais maintenant</div>
    <p>Cette formule ne dit pas "voilà la bonne réponse". Elle dit "voilà comment corriger légèrement ton erreur actuelle". C'est ça l'apprentissage itératif. Chaque épisode est une petite correction, pas une révélation.</p>
  </div>
</div>
</div>

<div id="hyper" class="section">
<div class="container">
  <div class="section-label">Section 06</div>
  <h2>Les 3 hyperparamètres</h2>
  <p>Les hyperparamètres sont les boutons que <em>toi</em> tu règles avant l'entraînement. Ton agent ne les apprend pas, tu les choisis. Et ils changent tout.</p>
  <div class="hyperparam">
    <div class="hyperparam-header">
      <div class="hyperparam-symbol alpha">α</div>
      <div class="hyperparam-info"><h4>Alpha : le taux d'apprentissage</h4><p>À quelle vitesse le taxi met à jour ses croyances</p></div>
    </div>
    <div class="hyperparam-body">
      <span class="range">Intervalle : 0.0 → 1.0 · Valeur typique : 0.1</span>
      <p>Imagine que tu apprends à faire du vélo. Chaque chute te donne une information. <strong>α = 1.0</strong>, c'est oublier tout ce que tu savais avant et ne retenir que la dernière chute. <strong>α = 0.01</strong>, c'est ignorer presque toutes les chutes. Le bon réglage, c'est intégrer chaque expérience progressivement.</p>
      <p>Trop élevé → l'agent oscille, il "surréagit" à chaque expérience. Trop bas → il apprend trop lentement et nécessite des millions d'épisodes.</p>
      <p><strong>Formule partielle :</strong> <code>new_Q = old_Q + α × (target - old_Q)</code></p>
    </div>
  </div>
  <div class="hyperparam">
    <div class="hyperparam-header">
      <div class="hyperparam-symbol gamma">γ</div>
      <div class="hyperparam-info"><h4>Gamma : le facteur d'actualisation</h4><p>À quel point l'agent valorise le futur par rapport au présent</p></div>
    </div>
    <div class="hyperparam-body">
      <span class="range">Intervalle : 0.0 → 1.0 · Valeur typique : 0.6 – 0.99</span>
      <p>Est-ce que tu préfères 10€ maintenant ou 12€ dans un an ? La réponse dépend de ton gamma. <strong>γ = 0</strong>, c'est un agent myope qui ne pense qu'au prochain coup. <strong>γ = 0.99</strong>, c'est un agent prévoyant qui planifie sur des dizaines d'étapes.</p>
      <p>Pour Taxi-v3, un gamma élevé (0.95+) est conseillé : le taxi doit planifier plusieurs mouvements à l'avance pour ramasser et déposer efficacement.</p>
      <p><strong>Intuition mathématique :</strong> une récompense dans 10 étapes vaut <code>γ¹⁰ × r</code>. Avec γ=0.9, ça donne 0.35r. Avec γ=0.5, ça donne 0.001r.</p>
    </div>
  </div>
  <div class="hyperparam">
    <div class="hyperparam-header">
      <div class="hyperparam-symbol epsilon">ε</div>
      <div class="hyperparam-info"><h4>Epsilon : le taux d'exploration</h4><p>La probabilité de choisir une action aléatoire plutôt que la meilleure connue</p></div>
    </div>
    <div class="hyperparam-body">
      <span class="range">Intervalle : 0.0 → 1.0 · Valeur initiale typique : 1.0</span>
      <p>Tu connais un bon restaurant. Est-ce que tu y vas tous les soirs, ou tu essaies parfois des nouveaux endroits au risque d'être déçu ? <strong>ε = 1.0</strong> → toujours un nouveau restaurant. <strong>ε = 0.0</strong> → toujours le même. La sagesse, c'est commencer à 1.0 et descendre progressivement.</p>
      <p>C'est le cœur de la méthode <strong>epsilon-greedy</strong> qu'on voit en détail dans la section suivante.</p>
    </div>
  </div>
  <div class="feynman-close">
    <div class="label">La chose que tu sais maintenant</div>
    <p>α, γ, et ε sont indépendants mais ils interagissent. Un α élevé avec un ε élevé = chaos. Un α bas avec un γ élevé = apprentissage lent mais stable. Trouver la bonne combinaison, c'est l'art du réglage d'hyperparamètres.</p>
  </div>
</div>
</div>

<div id="epsilon" class="section">
<div class="container">
  <div class="section-label">Section 07</div>
  <h2>Epsilon-greedy &amp; Epsilon Decay</h2>
  <p>La politique epsilon-greedy résout un problème fondamental : comment explorer intelligemment sans être stupide indéfiniment ?</p>
  <h3>La politique epsilon-greedy</h3>
  <p>À chaque pas, le taxi tire un nombre aléatoire entre 0 et 1. Si ce nombre est inférieur à ε, il fait une action aléatoire (exploration). Sinon, il choisit la meilleure action connue dans sa Q-table (exploitation).</p>
  <div class="formula-block">
    <div class="formula-main">
      action = { <span class="fr">random()</span> si <span class="fy">rand(0,1) &lt; ε</span> · <span class="fg">argmax Q(s, a)</span> sinon }
    </div>
    <div class="formula-legend">
      <div class="formula-item"><span class="formula-symbol fr">random</span><div class="formula-desc"><strong>Action aléatoire</strong>N'importe laquelle des 6 actions, tirée au sort. Phase d'exploration.</div></div>
      <div class="formula-item"><span class="formula-symbol fg">argmax</span><div class="formula-desc"><strong>Meilleure action connue</strong>L'action avec la plus haute Q-value depuis l'état actuel. Phase d'exploitation.</div></div>
    </div>
  </div>
  <h3>L'Epsilon Decay : la décroissance progressive</h3>
  <p>L'idée géniale : commencer avec ε = 1.0 (100% exploration) et le diminuer progressivement après chaque épisode. Le taxi explore tout au début, puis exploite de plus en plus ce qu'il a appris.</p>
  <div class="formula-block">
    <div class="formula-main">
      <span class="fy">ε</span> ← <span class="fa">max</span>( <span class="fg">ε_min</span> , <span class="fy">ε</span> × <span class="fp">decay_rate</span> )
    </div>
    <div class="formula-legend">
      <div class="formula-item"><span class="formula-symbol fg">ε_min</span><div class="formula-desc"><strong>Epsilon minimum</strong>On ne descend jamais à 0. On garde toujours un peu d'exploration. Typiquement 0.01 – 0.05.</div></div>
      <div class="formula-item"><span class="formula-symbol fp">decay_rate</span><div class="formula-desc"><strong>Taux de décroissance</strong>Un nombre entre 0.99 et 0.9999. Chaque épisode, ε est multiplié par ce facteur.</div></div>
    </div>
  </div>
  <h3>Visualisation de la décroissance</h3>
  <div class="decay-viz">
    <p style="font-size:13px;color:var(--muted);margin-bottom:16px;">Évolution d'epsilon avec decay_rate = 0.995 sur 1000 épisodes</p>
    <div class="decay-bar-container"><span class="decay-label">Épisode 0</span><div class="decay-bar-wrap"><div class="decay-bar" style="width:100%;background:#c84b2f;"></div></div><span class="decay-val">ε = 1.00</span></div>
    <div class="decay-bar-container"><span class="decay-label">Épisode 200</span><div class="decay-bar-wrap"><div class="decay-bar" style="width:67%;background:#d4783a;"></div></div><span class="decay-val">ε = 0.67</span></div>
    <div class="decay-bar-container"><span class="decay-label">Épisode 500</span><div class="decay-bar-wrap"><div class="decay-bar" style="width:28%;background:#a07a55;"></div></div><span class="decay-val">ε = 0.28</span></div>
    <div class="decay-bar-container"><span class="decay-label">Épisode 800</span><div class="decay-bar-wrap"><div class="decay-bar" style="width:9%;background:#7a8a7a;"></div></div><span class="decay-val">ε = 0.09</span></div>
    <div class="decay-bar-container"><span class="decay-label">Épisode 1000+</span><div class="decay-bar-wrap"><div class="decay-bar" style="width:2%;background:#3a7a4a;"></div></div><span class="decay-val">ε = 0.01</span></div>
  </div>
  <p>Pourquoi ne pas mettre ε_min à 0 ? Parce que l'environnement peut changer, et parce qu'on veut toujours une petite chance de découvrir de meilleures stratégies qu'on aurait ratées.</p>
  <div class="feynman-close">
    <div class="label">La chose que tu sais maintenant</div>
    <p>Epsilon Decay c'est la traduction algorithmique d'une sagesse humaine : au début d'un nouveau boulot, explore tout. Après 6 mois, t'as tes habitudes. Mais garde toujours une petite curiosité.</p>
  </div>
</div>
</div>

<div id="sarsa" class="section">
<div class="container">
  <div class="section-label">Section 08</div>
  <h2>SARSA vs Q-Learning</h2>
  <p>Ce sont deux algorithmes qui font presque la même chose. La différence tient à une seule lettre dans leur formule. Mais cette différence change fondamentalement leur philosophie.</p>
  <h3>Les deux formules côte à côte</h3>
  <div class="two-formulas">
    <div class="formula-card ql"><h5>Q-Learning (off-policy)</h5><div class="fm">Q(s,a) ← Q(s,a) + α<br>× [r + γ ×<br><span class="diff-ql">max Q(s', a')</span><br>− Q(s,a)]</div></div>
    <div class="formula-card sa"><h5>SARSA (on-policy)</h5><div class="fm">Q(s,a) ← Q(s,a) + α<br>× [r + γ ×<br><span class="diff-sa">Q(s', a')</span><br>− Q(s,a)]</div></div>
  </div>
  <p>Tu vois la différence ? Q-Learning utilise <code>max Q(s', a')</code> : la <em>meilleure</em> action possible dans le prochain état, même si l'agent ne la prend pas forcément. SARSA utilise <code>Q(s', a')</code> : l'action que l'agent va <em>vraiment</em> prendre.</p>
  <h3>Off-policy vs On-policy : l'intuition</h3>
  <div class="cards">
    <div class="card"><div class="card-icon">🗺️</div><h4>Q-Learning (off-policy)</h4><p>Apprend en imaginant qu'il prendrait toujours la <em>meilleure</em> décision future. Optimiste. Apprend plus vite en théorie.</p></div>
    <div class="card"><div class="card-icon">🚶</div><h4>SARSA (on-policy)</h4><p>Apprend en tenant compte de ce qu'il <em>fait vraiment</em>, y compris ses erreurs d'exploration. Plus prudent et conservateur.</p></div>
  </div>
  <p>L'analogie : imagine que tu apprends à conduire. <strong>Q-Learning</strong>, c'est apprendre en supposant que tu rouleras toujours parfaitement. <strong>SARSA</strong>, c'est apprendre en incluant le fait que tu feras des erreurs de débutant pendant encore un moment.</p>
  <h3>Comparaison pratique</h3>
  <table class="compare-table">
    <thead><tr><th>Critère</th><th class="ql">Q-Learning</th><th class="sa">SARSA</th></tr></thead>
    <tbody>
      <tr><td>Type</td><td class="ql">Off-policy</td><td class="sa">On-policy</td></tr>
      <tr><td>Vitesse de convergence</td><td class="ql">⚡ Plus rapide</td><td class="sa">🐢 Plus lent</td></tr>
      <tr><td>Stabilité</td><td class="ql">⚠️ Peut osciller</td><td class="sa">✅ Plus stable</td></tr>
      <tr><td>Performance finale</td><td class="ql">🏆 Optimal théorique</td><td class="sa">Conservateur</td></tr>
      <tr><td>Recommandé pour Taxi-v3</td><td class="ql"><span class="check">✓</span></td><td class="sa"><span class="check">✓</span></td></tr>
    </tbody>
  </table>
  <p>Pour Taxi-v3, les deux fonctionnent bien. Q-Learning converge généralement en moins d'épisodes mais peut être un peu plus instable pendant l'entraînement. SARSA est plus prudent.</p>
  <h3>SARSA : State Action Reward State Action</h3>
  <p>Le nom SARSA vient de la séquence d'éléments utilisés dans la mise à jour :</p>
  <div class="formula-block">
    <div class="formula-main" style="font-size:16px;letter-spacing:2px;">
      <span class="fy">S</span>tate → <span class="fa">A</span>ction → <span class="fr">R</span>eward → <span class="fg">S</span>tate' → <span class="fp">A</span>ction'
    </div>
  </div>
  <p>La mise à jour utilise exactement ces 5 valeurs. C'est pour ça qu'on dit que SARSA est "on-policy" : il a besoin de savoir <em>quelle action il va vraiment prendre ensuite</em> (A') avant de mettre à jour.</p>
  <div class="feynman-close">
    <div class="label">La chose que tu sais maintenant</div>
    <p>Q-Learning est un rêveur optimiste, il suppose toujours qu'il fera le meilleur choix futur. SARSA est un réaliste, il inclut ses propres imperfections dans son apprentissage.</p>
  </div>
</div>
</div>

<div id="results" class="section">
<div class="container">
  <div class="section-label">Section 09</div>
  <h2>Résultats &amp; Benchmark</h2>
  <p>Les chiffres parlent d'eux-mêmes. Après entraînement sur 10 000 épisodes, voici ce que donnent les différents algorithmes en phase de test.</p>
  <div class="step-compare">
    <div class="step-card bad"><div class="step-method">🎲 Brute Force</div><div class="step-number">350</div><div class="step-label">steps moyens · reward ≈ −400</div></div>
    <div class="step-card" style="border-color:rgba(37,99,168,0.3);background:rgba(37,99,168,0.03)"><div class="step-method" style="color:#2563a8">🧮 SARSA</div><div class="step-number" style="color:#2563a8">16</div><div class="step-label">steps moyens · reward ≈ +7</div></div>
  </div>
  <div class="step-compare">
    <div class="step-card good"><div class="step-method">🏆 Q-Learning</div><div class="step-number">13</div><div class="step-label">steps moyens · reward ≈ +8</div></div>
    <div class="step-card" style="border-color:rgba(0,0,0,0.08)"><div class="step-method">🎯 Optimal théorique</div><div class="step-number">~10</div><div class="step-label">steps (Manhattan distance min)</div></div>
  </div>
  <h3>Ce que révèle ce benchmark</h3>
  <p>La différence entre brute force et Q-learning, c'est pas une amélioration. C'est un changement de nature. L'agent aléatoire tâtonne. L'agent Q-learning sait. Ce sont deux comportements fondamentalement différents produits par la même boucle d'interaction avec l'environnement, juste avec ou sans mémoire organisée.</p>
  <p>Le fait que Q-Learning arrive si proche de l'optimal théorique (~10 steps) après seulement 10 000 épisodes montre l'efficacité du Temporal Difference Learning : pas besoin de finir une partie pour apprendre, on apprend à chaque step.</p>
  <div class="pullquote"><p>La vraie leçon : ce n'est pas l'algorithme qui fait la différence. C'est la mémoire. Donner une mémoire organisée à un agent, c'est lui donner la capacité d'apprendre.</p></div>
  <div class="feynman-close">
    <div class="label">La chose que tu sais maintenant que la plupart des adultes ne savent pas</div>
    <p>Le Reinforcement Learning n'est pas de l'intelligence artificielle au sens mystérieux du terme. C'est de la mémoire organisée du résultat de ses propres erreurs, mise à jour par une seule formule, répétée des milliers de fois. La sophistication émerge de la répétition, pas de la complexité.</p>
  </div>
</div>
</div>

<div class="rl-footer">
  <strong>Reinforcement Learning Feynman</strong><br>
  Expliqué dans le style Feynman
</div>

</div>
