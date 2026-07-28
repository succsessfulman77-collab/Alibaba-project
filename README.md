<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<title>Challenge Tracker</title>
<style>
/* ===== iOS 27 DARK SYSTEM ===== */
:root {
  --blue: #0A84FF; --green: #30D158; --red: #FF453A; --orange: #FF9F0A;
  --yellow: #FFD60A; --purple: #BF5AF2; --pink: #FF375F; --teal: #64D2FF;
  --indigo: #5E5CE6; --gray: #8E8E93;
  --bg0: #000000; --bg1: #1C1C1E; --bg2: #2C2C2E; --bg3: #3A3A3C;
  --sep: #38383A; --sep2: #48484A;
  --txt: #FFFFFF; --txt2: rgba(255,255,255,0.6); --txt3: rgba(255,255,255,0.35); --txt4: rgba(255,255,255,0.18);
  --r-sm: 10px; --r-md: 14px; --r-lg: 20px; --r-xl: 24px; --r-2xl: 28px;
}
@media (prefers-color-scheme: light) {
  :root {
    --bg0: #F2F2F7; --bg1: #FFFFFF; --bg2: #F2F2F7; --bg3: #E5E5EA;
    --sep: #E5E5EA; --sep2: #D1D1D6;
    --txt: #000000; --txt2: rgba(0,0,0,0.55); --txt3: rgba(0,0,0,0.3); --txt4: rgba(0,0,0,0.15);
  }
}
* { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }
html, body { height: 100%; overflow: hidden; font-family: -apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Helvetica Neue', Arial, sans-serif; background: var(--bg0); color: var(--txt); font-size: 17px; line-height: 1.3; letter-spacing: -0.021em; -webkit-font-smoothing: antialiased; }
#app { height: 100%; display: flex; flex-direction: column; }
.status-bar { height: max(44px, env(safe-area-inset-top)); flex-shrink: 0; }
.content { flex: 1; overflow-y: auto; -webkit-overflow-scrolling: touch; padding-bottom: calc(80px + env(safe-area-inset-bottom)); }
.content::-webkit-scrollbar { display: none; }

/* Nav */
.nav { padding: 8px 16px; padding-top: calc(8px + env(safe-area-inset-top)); position: sticky; top: 0; z-index: 100; background: rgba(0,0,0,0.75); backdrop-filter: saturate(180%) blur(24px); -webkit-backdrop-filter: saturate(180%) blur(24px); border-bottom: 0.5px solid var(--sep); }
@media (prefers-color-scheme: light) { .nav { background: rgba(242,242,247,0.85); } }
.nav-inner { display: flex; align-items: center; justify-content: space-between; max-width: 680px; margin: 0 auto; }
.nav-title { font-size: 28px; font-weight: 700; letter-spacing: -0.021em; }
.nav-btn { width: 36px; height: 36px; border-radius: 50%; background: var(--blue); color: white; border: none; display: flex; align-items: center; justify-content: center; font-size: 22px; cursor: pointer; transition: transform 0.15s; }
.nav-btn:active { transform: scale(0.88); }

/* Tab Bar */
.tabs { position: fixed; bottom: 0; left: 0; right: 0; height: calc(64px + env(safe-area-inset-bottom)); padding-bottom: env(safe-area-inset-bottom); background: rgba(28,28,30,0.72); backdrop-filter: saturate(180%) blur(24px); -webkit-backdrop-filter: saturate(180%) blur(24px); border-top: 0.5px solid var(--sep); display: flex; justify-content: space-around; align-items: center; z-index: 1000; }
@media (prefers-color-scheme: light) { .tabs { background: rgba(255,255,255,0.72); } }
.tab { display: flex; flex-direction: column; align-items: center; gap: 3px; padding: 6px 12px; cursor: pointer; transition: all 0.2s; border: none; background: none; color: var(--txt3); font-family: inherit; }
.tab.active { color: var(--blue); }
.tab:active { transform: scale(0.9); }
.tab-icon { font-size: 22px; line-height: 1; transition: transform 0.2s cubic-bezier(0.34, 1.56, 0.64, 1); }
.tab.active .tab-icon { transform: translateY(-2px); }
.tab-label { font-size: 10px; font-weight: 500; }

/* Screens */
.screen { display: none; animation: enter 0.35s cubic-bezier(0.25, 0.46, 0.45, 0.94); }
.screen.active { display: block; }
@keyframes enter { from { opacity: 0; transform: translateY(12px); } to { opacity: 1; transform: translateY(0); } }

/* Cards */
.group { background: var(--bg1); border-radius: var(--r-lg); margin: 12px 16px; overflow: hidden; box-shadow: 0 1px 3px rgba(0,0,0,0.15); }
.cell { display: flex; align-items: center; padding: 12px 16px; gap: 12px; border-bottom: 0.5px solid var(--sep); min-height: 52px; cursor: pointer; transition: background 0.1s; }
.cell:last-child { border-bottom: none; }
.cell:active { background: rgba(128,128,128,0.12); }
.cell-icon { width: 32px; height: 32px; border-radius: 8px; display: flex; align-items: center; justify-content: center; font-size: 18px; flex-shrink: 0; }
.cell-body { flex: 1; min-width: 0; }
.cell-title { font-size: 16px; font-weight: 400; }
.cell-sub { font-size: 13px; color: var(--txt2); margin-top: 1px; }
.cell-right { font-size: 16px; color: var(--txt2); flex-shrink: 0; display: flex; gap: 6px; align-items: center; }

/* Progress */
.progress { height: 5px; background: var(--bg3); border-radius: 2.5px; overflow: hidden; margin-top: 8px; }
.progress > div { height: 100%; border-radius: 2.5px; transition: width 0.6s cubic-bezier(0.25, 0.46, 0.45, 0.94); }

/* Hero Stats */
.hero { display: flex; gap: 10px; padding: 16px; padding-bottom: 8px; }
.stat-pill { flex: 1; background: var(--bg1); border-radius: var(--r-lg); padding: 16px; text-align: center; position: relative; overflow: hidden; }
.stat-pill::before { content: ''; position: absolute; top: 0; left: 0; right: 0; height: 3px; background: linear-gradient(90deg, var(--blue), var(--teal)); }
.stat-val { font-size: 30px; font-weight: 700; letter-spacing: -0.021em; line-height: 1; font-variant-numeric: tabular-nums; }
.stat-label { font-size: 11px; color: var(--txt2); margin-top: 6px; font-weight: 600; text-transform: uppercase; letter-spacing: 0.05em; }

/* Section */
.sec-head { padding: 20px 16px 8px; display: flex; align-items: baseline; justify-content: space-between; max-width: 680px; margin: 0 auto; }
.sec-title { font-size: 20px; font-weight: 700; }
.sec-action { font-size: 15px; color: var(--blue); font-weight: 500; background: none; border: none; cursor: pointer; font-family: inherit; }
.sec-action:active { opacity: 0.6; }

/* Focus Hero */
.focus-hero { background: linear-gradient(135deg, #0f0c29, #302b63, #24243e); border-radius: var(--r-xl); margin: 16px; padding: 28px 20px; text-align: center; position: relative; overflow: hidden; box-shadow: 0 8px 32px rgba(10,132,255,0.2); }
.focus-hero::after { content: ''; position: absolute; top: -50%; left: -50%; width: 200%; height: 200%; background: radial-gradient(circle at 30% 30%, rgba(10,132,255,0.1), transparent 60%); pointer-events: none; }
.focus-label { font-size: 12px; font-weight: 600; text-transform: uppercase; letter-spacing: 0.08em; color: rgba(255,255,255,0.4); margin-bottom: 16px; }
.timer-wrap { position: relative; width: 180px; height: 180px; margin: 0 auto 16px; }
.timer-svg { transform: rotate(-90deg); width: 100%; height: 100%; }
.timer-svg circle { fill: none; stroke-width: 8; }
.timer-bg { stroke: rgba(255,255,255,0.08); }
.timer-bar { stroke: url(#tg); stroke-linecap: round; transition: stroke-dashoffset 0.3s linear; }
.timer-text { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); font-size: 44px; font-weight: 200; letter-spacing: -0.03em; font-variant-numeric: tabular-nums; color: white; }
.chips { display: flex; gap: 8px; justify-content: center; margin: 16px 0; flex-wrap: wrap; }
.chip { padding: 8px 16px; border-radius: 50px; border: 1px solid rgba(255,255,255,0.15); background: rgba(255,255,255,0.06); color: rgba(255,255,255,0.7); font-size: 14px; font-weight: 600; cursor: pointer; transition: all 0.2s; font-family: inherit; }
.chip.on { background: var(--blue); color: white; border-color: var(--blue); box-shadow: 0 2px 12px rgba(10,132,255,0.4); }
.chip:active { transform: scale(0.92); }
.app-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; margin: 14px 0; }
.app-item { display: flex; flex-direction: column; align-items: center; gap: 4px; padding: 8px; border-radius: var(--r-md); cursor: pointer; transition: all 0.2s; border: 1.5px solid transparent; background: rgba(255,255,255,0.04); }
.app-item:active { transform: scale(0.9); }
.app-item.locked { border-color: var(--red); background: rgba(255,69,58,0.08); }
.app-icon { width: 42px; height: 42px; border-radius: 11px; background: rgba(255,255,255,0.08); display: flex; align-items: center; justify-content: center; font-size: 20px; }
.app-item.locked .app-icon { opacity: 0.3; position: relative; }
.app-item.locked .app-icon::after { content: '🔒'; position: absolute; font-size: 18px; opacity: 1; }
.app-name { font-size: 10px; color: rgba(255,255,255,0.5); font-weight: 500; }

/* Overlay */
.overlay { position: fixed; inset: 0; background: #000; z-index: 5000; display: none; flex-direction: column; align-items: center; justify-content: center; color: white; text-align: center; padding: 40px 24px; }
.overlay.on { display: flex; }
.ov-timer { font-size: 72px; font-weight: 200; letter-spacing: -0.03em; font-variant-numeric: tabular-nums; line-height: 1; margin-bottom: 8px; }
.ov-task { font-size: 20px; font-weight: 600; margin-bottom: 8px; }
.ov-msg { font-size: 15px; color: rgba(255,255,255,0.4); margin-bottom: 32px; }
.ov-apps { display: flex; gap: 10px; margin-bottom: 40px; flex-wrap: wrap; justify-content: center; }
.ov-badge { padding: 6px 12px; border-radius: 50px; background: rgba(255,69,58,0.15); border: 1px solid rgba(255,69,58,0.3); font-size: 13px; color: var(--red); font-weight: 500; }
.ov-btn { padding: 14px 36px; border-radius: 50px; border: 1px solid rgba(255,255,255,0.2); background: transparent; color: rgba(255,255,255,0.5); font-size: 16px; font-weight: 600; cursor: pointer; transition: all 0.2s; font-family: inherit; }
.ov-btn:active { background: rgba(255,255,255,0.1); transform: scale(0.95); }

/* Modal */
.modal-bg { position: fixed; inset: 0; background: rgba(0,0,0,0.5); z-index: 4000; display: none; align-items: flex-end; justify-content: center; opacity: 0; transition: opacity 0.3s; }
.modal-bg.on { display: flex; opacity: 1; }
.modal { background: var(--bg1); border-radius: var(--r-2xl) var(--r-2xl) 0 0; width: 100%; max-width: 680px; max-height: 85vh; overflow-y: auto; padding: 16px 16px calc(32px + env(safe-area-inset-bottom)); transform: translateY(100%); transition: transform 0.35s cubic-bezier(0.25, 0.46, 0.45, 0.94); }
.modal-bg.on .modal { transform: translateY(0); }
.modal-handle { width: 36px; height: 5px; border-radius: 3px; background: var(--sep2); margin: 0 auto 16px; }
.modal-title { font-size: 20px; font-weight: 700; text-align: center; margin-bottom: 20px; }
.form { margin-bottom: 16px; }
.form label { display: block; font-size: 13px; font-weight: 500; color: var(--txt2); margin-bottom: 6px; text-transform: uppercase; letter-spacing: 0.04em; }
.form input, .form select { width: 100%; padding: 12px 14px; border-radius: var(--r-md); border: 1px solid var(--sep); background: var(--bg2); color: var(--txt); font-size: 17px; outline: none; transition: border-color 0.2s, box-shadow 0.2s; font-family: inherit; }
.form input:focus, .form select:focus { border-color: var(--blue); box-shadow: 0 0 0 3px rgba(10,132,255,0.15); }
.modal-btns { display: flex; gap: 12px; margin-top: 20px; }
.btn { flex: 1; padding: 14px; border-radius: var(--r-md); font-size: 17px; font-weight: 600; border: none; cursor: pointer; transition: all 0.15s; font-family: inherit; text-align: center; }
.btn:active { transform: scale(0.97); opacity: 0.9; }
.btn-pri { background: var(--blue); color: white; }
.btn-sec { background: var(--bg2); color: var(--blue); }

/* Celebrate */
.celebrate { position: fixed; inset: 0; background: rgba(0,0,0,0.85); backdrop-filter: blur(20px); z-index: 6000; display: none; align-items: center; justify-content: center; flex-direction: column; color: white; text-align: center; padding: 40px; opacity: 0; transition: opacity 0.4s; }
.celebrate.on { display: flex; opacity: 1; }
.celebrate .emoji { font-size: 72px; margin-bottom: 20px; animation: pop 0.5s cubic-bezier(0.34, 1.56, 0.64, 1); }
@keyframes pop { 0% { transform: scale(0) rotate(-20deg); } 100% { transform: scale(1) rotate(0deg); } }
.celebrate h2 { font-size: 32px; font-weight: 700; margin-bottom: 8px; }
.celebrate p { font-size: 17px; color: var(--txt2); margin-bottom: 24px; }
.celebrate .xp { font-size: 48px; font-weight: 700; background: linear-gradient(135deg, var(--blue), var(--teal)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; margin-bottom: 32px; }

/* Empty */
.empty { text-align: center; padding: 48px 24px; color: var(--txt2); }
.empty .icon { font-size: 56px; margin-bottom: 16px; opacity: 0.4; }
.empty h3 { font-size: 19px; font-weight: 600; color: var(--txt); margin-bottom: 6px; }
.empty p { font-size: 15px; }

/* Toast */
.toast { position: fixed; top: calc(60px + env(safe-area-inset-top)); left: 50%; transform: translateX(-50%) translateY(-20px); background: rgba(60,60,67,0.9); backdrop-filter: blur(12px); color: white; padding: 10px 20px; border-radius: 50px; font-size: 14px; font-weight: 500; z-index: 7000; opacity: 0; transition: all 0.3s; pointer-events: none; white-space: nowrap; }
.toast.show { transform: translateX(-50%) translateY(0); opacity: 1; }

/* Charts */
.weekly { display: flex; align-items: flex-end; gap: 10px; height: 140px; padding: 12px 8px; }
.wb { flex: 1; display: flex; flex-direction: column; align-items: center; gap: 5px; }
.wb-track { width: 100%; height: 100px; background: var(--bg3); border-radius: 8px; position: relative; overflow: hidden; }
.wb-fill { position: absolute; bottom: 0; left: 0; right: 0; border-radius: 0 0 8px 8px; background: linear-gradient(180deg, var(--blue), var(--teal)); transition: height 0.6s; }
.wb-label { font-size: 11px; color: var(--txt2); font-weight: 500; }
.wb-val { font-size: 11px; font-weight: 600; }

.heatmap { display: grid; grid-template-columns: repeat(7, 1fr); gap: 4px; padding: 8px; }
.hc { aspect-ratio: 1; border-radius: 4px; background: var(--bg3); transition: all 0.2s; }
.hc.a1 { background: rgba(10,132,255,0.25); }
.hc.a2 { background: rgba(10,132,255,0.5); }
.hc.a3 { background: var(--blue); }
.hc.today { box-shadow: 0 0 0 2px var(--orange); }

/* Achievements */
.ach-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 10px; padding: 0 16px; }
.ach { background: var(--bg1); border-radius: var(--r-lg); padding: 16px 8px; text-align: center; transition: all 0.2s; opacity: 0.3; border: 1px solid transparent; }
.ach.on { opacity: 1; border-color: var(--green); background: linear-gradient(180deg, var(--bg1), rgba(48,209,88,0.06)); }
.ach .ic { font-size: 28px; margin-bottom: 6px; }
.ach .nm { font-size: 12px; font-weight: 600; }
.ach .ds { font-size: 11px; color: var(--txt2); margin-top: 2px; }

/* Stats grid */
.sgrid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 12px; padding: 0 16px; margin-top: 8px; }
.sbox { background: var(--bg1); border-radius: var(--r-lg); padding: 16px; }
.sbox .v { font-size: 28px; font-weight: 700; font-variant-numeric: tabular-nums; }
.sbox .l { font-size: 12px; color: var(--txt2); margin-top: 4px; font-weight: 500; }

/* Past weeks */
.pw { background: var(--bg1); border-radius: var(--r-lg); padding: 16px; margin: 0 16px 10px; }
.pw h4 { font-size: 15px; font-weight: 600; margin-bottom: 4px; }
.pw p { font-size: 13px; color: var(--txt2); }
.pw-bar { height: 6px; background: var(--bg3); border-radius: 3px; overflow: hidden; margin-top: 10px; }
.pw-bar > div { height: 100%; border-radius: 3px; background: linear-gradient(90deg, var(--blue), var(--teal)); }

/* Task selector in focus */
.tsel { display: flex; align-items: center; padding: 12px 16px; gap: 12px; border-bottom: 0.5px solid rgba(255,255,255,0.1); cursor: pointer; transition: background 0.1s; }
.tsel:last-child { border-bottom: none; }
.tsel:active { background: rgba(255,255,255,0.05); }
.trad { width: 22px; height: 22px; border-radius: 50%; border: 2px solid rgba(255,255,255,0.3); display: flex; align-items: center; justify-content: center; flex-shrink: 0; transition: all 0.15s; }
.trad.on { border-color: var(--blue); background: var(--blue); }
.trad.on::after { content: ''; width: 8px; height: 8px; background: white; border-radius: 50%; }

/* Badge */
.badge { display: inline-flex; align-items: center; gap: 4px; padding: 3px 10px; border-radius: 50px; background: rgba(48,209,88,0.15); color: var(--green); font-size: 12px; font-weight: 600; }

/* Responsive */
@media (max-width: 400px) { .app-grid { grid-template-columns: repeat(3, 1fr); } .ach-grid { grid-template-columns: repeat(2, 1fr); } .timer-text { font-size: 36px; } .ov-timer { font-size: 56px; } .stat-val { font-size: 26px; } }
* { scrollbar-width: none; } *::-webkit-scrollbar { display: none; }
</style>
</head>
<body>

<svg width="0" height="0" style="position:absolute;"><defs><linearGradient id="tg" x1="0%" y1="0%" x2="100%" y2="0%"><stop offset="0%" style="stop-color:#0A84FF"/><stop offset="100%" style="stop-color:#64D2FF"/></linearGradient></defs></svg>

<div id="app">
  <div class="status-bar"></div>
  <div class="nav"><div class="nav-inner"><div class="nav-title" id="navTitle">Today</div><button class="nav-btn" id="navBtn" onclick="navAction()">+</button></div></div>
  <div class="content" id="content">

    <!-- TODAY -->
    <div class="screen active" id="sc-today">
      <div class="hero">
        <div class="stat-pill"><div class="stat-val" id="hXP">0</div><div class="stat-label">XP</div></div>
        <div class="stat-pill"><div class="stat-val" id="hLvl">1</div><div class="stat-label">Level</div></div>
        <div class="stat-pill"><div class="stat-val" style="color:var(--orange)" id="hStr">0</div><div class="stat-label">Streak</div></div>
      </div>
      <div class="sec-head"><div class="sec-title">Today's Focus</div><button class="sec-action" onclick="goTab('focus')">Start &rarr;</button></div>
      <div id="todayFocus"></div>
      <div class="sec-head"><div class="sec-title">Active Challenges</div></div>
      <div id="todayChal"></div>
      <div class="sec-head" style="margin-top:8px;"><div class="sec-title">Achievements</div></div>
      <div class="ach-grid" id="todayAch"></div>
    </div>

    <!-- CHALLENGES -->
    <div class="screen" id="sc-chal"><div id="chalList"></div></div>

    <!-- FOCUS -->
    <div class="screen" id="sc-focus">
      <div class="focus-hero">
        <div class="focus-label">Focus Session</div>
        <div class="timer-wrap">
          <svg class="timer-svg" viewBox="0 0 220 220"><circle class="timer-bg" cx="110" cy="110" r="100"/><circle class="timer-bar" id="tCircle" cx="110" cy="110" r="100" stroke-dasharray="628.32" stroke-dashoffset="0"/></svg>
          <div class="timer-text" id="tText">25:00</div>
        </div>
        <div class="chips">
          <button class="chip" data-m="15" onclick="setDur(15,this)">15m</button>
          <button class="chip on" data-m="25" onclick="setDur(25,this)">25m</button>
          <button class="chip" data-m="45" onclick="setDur(45,this)">45m</button>
          <button class="chip" data-m="60" onclick="setDur(60,this)">1h</button>
          <button class="chip" data-m="90" onclick="setDur(90,this)">90m</button>
          <button class="chip" data-m="120" onclick="setDur(120,this)">2h</button>
        </div>
        <div style="text-align:left;margin-top:18px;">
          <div style="font-size:12px;font-weight:600;text-transform:uppercase;letter-spacing:0.07em;color:rgba(255,255,255,0.4);margin-bottom:10px;">Apps to avoid</div>
          <div class="app-grid" id="appGrid"></div>
        </div>
        <div style="text-align:left;margin-top:18px;">
          <div style="font-size:12px;font-weight:600;text-transform:uppercase;letter-spacing:0.07em;color:rgba(255,255,255,0.4);margin-bottom:10px;">Link to challenge</div>
          <div id="taskSel" style="background:rgba(255,255,255,0.06);border-radius:14px;overflow:hidden;"></div>
        </div>
        <button class="btn btn-pri" style="margin-top:24px;width:100%;padding:16px;font-size:17px;border-radius:14px;" onclick="startFocus()">&#9654; Start Focus</button>
      </div>
      <div class="sec-head"><div class="sec-title">Recent Sessions</div></div>
      <div id="recentSess"></div>
    </div>

    <!-- STATS -->
    <div class="screen" id="sc-stats">
      <div class="sgrid" style="margin-top:8px;">
        <div class="sbox"><div class="v" id="stTotal">0</div><div class="l">Total hours</div></div>
        <div class="sbox"><div class="v" id="stDone">0</div><div class="l">Completed</div></div>
        <div class="sbox"><div class="v" id="stBest">0</div><div class="l">Best day (h)</div></div>
        <div class="sbox"><div class="v" id="stAvg">0</div><div class="l">Avg/day (h)</div></div>
      </div>
      <div class="sec-head"><div class="sec-title">This Week</div></div>
      <div class="group"><div class="weekly" id="weekChart"></div></div>
      <div class="sec-head"><div class="sec-title">Activity</div></div>
      <div class="group"><div class="heatmap" id="heatMap"></div></div>
      <div class="sec-head"><div class="sec-title">Past Weeks</div></div>
      <div id="pastWeeks"></div>
    </div>

    <!-- ARCHIVE -->
    <div class="screen" id="sc-arch"><div id="archList"></div></div>

  </div>

  <div class="tabs">
    <button class="tab active" onclick="goTab('today')"><span class="tab-icon">&#127968;</span><span class="tab-label">Today</span></button>
    <button class="tab" onclick="goTab('challenges')"><span class="tab-icon">&#127919;</span><span class="tab-label">Challenges</span></button>
    <button class="tab" onclick="goTab('focus')"><span class="tab-icon">&#129496;</span><span class="tab-label">Focus</span></button>
    <button class="tab" onclick="goTab('stats')"><span class="tab-icon">&#128202;</span><span class="tab-label">Stats</span></button>
    <button class="tab" onclick="goTab('archive')"><span class="tab-icon">&#127942;</span><span class="tab-label">Archive</span></button>
  </div>
</div>

<!-- Focus Overlay -->
<div class="overlay" id="ov">
  <div style="font-size:60px;margin-bottom:20px;animation:pulse 2s infinite;">&#127919;</div>
  <div class="ov-task" id="ovTask">Focus</div>
  <div class="ov-timer" id="ovTime">25:00</div>
  <div class="ov-msg" id="ovMsg">Stay focused. Avoid distractions.</div>
  <div class="ov-apps" id="ovApps"></div>
  <button class="ov-btn" onclick="endEarly()">End Session</button>
  <div style="margin-top:16px;font-size:12px;color:rgba(255,255,255,0.25);">Closing this tab breaks your streak</div>
</div>
<style>@keyframes pulse { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:0.7;transform:scale(1.08)} }</style>

<!-- Celebrate -->
<div class="celebrate" id="cel">
  <div class="emoji" id="celEm">&#127881;</div>
  <h2 id="celT">Focus Complete!</h2>
  <p id="celS">Great work staying focused</p>
  <div class="xp" id="celXP">+25 XP</div>
  <button class="btn btn-pri" style="min-width:200px;" onclick="closeCel()">Continue</button>
</div>

<!-- Task Modal -->
<div class="modal-bg" id="modTask">
  <div class="modal">
    <div class="modal-handle"></div>
    <div class="modal-title">New Challenge</div>
    <div class="form"><label>Name</label><input id="inName" placeholder="e.g. Read 20 books"></div>
    <div class="form"><label>Target hours</label><input id="inTarget" type="number" min="0.5" step="0.5" placeholder="20"></div>
    <div class="form"><label>Category</label>
      <select id="inCat">
        <option value="reading">&#128218; Reading</option><option value="english">&#127758; English</option><option value="coding">&#128187; Coding</option>
        <option value="exercise">&#128170; Exercise</option><option value="design">&#127912; Design</option><option value="math">&#128290; Math</option>
        <option value="writing">&#9997; Writing</option><option value="music">&#127925; Music</option><option value="other">&#128204; Other</option>
      </select>
    </div>
    <div class="modal-btns"><button class="btn btn-sec" onclick="closeTask()">Cancel</button><button class="btn btn-pri" onclick="saveTask()">Save</button></div>
  </div>
</div>

<!-- Log Modal -->
<div class="modal-bg" id="modLog">
  <div class="modal">
    <div class="modal-handle"></div>
    <div class="modal-title">Log Time</div>
    <div class="form"><label>Hours spent</label><input id="inHours" type="number" step="0.1" min="0.1" placeholder="1.5"></div>
    <div class="modal-btns"><button class="btn btn-sec" onclick="closeLog()">Cancel</button><button class="btn btn-pri" onclick="saveLog()">Log</button></div>
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
// ===== CONFIG =====
const LEVELS = [
  {name:'Novice',xp:0},{name:'Apprentice',xp:100},{name:'Scholar',xp:250},
  {name:'Expert',xp:500},{name:'Master',xp:900},{name:'Legend',xp:1500}
];
const CATS = {
  reading:{ic:'📚',c:'#0A84FF'}, english:{ic:'🌍',c:'#FF453A'}, coding:{ic:'💻',c:'#30D158'},
  exercise:{ic:'💪',c:'#FF9F0A'}, design:{ic:'🎨',c:'#BF5AF2'}, math:{ic:'🔢',c:'#5E5CE6'},
  writing:{ic:'✍️',c:'#FF375F'}, music:{ic:'🎵',c:'#64D2FF'}, other:{ic:'📌',c:'#8E8E93'}
};
const APPS = [
  {id:'instagram',ic:'📸',nm:'Instagram'},{id:'tiktok',ic:'🎵',nm:'TikTok'},{id:'youtube',ic:'▶️',nm:'YouTube'},
  {id:'games',ic:'🎮',nm:'Games'},{id:'twitter',ic:'🐦',nm:'X'},{id:'snapchat',ic:'👻',nm:'Snapchat'},
  {id:'netflix',ic:'🍿',nm:'Netflix'},{id:'whatsapp',ic:'💬',nm:'WhatsApp'}
];
const ACHS = [
  {id:'first',nm:'First Step',ds:'Create challenge',ic:'🌱'},{id:'bookworm',nm:'Bookworm',ds:'10h reading',ic:'📚'},
  {id:'polyglot',nm:'Polyglot',ds:'10h English',ic:'🌍'},{id:'coder',nm:'Hacker',ds:'10h coding',ic:'💻'},
  {id:'streak3',nm:'On Fire',ds:'3-day streak',ic:'🔥'},{id:'streak7',nm:'Unstoppable',ds:'7-day streak',ic:'⚡'},
  {id:'streak30',nm:'Iron Will',ds:'30-day streak',ic:'💪'},{id:'marathon',nm:'Marathon',ds:'50h total',ic:'🏃'},
  {id:'century',nm:'Century',ds:'100h total',ic:'💯'},{id:'completionist',nm:'Finisher',ds:'Complete one',ic:'🏆'},
  {id:'focus10',nm:'Deep Work',ds:'10 focus sessions',ic:'🎯'},{id:'focus50',nm:'Monk Mode',ds:'50 focus sessions',ic:'🧘'}
];

// ===== STATE =====
let tasks = [], sess = [], curTab = 'today';
let xp = 0, streak = 0, editId = null, logId = null, fDur = 25, fId = null, fSec = 0, fTimer = null, fOn = false, prevLvl = -1;
let selApps = new Set();

// ===== STORAGE =====
function load() {
  try {
    const t = localStorage.getItem('ct_tasks'), s = localStorage.getItem('ct_sess');
    if (t) tasks = JSON.parse(t); if (s) sess = JSON.parse(s);
  } catch(e) {}
  if (!tasks.length) {
    tasks = [
      {id:1,nm:'Read books',tg:20,cur:7.5,cat:'reading',lg:{'2026-07-19':2,'2026-07-20':3,'2026-07-21':2.5},cr:'2026-07-15'},
      {id:2,nm:'Learn English',tg:30,cur:12,cat:'english',lg:{'2026-07-18':1.5,'2026-07-19':2,'2026-07-20':3,'2026-07-21':5.5},cr:'2026-07-15'},
      {id:3,nm:'Coding practice',tg:15,cur:3,cat:'coding',lg:{'2026-07-20':1.5,'2026-07-21':1.5},cr:'2026-07-20'}
    ];
  }
}
function save() { try { localStorage.setItem('ct_tasks',JSON.stringify(tasks)); localStorage.setItem('ct_sess',JSON.stringify(sess)); } catch(e){} }

// ===== UTILS =====
function today() { return new Date().toISOString().slice(0,10); }
function getLvl(x) { for(let i=LEVELS.length-1;i>=0;i--) if(x>=LEVELS[i].xp) return LEVELS[i]; return LEVELS[0]; }
function getLi(x) { for(let i=LEVELS.length-1;i>=0;i--) if(x>=LEVELS[i].xp) return i; return 0; }
function toast(m) { const t=document.getElementById('toast'); t.textContent=m; t.classList.add('show'); setTimeout(()=>t.classList.remove('show'),2500); }
function sound(type) {
  try {
    const ctx=new(window.AudioContext||window.webkitAudioContext)(), o=ctx.createOscillator(), g=ctx.createGain();
    o.connect(g); g.connect(ctx.destination); const n=ctx.currentTime;
    if(type==='start'){o.type='sine';o.frequency.setValueAtTime(523,n);o.frequency.setValueAtTime(659,n+.1);g.gain.setValueAtTime(.1,n);g.gain.exponentialRampToValueAtTime(.001,n+.25);o.start();o.stop(n+.25);}
    else if(type==='done'){o.type='triangle';[523,659,784,1047,1319].forEach((f,i)=>o.frequency.setValueAtTime(f,n+i*.1));g.gain.setValueAtTime(.1,n);g.gain.exponentialRampToValueAtTime(.001,n+.6);o.start();o.stop(n+.6);}
    else if(type==='tick'){o.type='sine';o.frequency.setValueAtTime(880,n);g.gain.setValueAtTime(.03,n);g.gain.exponentialRampToValueAtTime(.001,n+.04);o.start();o.stop(n+.04);}
    else if(type==='log'){o.type='sine';o.frequency.setValueAtTime(440,n);o.frequency.setValueAtTime(554,n+.07);g.gain.setValueAtTime(.06,n);g.gain.exponentialRampToValueAtTime(.001,n+.15);o.start();o.stop(n+.15);}
  }catch(e){}
}
function cel(t,s,x,e) { document.getElementById('celEm').textContent=e; document.getElementById('celT').textContent=t; document.getElementById('celS').textContent=s; document.getElementById('celXP').textContent=x; document.getElementById('cel').classList.add('on'); }
function closeCel() { document.getElementById('cel').classList.remove('on'); }

// ===== CALC =====
function calcXP() {
  let total=0;
  tasks.forEach(t=>{Object.values(t.lg||{}).forEach(h=>total+=h*10); if(t.cur>=t.tg) total+=100;});
  sess.forEach(s=>total+=Math.floor(s.dur/6));
  return Math.floor(total);
}
function calcStr() {
  const dates=new Set();
  tasks.forEach(t=>Object.entries(t.lg||{}).forEach(([d,h])=>{if(h>=1)dates.add(d);}));
  if(!dates.size) return 0;
  const sorted=Array.from(dates).sort(); let s=1;
  for(let i=sorted.length-2;i>=0;i--){const a=new Date(sorted[i+1]),b=new Date(sorted[i]);if((a-b)/864e5===1)s++;else break;}
  const td=today(),last=sorted[sorted.length-1],yd=new Date(Date.now()-864e5).toISOString().slice(0,10);
  if(last!==td&&last!==yd) return 0;
  return s;
}

// ===== TABS =====
function goTab(tab) {
  curTab=tab;
  document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));
  document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
  document.getElementById('sc-'+tab).classList.add('active');
  const idx=['today','challenges','focus','stats','archive'].indexOf(tab);
  document.querySelectorAll('.tab')[idx]?.classList.add('active');
  const titles={today:'Today',challenges:'Challenges',focus:'Focus',stats:'Statistics',archive:'Archive'};
  document.getElementById('navTitle').textContent=titles[tab];
  document.getElementById('navBtn').style.display=(tab==='challenges'||tab==='today')?'flex':'none';
  refresh();
}
function navAction() { if(curTab==='today'||curTab==='challenges') openTask(); }

