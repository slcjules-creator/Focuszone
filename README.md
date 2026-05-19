# Focuszone
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>FocusZone — Ton espace de travail</title>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@400;600;700;800&family=DM+Sans:ital,wght@0,300;0,400;0,500;1,400&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0a0f;
    --surface: #12121a;
    --card: #1a1a26;
    --border: #252535;
    --accent: #7c5cfc;
    --accent2: #fc5c7d;
    --accent3: #5cfcb8;
    --text: #e8e8f0;
    --muted: #6b6b88;
    --xp: #ffd700;
    --success: #5cfcb8;
    --danger: #fc5c7d;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    font-family: 'DM Sans', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Background noise texture */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 512 512' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.75' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.03'/%3E%3C/svg%3E");
    pointer-events: none;
    z-index: 0;
  }

  /* Ambient glow */
  body::after {
    content: '';
    position: fixed;
    width: 600px; height: 600px;
    background: radial-gradient(circle, rgba(124,92,252,0.06) 0%, transparent 70%);
    top: -200px; left: -200px;
    pointer-events: none;
    z-index: 0;
  }

  /* ── LAYOUT ── */
  .app {
    display: grid;
    grid-template-columns: 240px 1fr;
    grid-template-rows: 60px 1fr;
    min-height: 100vh;
    position: relative;
    z-index: 1;
  }

  /* ── TOPBAR ── */
  .topbar {
    grid-column: 1 / -1;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0 24px;
    border-bottom: 1px solid var(--border);
    background: rgba(10,10,15,0.8);
    backdrop-filter: blur(20px);
    position: sticky;
    top: 0;
    z-index: 100;
  }

  .logo {
    font-family: 'Syne', sans-serif;
    font-weight: 800;
    font-size: 1.3rem;
    letter-spacing: -0.02em;
    background: linear-gradient(135deg, #7c5cfc, #fc5c7d);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
  }

  .topbar-right {
    display: flex;
    align-items: center;
    gap: 16px;
  }

  .xp-badge {
    display: flex;
    align-items: center;
    gap: 8px;
    background: rgba(255,215,0,0.1);
    border: 1px solid rgba(255,215,0,0.2);
    border-radius: 20px;
    padding: 5px 14px;
    font-size: 0.85rem;
    font-weight: 500;
    color: var(--xp);
  }

  .level-badge {
    display: flex;
    align-items: center;
    gap: 8px;
    background: rgba(124,92,252,0.12);
    border: 1px solid rgba(124,92,252,0.25);
    border-radius: 20px;
    padding: 5px 14px;
    font-size: 0.85rem;
    font-weight: 500;
    color: var(--accent);
  }

  /* ── SIDEBAR ── */
  .sidebar {
    border-right: 1px solid var(--border);
    padding: 20px 12px;
    display: flex;
    flex-direction: column;
    gap: 4px;
    background: rgba(18,18,26,0.5);
  }

  .nav-item {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 10px 14px;
    border-radius: 10px;
    cursor: pointer;
    font-size: 0.9rem;
    font-weight: 500;
    color: var(--muted);
    transition: all 0.15s ease;
    user-select: none;
  }

  .nav-item:hover { background: var(--card); color: var(--text); }
  .nav-item.active { background: rgba(124,92,252,0.15); color: var(--accent); }
  .nav-item .icon { font-size: 1.1rem; }

  .sidebar-section {
    font-size: 0.7rem;
    font-weight: 600;
    letter-spacing: 0.1em;
    text-transform: uppercase;
    color: var(--muted);
    padding: 16px 14px 4px;
  }

  .progress-section {
    margin-top: auto;
    padding: 16px;
    background: var(--card);
    border-radius: 14px;
    border: 1px solid var(--border);
  }

  .progress-label {
    display: flex;
    justify-content: space-between;
    font-size: 0.78rem;
    color: var(--muted);
    margin-bottom: 8px;
  }

  .progress-label span:last-child { color: var(--accent); font-weight: 600; }

  .progress-bar {
    height: 6px;
    background: var(--border);
    border-radius: 99px;
    overflow: hidden;
  }

  .progress-fill {
    height: 100%;
    background: linear-gradient(90deg, var(--accent), var(--accent2));
    border-radius: 99px;
    transition: width 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
  }

  /* ── MAIN CONTENT ── */
  .main {
    padding: 28px 32px;
    overflow-y: auto;
    max-height: calc(100vh - 60px);
  }

  /* ── PAGES ── */
  .page { display: none; }
  .page.active { display: block; }

  /* ── DASHBOARD ── */
  .page-title {
    font-family: 'Syne', sans-serif;
    font-size: 1.7rem;
    font-weight: 700;
    margin-bottom: 6px;
  }

  .page-sub { color: var(--muted); font-size: 0.9rem; margin-bottom: 28px; }

  .stats-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 16px;
    margin-bottom: 28px;
  }

  .stat-card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 20px;
    position: relative;
    overflow: hidden;
  }

  .stat-card::before {
    content: '';
    position: absolute;
    top: -30px; right: -30px;
    width: 80px; height: 80px;
    border-radius: 50%;
    opacity: 0.15;
  }

  .stat-card.purple::before { background: var(--accent); }
  .stat-card.pink::before { background: var(--accent2); }
  .stat-card.green::before { background: var(--accent3); }
  .stat-card.gold::before { background: var(--xp); }

  .stat-icon { font-size: 1.5rem; margin-bottom: 10px; }
  .stat-value {
    font-family: 'Syne', sans-serif;
    font-size: 1.8rem;
    font-weight: 800;
    line-height: 1;
    margin-bottom: 4px;
  }
  .stat-label { font-size: 0.8rem; color: var(--muted); }

  .dashboard-grid {
    display: grid;
    grid-template-columns: 1fr 320px;
    gap: 20px;
  }

  /* ── TODO ── */
  .card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 18px;
    padding: 22px;
  }

  .card-title {
    font-family: 'Syne', sans-serif;
    font-size: 1rem;
    font-weight: 700;
    margin-bottom: 16px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .todo-input-row {
    display: flex;
    gap: 8px;
    margin-bottom: 16px;
  }

  input[type="text"], select, textarea {
    background: var(--surface);
    border: 1px solid var(--border);
    color: var(--text);
    border-radius: 10px;
    padding: 9px 14px;
    font-family: inherit;
    font-size: 0.88rem;
    outline: none;
    transition: border-color 0.15s;
    width: 100%;
  }

  input[type="text"]:focus, select:focus, textarea:focus {
    border-color: var(--accent);
  }

  .btn {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    padding: 9px 18px;
    border-radius: 10px;
    border: none;
    cursor: pointer;
    font-family: inherit;
    font-size: 0.88rem;
    font-weight: 600;
    transition: all 0.15s;
    white-space: nowrap;
  }

  .btn-primary {
    background: var(--accent);
    color: #fff;
  }
  .btn-primary:hover { background: #6a4de8; transform: translateY(-1px); }

  .btn-ghost {
    background: transparent;
    color: var(--muted);
    border: 1px solid var(--border);
  }
  .btn-ghost:hover { background: var(--card); color: var(--text); }

  .btn-danger { background: rgba(252,92,125,0.15); color: var(--danger); border: 1px solid rgba(252,92,125,0.2); }
  .btn-danger:hover { background: rgba(252,92,125,0.25); }

  .btn-sm { padding: 5px 12px; font-size: 0.8rem; border-radius: 8px; }

  .todo-list { display: flex; flex-direction: column; gap: 8px; }

  .todo-item {
    display: flex;
    align-items: center;
    gap: 12px;
    padding: 12px 14px;
    background: var(--surface);
    border-radius: 12px;
    border: 1px solid var(--border);
    transition: all 0.15s;
    animation: slideIn 0.2s ease;
  }

  @keyframes slideIn {
    from { opacity: 0; transform: translateY(-8px); }
    to { opacity: 1; transform: translateY(0); }
  }

  .todo-item:hover { border-color: var(--accent); }
  .todo-item.done { opacity: 0.5; }
  .todo-item.done .todo-text { text-decoration: line-through; }

  .todo-check {
    width: 20px; height: 20px;
    border-radius: 6px;
    border: 2px solid var(--border);
    cursor: pointer;
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.15s;
    background: transparent;
  }

  .todo-check.checked { background: var(--accent); border-color: var(--accent); }

  .todo-text { flex: 1; font-size: 0.9rem; }

  .todo-priority {
    font-size: 0.7rem;
    padding: 2px 8px;
    border-radius: 99px;
    font-weight: 600;
  }

  .priority-high { background: rgba(252,92,125,0.15); color: var(--accent2); }
  .priority-medium { background: rgba(255,215,0,0.1); color: var(--xp); }
  .priority-low { background: rgba(92,252,184,0.1); color: var(--accent3); }

  .todo-delete { cursor: pointer; color: var(--muted); font-size: 0.85rem; transition: color 0.15s; }
  .todo-delete:hover { color: var(--danger); }

  /* ── POMODORO ── */
  .pomodoro-wrapper {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 28px;
    padding: 20px;
  }

  .timer-circle {
    position: relative;
    width: 240px;
    height: 240px;
  }

  .timer-circle svg {
    transform: rotate(-90deg);
    position: absolute;
    top: 0; left: 0;
  }

  .timer-track { fill: none; stroke: var(--border); stroke-width: 6; }
  .timer-progress {
    fill: none;
    stroke: url(#timerGrad);
    stroke-width: 6;
    stroke-linecap: round;
    transition: stroke-dashoffset 1s linear;
  }

  .timer-center {
    position: absolute;
    inset: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  .timer-display {
    font-family: 'Syne', sans-serif;
    font-size: 3rem;
    font-weight: 800;
    letter-spacing: -0.03em;
    line-height: 1;
  }

  .timer-mode {
    font-size: 0.78rem;
    color: var(--muted);
    font-weight: 500;
    margin-top: 4px;
  }

  .timer-controls { display: flex; gap: 12px; align-items: center; }

  .timer-btn {
    width: 52px; height: 52px;
    border-radius: 50%;
    border: none;
    cursor: pointer;
    font-size: 1.2rem;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.2s;
  }

  .timer-btn-main {
    background: var(--accent);
    color: white;
    width: 64px; height: 64px;
    font-size: 1.4rem;
    box-shadow: 0 0 30px rgba(124,92,252,0.4);
  }

  .timer-btn-main:hover { transform: scale(1.05); box-shadow: 0 0 40px rgba(124,92,252,0.6); }

  .timer-btn-secondary {
    background: var(--card);
    color: var(--muted);
    border: 1px solid var(--border);
  }

  .timer-btn-secondary:hover { color: var(--text); border-color: var(--accent); }

  .timer-modes { display: flex; gap: 8px; }

  .mode-pill {
    padding: 6px 16px;
    border-radius: 99px;
    font-size: 0.8rem;
    font-weight: 600;
    cursor: pointer;
    border: 1px solid var(--border);
    background: transparent;
    color: var(--muted);
    transition: all 0.15s;
    font-family: inherit;
  }

  .mode-pill.active { background: var(--accent); color: white; border-color: var(--accent); }

  .sessions-dots { display: flex; gap: 8px; }
  .session-dot {
    width: 10px; height: 10px;
    border-radius: 50%;
    background: var(--border);
    transition: background 0.3s;
  }
  .session-dot.done { background: var(--accent); }

  /* ── HABITS ── */
  .habits-grid { display: flex; flex-direction: column; gap: 12px; }

  .habit-row {
    display: flex;
    align-items: center;
    gap: 16px;
    padding: 14px 16px;
    background: var(--surface);
    border-radius: 12px;
    border: 1px solid var(--border);
  }

  .habit-icon { font-size: 1.3rem; }
  .habit-name { flex: 1; font-size: 0.9rem; font-weight: 500; }
  .habit-streak {
    font-size: 0.8rem;
    color: var(--xp);
    font-weight: 600;
    display: flex;
    align-items: center;
    gap: 4px;
  }

  .habit-days { display: flex; gap: 6px; }

  .habit-day {
    width: 28px; height: 28px;
    border-radius: 8px;
    border: 1px solid var(--border);
    background: var(--card);
    cursor: pointer;
    font-size: 0.65rem;
    font-weight: 700;
    color: var(--muted);
    display: flex;
    align-items: center;
    justify-content: center;
    transition: all 0.15s;
  }

  .habit-day.done { background: var(--accent); border-color: var(--accent); color: white; }
  .habit-day.today { border-color: var(--accent2); }

  /* ── STATS PAGE ── */
  .stats-page-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
  }

  .heatmap-grid {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    gap: 4px;
    margin-top: 12px;
  }

  .heatmap-cell {
    aspect-ratio: 1;
    border-radius: 4px;
    background: var(--border);
    transition: all 0.2s;
    cursor: pointer;
  }

  .heatmap-cell:hover { transform: scale(1.2); }
  .heatmap-cell[data-level="1"] { background: rgba(124,92,252,0.25); }
  .heatmap-cell[data-level="2"] { background: rgba(124,92,252,0.5); }
  .heatmap-cell[data-level="3"] { background: rgba(124,92,252,0.75); }
  .heatmap-cell[data-level="4"] { background: var(--accent); }

  .bar-chart { display: flex; align-items: flex-end; gap: 8px; height: 100px; margin-top: 12px; }

  .bar-wrap { display: flex; flex-direction: column; align-items: center; gap: 4px; flex: 1; }
  .bar {
    width: 100%;
    background: linear-gradient(180deg, var(--accent), rgba(124,92,252,0.3));
    border-radius: 6px 6px 0 0;
    transition: height 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
    min-height: 4px;
  }
  .bar-label { font-size: 0.65rem; color: var(--muted); }

  .achievements-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 12px;
    margin-top: 12px;
  }

  .achievement {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 14px;
    text-align: center;
    transition: all 0.2s;
  }

  .achievement.unlocked { border-color: rgba(255,215,0,0.3); background: rgba(255,215,0,0.05); }
  .achievement:hover { transform: translateY(-2px); }
  .achievement-icon { font-size: 1.8rem; margin-bottom: 6px; }
  .achievement-name { font-size: 0.75rem; font-weight: 600; color: var(--text); }
  .achievement-desc { font-size: 0.68rem; color: var(--muted); margin-top: 2px; }
  .achievement.locked .achievement-icon { filter: grayscale(1); opacity: 0.4; }

  /* ── XP POPUP ── */
  .xp-popup {
    position: fixed;
    top: 80px;
    right: 24px;
    background: var(--card);
    border: 1px solid rgba(255,215,0,0.3);
    border-radius: 14px;
    padding: 14px 20px;
    display: flex;
    align-items: center;
    gap: 10px;
    font-weight: 600;
    color: var(--xp);
    z-index: 200;
    box-shadow: 0 8px 32px rgba(0,0,0,0.5);
    animation: popIn 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
    pointer-events: none;
  }

  @keyframes popIn {
    from { opacity: 0; transform: translateX(20px) scale(0.9); }
    to { opacity: 1; transform: translateX(0) scale(1); }
  }

  .xp-popup.hiding { animation: popOut 0.3s ease forwards; }

  @keyframes popOut {
    to { opacity: 0; transform: translateX(20px) scale(0.9); }
  }

  /* ── RESPONSIVE ── */
  @media (max-width: 900px) {
    .app { grid-template-columns: 1fr; }
    .sidebar { display: none; }
    .stats-grid { grid-template-columns: 1fr 1fr; }
    .dashboard-grid { grid-template-columns: 1fr; }
    .stats-page-grid { grid-template-columns: 1fr; }
  }

  /* ── UTILITY ── */
  .flex { display: flex; }
  .items-center { align-items: center; }
  .gap-2 { gap: 8px; }
  .mt-4 { margin-top: 16px; }
  .empty-state {
    text-align: center;
    padding: 32px;
    color: var(--muted);
    font-size: 0.88rem;
  }
  .empty-state .empty-icon { font-size: 2rem; margin-bottom: 8px; }

  /* Session active flash */
  .session-active .timer-display {
    animation: pulse 2s ease-in-out infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.7; }
  }

  .tag {
    font-size: 0.72rem;
    padding: 2px 8px;
    border-radius: 99px;
    font-weight: 600;
    background: rgba(124,92,252,0.15);
    color: var(--accent);
  }
</style>
</head>
<body>

<div class="app">

  <!-- TOPBAR -->
  <header class="topbar">
    <div class="logo">⚡ FocusZone</div>
    <div class="topbar-right">
      <div class="xp-badge">⭐ <span id="totalXP">320</span> XP</div>
      <div class="level-badge">🏆 Niveau <span id="userLevel">4</span></div>
    </div>
  </header>

  <!-- SIDEBAR -->
  <aside class="sidebar">
    <div class="nav-item active" data-page="dashboard" onclick="navigate('dashboard')">
      <span class="icon">🏠</span> Tableau de bord
    </div>
    <div class="nav-item" data-page="todo" onclick="navigate('todo')">
      <span class="icon">✅</span> To-do list
    </div>
    <div class="nav-item" data-page="pomodoro" onclick="navigate('pomodoro')">
      <span class="icon">⏱️</span> Session focus
    </div>
    <div class="nav-item" data-page="habits" onclick="navigate('habits')">
      <span class="icon">🔥</span> Habitudes
    </div>

    <div class="sidebar-section">Progression</div>
    <div class="nav-item" data-page="stats" onclick="navigate('stats')">
      <span class="icon">📊</span> Statistiques
    </div>

    <div class="progress-section">
      <div class="progress-label">
        <span>Niveau <span id="sideLevel">4</span></span>
        <span id="progressText">320 / 500 XP</span>
      </div>
      <div class="progress-bar">
        <div class="progress-fill" id="xpBar" style="width: 64%"></div>
      </div>
    </div>
  </aside>

  <!-- MAIN -->
  <main class="main">

    <!-- DASHBOARD -->
    <div class="page active" id="page-dashboard">
      <div class="page-title">Bonjour ! 👋</div>
      <div class="page-sub">Voici ton résumé de la journée</div>

      <div class="stats-grid">
        <div class="stat-card purple">
          <div class="stat-icon">⏱️</div>
          <div class="stat-value" id="statMinutes">0</div>
          <div class="stat-label">Minutes focus</div>
        </div>
        <div class="stat-card pink">
          <div class="stat-icon">✅</div>
          <div class="stat-value" id="statTasks">0</div>
          <div class="stat-label">Tâches faites</div>
        </div>
        <div class="stat-card green">
          <div class="stat-icon">🔥</div>
          <div class="stat-value" id="statStreak">3</div>
          <div class="stat-label">Jours de suite</div>
        </div>
        <div class="stat-card gold">
          <div class="stat-icon">⭐</div>
          <div class="stat-value" id="statXpToday">0</div>
          <div class="stat-label">XP aujourd'hui</div>
        </div>
      </div>

      <div class="dashboard-grid">
        <div class="card">
          <div class="card-title">
            Tâches prioritaires
            <button class="btn btn-ghost btn-sm" onclick="navigate('todo')">Voir tout →</button>
          </div>
          <div id="dashTodoList" class="todo-list">
            <div class="empty-state"><div class="empty-icon">📋</div>Aucune tâche — ajoutes-en dans To-do list !</div>
          </div>
        </div>

        <div class="card">
          <div class="card-title">Habitudes du jour</div>
          <div id="dashHabits" class="habits-grid"></div>
        </div>
      </div>
    </div>

    <!-- TODO PAGE -->
    <div class="page" id="page-todo">
      <div class="page-title">To-do list ✅</div>
      <div class="page-sub">Organise et priorise ton travail</div>

      <div class="card">
        <div class="todo-input-row">
          <input type="text" id="todoInput" placeholder="Nouvelle tâche…" onkeydown="if(event.key==='Enter') addTodo()">
          <select id="todoPriority" style="width:130px">
            <option value="high">🔴 Urgent</option>
            <option value="medium" selected>🟡 Normal</option>
            <option value="low">🟢 Facile</option>
          </select>
          <button class="btn btn-primary" onclick="addTodo()">+ Ajouter</button>
        </div>

        <div id="todoList" class="todo-list">
          <div class="empty-state"><div class="empty-icon">✨</div>Aucune tâche pour l'instant. C'est parti !</div>
        </div>
      </div>
    </div>

    <!-- POMODORO PAGE -->
    <div class="page" id="page-pomodoro">
      <div class="page-title">Session focus ⏱️</div>
      <div class="page-sub">Entre dans ta bulle, 0 distraction</div>

      <div style="display:grid; grid-template-columns: 1fr 280px; gap: 20px;">
        <div class="card">
          <div class="pomodoro-wrapper">
            <div class="timer-modes">
              <button class="mode-pill active" onclick="setMode('focus', this)">Focus 25min</button>
              <button class="mode-pill" onclick="setMode('short', this)">Pause 5min</button>
              <button class="mode-pill" onclick="setMode('long', this)">Grande pause 15min</button>
            </div>

            <div class="timer-circle">
              <svg width="240" height="240" viewBox="0 0 240 240">
                <defs>
                  <linearGradient id="timerGrad" x1="0%" y1="0%" x2="100%" y2="100%">
                    <stop offset="0%" style="stop-color:#7c5cfc"/>
                    <stop offset="100%" style="stop-color:#fc5c7d"/>
                  </linearGradient>
                </defs>
                <circle class="timer-track" cx="120" cy="120" r="108"/>
                <circle class="timer-progress" id="timerArc" cx="120" cy="120" r="108"
                  stroke-dasharray="678.58" stroke-dashoffset="0"/>
              </svg>
              <div class="timer-center">
                <div class="timer-display" id="timerDisplay">25:00</div>
                <div class="timer-mode" id="timerMode">Focus</div>
              </div>
            </div>

            <div class="timer-controls">
              <button class="timer-btn timer-btn-secondary" onclick="resetTimer()">↺</button>
              <button class="timer-btn timer-btn-main" id="timerPlayBtn" onclick="toggleTimer()">▶</button>
              <button class="timer-btn timer-btn-secondary" onclick="skipTimer()">⏭</button>
            </div>

            <div class="sessions-dots" id="sessionDots">
              <div class="session-dot"></div>
              <div class="session-dot"></div>
              <div class="session-dot"></div>
              <div class="session-dot"></div>
            </div>
          </div>
        </div>

        <div style="display:flex; flex-direction:column; gap:16px;">
          <div class="card">
            <div class="card-title">Session en cours</div>
            <div style="font-size:0.85rem; color:var(--muted); margin-bottom:12px">Quelle tâche travailles-tu ?</div>
            <select id="sessionTask" style="width:100%; margin-bottom:12px">
              <option value="">— Choisir une tâche —</option>
            </select>
            <div style="font-size:0.78rem; color:var(--muted)">Sessions complétées aujourd'hui</div>
            <div class="stat-value" style="font-size:1.8rem; margin-top:4px;" id="sessionCount">0</div>
          </div>

          <div class="card">
            <div class="card-title">Conseils 💡</div>
            <div style="font-size:0.83rem; color:var(--muted); line-height:1.6;" id="tipText">
              Lance une session de 25 minutes de focus intense, puis prends une vraie pause de 5 minutes. Ton cerveau consolidera mieux !
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- HABITS PAGE -->
    <div class="page" id="page-habits">
      <div class="page-title">Habitudes 🔥</div>
      <div class="page-sub">Construis ta routine de champion</div>

      <div class="card" style="margin-bottom: 20px;">
        <div class="card-title">Ajouter une habitude</div>
        <div class="todo-input-row">
          <input type="text" id="habitInput" placeholder="ex: Lire 20 minutes…" onkeydown="if(event.key==='Enter') addHabit()">
          <select id="habitIcon" style="width:100px">
            <option>📚</option>
            <option>🏃</option>
            <option>🧘</option>
            <option>✍️</option>
            <option>💧</option>
            <option>🎵</option>
            <option>🌅</option>
            <option>💪</option>
          </select>
          <button class="btn btn-primary" onclick="addHabit()">+ Ajouter</button>
        </div>
      </div>

      <div class="card">
        <div class="card-title">Mes habitudes <span class="tag" id="habitCount">0 habitudes</span></div>
        <div id="habitList" class="habits-grid">
          <div class="empty-state"><div class="empty-icon">🌱</div>Ajoute ta première habitude !</div>
        </div>
      </div>
    </div>

    <!-- STATS PAGE -->
    <div class="page" id="page-stats">
      <div class="page-title">Statistiques 📊</div>
      <div class="page-sub">Ta progression en un coup d'œil</div>

      <div class="stats-page-grid">
        <div class="card">
          <div class="card-title">Focus cette semaine (min)</div>
          <div class="bar-chart" id="weekChart"></div>
        </div>

        <div class="card">
          <div class="card-title">Activité (30 derniers jours)</div>
          <div class="heatmap-grid" id="heatmap"></div>
        </div>

        <div class="card" style="grid-column: 1 / -1;">
          <div class="card-title">🏅 Succès</div>
          <div class="achievements-grid" id="achievementsGrid"></div>
        </div>
      </div>
    </div>

  </main>
</div>

<!-- XP POPUP -->
<div class="xp-popup" id="xpPopup" style="display:none">
  ⭐ <span id="xpPopupAmount">+10 XP</span>
</div>

<script>
// ── STATE ──
let state = {
  xp: 320,
  level: 4,
  todayXP: 0,
  todayMinutes: 0,
  todayTasks: 0,
  streak: 3,
  todos: [],
  habits: [],
  sessions: 0,
  weekData: [45, 30, 60, 0, 90, 25, 0],
};

// Load from localStorage
try {
  const saved = localStorage.getItem('focuszone');
  if (saved) {
    const parsed = JSON.parse(saved);
    Object.assign(state, parsed);
  }
} catch(e) {}

function save() {
  try { localStorage.setItem('focuszone', JSON.stringify(state)); } catch(e) {}
}

// ── XP SYSTEM ──
function gainXP(amount, reason) {
  state.xp += amount;
  state.todayXP += amount;
  save();
  updateXPDisplay();
  showXPPopup(`+${amount} XP`);
}

function updateXPDisplay() {
  const xpPerLevel = 500;
  const level = Math.floor(state.xp / xpPerLevel) + 1;
  state.level = level;
  const progress = (state.xp % xpPerLevel) / xpPerLevel * 100;
  const xpInLevel = state.xp % xpPerLevel;

  document.getElementById('totalXP').textContent = state.xp;
  document.getElementById('userLevel').textContent = level;
  document.getElementById('sideLevel').textContent = level;
  document.getElementById('progressText').textContent = `${xpInLevel} / ${xpPerLevel} XP`;
  document.getElementById('xpBar').style.width = progress + '%';
  document.getElementById('statXpToday').textContent = state.todayXP;
  document.getElementById('statMinutes').textContent = state.todayMinutes;
  document.getElementById('statTasks').textContent = state.todayTasks;
}

function showXPPopup(text) {
  const popup = document.getElementById('xpPopup');
  document.getElementById('xpPopupAmount').textContent = text;
  popup.style.display = 'flex';
  popup.classList.remove('hiding');
  setTimeout(() => {
    popup.classList.add('hiding');
    setTimeout(() => { popup.style.display = 'none'; }, 300);
  }, 2000);
}

// ── NAVIGATION ──
function navigate(page) {
  document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n => n.classList.remove('active'));
  document.getElementById('page-' + page).classList.add('active');
  document.querySelector(`[data-page="${page}"]`)?.classList.add('active');
  if (page === 'dashboard') refreshDashboard();
  if (page === 'stats') renderStats();
}