// ===== RENDER =====
function refresh() {
  updateStats();
  if(curTab==='today') renderToday();
  if(curTab==='challenges') renderChal();
  if(curTab==='focus') renderFocus();
  if(curTab==='stats') renderStats();
  if(curTab==='archive') renderArch();
}

function updateStats() {
  xp=calcXP(); streak=calcStr();
  const lv=getLvl(xp), li=getLi(xp);
  document.getElementById('hXP').textContent=xp;
  document.getElementById('hLvl').textContent=li+1;
  document.getElementById('hStr').textContent=streak;
  if(prevLvl>=0&&li>prevLvl){sound('done');cel('Level Up!','You reached '+lv.nm,'Keep going!','⭐');}
  prevLvl=li;
}

function renderToday() {
  const inc=tasks.filter(t=>t.cur<t.tg);
  const el=document.getElementById('todayFocus');
  if(!inc.length){el.innerHTML='<div class="empty"><div class="icon">🎯</div><h3>No active challenges</h3><p>Add a challenge to start tracking</p></div>';}
  else{
    el.innerHTML='<div class="group">'+inc.slice(0,3).map(t=>{
      const c=CATS[t.cat]||CATS.other, td=t.lg&&t.lg[today()]?t.lg[today()]:0, ok=td>=1;
      return `<div class="cell" onclick="openLog(${t.id})"><div class="cell-icon" style="background:${c.c}22;color:${c.c};">${c.ic}</div><div class="cell-body"><div class="cell-title">${t.nm}</div><div class="cell-sub">${t.cur.toFixed(1)}/${t.tg}h · Today: +${td}h</div></div><div style="width:24px;height:24px;border-radius:50%;border:2px solid ${ok?c.c:'var(--sep)'};display:flex;align-items:center;justify-content:center;font-size:14px;color:${ok?c.c:'transparent'};">${ok?'✓':''}</div></div>`;
    }).join('')+'</div>';
  }
  renderChal();
  renderAch(document.getElementById('todayAch'));
}