// ── TODO ──
function addTodo() {
  const input = document.getElementById('todoInput');
  const text = input.value.trim();
  if (!text) return;
  const todo = {
    id: Date.now(),
    text,
    priority: document.getElementById('todoPriority').value,
    done: false,
    date: new Date().toDateString()
  };
  state.todos.unshift(todo);
  input.value = '';
  save();
  renderTodos();
  updateSessionTasks();
  gainXP(5, 'Tâche créée');
}

function toggleTodo(id) {
  const todo = state.todos.find(t => t.id === id);
  if (!todo) return;
  todo.done = !todo.done;
  if (todo.done) {
    state.todayTasks++;
    gainXP(20, 'Tâche complétée');
  } else {
    state.todayTasks = Math.max(0, state.todayTasks - 1);
    state.xp = Math.max(0, state.xp - 20);
    state.todayXP = Math.max(0, state.todayXP - 20);
    updateXPDisplay();
  }
  save();
  renderTodos();
  refreshDashboard();
}

function deleteTodo(id) {
  state.todos = state.todos.filter(t => t.id !== id);
  save();
  renderTodos();
  updateSessionTasks();
  refreshDashboard();
}

function renderTodos() {
  const list = document.getElementById('todoList');
  if (!state.todos.length) {
    list.innerHTML = '<div class="empty-state"><div class="empty-icon">✨</div>Aucune tâche pour l\'instant. C\'est parti !</div>';
    return;
  }
  list.innerHTML = state.todos.map(todo => `
    <div class="todo-item ${todo.done ? 'done' : ''}" id="todo-${todo.id}">
      <div class="todo-check ${todo.done ? 'checked' : ''}" onclick="toggleTodo(${todo.id})">
        ${todo.done ? '✓' : ''}
      </div>
      <span class="todo-text">${escHtml(todo.text)}</span>
      <span class="todo-priority priority-${todo.priority}">
        ${todo.priority === 'high' ? '🔴 Urgent' : todo.priority === 'medium' ? '🟡 Normal' : '🟢 Facile'}
      </span>
      <span class="todo-delete" onclick="deleteTodo(${todo.id})">✕</span>
    </div>
  `).join('');
}