function renderChal() {
  const el=document.getElementById('todayChal'), list=document.getElementById('chalList');
  const act=tasks.filter(t=>t.cur<t.tg);
  const html=act.length?'<div class="group">'+act.map(t=>{
    const pct=Math.min(100,(t.cur/t.tg)*100), c=CATS[t.cat]||CATS.other, td=t.lg&&t.lg[today()]?t.lg[today()]:0;
    return `<div class="cell"><div class="cell-icon" style="background:${c.c}22;color:${c.c};">${c.ic}</div><div class="cell-body" style="flex:1;min-width:0;"><div class="cell-title">${t.nm}</div><div class="cell-sub">${t.cat} · ${t.cur.toFixed(1)}/${t.tg}h · Today: +${td}h</div><div class="progress"><div style="width:${pct}%;background:${c.c};"></div></div></div><div class="cell-right"><button class="btn btn-pri" style="padding:5px 10px;font-size:12px;" onclick="event.stopPropagation();finish(${t.id})">✓</button><button class="btn btn-sec" style="padding:5px 8px;font-size:12px;" onclick="event.stopPropagation();openLog(${t.id})">+</button><button class="btn btn-sec" style="padding:5px 8px;font-size:12px;" onclick="event.stopPropagation();openTask(${t.id})">✎</button><button class="btn btn-sec" style="padding:5px 8px;font-size:12px;color:var(--red);" onclick="event.stopPropagation();delTask(${t.id})">🗑</button></div></div>`;
  }).join('')+'</div>':'<div class="empty"><div class="icon">📚</div><h3>No active challenges</h3><p>Tap + to add your first challenge</p></div>';
  if(el) el.innerHTML=html; if(list) list.innerHTML=html;
}

function renderFocus() {
  const g=document.getElementById('appGrid');
  g.innerHTML=APPS.map(a=>`<div class="app-item ${selApps.has(a.id)?'locked':''}" data-app="${a.id}" onclick="toggleApp(this)"><div class="app-icon">${a.ic}</div><div class="app-name">${a.nm}</div></div>`).join('');
  const s=document.getElementById('taskSel');
  const inc=tasks.filter(t=>t.cur<t.tg);
  if(!inc.length){s.innerHTML='<div style="padding:14px;color:rgba(255,255,255,0.4);font-size:14px;text-align:center;">No active challenges. Add one first.</div>';}
  else{
    s.innerHTML=inc.map(t=>{const c=CATS[t.cat]||CATS.other; return `<div class="tsel" onclick="selFTask(${t.id})"><div class="trad ${fId===t.id?'on':''}" data-task="${t.id}"></div><div style="width:32px;height:32px;border-radius:8px;background:${c.c};display:flex;align-items:center;justify-content:center;color:white;font-size:14px;flex-shrink:0;">${c.ic}</div><div style="flex:1;min-width:0;"><div style="font-size:15px;font-weight:600;color:white;">${t.nm}</div><div style="font-size:12px;color:rgba(255,255,255,0.5);">${t.cur.toFixed(1)}/${t.tg}h</div></div></div>`;}).join('');
    if(!fId&&inc.length) fId=inc[0].id;
  }
  const r=document.getElementById('recentSess');
  if(!sess.length){r.innerHTML='<div class="empty" style="padding:24px;"><p>No focus sessions yet</p></div>';}
  else{
    r.innerHTML='<div class="group">'+sess.slice().reverse().slice(0,5).map(s=>{const t=tasks.find(x=>x.id===s.tid); return `<div class="cell"><div class="cell-icon" style="background:var(--blue)22;color:var(--blue);">🎯</div><div class="cell-body"><div class="cell-title">${t?t.nm:'Focus'}</div><div class="cell-sub">${s.dur}m · ${s.dt}</div></div><div class="cell-right">+${Math.floor(s.dur/6)} XP</div></div>`;}).join('')+'</div>';
  }
}