function escHtml(str) {
  return str.replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;');
}

// ── HABITS ──
const DAYS = ['L','M','M','J','V','S','D'];

function addHabit() {
  const input = document.getElementById('habitInput');
  const text = input.value.trim();
  if (!text) return;
  const habit = {
    id: Date.now(),
    name: text,
    icon: document.getElementById('habitIcon').value,
    streak: 0,
    days: [false, false, false, false, false, false, false]
  };
  state.habits.push(habit);
  input.value = '';
  save();
  renderHabits();
  gainXP(10, 'Habitude créée');
}

function toggleHabitDay(hid, dayIdx) {
  const habit = state.habits.find(h => h.id === hid);
  if (!habit) return;
  habit.days[dayIdx] = !habit.days[dayIdx];
  // Update streak
  habit.streak = habit.days.filter(Boolean).length;
  save();
  renderHabits();
  if (habit.days[dayIdx]) gainXP(15, 'Habitude accomplie');
}

function deleteHabit(id) {
  state.habits = state.habits.filter(h => h.id !== id);
  save();
  renderHabits();
}

function renderHabits() {
  const list = document.getElementById('habitList');
  const count = document.getElementById('habitCount');
  count.textContent = state.habits.length + ' habitude' + (state.habits.length > 1 ? 's' : '');

  if (!state.habits.length) {
    list.innerHTML = '<div class="empty-state"><div class="empty-icon">🌱</div>Ajoute ta première habitude !</div>';
    return;
  }

  list.innerHTML = state.habits.map(h => `
    <div class="habit-row">
      <span class="habit-icon">${h.icon}</span>
      <span class="habit-name">${escHtml(h.name)}</span>
      <span class="habit-streak">🔥 ${h.streak}j</span>
      <div class="habit-days">
        ${h.days.map((done, i) => `
          <div class="habit-day ${done ? 'done' : ''} ${i === new Date().getDay() - 1 ? 'today' : ''}"
               onclick="toggleHabitDay(${h.id}, ${i})">${DAYS[i]}</div>
        `).join('')}
      </div>
      <span class="todo-delete" onclick="deleteHabit(${h.id})" style="margin-left:4px">✕</span>
    </div>
  `).join('');
}