function renderStats() {
  let totalH=0, bestDay=0, dayT={};
  tasks.forEach(t=>{Object.entries(t.lg||{}).forEach(([d,h])=>{totalH+=h; dayT[d]=(dayT[d]||0)+h;});});
  bestDay=Math.max(...Object.values(dayT).concat([0]));
  const done=tasks.filter(t=>t.cur>=t.tg).length, uDays=Object.keys(dayT).length||1;
  document.getElementById('stTotal').textContent=totalH.toFixed(1);
  document.getElementById('stDone').textContent=done;
  document.getElementById('stBest').textContent=bestDay.toFixed(1);
  document.getElementById('stAvg').textContent=(totalH/uDays).toFixed(1);

  const days=[], td=new Date();
  for(let i=6;i>=0;i--){const d=new Date(td);d.setDate(d.getDate()-i);days.push(d.toISOString().slice(0,10));}
  const totals=days.map(d=>{let s=0;tasks.forEach(t=>s+=(t.lg&&t.lg[d])||0);return s;});
  const mx=Math.max(...totals,1);
  const ns=['Sun','Mon','Tue','Wed','Thu','Fri','Sat'];
  document.getElementById('weekChart').innerHTML=days.map((d,i)=>`<div class="wb"><div class="wb-label">${ns[new Date(d).getDay()]}</div><div class="wb-track"><div class="wb-fill" style="height:${(totals[i]/mx)*100}px;"></div></div><div class="wb-val">${totals[i].toFixed(1)}</div></div>`).join('')+`<div style="width:100%;text-align:center;margin-top:8px;font-size:13px;color:var(--txt2);">Total: <strong style="color:var(--txt);">${totals.reduce((a,b)=>a+b,0).toFixed(1)}h</strong> · Avg: <strong style="color:var(--txt);">${(totals.reduce((a,b)=>a+b,0)/7).toFixed(1)}h</strong>/day</div>`;

  const hds=[];
  for(let i=27;i>=0;i--){const d=new Date(td);d.setDate(d.getDate()-i);hds.push(d.toISOString().slice(0,10));}
  const act={};
  Object.entries(dayT).forEach(([d,h])=>{if(h>=4)act[d]=3;else if(h>=2)act[d]=2;else if(h>=0.5)act[d]=1;});
  document.getElementById('heatMap').innerHTML=hds.map(d=>`<div class="hc ${act[d]===3?'a3':act[d]===2?'a2':act[d]===1?'a1':''} ${d===today()?'today':''}"></div>`).join('');

  const weeks=[];
  for(let w=1;w<=4;w++){
    const wds=[];
    for(let i=w*7+6;i>=w*7;i--){const d=new Date(td);d.setDate(d.getDate()-i);wds.push(d.toISOString().slice(0,10));}
    const wt=wds.reduce((sum,d)=>sum+(dayT[d]||0),0);
    weeks.push({total:wt,label:wkLbl(w)});
  }
  document.getElementById('pastWeeks').innerHTML=weeks.map(w=>`<div class="pw"><h4>${w.label}</h4><p>${w.total.toFixed(1)} hours total</p><div class="pw-bar"><div style="width:${Math.min(100,(w.total/20)*100)}%;"></div></div></div>`).join('');
}
function wkLbl(w) {
  const d=new Date(); d.setDate(d.getDate()-w*7);
  return d.toLocaleDateString('en-US',{month:'short',day:'numeric'})+' Week';
}

function renderArch() {
  const done=tasks.filter(t=>t.cur>=t.tg);
  const el=document.getElementById('archList');
  if(!done.length){el.innerHTML='<div class="empty"><div class="icon">🏆</div><h3>No completed challenges</h3><p>Finish a challenge to see it here</p></div>'; return;}
  el.innerHTML='<div class="group">'+done.map(t=>{const c=CATS[t.cat]||CATS.other; return `<div class="cell"><div class="cell-icon" style="background:${c.c}22;color:${c.c};">${c.ic}</div><div class="cell-body"><div class="cell-title">${t.nm}</div><div class="cell-sub">${t.tg}h · ${t.cat}</div></div><div class="badge">✓ Done</div></div>`;}).join('')+'</div>';
}

function renderAch(container) {
  const un=new Set();
  if(tasks.length) un.add('first');
  let th=0,rh=0,eh=0,ch=0;
  tasks.forEach(t=>{Object.values(t.lg||{}).forEach(h=>{th+=h;if(t.cat==='reading')rh+=h;if(t.cat==='english')eh+=h;if(t.cat==='coding')ch+=h;});if(t.cur>=t.tg)un.add('completionist');});
  if(rh>=10)un.add('bookworm');if(eh>=10)un.add('polyglot');if(ch>=10)un.add('coder');
  if(streak>=3)un.add('streak3');if(streak>=7)un.add('streak7');if(streak>=30)un.add('streak30');
  if(th>=50)un.add('marathon');if(th>=100)un.add('century');
  if(sess.length>=10)un.add('focus10');if(sess.length>=50)un.add('focus50');
  container.innerHTML=ACHS.map(a=>`<div class="ach ${un.has(a.id)?'on':''}"><div class="ic">${a.ic}</div><div class="nm">${a.nm}</div><div class="ds">${a.ds}</div></div>`).join('');
}