// ── POMODORO ──
let timerInterval = null;
let timerRunning = false;
let timerSeconds = 25 * 60;
let timerTotal = 25 * 60;
let currentMode = 'focus';
let completedSessions = 0;

const MODES = {
  focus: { label: 'Focus', seconds: 25 * 60 },
  short: { label: 'Pause courte', seconds: 5 * 60 },
  long: { label: 'Grande pause', seconds: 15 * 60 },
};

const TIPS = [
  "Lance une session de 25 min de focus intense. Ton cerveau consolide mieux après une vraie pause !",
  "Éloigne ton téléphone. La simple présence d'un smartphone réduit les capacités cognitives.",
  "Commencer est la partie la plus difficile. Lance juste 5 minutes.",
  "Le Pomodoro fonctionne parce qu'il rend le travail fini. 25 minutes, c'est supportable !",
  "Note tout ce qui te distrait sur un papier. Tu t'en occuperas après la session.",
];

function setMode(mode, btn) {
  currentMode = mode;
  document.querySelectorAll('.mode-pill').forEach(p => p.classList.remove('active'));
  btn.classList.add('active');
  resetTimer();
  document.getElementById('tipText').textContent = TIPS[Math.floor(Math.random() * TIPS.length)];
}

function updateTimerDisplay() {
  const m = Math.floor(timerSeconds / 60).toString().padStart(2,'0');
  const s = (timerSeconds % 60).toString().padStart(2,'0');
  document.getElementById('timerDisplay').textContent = `${m}:${s}`;
  document.getElementById('timerMode').textContent = MODES[currentMode].label;

  const circ = 2 * Math.PI * 108;
  const progress = timerSeconds / timerTotal;
  document.getElementById('timerArc').style.strokeDashoffset = circ * (1 - progress);
}

function toggleTimer() {
  if (timerRunning) {
    clearInterval(timerInterval);
    timerRunning = false;
    document.getElementById('timerPlayBtn').textContent = '▶';
    document.querySelector('.pomodoro-wrapper').classList.remove('session-active');
  } else {
    timerRunning = true;
    document.getElementById('timerPlayBtn').textContent = '⏸';
    document.querySelector('.pomodoro-wrapper').classList.add('session-active');
    timerInterval = setInterval(() => {
      timerSeconds--;
      updateTimerDisplay();
      if (timerSeconds <= 0) {
        clearInterval(timerInterval);
        timerRunning = false;
        document.getElementById('timerPlayBtn').textContent = '▶';
        document.querySelector('.pomodoro-wrapper').classList.remove('session-active');
        onTimerEnd();
      }
    }, 1000);
  }
}

function resetTimer() {
  clearInterval(timerInterval);
  timerRunning = false;
  timerSeconds = MODES[currentMode].seconds;
  timerTotal = timerSeconds;
  document.getElementById('timerPlayBtn').textContent = '▶';
  document.querySelector('.pomodoro-wrapper')?.classList.remove('session-active');
  updateTimerDisplay();
}

function skipTimer() {
  clearInterval(timerInterval);
  timerRunning = false;
  timerSeconds = 0;
  updateTimerDisplay();
  onTimerEnd();
}