// ===== FOCUS =====
function toggleApp(el) {
  const id=el.dataset.app;
  if(selApps.has(id)){selApps.delete(id);el.classList.remove('locked');}
  else{selApps.add(id);el.classList.add('locked');}
}
function setDur(m,el) {
  fDur=m;
  document.querySelectorAll('.chip').forEach(c=>c.classList.remove('on'));
  el.classList.add('on');
  document.getElementById('tText').textContent=String(m).padStart(2,'0')+':00';
  circ(1);
}
function selFTask(id) {
  fId=id;
  document.querySelectorAll('.trad').forEach(r=>r.classList.remove('on'));
  const el=document.querySelector(`.trad[data-task="${id}"]`);
  if(el) el.classList.add('on');
}
function circ(r) { document.getElementById('tCircle').style.strokeDashoffset=628.32*(1-r); }

function startFocus() {
  if(!fId){toast('Select a challenge first');return;}
  fSec=fDur*60; fOn=true; sound('start');
  const ov=document.getElementById('ov');
  const t=tasks.find(x=>x.id===fId);
  document.getElementById('ovTask').textContent=t?t.nm:'Focus';
  document.getElementById('ovApps').innerHTML=Array.from(selApps).map(a=>{const ap=APPS.find(x=>x.id===a);return `<div class="ov-badge">${ap?ap.ic:'📱'} ${ap?ap.nm:a}</div>`;}).join('');
  ov.classList.add('on');
  updOv();
  fTimer=setInterval(()=>{fSec--;updOv();if(fSec<=0)finishFocus();},1000);
  window.addEventListener('beforeunload',bue);
}
function bue(e){e.preventDefault();e.returnValue='';}
function updOv() {
  const m=Math.floor(fSec/60),s=fSec%60;
  const txt=String(m).padStart(2,'0')+':'+String(s).padStart(2,'0');
  document.getElementById('ovTime').textContent=txt;
  document.title=txt+' · Focus';
  circ(fSec/(fDur*60));
  if(fSec<=10&&fSec>0) sound('tick');
}
function endEarly() {
  if(!confirm('End session early? Your streak will be affected.')) return;
  clearInterval(fTimer); fOn=false;
  window.removeEventListener('beforeunload',bue);
  document.getElementById('ov').classList.remove('on');
  document.title='Challenge Tracker';
  toast('Session ended early');
}
function finishFocus() {
  clearInterval(fTimer); fOn=false;
  window.removeEventListener('beforeunload',bue);
  document.getElementById('ov').classList.remove('on');
  document.title='Challenge Tracker';
  sound('done');
  const h=fDur/60;
  const t=tasks.find(x=>x.id===fId);
  if(t){t.lg=t.lg||{};t.lg[today()]=(t.lg[today()]||0)+h;t.cur=Object.values(t.lg).reduce((a,b)=>a+b,0);}
  sess.push({tid:fId,dur:fDur,dt:today(),ts:Date.now()});
  save();
  const xpG=Math.floor(h*10);
  cel('Focus Complete!','You focused for '+fDur+' minutes','+'+xpG+' XP','🎉');
  refresh();
}

// ===== TASK CRUD =====
function openTask(id=null) {
  editId=id;
  const m=document.getElementById('modTask');
  if(id){const t=tasks.find(x=>x.id===id);document.getElementById('inName').value=t.nm;document.getElementById('inTarget').value=t.tg;document.getElementById('inCat').value=t.cat;}
 