function onTimerEnd() {
  if (currentMode === 'focus') {
    completedSessions++;
    const mins = 25;
    state.todayMinutes += mins;
    // Update week data for today
    const day = new Date().getDay();
    const idx = day === 0 ? 6 : day - 1;
    state.weekData[idx] = (state.weekData[idx] || 0) + mins;
    state.sessions++;
    document.getElementById('sessionCount').textContent = state.sessions;
    save();
    gainXP(50, 'Session focus');
    updateSessionDots();
    updateXPDisplay();
  }
  // Auto switch
  if (currentMode === 'focus') {
    const nextMode = completedSessions % 4 === 0 ? 'long' : 'short';
    setModeAuto(nextMode);
  } else {
    setModeAuto('focus');
  }
}

function setModeAuto(mode) {
  currentMode = mode;
  document.querySelectorAll('.mode-pill').forEach((p, i) => {
    p.classList.toggle('active', ['focus','short','long'][i] === mode);
  });
  resetTimer();
}

function updateSessionDots() {
  const dots = document.querySelectorAll('.session-dot');
  dots.forEach((d, i) => {
    d.classList.toggle('done', i < completedSessions % 4);
  });
}

function updateSessionTasks() {
  const sel = document.getElementById('sessionTask');
  if (!sel) return;
  const current = sel.value;
  sel.innerHTML = '<option value="">— Choisir une tâche —</option>' +
    state.todos.filter(t => !t.done).map(t =>
      `<option value="${t.id}" ${t.id == current ? 'selected' : ''}>${escHtml(t.text)}</option>`
    ).join('');
}

// ── DASHBOARD ──
function refreshDashboard() {
  updateXPDisplay();
  document.getElementById('statStreak').textContent = state.streak;

  // Todo preview
  const dashList = document.getElementById('dashTodoList');
  const pending = state.todos.filter(t => !t.done).slice(0, 4);
  if (!pending.length) {
    dashList.innerHTML = '<div class="empty-state"><div class="empty-icon">🎉</div>Toutes les tâches sont faites !</div>';
  } else {
    dashList.innerHTML = pending.map(todo => `
      <div class="todo-item ${todo.done ? 'done' : ''}">
        <div class="todo-check ${todo.done ? 'checked' : ''}" onclick="toggleTodo(${todo.id}); refreshDashboard()">
          ${todo.done ? '✓' : ''}
        </div>
        <span class="todo-text">${escHtml(todo.text)}</span>
        <span class="todo-priority priority-${todo.priority}">
          ${todo.priority === 'high' ? '🔴' : todo.priority === 'medium' ? '🟡' : '🟢'}
        </span>
      </div>
    `).join('');
  }

  // Habit preview
  const dashHabits = document.getElementById('dashHabits');
  if (!state.habits.length) {
    dashHabits.innerHTML = '<div class="empty-state"><div class="empty-icon">🌱</div>Ajoute des habitudes !</div>';
  } else {
    const today = new Date().getDay();
    const idx = today === 0 ? 6 : today - 1;
    dashHabits.innerHTML = state.habits.slice(0, 4).map(h => `
      <div class="habit-row">
        <span class="habit-icon">${h.icon}</span>
        <span class="habit-name">${escHtml(h.name)}</span>
        <span class="habit-streak">🔥 ${h.streak}j</span>
        <div class="habit-day ${h.days[idx] ? 'done' : 'today'}"
             onclick="toggleHabitDay(${h.id}, ${idx}); refreshDashboard()">
          ${h.days[idx] ? '✓' : DAYS[idx]}
        </div>
      </div>
    `).join('');
  }
}

// ── STATS ──
const ACHIEVEMENTS = [
  { icon: '🚀', name: 'Premier décollage', desc: 'Première session focus', unlocked: true },
  { icon: '🔥', name: 'Série de feu', desc: '7 jours consécutifs', unlocked: false },
  { icon: '💎', name: 'Diamant', desc: '1000 XP gagnés', unlocked: false },
  { icon: '⏰', name: 'Maître du temps', desc: '10h de focus total', unlocked: false },
  { icon: '📚', name: 'Bibliothécaire', desc: '50 tâches complétées', unlocked: false },
  { icon: '🏆', name: 'Champion', desc: 'Niveau 10 atteint', unlocked: false },
];

function renderStats() {
  // Bar chart
  const chart = document.getElementById('weekChart');
  const max = Math.max(...state.weekData, 1);
  const days = ['Lun', 'Mar', 'Mer', 'Jeu', 'Ven', 'Sam', 'Dim'];
  chart.innerHTML = state.weekData.map((v, i) => `
    <div class="bar-wrap">
      <div class="bar" style="height: ${(v / max) * 90}px; opacity: ${v > 0 ? 1 : 0.3}"></div>
      <div class="bar-label">${days[i]}</div>
    </div>
  `).join('');

  // Heatmap
  const heatmap = document.getElementById('heatmap');
  const cells = Array.from({length: 35}, (_, i) => {
    const lvl = Math.random() > 0.6 ? Math.ceil(Math.random() * 4) : 0;
    return `<div class="heatmap-cell" data-level="${lvl}" title="${lvl > 0 ? lvl * 25 + ' min' : 'Pas de session'}"></div>`;
  });
  heatmap.innerHTML = cells.join('');

  // Achievements
  const xpThreshold = state.xp >= 1000;
  const lvl10 = state.level >= 10;
  const unlocked = [true, state.streak >= 7, xpThreshold, state.todayMinutes >= 600, state.todayTasks >= 50, lvl10];
  const achievementsGrid = document.getElementById('achievementsGrid');
  achievementsGrid.innerHTML = ACHIEVEMENTS.map((a, i) => `
    <div class="achievement ${unlocked[i] ? 'unlocked' : 'locked'}">
      <div class="achievement-icon">${a.icon}</div>
      <div class="achievement-name">${a.name}</div>
      <div class="achievement-desc">${a.desc}</div>
    </div>
  `).join('');
}

// ── INIT ──
renderTodos();
renderHabits();
updateTimerDisplay();
updateXPDisplay();
refreshDashboard();
updateSessionTasks();
</script>
</body>
</html>
