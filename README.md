<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>StageDesk — Production Manager</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Oswald:wght@400;500;600;700&family=Inter:wght@400;500;600;700&family=IBM+Plex+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root{
    --ink:#14161A; --panel:#1B2A4A; --panel-2:#22335A;
    --paper:#F2EFE9; --paper-dim:#B9C0CC;
    --amber:#E8A33D; --sage:#6B8F71; --red:#C1443C; --blue:#4C7A93; --gold:#C9A227;
    --line: rgba(242,239,233,0.14);
    --shadow: 0 8px 24px rgba(0,0,0,0.35);
    --radius: 3px;
  }
  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;}
  body{ background: radial-gradient(circle at 15% 0%, rgba(232,163,61,0.05), transparent 45%), var(--ink);
    color:var(--paper); font-family:'Inter', sans-serif; min-height:100vh; -webkit-font-smoothing:antialiased; }
  ::selection{ background:var(--amber); color:var(--ink); }
  .stencil{ font-family:'Oswald', sans-serif; text-transform:uppercase; letter-spacing:0.04em; }
  .mono{ font-family:'IBM Plex Mono', monospace; }
  button, input, select, textarea{ font-family:inherit; }

  .callsheet-header{ background: repeating-linear-gradient(135deg, rgba(255,255,255,0.02) 0 2px, transparent 2px 6px), linear-gradient(180deg, var(--panel), var(--panel-2));
    border-bottom:2px solid var(--amber); padding:20px 24px 16px; position:relative; }
  .callsheet-header::after{ content:""; position:absolute; left:0; right:0; bottom:-6px; height:6px;
    background-image: radial-gradient(circle, var(--ink) 3px, transparent 3.5px); background-size:16px 16px; background-position:8px 0; }
  .header-row{ max-width:1240px; margin:0 auto; display:flex; justify-content:space-between; align-items:flex-end; gap:16px; flex-wrap:wrap; }
  .eyebrow{ font-family:'IBM Plex Mono',monospace; font-size:11px; letter-spacing:0.18em; text-transform:uppercase; color:var(--amber); margin-bottom:6px; }
  .prod-input{ background:none; border:none; border-bottom:1px dashed var(--line); color:var(--paper); font-family:'Oswald',sans-serif;
    font-size:clamp(22px,3.4vw,36px); text-transform:uppercase; letter-spacing:0.04em; font-weight:700; padding:0 2px; max-width:68vw; }
  .prod-input:focus{ outline:none; border-bottom-color:var(--amber); }
  .header-meta{ display:flex; gap:16px; font-family:'IBM Plex Mono',monospace; font-size:12px; color:var(--paper-dim); margin-top:6px; flex-wrap:wrap; }
  .header-meta b{ color:var(--paper); }
  .header-right{ display:flex; align-items:center; gap:10px; flex-wrap:wrap; }
  .who-picker select{ background:rgba(0,0,0,0.25); border:1px solid var(--line); color:var(--paper); padding:7px 10px; border-radius:16px; font-size:12.5px; max-width:220px; }
  .role-toggle{ display:flex; align-items:center; gap:4px; background:rgba(0,0,0,0.25); border:1px solid var(--line); border-radius:20px; padding:4px; }
  .role-toggle button{ background:none; border:none; color:var(--paper-dim); padding:7px 14px; border-radius:16px; font-size:12.5px; letter-spacing:0.06em; cursor:pointer; font-weight:600; }
  .role-toggle button.active{ background:var(--amber); color:var(--ink); }

  .tabs-wrap{ max-width:1240px; margin:0 auto; padding:0 10px; border-bottom:1px solid var(--line); display:flex; align-items:stretch; gap:2px; }
  .tabs{ display:flex; gap:2px; overflow-x:auto; scroll-behavior:smooth; flex:1; min-width:0; scrollbar-width:thin; }
  .tabs-scroll-btn{ background:none; border:none; color:var(--paper-dim); width:24px; flex-shrink:0; cursor:pointer; font-size:18px; }
  .tabs-scroll-btn:hover:not(:disabled){ color:var(--amber); }
  .tabs-scroll-btn:disabled{ opacity:0.15; cursor:default; }
  .tabs button{ background:none; border:none; color:var(--paper-dim); padding:13px 14px; cursor:pointer; font-family:'Oswald',sans-serif;
    letter-spacing:0.05em; text-transform:uppercase; font-size:12.5px; border-bottom:3px solid transparent; white-space:nowrap; position:relative; flex-shrink:0; }
  .tabs button:hover{ color:var(--paper); }
  .tabs button.active{ color:var(--amber); border-bottom-color:var(--amber); }
  .badge{ position:absolute; top:6px; right:0; background:var(--red); color:#fff; font-size:10px; font-weight:700;
    border-radius:50%; width:16px; height:16px; display:flex; align-items:center; justify-content:center; font-family:'IBM Plex Mono',monospace; }

  main{ max-width:1240px; margin:0 auto; padding:26px 24px 90px; }
  .view{ display:none; animation:fadein .25s ease; }
  .view.active{ display:block; }
  @keyframes fadein{ from{opacity:0; transform:translateY(4px);} to{opacity:1; transform:none;} }

  .card{ background:var(--panel); border:1px solid var(--line); border-radius:var(--radius); padding:20px; box-shadow:var(--shadow); margin-bottom:20px; }
  .card h2{ font-family:'Oswald',sans-serif; letter-spacing:0.05em; font-size:16.5px; margin:0 0 14px; text-transform:uppercase; }
  .card h3{ font-family:'Oswald',sans-serif; letter-spacing:0.04em; font-size:13.5px; margin:0 0 10px; text-transform:uppercase; color:var(--paper-dim); }
  .grid-2{ display:grid; grid-template-columns:1.3fr 1fr; gap:20px; }
  @media (max-width:800px){ .grid-2{ grid-template-columns:1fr; } }

  .empty-state{ border:1px dashed var(--line); border-radius:var(--radius); padding:22px; text-align:center; color:var(--paper-dim); font-size:13.5px; }
  .empty-state .lamp{ font-size:22px; margin-bottom:8px; }

  .btn{ background:var(--amber); color:var(--ink); border:none; padding:9px 16px; border-radius:3px; font-weight:700; font-size:12.5px;
    letter-spacing:0.03em; cursor:pointer; font-family:'Oswald',sans-serif; text-transform:uppercase; }
  .btn:hover{ filter:brightness(1.08); }
  .btn.ghost{ background:none; border:1px solid var(--paper-dim); color:var(--paper); }
  .btn.danger{ background:var(--red); color:#fff; }
  .btn.small{ padding:6px 12px; font-size:11px; }
  .btn:disabled{ opacity:.45; cursor:not-allowed; }

  .dash-grid{ display:grid; grid-template-columns:repeat(auto-fit, minmax(160px,1fr)); gap:12px; margin-bottom:18px; }
  .dept-tile{ border:1px solid var(--line); border-left:4px solid var(--tile-color, var(--amber)); border-radius:var(--radius); padding:12px; background:var(--panel); cursor:pointer; }
  .dept-tile:hover{ border-color:var(--tile-color); }
  .dept-tile .name{ font-family:'Oswald',sans-serif; letter-spacing:0.04em; font-size:12.5px; margin-bottom:8px; }
  .dept-tile .bar{ height:6px; background:rgba(255,255,255,0.08); border-radius:5px; overflow:hidden; margin-bottom:6px; }
  .dept-tile .bar-fill{ height:100%; background:var(--tile-color); }
  .dept-tile .pct{ font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--paper-dim); }

  .list-item{ display:flex; justify-content:space-between; align-items:center; padding:9px 0; border-bottom:1px dashed var(--line); font-size:13px; gap:10px; }
  .list-item:last-child{ border-bottom:none; }
  .list-item .d{ font-family:'IBM Plex Mono',monospace; color:var(--amber); font-size:11.5px; white-space:nowrap; }

  .cal-nav{ display:flex; align-items:center; justify-content:center; gap:16px; margin-bottom:14px; flex-wrap:wrap; }
  .cal-legend{ display:flex; gap:10px; flex-wrap:wrap; margin-bottom:14px; font-size:11px; color:var(--paper-dim); }
  .cal-legend span{ display:inline-flex; align-items:center; gap:5px; }
  .cal-legend .sw{ width:9px; height:9px; border-radius:2px; display:inline-block; }
  .cal-grid{ display:grid; grid-template-columns:repeat(7,1fr); gap:5px; }
  .cal-dow{ font-family:'IBM Plex Mono',monospace; font-size:10.5px; color:var(--paper-dim); text-align:center; padding-bottom:4px; text-transform:uppercase; letter-spacing:0.05em; }
  .cal-day{ min-height:76px; border:1px solid var(--line); border-radius:3px; padding:5px 6px; cursor:pointer; background:rgba(255,255,255,0.02); display:flex; flex-direction:column; gap:3px; }
  .cal-day:hover{ border-color:var(--amber); }
  .cal-day.other-month{ opacity:0.3; }
  .cal-day.is-today{ border-color:var(--amber); box-shadow: inset 0 0 0 1px var(--amber); }
  .cal-day.is-selected{ background:rgba(232,163,61,0.1); }
  .cal-day .daynum{ font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--paper-dim); }
  .cal-day.is-today .daynum{ color:var(--amber); font-weight:700; }
  .cal-day-dots{ display:flex; flex-wrap:wrap; gap:2px; }
  .cal-day-dot{ width:7px; height:7px; border-radius:50%; flex-shrink:0; }
  .cal-day-more{ font-size:9.5px; color:var(--paper-dim); font-family:'IBM Plex Mono',monospace; }
  @media (max-width:640px){ .cal-day{ min-height:52px; } .cal-day .daynum{ font-size:10px; } }

  .cal-event{ background:var(--panel); border:1px solid var(--line); border-radius:var(--radius); padding:14px 16px; margin-bottom:10px; box-shadow:var(--shadow); }
  .cal-event-top{ display:flex; justify-content:space-between; align-items:flex-start; gap:10px; flex-wrap:wrap; }
  .cal-date-chip{ font-family:'IBM Plex Mono',monospace; background:rgba(232,163,61,0.12); border:1px solid rgba(232,163,61,0.35); color:var(--amber); padding:4px 9px; border-radius:3px; font-size:11.5px; white-space:nowrap; }
  .cal-title{ font-weight:600; font-size:15px; margin:6px 0 4px; }
  .cal-meta{ font-family:'IBM Plex Mono',monospace; font-size:11.5px; color:var(--paper-dim); margin-bottom:6px; }
  .chip-row{ display:flex; gap:6px; flex-wrap:wrap; margin-top:6px; }
  .chip{ font-size:10.5px; padding:3px 8px; border-radius:10px; font-family:'IBM Plex Mono',monospace; color:#fff; }
  .chip.outline{ background:none; border:1px solid var(--line); color:var(--paper-dim); }
  .cal-notes{ font-size:13px; color:var(--paper-dim); margin-top:6px; font-style:italic; }
  .cal-actions{ display:flex; gap:8px; margin-top:10px; flex-wrap:wrap; }

  .form-grid{ display:grid; grid-template-columns:repeat(auto-fit,minmax(150px,1fr)); gap:10px; margin-bottom:10px; }
  .form-grid input, .form-grid select, .form-grid textarea{ background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:9px 10px; border-radius:3px; font-size:13px; width:100%; }
  .checkbox-row{ display:flex; gap:12px; flex-wrap:wrap; margin-bottom:12px; }
  .checkbox-row label{ display:flex; align-items:center; gap:6px; font-size:12px; cursor:pointer; }
  .full-width{ width:100%; margin-bottom:10px; }

  .conflict-card{ background:var(--panel); border:1px solid var(--line); border-left:4px solid var(--red); border-radius:var(--radius); padding:12px 14px; margin-bottom:8px; }
  .conflict-top{ display:flex; justify-content:space-between; font-size:13px; font-weight:600; flex-wrap:wrap; gap:6px; }
  .conflict-meta{ font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--paper-dim); margin-top:3px; }
  .conflict-reason{ font-size:13px; margin-top:6px; color:var(--paper-dim); }
  .conflict-group-title{ font-family:'Oswald',sans-serif; letter-spacing:0.05em; font-size:13px; color:var(--amber); margin:16px 0 8px; text-transform:uppercase; }
  .conflict-group-title:first-child{ margin-top:0; }
  .status-chip{ font-size:10.5px; padding:3px 9px; border-radius:10px; font-weight:700; text-transform:uppercase; letter-spacing:0.04em; }
  .status-chip.pending{ background:rgba(232,163,61,0.15); color:var(--amber); }
  .status-chip.approved{ background:rgba(107,143,113,0.18); color:var(--sage); }
  .status-chip.denied{ background:rgba(193,68,60,0.18); color:var(--red); }

  .pill-row{ display:flex; gap:7px; flex-wrap:wrap; margin-bottom:20px; }
  .pill{ border:2px solid var(--pill-color, var(--amber)); color:var(--pill-color, var(--amber)); background:none; padding:7px 14px;
    border-radius:20px; font-family:'Oswald',sans-serif; letter-spacing:0.03em; font-size:12px; text-transform:uppercase; cursor:pointer; }
  .pill.active{ background:var(--pill-color, var(--amber)); color:var(--ink); }
  .subtabs{ display:flex; gap:4px; margin-bottom:18px; border-bottom:1px solid var(--line); overflow-x:auto; }
  .subtabs button{ background:none; border:none; color:var(--paper-dim); padding:8px 13px; cursor:pointer; font-size:12px; letter-spacing:0.05em;
    text-transform:uppercase; border-bottom:2px solid transparent; white-space:nowrap; }
  .subtabs button.active{ color:var(--paper); border-bottom-color:var(--dept-color, var(--amber)); }

  .progress-wrap{ margin-bottom:20px; }
  .progress-top{ display:flex; justify-content:space-between; align-items:baseline; margin-bottom:8px; flex-wrap:wrap; gap:6px; }
  .bar{ height:9px; background:rgba(255,255,255,0.08); border-radius:6px; overflow:hidden; border:1px solid var(--line); }
  .bar-fill{ height:100%; transition:width .4s ease; }

  .task-row{ display:flex; flex-direction:column; gap:10px; margin-bottom:14px; }
  .task-card{ position:relative; background:var(--panel); border:1px solid var(--line); border-left:4px solid var(--dept-color, var(--amber));
    border-radius:var(--radius); padding:12px 14px; display:flex; align-items:flex-start; gap:12px; box-shadow:var(--shadow); }
  .status-btn{ flex-shrink:0; margin-top:1px; border:none; cursor:pointer; font-family:'IBM Plex Mono',monospace; font-size:10px; font-weight:700;
    text-transform:uppercase; padding:6px 9px; border-radius:3px; letter-spacing:0.03em; white-space:nowrap; }
  .status-btn.todo{ background:rgba(255,255,255,0.08); color:var(--paper-dim); }
  .status-btn.in_progress{ background:rgba(232,163,61,0.18); color:var(--amber); }
  .status-btn.done{ background:rgba(107,143,113,0.2); color:var(--sage); }
  .task-card.done .task-title{ text-decoration:line-through; text-decoration-color:var(--sage); opacity:.75; }
  .task-body{ flex:1; min-width:0; }
  .task-title{ font-weight:600; font-size:14.5px; margin-bottom:4px; }
  .task-desc{ font-size:12.5px; color:var(--paper-dim); margin-bottom:6px; }
  .task-meta-row{ display:flex; gap:8px; flex-wrap:wrap; align-items:center; }
  .task-meta-row .assignee{ width:120px; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper-dim); font-family:'IBM Plex Mono',monospace; font-size:11.5px; border-radius:3px; padding:4px 7px; }
  .tag{ font-family:'IBM Plex Mono',monospace; font-size:10.5px; border-radius:3px; padding:2px 7px; border:1px solid var(--line); color:var(--paper-dim); }
  .tag.priority-high{ border-color:var(--red); color:var(--red); }
  .tag.priority-medium{ border-color:var(--amber); color:var(--amber); }
  .tag.priority-low{ border-color:var(--sage); color:var(--sage); }
  .task-del{ background:none; border:none; color:var(--paper-dim); cursor:pointer; font-size:15px; opacity:0.5; margin-left:auto; }
  .task-del:hover{ opacity:1; color:var(--red); }

  .task-expand-btn{ background:none; border:none; color:var(--paper-dim); cursor:pointer; font-size:11px; font-family:'IBM Plex Mono',monospace; display:flex; align-items:center; gap:4px; padding:2px 0; }
  .task-expand-btn:hover{ color:var(--paper); }
  .task-checklist-progress{ font-family:'IBM Plex Mono',monospace; font-size:10.5px; color:var(--sage); }
  .task-detail-panel{ margin-top:10px; padding-top:10px; border-top:1px dashed var(--line); }
  .task-detail-panel textarea.task-desc-input{ width:100%; min-height:54px; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); border-radius:3px; padding:8px 10px; font-size:13px; font-family:'Inter',sans-serif; resize:vertical; margin-bottom:10px; }
  .checklist-label{ font-size:10.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em; display:block; margin-bottom:6px; }
  .checklist-item{ display:flex; align-items:center; gap:8px; padding:4px 0; font-size:13px; }
  .checklist-item.checked span{ text-decoration:line-through; color:var(--paper-dim); }
  .checklist-item input[type=checkbox]{ accent-color:var(--sage); width:15px; height:15px; flex-shrink:0; }
  .checklist-item .cl-del{ background:none; border:none; color:var(--paper-dim); opacity:0.4; cursor:pointer; font-size:13px; margin-left:auto; }
  .checklist-item .cl-del:hover{ opacity:1; color:var(--red); }
  .checklist-add-row{ display:flex; gap:6px; margin-top:6px; }
  .checklist-add-row input{ flex:1; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); border-radius:3px; padding:6px 9px; font-size:12.5px; }

  .add-form{ display:none; flex-direction:column; gap:8px; background:rgba(0,0,0,0.2); border:1px solid var(--line); border-radius:var(--radius); padding:12px; }
  .add-form.open{ display:flex; }
  .add-form-row{ display:flex; gap:8px; flex-wrap:wrap; }
  .add-form input, .add-form select{ background:var(--ink); border:1px solid var(--line); color:var(--paper); padding:8px 10px; border-radius:3px; font-size:12.5px; }
  .add-form input[name="title"]{ flex:2; min-width:180px; }

  textarea.notes-field{ width:100%; min-height:70px; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); border-radius:3px; padding:10px; font-size:13.5px; resize:vertical; margin-bottom:10px; }

  .pipeline{ display:flex; gap:4px; margin-bottom:14px; flex-wrap:wrap; }
  .pipeline .step{ font-family:'IBM Plex Mono',monospace; font-size:10.5px; text-transform:uppercase; letter-spacing:0.04em; padding:5px 11px; border-radius:12px; background:rgba(255,255,255,0.06); color:var(--paper-dim); }
  .pipeline .step.active{ background:var(--amber); color:var(--ink); font-weight:700; }
  .pipeline .step.past{ background:rgba(107,143,113,0.2); color:var(--sage); }

  .hist-item{ background:var(--panel); border:1px solid var(--line); border-radius:var(--radius); padding:14px 16px; margin-bottom:10px; cursor:pointer; }
  .hist-item:hover{ border-color:var(--amber); }
  .hist-top{ display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:6px; }
  .hist-detail{ display:none; margin-top:12px; border-top:1px dashed var(--line); padding-top:12px; }
  .hist-item.open .hist-detail{ display:block; }
  .stamp{ display:inline-flex; align-items:center; gap:8px; border:3px solid var(--sage); color:var(--sage); padding:6px 14px; border-radius:6px;
    transform:rotate(-2deg); font-family:'Oswald',sans-serif; letter-spacing:0.06em; font-weight:700; font-size:14px; text-transform:uppercase; }
  .stamp.fail{ border-color:var(--red); color:var(--red); }
  .adjustment-row{ display:flex; gap:8px; align-items:center; padding:7px 0; border-bottom:1px dashed var(--line); font-size:12.5px; flex-wrap:wrap; }
  .adjustment-row:last-child{ border-bottom:none; }

  .notif-item{ background:var(--panel); border:1px solid var(--line); border-radius:var(--radius); padding:12px 14px; margin-bottom:8px; display:flex; gap:10px; align-items:flex-start; }
  .notif-item.unread{ border-left:4px solid var(--amber); }
  .notif-dot{ width:8px; height:8px; border-radius:50%; background:var(--amber); margin-top:6px; flex-shrink:0; }
  .notif-item.read .notif-dot{ background:transparent; }
  .notif-time{ font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--paper-dim); }

  .roster-row{ display:flex; gap:10px; align-items:center; padding:8px 0; border-bottom:1px dashed var(--line); font-size:13px; flex-wrap:wrap; }
  .roster-row:last-child{ border-bottom:none; }
  .roster-row .dot{ width:9px; height:9px; border-radius:2px; transform:rotate(45deg); flex-shrink:0; }
  .roster-row .rname{ flex:1; min-width:100px; }
  .roster-row .rmeta{ font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--paper-dim); }

  .field-row{ margin-bottom:12px; }
  .field-row label{ display:block; font-size:11.5px; color:var(--paper-dim); margin-bottom:5px; text-transform:uppercase; letter-spacing:0.05em; }
  .field-row input, .field-row select{ width:100%; max-width:260px; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:8px 10px; border-radius:3px; }

  .costume-card{ background:var(--panel); border:1px solid var(--line); border-left:4px solid var(--costumes,#9C5A88); border-radius:var(--radius); padding:14px 16px; margin-bottom:12px; }
  .costume-grid{ display:grid; grid-template-columns:repeat(auto-fit, minmax(80px,1fr)); gap:8px; margin:10px 0; }
  .costume-grid div{ font-size:11px; }
  .costume-grid label{ display:block; color:var(--paper-dim); font-size:10px; text-transform:uppercase; margin-bottom:2px; }
  .costume-grid input{ width:100%; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); border-radius:3px; padding:5px 6px; font-size:12px; }
  .piece-chip{ display:inline-flex; align-items:center; gap:6px; background:rgba(255,255,255,0.06); border-radius:12px; padding:4px 10px; font-size:11.5px; margin:3px 4px 0 0; }
  .piece-status{ font-size:10px; padding:1px 6px; border-radius:8px; background:rgba(232,163,61,0.18); color:var(--amber); cursor:pointer; }

  .lockmsg{ font-size:12px; color:var(--paper-dim); border:1px dashed var(--line); padding:10px 12px; border-radius:3px; margin-bottom:14px; }

  .att-grid-scroll{ overflow-x:auto; border:1px solid var(--line); border-radius:var(--radius); }
  .att-table{ border-collapse:collapse; width:100%; font-family:'IBM Plex Mono',monospace; font-size:11.5px; white-space:nowrap; }
  .att-table th, .att-table td{ padding:7px 10px; border-bottom:1px solid var(--line); border-right:1px solid var(--line); text-align:center; }
  .att-table th{ background:var(--panel-2); font-weight:600; letter-spacing:0.03em; }
  .att-table td.att-name-cell, .att-table th.att-name-th{
    position:sticky; left:0; background:var(--panel); text-align:left; font-family:'Inter',sans-serif; font-size:12.5px; z-index:2; min-width:150px;
  }
  .att-table th.att-name-th{ background:var(--panel-2); z-index:3; }
  .att-table td.att-total-cell, .att-table th.att-total-th{
    position:sticky; right:0; background:var(--panel); font-weight:700; z-index:2;
  }
  .att-table th.att-total-th{ background:var(--panel-2); z-index:3; }
  .att-cell-p{ color:var(--sage); font-weight:700; }
  .att-cell-a{ color:var(--red); font-weight:700; background:rgba(193,68,60,0.1); }
  .att-cell-e{ color:var(--amber); font-weight:700; }
  .att-cell-blank{ color:var(--paper-dim); opacity:0.35; }
  .att-legend{ display:flex; gap:14px; flex-wrap:wrap; font-size:11.5px; color:var(--paper-dim); margin-bottom:12px; }

  .incident-card{ background:var(--panel); border:1px solid var(--line); border-left:4px solid var(--red); border-radius:var(--radius); padding:12px 14px; margin-bottom:8px; }
  .announcement-item{ background:var(--panel); border:1px solid var(--line); border-left:4px solid var(--amber); border-radius:var(--radius); padding:12px 14px; margin-bottom:8px; }
  .announcement-top{ display:flex; justify-content:space-between; align-items:flex-start; gap:8px; }
  .announcement-text{ font-size:14px; white-space:pre-wrap; }
  .announcement-meta{ font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--paper-dim); margin-top:6px; }

  .team-note-item{ background:var(--panel); border:1px solid var(--line); border-left:4px solid var(--sage); border-radius:var(--radius); padding:12px 14px; margin-bottom:8px; }
  .team-note-item.staff .team-note-top .author{ color:var(--amber); }
  .team-note-top{ display:flex; justify-content:space-between; align-items:flex-start; gap:8px; }
  .team-note-top .author{ font-weight:600; font-size:13px; }
  .team-note-text{ font-size:14px; white-space:pre-wrap; margin-top:4px; }
  .team-note-meta{ font-family:'IBM Plex Mono',monospace; font-size:10.5px; color:var(--paper-dim); margin-top:6px; }
  .staff-tag{ font-size:9.5px; padding:1px 6px; border-radius:8px; background:rgba(232,163,61,0.18); color:var(--amber); margin-left:6px; vertical-align:middle; }

  .whiteboard-wrap{
    position:relative; width:100%; height:480px; overflow:auto; border:1px solid var(--line); border-radius:var(--radius);
    background-color:rgba(0,0,0,0.15);
    background-image: linear-gradient(rgba(255,255,255,0.04) 1px, transparent 1px), linear-gradient(90deg, rgba(255,255,255,0.04) 1px, transparent 1px);
    background-size: 24px 24px;
  }
  .whiteboard-canvas{ position:relative; width:1400px; height:900px; }
  .sticky-note{
    position:absolute; width:170px; min-height:120px; border-radius:2px; padding:0 10px 26px;
    box-shadow:0 4px 10px rgba(0,0,0,0.35); font-family:'Inter',sans-serif; font-size:12.5px; color:#241f14;
    display:flex; flex-direction:column; user-select:none;
  }
  .sticky-note.dragging{ z-index:50; box-shadow:0 8px 20px rgba(0,0,0,0.5); }
  .sticky-note .sn-handle{
    height:20px; margin:0 -10px 6px; padding:0 10px; display:flex; align-items:center;
    font-family:'IBM Plex Mono',monospace; font-size:9.5px; letter-spacing:0.06em; text-transform:uppercase;
    background:rgba(0,0,0,0.12); border-radius:2px 2px 0 0; cursor:grab; touch-action:none; opacity:0.7;
  }
  .sticky-note .sn-handle:active{ cursor:grabbing; }
  .sticky-note textarea{
    background:transparent; border:none; resize:none; width:100%; flex:1; font-family:inherit; font-size:inherit; color:inherit;
    outline:none; cursor:text;
  }
  .sticky-note .sn-meta{ position:absolute; bottom:6px; left:10px; right:28px; font-size:9.5px; opacity:0.65; font-family:'IBM Plex Mono',monospace; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; }
  .sticky-note .sn-del{ position:absolute; bottom:4px; right:6px; background:none; border:none; cursor:pointer; font-size:13px; opacity:0.5; color:#241f14; }
  .sticky-note .sn-del:hover{ opacity:1; }
  .sticky-color-row{ display:flex; gap:6px; margin-bottom:10px; }
  .sticky-color-swatch{ width:22px; height:22px; border-radius:4px; cursor:pointer; border:2px solid transparent; }
  .sticky-color-swatch.active{ border-color:var(--paper); }

  .wb-toolbar{ display:flex; align-items:center; gap:8px; flex-wrap:wrap; margin-bottom:10px; padding:10px; background:rgba(0,0,0,0.2); border:1px solid var(--line); border-radius:var(--radius); }
  .wb-tool-btn{ background:none; border:1px solid var(--line); color:var(--paper-dim); padding:7px 12px; border-radius:16px; font-size:12px; cursor:pointer; }
  .wb-tool-btn.active{ background:var(--amber); color:var(--ink); border-color:var(--amber); font-weight:600; }
  .wb-sep{ width:1px; align-self:stretch; background:var(--line); margin:0 2px; }
  .wb-color-row{ display:flex; gap:5px; }
  .wb-width-row{ display:flex; gap:5px; }
  .wb-width-btn.active{ background:var(--amber); color:var(--ink); }
  .wb-image-add-row{ display:flex; align-items:center; gap:8px; flex-wrap:wrap; margin-bottom:12px; }

  .sd-layout{ display:flex; gap:14px; flex-wrap:wrap; }
  .sd-canvas-wrap{ flex:1; min-width:300px; height:520px; position:relative; border:1px solid var(--line); border-radius:var(--radius); overflow:hidden; background:#0c0d10; }
  .sd-canvas-wrap canvas{ display:block; width:100%; height:100%; touch-action:none; }
  .sd-hint{ position:absolute; bottom:8px; left:10px; font-family:'IBM Plex Mono',monospace; font-size:10.5px; color:rgba(255,255,255,0.5); pointer-events:none; }
  .sd-side-panel{ width:250px; flex-shrink:0; }
  .sd-piece-list{ max-height:180px; overflow-y:auto; border:1px solid var(--line); border-radius:3px; margin-bottom:14px; }
  .sd-piece-row{ display:flex; justify-content:space-between; align-items:center; padding:7px 10px; font-size:12px; border-bottom:1px dashed var(--line); cursor:pointer; }
  .sd-piece-row:last-child{ border-bottom:none; }
  .sd-piece-row.active{ background:rgba(232,163,61,0.15); }
  .sd-piece-row .sw{ width:9px; height:9px; border-radius:2px; display:inline-block; margin-right:6px; flex-shrink:0; }
  .sd-props label{ font-size:10.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em; display:block; margin:8px 0 3px; }
  .sd-props input[type=text], .sd-props input[type=number]{ width:100%; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:6px 8px; border-radius:3px; font-size:12.5px; }
  .sd-props input[type=color]{ width:100%; height:28px; border:1px solid var(--line); border-radius:3px; background:none; padding:2px; }
  .sd-dims-row{ display:flex; gap:6px; }
  .sd-dims-row > div{ flex:1; }

  .ll-layout{ display:flex; flex-direction:column; gap:16px; }
  .ll-board{ width:100%; background:rgba(0,0,0,0.25); border:1px solid var(--line); border-radius:var(--radius); padding:14px; }
  .ll-board-group-label{ font-size:11px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em; margin:10px 0 6px; }
  .ll-channels-row{ display:flex; gap:8px; flex-wrap:wrap; }
  .ll-channel{ width:64px; background:rgba(0,0,0,0.2); border:1px solid var(--line); border-radius:6px; padding:8px 6px; display:flex; flex-direction:column; align-items:center; }
  .ll-channel-label{ font-family:'IBM Plex Mono',monospace; font-size:10px; color:var(--paper-dim); margin-bottom:6px; text-align:center; min-height:24px; }
  .ll-fader-track{ height:130px; display:flex; align-items:center; justify-content:center; width:100%; }
  .ll-fader-track input[type=range]{ width:120px; height:20px; transform:rotate(-90deg); accent-color:var(--amber); }
  .ll-channel-pct{ font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--paper); margin-top:6px; }
  .ll-channel-color{ width:36px; height:20px; border:1px solid var(--line); border-radius:4px; background:none; padding:0; margin-top:6px; cursor:pointer; }
  .ll-swatch-row{ display:flex; gap:3px; flex-wrap:wrap; justify-content:center; margin-top:5px; max-width:56px; }
  .ll-swatch{ width:11px; height:11px; border-radius:2px; cursor:pointer; border:1px solid rgba(255,255,255,0.3); }
  .ll-stage-panel{ width:100%; }
  .ll-canvas-wrap{ width:100%; height:600px; position:relative; border:1px solid var(--line); border-radius:var(--radius); overflow:hidden; background:#050608; }
  .ll-canvas-wrap canvas{ display:block; width:100%; height:100%; touch-action:none; }
  .ll-cue-item{ display:flex; justify-content:space-between; align-items:center; padding:8px 10px; border-bottom:1px dashed var(--line); font-size:12.5px; }
  .ll-cue-item:last-child{ border-bottom:none; }

  .bk-stage-wrap{ position:relative; width:100%; height:520px; overflow:auto; border:1px solid var(--line); border-radius:var(--radius); background:rgba(0,0,0,0.15); }
  .bk-stage{ position:relative; width:900px; height:560px; background-color:#16233f;
    background-image: linear-gradient(rgba(255,255,255,0.05) 1px, transparent 1px), linear-gradient(90deg, rgba(255,255,255,0.05) 1px, transparent 1px);
    background-size:20px 20px; }
  .bk-centerline{ position:absolute; left:450px; top:0; bottom:0; width:1px; background:rgba(232,163,61,0.5); border-left:1px dashed rgba(232,163,61,0.5); }
  .bk-marker{ position:absolute; display:flex; flex-direction:column; align-items:center; width:70px; }
  .bk-marker-shape{ width:36px; height:36px; border:2px solid #14161a; cursor:grab; touch-action:none; display:flex; align-items:center; justify-content:center; font-size:9px; color:#14161a; font-weight:700; }
  .bk-marker-shape:active{ cursor:grabbing; }
  .bk-marker[data-type="actor"] .bk-marker-shape{ border-radius:50%; background:#E8A33D; }
  .bk-marker[data-type="cube"] .bk-marker-shape{ border-radius:5px; background:#9BB8D3; }
  .bk-marker-label{ margin-top:3px; font-size:10px; background:rgba(0,0,0,0.75); color:#fff; padding:1px 6px; border-radius:8px; max-width:100%; text-align:center; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; cursor:pointer; }
  .bk-marker-del{ position:absolute; top:-6px; right:14px; width:16px; height:16px; border-radius:50%; background:var(--red); color:#fff; border:none; font-size:10px; line-height:1; cursor:pointer; padding:0; }
  .bk-marker-color{ position:absolute; top:-6px; left:14px; width:16px; height:16px; padding:0; border:1px solid #14161a; border-radius:3px; cursor:pointer; background:none; }
  .bk-marker[data-type="door"] .bk-marker-shape{ width:44px; height:30px; border-radius:3px; font-size:14px; }
  .bk-marker[data-type="number"] .bk-marker-shape{ width:30px; height:30px; border-radius:50%; font-size:14px; font-weight:800; }
  .bk-page-nav{ display:flex; align-items:center; justify-content:center; gap:12px; margin:10px 0; font-size:12.5px; color:var(--paper-dim); }
  .bk-sticky{ position:absolute; width:120px; }
  .bk-sticky .sn-handle{ background:rgba(0,0,0,0.15); }
  .bk-note-item{ padding:8px 0; border-bottom:1px dashed var(--line); font-size:12px; }
  .bk-note-item:last-child{ border-bottom:none; }
  .wb-image-add-row input{ flex:1; min-width:200px; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:8px 10px; border-radius:3px; font-size:12.5px; }
  #wbDrawCanvas{ position:absolute; top:0; left:0; width:1400px; height:900px; z-index:40; pointer-events:none; }

  .wb-image{
    position:absolute; box-shadow:0 4px 10px rgba(0,0,0,0.35); border:2px solid rgba(255,255,255,0.12); border-radius:2px;
    background:rgba(0,0,0,0.2); display:flex; flex-direction:column; overflow:visible;
  }
  .wb-image.dragging{ z-index:50; }
  .wb-image img{ width:100%; flex:1; object-fit:contain; background:rgba(0,0,0,0.25); pointer-events:none; min-height:0; }
  .wb-image .sn-handle{ position:relative; z-index:2; }
  .wb-image .sn-del{ position:absolute; top:2px; right:4px; bottom:auto; background:rgba(20,22,26,0.6); border-radius:50%; width:18px; height:18px; display:flex; align-items:center; justify-content:center; color:var(--paper); font-size:11px; }
  .img-resize-handle{
    position:absolute; bottom:-4px; right:-4px; width:14px; height:14px; background:var(--amber); border-radius:2px;
    cursor:nwse-resize; touch-action:none; border:1px solid var(--ink);
  }
  .incident-top{ display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:6px; }
  .incident-meta{ font-family:'IBM Plex Mono',monospace; font-size:11px; color:var(--paper-dim); margin-top:3px; }
  .incident-note{ font-size:13px; margin-top:6px; color:var(--paper-dim); }
  .behavior-score-good{ color:var(--sage); }
  .behavior-score-bad{ color:var(--red); }
  .behavior-score-mid{ color:var(--amber); }
  .att-legend span b{ font-family:'IBM Plex Mono',monospace; }
  .toast{ position:fixed; bottom:20px; left:50%; transform:translateX(-50%) translateY(20px); background:var(--paper); color:var(--ink);
    padding:11px 20px; border-radius:4px; font-size:13px; font-weight:600; box-shadow:var(--shadow); opacity:0; transition:all .25s; pointer-events:none; z-index:50; }
  .toast.show{ opacity:1; transform:translateX(-50%) translateY(0); }
</style>
<script type="importmap">
{
  "imports": {
    "three": "https://unpkg.com/three@0.160.0/build/three.module.js",
    "three/addons/": "https://unpkg.com/three@0.160.0/examples/jsm/"
  }
}
</script>
</head>
<body>

<div class="callsheet-header">
  <div class="header-row">
    <div class="header-left">
      <div class="eyebrow">StageDesk // Full Production Manager</div>
      <h1><input class="prod-input" id="prodName" value="Untitled Production" spellcheck="false"></h1>
      <div class="header-meta">
        <span id="todayDate">—</span>
        <span><b id="crewCount">0</b> cast/crew on roster</span>
      </div>
    </div>
    <div class="header-right">
      <div id="signedOutWrap" style="display:flex; align-items:center; gap:8px; flex-wrap:wrap; flex-direction:column; align-items:flex-end;">
        <div style="display:flex; align-items:center; gap:8px; flex-wrap:wrap; justify-content:flex-end;">
          <button class="btn" id="googleSignInBtn">Sign in with Google</button>
          <button class="btn ghost small" id="toggleEmailPwBtn">Email &amp; password</button>
        </div>
        <div id="emailPwWrap" style="display:none; gap:6px; flex-wrap:wrap; justify-content:flex-end;">
          <input type="email" id="epEmail" placeholder="email" style="background:rgba(0,0,0,0.25); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:14px; font-size:12px; width:140px;">
          <input type="password" id="epPassword" placeholder="password" style="background:rgba(0,0,0,0.25); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:14px; font-size:12px; width:120px;">
          <button class="btn small" id="epSignInBtn">Sign In</button>
          <button class="btn ghost small" id="epSignUpBtn">Create Account</button>
          <button class="btn ghost small" id="epForgotBtn" style="font-size:10.5px; opacity:0.75;">Forgot password?</button>
        </div>
      </div>
      <div id="signedInWrap" style="display:none; align-items:center; gap:10px;">
        <div style="text-align:right; line-height:1.3;">
          <div id="authUserName" style="font-size:13px; font-weight:600;"></div>
          <div id="authUserRole" class="mono" style="font-size:11px; color:var(--paper-dim);"></div>
        </div>
        <button class="btn ghost small" id="switchShowBtn" style="display:none;">Switch Show</button>
        <button class="btn ghost small" id="signOutBtn">Sign out</button>
      </div>
    </div>
  </div>
</div>

<div class="tabs-wrap" id="tabsWrap" style="display:none;">
  <button class="tabs-scroll-btn" id="tabsScrollLeft" aria-label="Scroll tabs left">‹</button>
  <nav class="tabs" id="mainTabs">
    <button data-view="dashboard" class="active">Dashboard</button>
    <button data-view="productions">Productions</button>
    <button data-view="calendar">Calendar</button>
    <button data-view="conflicts">Conflicts</button>
    <button data-view="attendance">Attendance</button>
    <button data-view="behavior">Behavior</button>
    <button data-view="departments">Departments</button>
    <button data-view="costumes">Costumes</button>
    <button data-view="stagedesign">3D Set Design</button>
    <button data-view="lightinglab">Lighting Lab</button>
    <button data-view="blocking">Blocking Assistant</button>
    <button data-view="notifications">Notifications<span class="badge" id="notifBadge" style="display:none;">0</span></button>
    <button data-view="setup">Roster / Setup</button>
  </nav>
  <button class="tabs-scroll-btn" id="tabsScrollRight" aria-label="Scroll tabs right">›</button>
</div>

<div id="signedOutShell" style="display:none; max-width:520px; margin:70px auto; text-align:center; padding:0 24px;">
  <div class="lamp" style="font-size:30px; margin-bottom:10px;">🔒</div>
  <p style="font-size:15px; color:var(--paper-dim); line-height:1.6;">Sign in above — with Google, or Email &amp; Password if your school blocks Google sign-in — to view this production. A real account is required now, even just to look around.</p>
</div>

<div id="pendingApprovalShell" style="display:none; max-width:520px; margin:70px auto; text-align:center; padding:0 24px;">
  <div class="lamp" style="font-size:30px; margin-bottom:10px;">⏳</div>
  <h2 style="margin-bottom:6px;">Waiting for approval</h2>
  <p style="font-size:15px; color:var(--paper-dim); line-height:1.6;">You're signed in as <b id="pendingApprovalEmail" style="color:var(--paper);"></b>, but you're not on the roster yet. Your Director needs to approve your account before you can get in — let them know you're waiting, then check back and refresh this page.</p>
</div>

<div id="showPickerShell" style="display:none; max-width:520px; margin:70px auto; text-align:center; padding:0 24px;">
  <div class="lamp" style="font-size:30px; margin-bottom:10px;">🎭</div>
  <h2 style="margin-bottom:6px;">Which show are you working on?</h2>
  <p style="font-size:14px; color:var(--paper-dim); line-height:1.6; margin-bottom:18px;">You're on the roster for more than one production.</p>
  <div id="showPickerList"></div>
</div>

<main id="mainContent" style="display:none;">

  <section class="view active" id="view-dashboard">
    <div class="card" id="announcementsCard">
      <h2>Announcements</h2>
      <div id="announcementPostWrap" style="display:none; margin-bottom:14px;">
        <textarea id="announcementText" class="full-width" style="min-height:60px;" placeholder="Post something everyone will see when they open the app..."></textarea>
        <button class="btn small" id="announcementPostBtn">Post Announcement</button>
      </div>
      <div id="announcementList"></div>
    </div>
    <div class="card"><h2>Department Progress — Today</h2><div class="dash-grid" id="dashDeptTiles"></div></div>
    <div class="grid-2">
      <div class="card"><h2>Upcoming Calls</h2><div id="dashUpcoming"></div></div>
      <div class="card"><h2>Needs Attention</h2><div id="dashAttention"></div></div>
    </div>
  </section>

  <section class="view" id="view-productions">
    <div id="productionsLockedMsg" class="empty-state" style="display:none;">
      <div class="lamp">🔒</div>Managing productions is Director-only. Ask the Director to create or switch shows.
    </div>
    <div id="productionsAuthorizedWrap">
      <div class="card">
        <h2>Start a New Production</h2>
        <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Your crew roster (names, roles, class periods, contact info) carries over between productions — you'll just reassign each student's team for the new show. Calendar, tasks, reports, grades, and costume records all start fresh.</p>
        <div class="form-grid">
          <input type="text" id="newProductionName" placeholder="Production name (e.g. Spring Musical 2027)">
          <select id="copyFromProduction"><option value="">Start with an empty roster</option></select>
        </div>
        <button class="btn" id="newProductionBtn">Create Production</button>
      </div>
      <div class="card">
        <h2>All Productions</h2>
        <div id="productionsList"></div>
      </div>
    </div>
  </section>

  <section class="view" id="view-calendar">
    <div class="subtabs" id="calSubtabs">
      <button data-cal-sub="month" class="active">Month View</button>
      <button data-cal-sub="agenda">Agenda</button>
    </div>

    <div id="calMonthView">
      <div class="card">
        <div class="cal-nav">
          <button class="btn ghost small" id="calPrevBtn">‹ Prev</button>
          <span class="stencil" id="calMonthLabel" style="font-size:17px;"></span>
          <button class="btn ghost small" id="calTodayBtn">Today</button>
          <button class="btn ghost small" id="calNextBtn">Next ›</button>
        </div>
        <div style="display:flex; justify-content:flex-end; margin-bottom:8px;">
          <button class="btn ghost small" id="printCalendarBtn2">🖨 Print Calendar</button>
        </div>
        <div class="cal-legend" id="calLegend"></div>
        <div class="cal-grid" id="calGrid"></div>
      </div>
      <div class="card" id="calDayDetailCard">
        <h3 id="calDayDetailTitle">Select a day</h3>
        <div id="calDayDetail"></div>
      </div>
    </div>

    <div id="calAgendaView" style="display:none;">
      <div class="card" id="calAddCard">
        <h2>Add Rehearsal / Build Call</h2>
        <div class="form-grid">
          <input type="date" id="calDate">
          <input type="time" id="calStart" placeholder="Start">
          <input type="time" id="calEnd" placeholder="End">
          <input type="text" id="calTitle" placeholder="Title (e.g. Full run-through)">
          <input type="text" id="calLocation" placeholder="Location">
        </div>
        <label style="display:flex; align-items:center; gap:8px; font-size:12.5px; margin-bottom:10px;">
          <input type="checkbox" id="calSpecific"> This is a specific call (only selected departments/students see it)
        </label>
        <div class="checkbox-row" id="calDeptChecks"></div>
        <div id="calStudentPickWrap" style="display:none; margin-bottom:10px;">
          <div id="calStudentChecks" class="checkbox-row"></div>
        </div>
        <textarea id="calNotes" class="full-width" style="min-height:50px; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:9px 10px; border-radius:3px;" placeholder="Notes (optional)"></textarea>
        <button class="btn" id="calAddBtn">Add to Calendar</button>
        <button class="btn ghost small" id="toggleCalBulkImport" style="margin-left:8px;">Bulk Import Calendar</button>
        <div id="calBulkImportWrap" style="display:none; margin-top:14px;">
          <p style="font-size:11.5px;color:var(--paper-dim);">One call per line, fields separated by <code>|</code>: <code>Date|Start|End|Title|Location|Departments|Notes</code>. Date as <code>YYYY-MM-DD</code>, times as <code>HH:MM</code> (24-hour, blank if none), Departments comma-separated (Set Design, Props, Costumes, Hair &amp; Makeup, Lighting Design, Sound Design, Stage Management, Run Crew, Marketing, Cast — blank means everyone). This does not post to GroupMe or send emails; use the normal schedule-send buttons afterward if you want to announce it.</p>
          <textarea id="calBulkImportText" class="full-width" style="min-height:140px; font-family:'IBM Plex Mono',monospace; font-size:11.5px;" placeholder="2026-09-08|16:00|17:45|Company launch, table read|Main Stage|Stage Management, Sound Design, Set Design, Props, Cast|First after-school rehearsal"></textarea>
          <button class="btn small" id="calBulkImportBtn" style="margin-top:8px;">Import Calls</button>
          <button class="btn danger small" id="calClearAllBtn" style="margin-top:8px;">Clear All Calendar Entries</button>
        </div>
      </div>
      <div class="card" id="groupmeAnnounceCard" style="display:none;">
        <h2>Post to GroupMe</h2>
        <textarea id="groupmeAnnounceText" class="full-width" style="min-height:60px;" placeholder="Write an announcement for the group..."></textarea>
        <div class="cal-actions">
          <button class="btn small" id="groupmeSendAnnounceBtn">Send Announcement</button>
          <button class="btn ghost small" id="groupmeSendScheduleBtn">Send This Week's Schedule</button>
        </div>
      </div>
      <div style="display:flex; justify-content:flex-end; margin-bottom:10px;">
        <button class="btn ghost small" id="printCalendarBtn">🖨 Print Calendar</button>
      </div>
      <div id="calList"></div>
    </div>
  </section>

  <section class="view" id="view-conflicts">
    <div class="card" id="conflictAddCard">
      <h2>Report a Conflict</h2>
      <div id="conflictNoCrewMsg" class="empty-state" style="display:none;">No crew on the roster yet — add crew members in <b>Roster / Setup</b> first, then come back here to submit a conflict.</div>
      <div id="conflictNoEventMsg" class="empty-state" style="display:none;">No upcoming calls on the calendar yet — add one in the <b>Calendar</b> tab first.</div>
      <div id="conflictNotSignedInMsg" class="empty-state" style="display:none;">Sign in with Google or Email &amp; Password (top right) to submit a conflict.</div>
      <div id="conflictNotOnRosterMsg" class="empty-state" style="display:none;">You're identified, but that email isn't on the roster yet — ask the Director to add it in Roster / Setup.</div>
      <div id="conflictFormWrap">
        <div class="form-grid">
          <select id="conflictWho"></select>
          <select id="conflictEvent"></select>
          <select id="conflictReason">
            <option value="">Select a reason</option>
            <option>Doctor Appointment</option><option>Family Emergency</option><option>School Conflict</option>
            <option>Religious Observance</option><option>Transportation Issue</option><option>Other</option>
          </select>
        </div>
        <textarea id="conflictNotes" class="full-width" style="min-height:50px;" placeholder="Additional notes (optional)"></textarea>
        <button class="btn" id="conflictAddBtn">Submit Conflict</button>
      </div>
    </div>
    <div class="card"><h2>All Conflicts</h2><div id="conflictList"></div></div>
  </section>

  <section class="view" id="view-attendance">
    <div id="attendanceLockedMsg" class="empty-state" style="display:none;">
      <div class="lamp">🔒</div>Attendance is only visible to the Director and Stage Management team.
    </div>
    <div id="attendanceAuthorizedWrap">
      <div class="subtabs" id="attendanceSubtabs">
        <button data-att-sub="mark" class="active">Mark Attendance</button>
        <button data-att-sub="grid">Summary Grid</button>
      </div>

      <div id="attendanceMarkView">
        <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px; margin-bottom:16px;">Mark each expected student Present, Absent, or Excused for every rehearsal call. Students with an already-approved conflict for a call show as Excused by default until changed.</p>
        <div id="attendanceList"></div>
      </div>

      <div id="attendanceGridView" style="display:none;">
        <div id="attendanceGridLockedMsg" class="empty-state" style="display:none;">This spreadsheet view is Director-only.</div>
        <div id="attendanceGridWrap"></div>
      </div>
    </div>
  </section>

  <section class="view" id="view-behavior">
    <div id="behaviorNotSignedInMsg" class="empty-state" style="display:none;">
      Sign in with Google or Email &amp; Password (top right) to see your behavior record.
    </div>
    <div id="behaviorNotOnRosterMsg" class="empty-state" style="display:none;">
      You're identified, but that email isn't on the roster yet — ask the Director to add it in Roster / Setup.
    </div>
    <div id="behaviorMyViewWrap" style="display:none;">
      <div class="card">
        <div class="cal-nav">
          <button class="btn ghost small" id="myBehPrevWeekBtn">‹ Prev Week</button>
          <span class="stencil" id="myBehWeekLabel" style="font-size:16px;"></span>
          <button class="btn ghost small" id="myBehThisWeekBtn">This Week</button>
          <button class="btn ghost small" id="myBehNextWeekBtn">Next Week ›</button>
        </div>
        <p style="font-size:12px;color:var(--paper-dim); text-align:center;">This is your own behavior/etiquette record — nobody else's is shown here.</p>
        <div id="myBehaviorScoreWrap" style="text-align:center; margin:14px 0;"></div>
        <div id="myBehaviorIncidentList"></div>
      </div>
    </div>
    <div id="behaviorAuthorizedWrap">
      <div class="subtabs" id="behaviorSubtabs">
        <button data-beh-sub="log" class="active">Log Infraction</button>
        <button data-beh-sub="summary" id="behSummaryTabBtn">Weekly Summary</button>
      </div>

      <div id="behaviorLogView">
        <div class="card">
          <h2>Log an Etiquette / Behavior Infraction</h2>
          <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Each school day is worth <span id="behPtsLabel">20</span> points toward the weekly behavior grade. Logging an infraction deducts points from that day; a student with no infractions all week gets full credit.</p>
          <div class="form-grid">
            <select id="behStudent"></select>
            <input type="date" id="behDate">
          </div>
          <div class="form-grid">
            <select id="behReason">
              <option value="">Select a reason</option>
              <option>Disrespect toward peers or staff</option>
              <option>Not following directions</option>
              <option>Phone / device misuse</option>
              <option>Disruptive behavior</option>
              <option>Unsafe behavior in the shop</option>
              <option>Other</option>
            </select>
            <input type="number" id="behDeduction" placeholder="Points to deduct" min="1" max="100">
          </div>
          <textarea id="behNotes" class="full-width" style="min-height:50px;" placeholder="Additional notes (optional)"></textarea>
          <button class="btn" id="behLogBtn">Log Infraction</button>
        </div>
        <div class="card" id="behRecentCard" style="display:none;">
          <h2>Recent Infractions</h2>
          <div id="behaviorIncidentList"></div>
        </div>
      </div>

      <div id="behaviorSummaryView" style="display:none;">
        <div class="card">
          <div class="cal-nav">
            <button class="btn ghost small" id="behPrevWeekBtn">‹ Prev Week</button>
            <span class="stencil" id="behWeekLabel" style="font-size:16px;"></span>
            <button class="btn ghost small" id="behThisWeekBtn">This Week</button>
            <button class="btn ghost small" id="behNextWeekBtn">Next Week ›</button>
          </div>
          <div style="display:flex; justify-content:flex-end; margin-bottom:10px;">
            <button class="btn ghost small" id="behExportCsvBtn">Download CSV for gradebook</button>
          </div>
          <div id="behaviorSummaryWrap"></div>
        </div>
      </div>
    </div>
  </section>

  <section class="view" id="view-departments">
    <div class="pill-row" id="deptPills"></div>
    <div class="subtabs" id="deptSubtabs">
      <button data-sub="tasks" class="active">Tasks</button>
      <button data-sub="templates">Templates</button>
      <button data-sub="report">Daily Report</button>
      <button data-sub="reports">Reports & Grading</button>
      <button data-sub="participation">Participation</button>
      <button data-sub="workspace">Team Workspace</button>
    </div>
    <div id="deptContent"></div>
  </section>

  <section class="view" id="view-costumes">
    <div id="costumeLockedMsg" class="empty-state" style="display:none;">
      <div class="lamp">🔒</div>Costume measurements are private to the Costumes team and the Director. Select yourself as a Costumes crew member above (or ask the Director to assign you) to view this.
    </div>
    <div id="costumeAuthorizedWrap">
      <div class="card">
        <h2>Add Cast / Crew Measurement Record</h2>
        <button class="btn ghost small" id="toggleCostumeForm">+ New Record</button>
        <div id="costumeAddForm" style="display:none; margin-top:14px;">
          <div class="form-grid">
            <input type="text" id="cName" placeholder="Name">
            <input type="text" id="cEmail" placeholder="Email (optional)">
          </div>
          <div class="costume-grid" id="cMeasureGrid"></div>
          <div style="display:flex; gap:8px; margin:10px 0;">
            <input type="text" id="cPieceName" placeholder="Add costume piece..." style="flex:1; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:8px 10px; border-radius:3px;">
            <select id="cPieceCat"><option>Shoes</option><option>Wig</option><option>Hat</option><option>Top</option><option>Bottom</option><option>Dress</option><option>Accessory</option><option>Outerwear</option><option>Other</option></select>
            <button class="btn small" id="cAddPiece">Add</button>
          </div>
          <div id="cPiecesList" style="margin-bottom:10px;"></div>
          <textarea id="cNotes" class="full-width" style="min-height:50px;" placeholder="Notes"></textarea>
          <button class="btn" id="cSaveBtn">Save Record</button>
        </div>
      </div>
      <div id="costumeList"></div>
    </div>
  </section>

  <section class="view" id="view-stagedesign">
    <div id="stageDesignLockedMsg" class="empty-state" style="display:none;">
      <div class="lamp">🔒</div>Sign in to view the 3D set design.
    </div>
    <div id="stageDesignWrap" style="display:none;">
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">A simple 3D layout tool for blocking the set — not photorealistic models. Drag on the empty background to orbit the camera, scroll to zoom. Grid lines are 1-foot squares — check your own contest venue's actual playing space, since that varies by site.</p>
      <div class="subtabs" id="sdBoardTabs">
        <button data-board="production" class="active">Production Set</button>
        <button data-board="sandbox">Practice Sandboxes</button>
      </div>
      <div id="sdSandboxBrowseWrap" style="display:none; margin-bottom:10px; align-items:center; gap:8px; flex-wrap:wrap;">
        <select id="sdSandboxBrowseSelect" style="background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:3px; font-size:12.5px; max-width:280px;"></select>
        <button class="btn small" id="sdNewSandboxBtn">+ New Sandbox</button>
        <button class="btn danger small" id="sdDeleteSandboxBtn" style="display:none;">Delete This Sandbox</button>
        <span style="font-size:11px; color:var(--paper-dim);">Each sandbox is its own separate board — make one per group so groups don't overwrite each other. Anyone who picks the same sandbox works on it together, live.</span>
      </div>
      <div id="sdToolbarWrap" style="display:none;">
        <p style="font-size:11px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em; margin:10px 0 4px;">Standard UIL One-Act Play Set (32-piece)</p>
        <div class="wb-toolbar" id="sdToolbarUil">
          <button class="wb-tool-btn" data-piece="uil_platform_4x8">▭ 4×8 Platform</button>
          <button class="wb-tool-btn" data-piece="uil_platform_4x4">▭ 4×4 Platform</button>
          <button class="wb-tool-btn" data-piece="uil_platform_1x1">▪ 1×1 Platform</button>
          <button class="wb-tool-btn" data-piece="uil_ramp_4x4">◺ 4×4 Ramp</button>
          <button class="wb-tool-btn" data-piece="uil_step_4">▲ 4' Steps</button>
          <button class="wb-tool-btn" data-piece="uil_step_2">▲ 2' Steps</button>
          <button class="wb-tool-btn" data-piece="uil_pylon_8">▮ 8' Pylon</button>
          <button class="wb-tool-btn" data-piece="uil_pylon_6">▮ 6' Pylon</button>
          <button class="wb-tool-btn" data-piece="uil_pylon_4">▮ 4' Pylon</button>
          <button class="wb-tool-btn" data-piece="uil_door">▯ Door Unit</button>
          <button class="wb-tool-btn" data-piece="uil_window">▯ Window Unit</button>
          <button class="wb-tool-btn" data-piece="uil_french_door">▯ French Door</button>
          <button class="wb-tool-btn" data-piece="uil_bifold_flat">▯ Bifold Flat</button>
          <button class="wb-tool-btn" data-piece="uil_trifold_flat">▯ Trifold Flat</button>
        </div>
        <p style="font-size:11px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em; margin:12px 0 4px;">Extra Furniture &amp; Set Dressing</p>
        <div class="wb-toolbar" id="sdToolbar">
          <button class="wb-tool-btn" data-piece="platform">▭ Platform</button>
          <button class="wb-tool-btn" data-piece="flat">▯ Flat</button>
          <button class="wb-tool-btn" data-piece="stairs">▲ Stairs</button>
          <button class="wb-tool-btn" data-piece="riser">▪ Riser</button>
          <button class="wb-tool-btn" data-piece="table">▤ Table</button>
          <button class="wb-tool-btn" data-piece="chair">◪ Chair</button>
          <button class="wb-tool-btn" data-piece="bench">▬ Bench</button>
          <button class="wb-tool-btn" data-piece="door">▮ Doorway</button>
          <span class="wb-sep"></span>
          <button class="btn ghost small" id="sdUndoBtn" disabled>↶ Undo</button>
          <button class="btn danger small" id="sdClearBtn">Clear All Pieces</button>
        </div>
      </div>
      <div style="display:flex; justify-content:flex-end; gap:8px; margin-bottom:10px; flex-wrap:wrap;">
        <button class="btn ghost small" id="sdPrintSnapshotBtn">🖨 Print 3D View</button>
        <button class="btn ghost small" id="sdPrintGroundPlanBtn">📐 Print Ground Plan</button>
      </div>
      <div class="sd-layout">
        <div class="sd-canvas-wrap" id="sdCanvasWrap">
          <canvas id="sdCanvas"></canvas>
          <div class="sd-hint">Drag background to orbit · scroll to zoom · click a piece to select</div>
        </div>
        <div class="sd-side-panel">
          <h3 style="margin-top:0;">Pieces on Stage</h3>
          <div class="sd-piece-list" id="sdPieceList"></div>
          <div id="sdPropsPanel" class="sd-props" style="display:none;">
            <h3>Selected Piece</h3>
            <label>Label</label>
            <input type="text" id="sdPropLabel">
            <label>Color</label>
            <input type="color" id="sdPropColor">
            <label>Dimensions (ft)</label>
            <div class="sd-dims-row">
              <div><input type="number" id="sdPropWidth" min="0.5" step="0.5" placeholder="W"></div>
              <div><input type="number" id="sdPropDepth" min="0.5" step="0.5" placeholder="D"></div>
              <div><input type="number" id="sdPropHeight" min="0.5" step="0.5" placeholder="H"></div>
            </div>
            <label>Rotation (°)</label>
            <input type="number" id="sdPropRotation" min="0" max="359" step="5">
            <label>Base Height Off Floor (ft)</label>
            <input type="number" id="sdPropBaseY" min="0" step="0.5">
            <button class="btn danger small" id="sdDeletePieceBtn" style="margin-top:12px; width:100%;">Delete Piece</button>
          </div>
        </div>
      </div>
    </div>
  </section>

  <section class="view" id="view-lightinglab">
    <div id="lightingLabLockedMsg" class="empty-state" style="display:none;">
      <div class="lamp">🔒</div>Sign in to use the Lighting Lab.
    </div>
    <div id="lightingLabWrap" style="display:none;">
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Practice lighting areas & cues on a virtual board — areas numbered 1-15 (1-5 front row, 6-10 middle, 11-15 back, left to right), matching how your program lays them out, plus 3 cyclorama zones. Board changes reset when you leave the tab; saved cues stick around for the whole team.</p>
      <div class="ll-layout">
        <div class="ll-stage-panel">
          <div class="wb-toolbar" id="llPieceToolbar" style="margin-bottom:10px;">
            <button class="wb-tool-btn" id="llAddActorBtn">🧍 Add Actor</button>
            <button class="wb-tool-btn" id="llAddCubeBtn">◼ Add Rehearsal Cube</button>
            <span class="wb-sep"></span>
            <button class="btn danger small" id="llClearPiecesBtn">Clear Actors/Cubes</button>
          </div>
          <div class="ll-canvas-wrap" id="llCanvasWrap">
            <canvas id="llCanvas"></canvas>
            <div class="sd-hint">Drag background to orbit · drag an actor/cube to move it · scroll to zoom</div>
          </div>
        </div>
        <div class="ll-board">
          <div class="ll-board-group-label">Stage Areas</div>
          <div class="ll-channels-row" id="llAreaChannels"></div>
          <div class="ll-board-group-label">Cyclorama</div>
          <div class="ll-channels-row" id="llCycChannels"></div>
        </div>
        <div class="card">
          <h3 style="margin-top:0;">Cues</h3>
          <div class="form-grid">
            <input type="text" id="llCueName" placeholder="Cue name (e.g. Cue 1 - Opening)">
            <button class="btn small" id="llSaveCueBtn">Save Current Look as Cue</button>
          </div>
          <button class="btn ghost small" id="llBlackoutBtn">Blackout (all to 0%)</button>
          <button class="btn ghost small" id="llExportCuesBtn">Export All Cues (CSV)</button>
          <button class="btn ghost small" id="llPrintLookBtn">🖨 Print This Look</button>
          <div id="llCueList" style="margin-top:10px;"></div>
        </div>
      </div>
    </div>
  </section>

  <section class="view" id="view-blocking">
    <div id="blockingLockedMsg" class="empty-state" style="display:none;">
      <div class="lamp">🔒</div>The Blocking Assistant is only visible to the Director and Stage Management.
    </div>
    <div id="blockingWrap" style="display:none;">
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">A blank top-down stage for blocking scenes — drag actor markers, rehearsal cubes, entrances, sequence numbers, and sticky notes into place (click a marker's color swatch to recolor it), then save the whole layout as a blocking note tied to a page number. Live-shared between Director and Stage Management while this tab is open.</p>
      <div class="wb-toolbar" id="bkToolbar">
        <button class="wb-tool-btn" id="bkAddActorBtn">🟠 Add Actor</button>
        <button class="wb-tool-btn" id="bkAddCubeBtn">◻ Add Rehearsal Cube</button>
        <button class="wb-tool-btn" id="bkAddDoorBtn">🚪 Add Entrance/Door</button>
        <button class="wb-tool-btn" id="bkAddNumberBtn">① Add Sequence #</button>
        <button class="wb-tool-btn" id="bkAddStickyBtn">🗒️ Add Sticky Note</button>
        <span class="wb-sep"></span>
        <button class="btn danger small" id="bkClearBoardBtn">Clear Board</button>
      </div>
      <div class="wb-toolbar" id="bkDrawToolbar">
        <button class="wb-tool-btn active" data-bktool="select">🖐 Move Markers</button>
        <button class="wb-tool-btn" data-bktool="pen">✏️ Pen (draw notes/arrows)</button>
        <button class="wb-tool-btn" data-bktool="eraser">🧹 Eraser</button>
        <span class="wb-sep"></span>
        <div id="bkPenOptionsRow" style="display:none; align-items:center; gap:10px;">
          <span class="wb-color-row" id="bkPenColorRow"></span>
          <span class="wb-width-row" id="bkPenWidthRow"></span>
        </div>
      </div>
      <div class="bk-page-nav" id="bkPageNav" style="display:none;">
        <button class="btn ghost small" id="bkPrevPageBtn">◀ Prev Page</button>
        <span id="bkPageNavLabel"></span>
        <button class="btn ghost small" id="bkNextPageBtn">Next Page ▶</button>
      </div>
      <div class="bk-stage-wrap" id="bkStageWrap">
        <div class="bk-stage" id="bkStage">
          <div class="bk-centerline"></div>
          <canvas id="bkDrawCanvas" width="900" height="560" style="position:absolute; left:0; top:0; pointer-events:none;"></canvas>
        </div>
      </div>
      <div style="display:flex; justify-content:flex-end; margin-top:8px;">
        <button class="btn ghost small" id="bkPrintCurrentBtn">🖨 Print Current Board</button>
      </div>
      <div class="card" style="margin-top:14px;">
        <h3 style="margin-top:0;" id="bkFormTitle">Save as Blocking Note</h3>
        <div class="form-grid">
          <input type="text" id="bkPageNumber" placeholder="Page # (e.g. 14 or 14-16)">
          <input type="text" id="bkSceneLabel" placeholder="Scene / moment (e.g. Confrontation)">
        </div>
        <textarea id="bkNoteText" class="full-width" style="min-height:50px;" placeholder="Additional blocking notes (optional)"></textarea>
        <button class="btn small" id="bkSaveNoteBtn">Save This Layout as a Blocking Note</button>
        <button class="btn ghost small" id="bkCancelEditBtn" style="display:none;">Cancel Edit</button>
      </div>
      <div class="card" style="margin-top:12px;">
        <h3 style="margin-top:0;">Saved Blocking Notes</h3>
        <div id="bkNoteList"></div>
      </div>
    </div>
  </section>

  <section class="view" id="view-notifications">
    <div class="card">
      <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:14px; flex-wrap:wrap; gap:8px;">
        <h2 style="margin:0;">Schedule & Grading Changes</h2>
        <span style="display:flex; gap:8px;">
          <button class="btn ghost small" id="markReadBtn">Mark all read</button>
          <button class="btn danger small" id="clearAllNotifsBtn" style="display:none;">Clear All</button>
        </span>
      </div>
      <div id="notifList"></div>
    </div>
  </section>

  <section class="view" id="view-setup">
    <div class="card" id="claimDirectorCard" style="display:none;">
      <h2>No Director Assigned Yet</h2>
      <p style="font-size:12.5px;color:var(--paper-dim);">Nobody has Director access on this production yet. If that's you, claim it now — after that, only existing Directors can add or remove Director access for others.</p>
      <button class="btn" id="claimDirectorBtn">Make me the Director</button>
    </div>
    <div class="card" id="pendingApprovalsCard" style="display:none;">
      <h2>Pending Approvals <span id="pendingApprovalsBadge" style="display:none; background:var(--red); color:#fff; font-size:11px; font-weight:700; padding:2px 8px; border-radius:10px; vertical-align:middle;">0</span></h2>
      <p style="font-size:12.5px;color:var(--paper-dim);">People who signed in but aren't on your roster yet — they can't see anything until you approve or deny them. Approve pre-fills the roster form below with their name and email; finish it out and add them like normal to grant access.</p>
      <div id="pendingApprovalsList"></div>
    </div>
    <div class="card" id="dirAccessCard" style="display:none;">
      <h2>Director Access</h2>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Anyone signed in with one of these Google accounts gets Director access. Real Google sign-in enforces this — it isn't just a UI toggle.</p>
      <div id="dirRecoveryWarning" class="lockmsg" style="display:none; border-color:var(--red); color:var(--red);">⚠ Only one Director email is on file. If you lose access to that Google account, there's no self-service way back in — add a second email you also control as a backup.</div>
      <div id="directorEmailsList" style="margin-bottom:10px;"></div>
      <div class="form-grid" style="max-width:320px;">
        <input type="text" id="newDirectorEmail" placeholder="name@school.edu">
        <button class="btn small" id="addDirectorEmailBtn">Add Director</button>
      </div>
      <h2 style="margin-top:22px;">Attendance Alerts</h2>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Flags a student on the Dashboard (and logs a private notification) once their recorded <b>unexcused absences</b> reach this number. Take attendance in the Attendance tab — marking someone "Excused" never counts toward this.</p>
      <div class="form-grid" style="max-width:220px;">
        <input type="number" id="attendanceThreshold" min="1" max="20">
        <button class="btn small" id="attendanceThresholdSaveBtn">Save</button>
      </div>
      <h2 style="margin-top:22px;">Behavior Grading</h2>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Sets how many points each school day is worth toward the weekly behavior/etiquette grade, and how many school days count in a week. Log infractions in the Behavior tab.</p>
      <div class="form-grid" style="max-width:320px;">
        <input type="number" id="behPointsPerDay" placeholder="Points per day" min="1" max="100">
        <input type="number" id="behDaysPerWeek" placeholder="Days per week" min="1" max="7">
        <button class="btn small" id="behConfigSaveBtn">Save</button>
      </div>
      <h2 style="margin-top:22px;">Participation Grading</h2>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Sets how many points each school day is worth toward the weekly participation grade (daily reports), and how many school days count in a week. Everyone starts at full marks; a student is only docked for days they're flagged as a non-contributor on their department's daily report.</p>
      <div class="form-grid" style="max-width:320px;">
        <input type="number" id="partPointsPerDay" placeholder="Points per day" min="1" max="100">
        <input type="number" id="partDaysPerWeek" placeholder="Days per week" min="1" max="7">
        <button class="btn small" id="partConfigSaveBtn">Save</button>
      </div>
    </div>
    <div class="card" id="emailAlertsCard">
      <h2>Email Alerts</h2>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Sends real emails using <a href="https://www.emailjs.com" target="_blank" style="color:var(--amber);">EmailJS</a> (free, no server needed). Each of the 4 alerts below is independent — leave any Template ID blank and that alert simply stays off, so you don't need all 4 set up at once. <b>EmailJS's free plan allows 2 templates</b> — Absence and Behavior are the two most people set up first; Deadline and Failing Grade are optional extras for later if you upgrade. Each alert emails both the student and their parent (using the emails on their roster entry) at the moment it happens.</p>
      <div class="form-grid" style="max-width:460px;">
        <input type="text" id="ejsPublicKey" placeholder="Public Key">
        <input type="text" id="ejsServiceId" placeholder="Service ID">
      </div>
      <div class="form-grid" style="max-width:460px;">
        <input type="text" id="ejsTemplateAbsence" placeholder="Template ID — Absence">
        <input type="text" id="ejsTemplateBehavior" placeholder="Template ID — Behavior Infraction">
      </div>
      <div class="form-grid" style="max-width:460px;">
        <input type="text" id="ejsTemplateDeadline" placeholder="Template ID — Deadline (optional)">
        <input type="text" id="ejsTemplateFailingGrade" placeholder="Template ID — Failing Grade (optional)">
      </div>
      <p style="font-size:11.5px;color:var(--paper-dim);">Suggested template variables — Absence: <code>to_email, student_name, event_title, event_date, production_name</code>. Behavior: <code>to_email, student_name, reason, infraction_date, deduction, notes, production_name</code>. Deadline: <code>to_email, student_name, task_title, due_date, department</code>. Failing Grade: <code>to_email, student_name, department, grade, grade_letter, report_date, production_name</code>. A failing grade is anything under 60.</p>
      <button class="btn small" id="ejsConfigSaveBtn">Save</button>
    </div>
    <div class="card" id="groupmeCard">
      <h2>GroupMe Announcements</h2>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Posts to GroupMe using your bot — new calls, reschedules, cancellations, day-before reminders, plus manual announcements and schedule posts (Calendar tab). Specific calls include which departments/students were called, so parents can see who's needed. Grades, absences, and behavior stay out of GroupMe entirely — those go through private email alerts instead. Paste your Bot ID below.</p>
      <div class="form-grid" style="max-width:320px;">
        <input type="text" id="groupmeBotId" placeholder="Bot ID">
        <button class="btn small" id="groupmeConfigSaveBtn">Save</button>
      </div>
      <p style="font-size:11px;color:var(--paper-dim);">If posts don't show up in the group, check the browser console for a "GroupMe post failed" warning — some networks or GroupMe's own policies can block direct browser calls.</p>
    </div>
    <div class="card">
      <h2>Cast &amp; Crew Roster</h2>
      <div id="rosterAddFormWrap">
        <div class="form-grid">
          <input type="text" id="rosterName" placeholder="Full name">
          <select id="rosterRole"><option value="student">Student</option><option value="teacher">Teacher/Director</option></select>
          <select id="rosterClassPeriod"><option>Class A</option><option>Class B</option><option>Class C</option><option>Class D</option></select>
        </div>
        <div class="checkbox-row" id="rosterDeptChecks" style="margin-bottom:10px;"></div>
        <label style="font-size:11px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em;">Add to Production(s)</label>
        <div class="checkbox-row" id="rosterProdChecks" style="margin-bottom:10px;"></div>
        <div class="form-grid">
          <input type="text" id="rosterCastRole" placeholder="Cast role / character (if applicable)">
        </div>
        <div class="form-grid">
          <input type="text" id="rosterEmail" placeholder="Their sign-in email (for Google login)">
          <input type="text" id="rosterParentEmail" placeholder="Parent email (optional)">
          <input type="text" id="rosterParentPhone" placeholder="Parent phone (optional)">
        </div>
        <button class="btn small" id="rosterAddBtn">Add Cast / Crew Member</button>
      </div>
      <div id="rosterLockedMsg" class="lockmsg" style="display:none;">Viewing as Team — only the Teacher/Director can add, remove, or reassign cast/crew.</div>
      <div id="rosterList" style="margin-top:14px;"></div>
    </div>
    <div class="card" id="bulkImportCard">
      <h2>Bulk Import Cast &amp; Crew</h2>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">No live Google Classroom connection is possible from a standalone file, but you can paste a roster copied from Classroom (or anywhere) — one person per line — and add them all at once. Assign teams (including Cast) to each afterward using "Edit Teams" below. Include their sign-in email so Google sign-in can match them automatically.</p>
      <textarea id="bulkImportText" class="full-width" style="min-height:110px;" placeholder="Jane Smith, jane@school.edu&#10;John Doe, john@school.edu"></textarea>
      <button class="btn ghost small" id="bulkImportBtn">Import as Students</button>
    </div>
    <div class="card" id="exportGradesCard">
      <h2>Export Grades</h2>
      <p style="font-size:12.5px;color:var(--paper-dim);">Downloads a spreadsheet with one row per student per report — final grade already accounts for any individual overrides — across every department at once, ready to paste into your gradebook.</p>
      <button class="btn small" id="exportAllGradesBtn">Download All Department Grades (CSV)</button>
    </div>
    <div class="card" id="participationExportCard">
      <h2>Weekly Participation (All Departments)</h2>
      <p style="font-size:12.5px;color:var(--paper-dim);">One row per student with their combined participation average across every department they're on, for a chosen week — everyone starts at full marks, only docked for days they were flagged as a non-contributor. This is the number to use for weekly participation grades.</p>
      <div class="cal-nav" style="margin-bottom:10px;">
        <button class="btn ghost small" id="allPartPrevWeekBtn">‹ Prev Week</button>
        <span class="stencil" id="allPartWeekLabel" style="font-size:15px;"></span>
        <button class="btn ghost small" id="allPartThisWeekBtn">This Week</button>
        <button class="btn ghost small" id="allPartNextWeekBtn">Next Week ›</button>
      </div>
      <button class="btn small" id="allPartExportCsvBtn">Download Weekly Participation Averages (CSV)</button>
    </div>
    <div class="card" id="resetCard">
      <h2>Data</h2>
      <p style="font-size:12.5px;color:var(--paper-dim);">All production data is shared and saved automatically to this app. Note: this app does not send real emails or sync with Google Classroom — those need a live server connection, which a standalone file can't provide.</p>
      <p style="font-size:12.5px; margin-bottom:4px;">Current production size: <b id="dataSizeLabel">—</b> <span style="color:var(--paper-dim);">(Firestore's per-document limit is 1 MB — everything in this production shares that one limit, so this is worth checking occasionally over a long season.)</span></p>
      <div class="bar" style="margin-bottom:14px;"><div class="bar-fill" id="dataSizeBar" style="width:0%;"></div></div>
      <button class="btn ghost small" id="downloadBackupBtn" style="margin-right:8px;">Download Full Backup (JSON)</button>
      <button class="btn danger small" id="resetAllBtn">Reset All Production Data</button>
    </div>
    <div class="card" id="archiveCard">
      <h2>Archive Old Records</h2>
      <p style="font-size:12.5px;color:var(--paper-dim);">Once you've exported a grading period's CSV into your real gradebook (Ascender, etc.), you don't need this app to keep holding onto it — this permanently deletes daily reports and behavior infractions dated before the date you choose, across every department. Grades/behavior after that date are untouched. <b>Export your CSVs first</b> — this cannot be undone.</p>
      <div class="form-grid" style="max-width:260px;">
        <input type="date" id="archiveCutoffDate">
        <button class="btn danger small" id="archiveDeleteBtn">Delete Records Before This Date</button>
      </div>
    </div>
  </section>

</main>

<div class="toast" id="toast"></div>

<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js"></script>

<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
  import { getFirestore, doc, getDoc, setDoc, updateDoc, arrayUnion, arrayRemove, onSnapshot, deleteDoc } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";
  import { getAuth, GoogleAuthProvider, signInWithPopup, signOut, onAuthStateChanged, createUserWithEmailAndPassword, signInWithEmailAndPassword, sendPasswordResetEmail } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";

  const firebaseConfig = {
    apiKey: "AIzaSyDbK6aryhE8wBUJ54Hq3_shhSOsJzSP-bY",
    authDomain: "stagedesk-f77d7.firebaseapp.com",
    projectId: "stagedesk-f77d7",
    storageBucket: "stagedesk-f77d7.firebasestorage.app",
    messagingSenderId: "30796013348",
    appId: "1:30796013348:web:3a3c5d65cd147f2d725db1",
    measurementId: "G-48CELBXKHK"
  };

  const app = initializeApp(firebaseConfig);
  const db = getFirestore(app);
  const auth = getAuth(app);
  const googleProvider = new GoogleAuthProvider();

  window.__fb = { db, doc, getDoc, setDoc, updateDoc, arrayUnion, arrayRemove, onSnapshot, deleteDoc, auth, googleProvider, signInWithPopup, signOut, onAuthStateChanged, createUserWithEmailAndPassword, signInWithEmailAndPassword, sendPasswordResetEmail };
  window.__firebaseReady = true;
  window.dispatchEvent(new Event('firebase-ready'));
</script>

<script>
(function(){

const DEPARTMENTS = [
  { key:'set_design',  label:'Set Design',       color:'#E8A33D' },
  { key:'props',       label:'Props',            color:'#B5715F' },
  { key:'costumes',    label:'Costumes',         color:'#9C5A88' },
  { key:'hair_makeup', label:'Hair & Makeup',    color:'#C97B84' },
  { key:'lighting',    label:'Lighting Design',  color:'#4C7A93' },
  { key:'sound',       label:'Sound Design',     color:'#4A8C82' },
  { key:'stage_mgmt',  label:'Stage Management', color:'#6E7B8B' },
  { key:'run_crew',    label:'Run Crew',         color:'#7A8450' },
  { key:'marketing',   label:'Marketing',        color:'#C9A227' },
  { key:'cast',        label:'Cast',             color:'#8C3B3B' },
];
const WORK_TYPES = ['Build','Paint','Source','Install','Coordinate','Document','Other'];
const PRIORITIES = ['low','medium','high'];
const SHIFTS = ['Morning','Afternoon','Evening','Full Day'];
const MEASURE_FIELDS = [
  {key:'height',label:'Height'},{key:'neck',label:'Neck'},{key:'chest',label:'Chest/Bust'},{key:'waist',label:'Waist'},
  {key:'hip',label:'Hip'},{key:'inseam',label:'Inseam'},{key:'sleeve',label:'Sleeve'},{key:'shoe_size',label:'Shoe'},
  {key:'dress_size',label:'Dress'},{key:'hat_size',label:'Hat/Wig'},
];
const PIECE_STATUSES = ['Assigned','Pulled','Fitted','Ready'];

function deptInfo(key){ return DEPARTMENTS.find(d=>d.key===key) || DEPARTMENTS[0]; }
function cryptoId(){ return 'id-' + Math.random().toString(36).slice(2,10) + Date.now().toString(36); }
function fmtDate(d){ return new Date(d+'T00:00:00').toLocaleDateString(undefined,{weekday:'short', month:'short', day:'numeric', year:'numeric'}); }
function fmtDateTime(iso){ return new Date(iso).toLocaleString(undefined,{month:'short', day:'numeric', hour:'numeric', minute:'2-digit'}); }
function todayISO(){ return new Date().toISOString().slice(0,10); }

function seedTasks(items){
  return items.map(([title, workType, priority, hours])=>({
    id:cryptoId(), title, description:'', workType, priority, dueDate:todayISO(), estimatedHours:hours, status:'todo', assignedTo:'', assignedToCrewId:null, deadlineAlertSent:false, checklist:[]
  }));
}
function defaultDeptState(seed){ return { tasks: seedTasks(seed), templates:[], reports:[], workspaceRestricted:false }; }

function freshDepartments(seeded){
  if(seeded){
    return {
      set_design: defaultDeptState([['Frame & brace flats for Scene 2','Build','high',3],['Base coat backdrop','Paint','medium',2]]),
      props: defaultDeptState([['Tag & inventory rehearsal props','Document','medium',1],['Source hero prop: sword','Source','high',2]]),
      costumes: defaultDeptState([['Pull costume stock for full cast','Source','high',3],['Fit Act 1 leads','Install','medium',2]]),
      hair_makeup: defaultDeptState([]),
      lighting: defaultDeptState([['Focus front wash','Install','high',2],['Program cues 1–10','Coordinate','medium',3]]),
      sound: defaultDeptState([['Mic check all wireless packs','Install','high',1],['Build cue playback list','Coordinate','medium',2]]),
      stage_mgmt: defaultDeptState([]),
      run_crew: defaultDeptState([]),
      marketing: defaultDeptState([]),
      cast: defaultDeptState([]),
    };
  }
  const d = {};
  DEPARTMENTS.forEach(dep=>{ d[dep.key] = defaultDeptState([]); });
  return d;
}

function defaultProductionState(name, seeded){
  return {
    productionName: name || 'Untitled Production',
    crew:[],
    calendar: seeded ? [
      { id:cryptoId(), title:'Full Run-Through', date:new Date(Date.now()+3*86400000).toISOString().slice(0,10),
        startTime:'17:00', endTime:'19:30', location:'Main Stage', notes:'', isSpecificCall:false, calledDepartments:[], calledStudentIds:[] }
    ] : [],
    conflicts:[],
    changelog:[ { id:cryptoId(), timestamp:new Date().toISOString(), message:'Production created.' } ],
    costumeRecords:[],
    behaviorIncidents:[],
    behaviorConfig: { pointsPerDay:20, daysPerWeek:5 },
    participationConfig: { pointsPerDay:20, daysPerWeek:5 },
    announcements:[],
    lightingCues:[],
    sandboxList:[],
    blockingNotes:[],
    departments: freshDepartments(seeded)
  };
}
// Production defaults are created via defaultProductionState(name, seeded) above.

const DEFAULT_GLOBAL = { directorEmails:[], attendanceAlertThreshold:3, productions:[], activeProductionId:null, emailjs:{ publicKey:'', serviceId:'', templateAbsence:'', templateDeadline:'', templateBehavior:'', templateFailingGrade:'' }, groupme:{ botId:'' }, rosterIndex:{}, pendingApprovals:[] };

let state = null;
let globalState = null;
let currentProductionId = null;
let device = { lastSeenChangelog:null };
const GLOBAL_DOC = ['config','main'];              // Firestore: collection 'config', doc 'main'
const PROD_COLLECTION = 'productions';             // Firestore: collection 'productions', doc <id>
const LEGACY_KEY = 'setdesk-production-state-v1';  // old single-production version (Claude.ai artifact only)
const DEVICE_STORAGE_KEY = 'setdesk-device-prefs-v2'; // now a real browser localStorage key
const memoryProductions = new Map(); // fallback cache, used only if Firestore is unreachable
let memoryFallbackGlobal = null;

function waitForFirebase(){
  return new Promise(resolve=>{
    if(window.__firebaseReady) return resolve();
    window.addEventListener('firebase-ready', ()=>resolve(), {once:true});
    setTimeout(resolve, 4000); // don't hang forever if the SDK failed to load (e.g. offline)
  });
}
async function fsGet(collectionName, docId){
  await waitForFirebase();
  try{
    if(window.__fb){
      const ref = window.__fb.doc(window.__fb.db, collectionName, docId);
      const snap = await window.__fb.getDoc(ref);
      if(snap.exists()) return snap.data();
    }
  }catch(e){ console.warn('Firestore read failed', e); }
  return null;
}
async function fsSet(collectionName, docId, data){
  await waitForFirebase();
  try{
    if(window.__fb){
      const ref = window.__fb.doc(window.__fb.db, collectionName, docId);
      await window.__fb.setDoc(ref, JSON.parse(JSON.stringify(data))); // strip undefined/functions, keep plain JSON
      return true;
    }
  }catch(e){ console.warn('Firestore write failed', e); }
  return false;
}

async function loadProductionState(id){
  const data = await fsGet(PROD_COLLECTION, id);
  if(data) return data;
  if(memoryProductions.has(id)) return memoryProductions.get(id);
  return defaultProductionState('Untitled Production', true);
}
async function saveState(){
  if(currentProductionId){
    memoryProductions.set(currentProductionId, state);
    await fsSet(PROD_COLLECTION, currentProductionId, state);
  }
}
async function loadGlobalStateRaw(){
  const data = await fsGet(GLOBAL_DOC[0], GLOBAL_DOC[1]);
  return data || memoryFallbackGlobal;
}
async function saveGlobalState(){
  memoryFallbackGlobal = globalState;
  await fsSet(GLOBAL_DOC[0], GLOBAL_DOC[1], globalState);
}
async function loadLegacyStateRaw(){
  // Only relevant if this file is ever reopened inside a Claude.ai artifact that still has
  // pre-Firebase data sitting in window.storage. Harmless no-op everywhere else (e.g. GitHub Pages).
  try{
    if(window.storage){
      const res = await window.storage.get(LEGACY_KEY, true);
      if(res && res.value) return JSON.parse(res.value);
    }
  }catch(e){}
  return null;
}

// One-time upgrade path: if this app was used before multi-production support existed,
// its single flat state gets wrapped into "Production 1" so nothing is lost.
async function loadOrMigrateGlobalState(){
  let g = await loadGlobalStateRaw();
  if(g) return g;

  const legacy = await loadLegacyStateRaw();
  const newGlobal = structuredClone(DEFAULT_GLOBAL);

  if(legacy){
    const prodId = cryptoId();
    const prodState = {
      productionName: legacy.productionName || 'Untitled Production',
      crew: legacy.crew || [],
      calendar: legacy.calendar || [],
      conflicts: legacy.conflicts || [],
      changelog: legacy.changelog || [],
      costumeRecords: legacy.costumeRecords || [],
      departments: legacy.departments || freshDepartments(true),
    };
    await saveProductionStateFor(prodId, prodState);
    // Note: the old PIN system is gone — directorEmails starts empty here.
    // Whoever signs in first after migration can claim Director access.
    newGlobal.attendanceAlertThreshold = legacy.attendanceAlertThreshold || 3;
    newGlobal.productions.push({ id:prodId, name:prodState.productionName, createdAt:new Date().toISOString() });
    newGlobal.activeProductionId = prodId;
  } else {
    const prodId = cryptoId();
    const prodState = defaultProductionState('Untitled Production', true);
    await saveProductionStateFor(prodId, prodState);
    newGlobal.productions.push({ id:prodId, name:prodState.productionName, createdAt:new Date().toISOString() });
    newGlobal.activeProductionId = prodId;
  }
  globalState = newGlobal;
  await saveGlobalState();
  return newGlobal;
}

async function saveProductionStateFor(id, data){
  memoryProductions.set(id, data);
  await fsSet(PROD_COLLECTION, id, data);
}
async function createProduction(name, copyFromId){
  const prodId = cryptoId();
  const prodState = defaultProductionState(name, false);
  if(copyFromId){
    const src = await loadProductionState(copyFromId);
    prodState.crew = (src.crew||[]).map(c=>({...c, id:cryptoId(), departments:[]}));
  }
  await saveProductionStateFor(prodId, prodState);
  globalState.productions.push({ id:prodId, name, createdAt:new Date().toISOString() });
  prodState.crew.forEach(c=>{ if(c.email) rosterIndexAdd(c.email, prodId, name, c.id); });
  await saveGlobalState();
  return prodId;
}
async function switchToProduction(id){
  stopWorkspaceListener();
  pauseStageDesignScene();
  pauseLightingLabScene();
  pauseBlockingScene();
  const prod = await loadProductionState(id);
  currentProductionId = id;
  state = prod;
  globalState.activeProductionId = id;
  await saveGlobalState();
  renderAll();
}

// Device prefs (notification read-state) are personal to this browser only — a normal localStorage key is the right tool here, not shared cloud storage.
function loadDevice(){
  try{
    const raw = localStorage.getItem(DEVICE_STORAGE_KEY);
    if(raw) return { lastSeenChangelog:null, manualEmail:'', ...JSON.parse(raw) };
  }catch(e){}
  return { lastSeenChangelog:null, manualEmail:'' };
}
function saveDevice(){
  try{ localStorage.setItem(DEVICE_STORAGE_KEY, JSON.stringify(device)); }
  catch(e){ console.warn('device save failed', e); }
}

function toast(msg){
  const t = document.getElementById('toast');
  t.textContent = msg; t.classList.add('show');
  clearTimeout(t._timer); t._timer = setTimeout(()=>t.classList.remove('show'), 2200);
}
function showSignedOutShell(){
  document.getElementById('tabsWrap').style.display = 'none';
  document.getElementById('mainContent').style.display = 'none';
  document.getElementById('signedOutShell').style.display = 'block';
  document.getElementById('pendingApprovalShell').style.display = 'none';
  document.getElementById('showPickerShell').style.display = 'none';
}
function showAppShell(){
  document.getElementById('tabsWrap').style.display = 'flex';
  document.getElementById('mainContent').style.display = 'block';
  document.getElementById('signedOutShell').style.display = 'none';
  document.getElementById('pendingApprovalShell').style.display = 'none';
  document.getElementById('showPickerShell').style.display = 'none';
  updateTabsScrollButtons();
}
function showPendingApprovalShell(){
  document.getElementById('tabsWrap').style.display = 'none';
  document.getElementById('mainContent').style.display = 'none';
  document.getElementById('signedOutShell').style.display = 'none';
  document.getElementById('pendingApprovalShell').style.display = 'block';
  document.getElementById('showPickerShell').style.display = 'none';
  const emailEl = document.getElementById('pendingApprovalEmail');
  if(emailEl) emailEl.textContent = activeEmail() || '';
}
function showProductionPickerShell(matches){
  document.getElementById('tabsWrap').style.display = 'none';
  document.getElementById('mainContent').style.display = 'none';
  document.getElementById('signedOutShell').style.display = 'none';
  document.getElementById('pendingApprovalShell').style.display = 'none';
  document.getElementById('showPickerShell').style.display = 'block';
  const list = document.getElementById('showPickerList');
  list.innerHTML = matches.map(m=>`<button class="btn" style="display:block; width:100%; margin-bottom:10px;" data-prodid="${m.prodId}">${escapeHtml(m.prodName)}</button>`).join('');
  list.querySelectorAll('[data-prodid]').forEach(btn=>btn.addEventListener('click', async ()=>{
    currentProductionId = btn.dataset.prodid;
    await finishLoadingChosenProduction();
  }));
}
async function finishLoadingChosenProduction(){
  stopWorkspaceListener();
  pauseStageDesignScene();
  pauseLightingLabScene();
  pauseBlockingScene();
  state = await loadProductionState(currentProductionId);
  if(!state.costumeRecords) state.costumeRecords = [];
  if(!state.behaviorIncidents) state.behaviorIncidents = [];
  if(!state.behaviorConfig) state.behaviorConfig = { pointsPerDay:20, daysPerWeek:5 };
  if(!state.participationConfig) state.participationConfig = { pointsPerDay:20, daysPerWeek:5 };
  if(!state.announcements) state.announcements = [];
  if(!state.lightingCues) state.lightingCues = [];
  if(!state.sandboxList) state.sandboxList = [];
  if(!state.blockingNotes) state.blockingNotes = [];
  if(!isApprovedUser()){
    await registerPendingApproval();
    showPendingApprovalShell();
    return;
  }
  showAppShell();
  renderAll();
  renderCostumeMeasureGrid(); renderCostumePieces();
  initEmailJs();
  renderHeader();
}
function updateTabsScrollButtons(){
  const nav = document.getElementById('mainTabs');
  const leftBtn = document.getElementById('tabsScrollLeft');
  const rightBtn = document.getElementById('tabsScrollRight');
  if(!nav || !leftBtn || !rightBtn) return;
  leftBtn.disabled = nav.scrollLeft <= 2;
  rightBtn.disabled = nav.scrollLeft >= nav.scrollWidth - nav.clientWidth - 2;
}
// True once someone is allowed to actually see the app: Director, matched to the roster by
// email, or — bootstrap case — nobody has claimed Director yet, so the very first sign-in
// needs through to find the "Make me the Director" button.
function isApprovedUser(){
  return isDirector() || !!currentUser() || !(globalState.directorEmails||[]).length;
}
async function registerPendingApproval(){
  const email = activeEmail();
  if(!email) return;
  if(!globalState.pendingApprovals) globalState.pendingApprovals = [];
  if(globalState.pendingApprovals.some(p=>p.email.toLowerCase()===email.toLowerCase())) return;
  const entry = { id:cryptoId(), email, displayName: (authUser&&authUser.displayName)||email, requestedAt:new Date().toISOString() };
  globalState.pendingApprovals.push(entry);
  // Atomic server-side append — critical here specifically, since many students can hit this
  // within seconds of each other during a class period. A normal saveGlobalState() would
  // overwrite the whole config document, and whoever saves last would silently wipe out
  // everyone else's pending request that raced with them.
  try{
    if(window.__fb){
      const ref = window.__fb.doc(window.__fb.db, GLOBAL_DOC[0], GLOBAL_DOC[1]);
      await window.__fb.updateDoc(ref, { pendingApprovals: window.__fb.arrayUnion(entry) });
    }
  }catch(e){ console.warn('Pending approval append failed', e); }
}
function renderPendingApprovals(){
  const card = document.getElementById('pendingApprovalsCard');
  card.style.display = isDirector() ? 'block' : 'none';
  if(!isDirector()) return;
  const pending = globalState.pendingApprovals || [];
  const badge = document.getElementById('pendingApprovalsBadge');
  badge.style.display = pending.length ? 'inline-block' : 'none';
  badge.textContent = pending.length;
  const list = document.getElementById('pendingApprovalsList');
  if(!pending.length){ list.innerHTML = `<div class="empty-state">No pending requests right now.</div>`; return; }
  list.innerHTML = pending.map(p=>`
    <div class="list-item">
      <span><b>${escapeHtml(p.displayName)}</b><br><span class="mono" style="font-size:11px;color:var(--paper-dim);">${escapeHtml(p.email)} · requested ${fmtDateTime(p.requestedAt)}</span></span>
      <span style="display:flex; gap:6px;">
        <button class="btn small" data-approve="${p.id}">Approve</button>
        <button class="btn danger small" data-deny="${p.id}">Deny</button>
      </span>
    </div>
  `).join('');
  list.querySelectorAll('[data-approve]').forEach(btn=>btn.addEventListener('click', ()=>{
    const p = pending.find(x=>x.id===btn.dataset.approve);
    if(!p) return;
    document.getElementById('rosterName').value = p.displayName;
    document.getElementById('rosterEmail').value = p.email;
    document.getElementById('rosterName').scrollIntoView({behavior:'smooth', block:'center'});
    toast('Pick which production(s) below, fill in their team & class period, then click "Add Cast / Crew Member"');
  }));
  list.querySelectorAll('[data-deny]').forEach(btn=>btn.addEventListener('click', async ()=>{
    if(!confirm('Deny this request? They\'ll stay blocked from the app.')) return;
    const removed = pending.find(x=>x.id===btn.dataset.deny);
    globalState.pendingApprovals = globalState.pendingApprovals.filter(x=>x.id!==btn.dataset.deny);
    try{
      if(window.__fb && removed){
        const ref = window.__fb.doc(window.__fb.db, GLOBAL_DOC[0], GLOBAL_DOC[1]);
        await window.__fb.updateDoc(ref, { pendingApprovals: window.__fb.arrayRemove(removed) });
      }
    }catch(e){ console.warn('Deny failed to sync', e); }
    renderPendingApprovals();
    toast('Request denied');
  }));
}

let ui = { activeDept:'set_design', activeSub:'tasks', calSub:'month', calMonthCursor:new Date(new Date().getFullYear(), new Date().getMonth(), 1), calSelectedDate: todayISO(), reportClassPeriod:null, attendanceSub:'mark', expandedTasks:new Set(), behaviorSub:'log', behaviorWeekCursor:null, myBehaviorWeekCursor:null, editingEventId:null, editingAnnouncementId:null, participationWeekCursor:null, allPartWeekCursor:null, bkTool:'select', bkPenColor:'#1a1a1a', bkPenWidth:3 };
let authUser = null; // { email, displayName } once signed in via Google or Email/Password, else null

// Reading any data now requires real Firebase Auth (Firestore rules enforce this), so
// identity is simply whether authUser is set — there's no unverified fallback anymore.
function activeEmail(){ return authUser ? authUser.email : null; }
function isVerified(){ return !!authUser; }
function currentUser(){
  const email = activeEmail();
  if(!email) return null;
  const lower = email.toLowerCase();
  return state.crew.find(c=>(c.email||'').toLowerCase()===lower) || null;
}
// Cross-production lookup: which production(s) is this email actually on the roster for?
// Needed because a student can be signed in while a DIFFERENT production than theirs
// happens to be the Director's globally-active one — we never want to gate their access
// on whatever show the Director last had open.
function rosterIndexAdd(email, prodId, prodName, crewId){
  if(!email) return;
  const key = email.toLowerCase();
  if(!globalState.rosterIndex) globalState.rosterIndex = {};
  const list = globalState.rosterIndex[key] || [];
  if(!list.some(m=>m.prodId===prodId)) list.push({ prodId, prodName, crewId });
  globalState.rosterIndex[key] = list;
}
function rosterIndexRemove(email, prodId){
  if(!email || !globalState.rosterIndex) return;
  const key = email.toLowerCase();
  const list = globalState.rosterIndex[key];
  if(!list) return;
  globalState.rosterIndex[key] = list.filter(m=>m.prodId!==prodId);
  if(!globalState.rosterIndex[key].length) delete globalState.rosterIndex[key];
}
// One-time backfill for productions that already had crew before this index existed —
// scans every production once and rebuilds the index from scratch. Safe to call repeatedly;
// only actually does the (relatively expensive) scan when the index looks empty/missing.
async function ensureRosterIndexBackfilled(){
  if(globalState.rosterIndex && Object.keys(globalState.rosterIndex).length) return;
  if(!globalState.productions || !globalState.productions.length) return;
  const freshIndex = {};
  for(const p of globalState.productions){
    const data = await fsGet(PROD_COLLECTION, p.id);
    if(data && Array.isArray(data.crew)){
      data.crew.forEach(c=>{
        if(!c.email) return;
        const key = c.email.toLowerCase();
        const list = freshIndex[key] || [];
        list.push({ prodId:p.id, prodName:p.name, crewId:c.id });
        freshIndex[key] = list;
      });
    }
  }
  globalState.rosterIndex = freshIndex;
  await saveGlobalState();
}
function isDirector(){
  // Director access requires real, verified Google sign-in — never the manual fallback.
  if(!authUser) return false;
  const email = authUser.email.toLowerCase();
  return (globalState.directorEmails||[]).map(e=>e.toLowerCase()).includes(email);
}
async function signInWithGoogle(){
  try{
    await waitForFirebase();
    if(!window.__fb) { toast('Sign-in is still loading, try again in a moment'); return; }
    await window.__fb.signInWithPopup(window.__fb.auth, window.__fb.googleProvider);
  }catch(e){
    console.warn('Sign-in failed', e);
    toast('Sign-in failed or was cancelled — try email & password instead if your school blocks this');
  }
}
function friendlyAuthError(e){
  const code = e && e.code || '';
  if(code.includes('wrong-password') || code.includes('invalid-credential')) return 'Incorrect email or password';
  if(code.includes('user-not-found')) return 'No account with that email — try "Create Account" instead';
  if(code.includes('email-already-in-use')) return 'An account already exists for that email — try "Sign In" instead';
  if(code.includes('weak-password')) return 'Password needs to be at least 6 characters';
  if(code.includes('invalid-email')) return 'Enter a valid email address';
  return 'Something went wrong — try again';
}
async function signInWithEmailPassword(email, password){
  try{
    await waitForFirebase();
    if(!window.__fb){ toast('Sign-in is still loading, try again in a moment'); return; }
    await window.__fb.signInWithEmailAndPassword(window.__fb.auth, email, password);
  }catch(e){
    console.warn('Email sign-in failed', e);
    toast(friendlyAuthError(e));
  }
}
async function signUpWithEmailPassword(email, password){
  try{
    await waitForFirebase();
    if(!window.__fb){ toast('Sign-in is still loading, try again in a moment'); return; }
    if(password.length < 6){ toast('Password needs to be at least 6 characters'); return; }
    await window.__fb.createUserWithEmailAndPassword(window.__fb.auth, email, password);
    toast('Account created — remember your password for next time!');
  }catch(e){
    console.warn('Account creation failed', e);
    toast(friendlyAuthError(e));
  }
}
async function sendPasswordReset(email){
  try{
    await waitForFirebase();
    if(!window.__fb){ toast('Still loading, try again in a moment'); return; }
    await window.__fb.sendPasswordResetEmail(window.__fb.auth, email);
    toast('Password reset email sent — check your inbox');
  }catch(e){
    console.warn('Password reset failed', e);
    toast(friendlyAuthError(e));
  }
}
async function signOutUser(){
  try{ if(window.__fb && authUser) await window.__fb.signOut(window.__fb.auth); }catch(e){ console.warn('Sign-out failed', e); }
  // The ongoing onAuthStateChanged listener (see init()) picks up the transition from here —
  // it clears loaded data and shows the sign-in shell, since reads require auth now.
}
function canViewDept(deptKey){
  if(isDirector()) return true;
  const u = currentUser();
  return !!(u && (u.departments||[]).includes(deptKey));
}
function canViewCostumes(){ return canViewDept('costumes'); }
function isDirectorOrStageMgmt(){ return isDirector() || canViewDept('stage_mgmt'); }
function canTakeAttendance(){ return isDirectorOrStageMgmt(); }
function canManageBehavior(){ return isDirectorOrStageMgmt(); }
function expectedAttendees(ev){
  if(ev.isSpecificCall){
    return state.crew.filter(c=>c.role==='student' && ((ev.calledStudentIds||[]).includes(c.id) || (c.departments||[]).some(d=>(ev.calledDepartments||[]).includes(d))));
  }
  return state.crew.filter(c=>c.role==='student');
}
function attendanceStatus(ev, crewId){
  if(ev.attendance && ev.attendance[crewId]) return ev.attendance[crewId];
  const hasApproved = state.conflicts.some(c=>c.eventId===ev.id && c.crewId===crewId && c.status==='approved');
  return hasApproved ? 'excused' : null;
}
// ---------------- EMAIL ALERTS (EmailJS — no server required) ----------------
function initEmailJs(){
  const cfg = globalState && globalState.emailjs;
  if(window.emailjs && cfg && cfg.publicKey){
    try{ emailjs.init({ publicKey: cfg.publicKey }); }catch(e){ console.warn('EmailJS init failed', e); }
  }
}
function emailAlertsConfigured(kind){
  const cfg = globalState && globalState.emailjs;
  if(!cfg || !window.emailjs || !cfg.publicKey || !cfg.serviceId) return false;
  if(kind==='absence') return !!cfg.templateAbsence;
  if(kind==='deadline') return !!cfg.templateDeadline;
  if(kind==='behavior') return !!cfg.templateBehavior;
  if(kind==='failingGrade') return !!cfg.templateFailingGrade;
  return false;
}
async function sendBehaviorInfractionEmail(student, incident){
  if(!emailAlertsConfigured('behavior')) return;
  const cfg = globalState.emailjs;
  const recipients = [student.email, student.parentEmail].filter(Boolean);
  if(!recipients.length) return;
  for(const to_email of recipients){
    try{
      await emailjs.send(cfg.serviceId, cfg.templateBehavior, {
        to_email, student_name: student.name, reason: incident.reason || 'No reason given',
        infraction_date: fmtDate(incident.date), deduction: incident.deduction, notes: incident.notes || '', production_name: state.productionName
      });
    }catch(e){ console.warn('Behavior infraction email failed to send to '+to_email, e); }
  }
}
async function checkAndSendFailingGradeEmails(report, dep){
  if(!emailAlertsConfigured('failingGrade')) return;
  if(!report.failingGradeEmailedIds) report.failingGradeEmailedIds = [];
  const cfg = globalState.emailjs;
  for(const crewId of (report.teamMemberIds||[])){
    if(report.failingGradeEmailedIds.includes(crewId)) continue;
    const adj = (report.individualAdjustments||[]).find(a=>a.crewId===crewId);
    const effectiveGrade = adj ? adj.adjustedGrade : report.grade;
    if(effectiveGrade >= 60) continue;
    const student = state.crew.find(c=>c.id===crewId);
    if(!student) continue;
    const recipients = [student.email, student.parentEmail].filter(Boolean);
    for(const to_email of recipients){
      try{
        await emailjs.send(cfg.serviceId, cfg.templateFailingGrade, {
          to_email, student_name: student.name, department: dep.label, grade: effectiveGrade,
          grade_letter: letterFor(effectiveGrade), report_date: fmtDate(report.date), production_name: state.productionName
        });
      }catch(e){ console.warn('Failing grade email failed to send to '+to_email, e); }
    }
    report.failingGradeEmailedIds.push(crewId);
  }
}
async function sendAbsenceEmail(student, ev){
  if(!emailAlertsConfigured('absence')) return;
  const cfg = globalState.emailjs;
  const recipients = [student.email, student.parentEmail].filter(Boolean);
  if(!recipients.length) return;
  for(const to_email of recipients){
    try{
      await emailjs.send(cfg.serviceId, cfg.templateAbsence, {
        to_email, student_name: student.name, event_title: ev.title, event_date: fmtDate(ev.date), production_name: state.productionName
      });
    }catch(e){ console.warn('Absence email failed to send to '+to_email, e); }
  }
}
let deadlineCheckRanThisSession = false;
async function checkAndSendDeadlineAlerts(){
  if(!emailAlertsConfigured('deadline')) return;
  if(deadlineCheckRanThisSession) return;
  deadlineCheckRanThisSession = true;
  const cfg = globalState.emailjs;
  const today = new Date(todayISO()+'T00:00');
  let anySent = false;
  for(const depKey of Object.keys(state.departments)){
    const depState = state.departments[depKey];
    for(const t of depState.tasks){
      if(t.status==='done' || t.deadlineAlertSent || !t.assignedToCrewId || !t.dueDate) continue;
      const due = new Date(t.dueDate+'T00:00');
      const diffDays = Math.round((due - today) / 86400000);
      if(diffDays >= 0 && diffDays <= 2){
        const person = state.crew.find(c=>c.id===t.assignedToCrewId);
        if(person && person.email){
          try{
            await emailjs.send(cfg.serviceId, cfg.templateDeadline, {
              to_email: person.email, student_name: person.name, task_title: t.title, due_date: t.dueDate, department: deptInfo(depKey).label
            });
            t.deadlineAlertSent = true;
            anySent = true;
          }catch(e){ console.warn('Deadline email failed for task '+t.title, e); }
        }
      }
    }
  }
  if(anySent) await saveState();
}

// ---------------- GROUPME ANNOUNCEMENTS (no server required) ----------------
// GroupMe's bot-post endpoint is called directly from the browser. If your school
// network or GroupMe itself ever blocks this cross-origin call, posts will just
// silently fail — check the browser console for a "GroupMe post failed" warning.
// Describes who a specific call was targeted at, so GroupMe posts (which parents see)
// make clear which team/group is actually needed — e.g. " (Called: Costumes, Sam Lee)".
function groupMeCallInfo(ev){
  if(!ev || !ev.isSpecificCall) return '';
  const depts = (ev.calledDepartments||[]).map(k=>deptInfo(k).label);
  const students = (ev.calledStudentIds||[]).map(id=>{ const c=state.crew.find(x=>x.id===id); return c?c.name:null; }).filter(Boolean);
  const parts = [...depts, ...students];
  return parts.length ? ` (Called: ${parts.join(', ')})` : '';
}
async function sendGroupMe(text){
  const cfg = globalState && globalState.groupme;
  if(!cfg || !cfg.botId) return;
  try{
    await fetch('https://api.groupme.com/v3/bots/post', {
      method:'POST',
      headers:{ 'Content-Type':'application/json' },
      body: JSON.stringify({ bot_id: cfg.botId, text })
    });
  }catch(e){ console.warn('GroupMe post failed', e); }
}
let groupMeReminderCheckRanThisSession = false;
async function checkAndSendGroupMeReminders(){
  const cfg = globalState && globalState.groupme;
  if(!cfg || !cfg.botId) return;
  if(groupMeReminderCheckRanThisSession) return;
  groupMeReminderCheckRanThisSession = true;
  const tomorrow = new Date(todayISO()+'T00:00'); tomorrow.setDate(tomorrow.getDate()+1);
  const tomorrowISO = tomorrow.toISOString().slice(0,10);
  let anySent = false;
  for(const ev of state.calendar){
    if(ev.reminderSent || ev.date !== tomorrowISO) continue;
    await sendGroupMe(`📢 Reminder: "${escapeHtml(ev.title)}" is tomorrow${ev.startTime?' at '+ev.startTime:''}${ev.location?' — '+ev.location:''}${groupMeCallInfo(ev)}.`);
    ev.reminderSent = true;
    anySent = true;
  }
  if(anySent) await saveState();
}

async function setAttendance(ev, crewId, status){
  if(!ev.attendance) ev.attendance = {};
  const before = absentCount(crewId);
  ev.attendance[crewId] = status;
  const after = absentCount(crewId);
  const threshold = globalState.attendanceAlertThreshold || 3;
  if(before < threshold && after >= threshold){
    const c = state.crew.find(x=>x.id===crewId);
    logChange(`🚩 Attendance alert: ${c?c.name:'A student'} has reached ${after} unexcused absences.`, {type:'directorOnly'});
  }
  if(status==='absent'){
    const student = state.crew.find(x=>x.id===crewId);
    if(student) sendAbsenceEmail(student, ev);
  }
  await saveState();
}
function absentCount(crewId){
  let n = 0;
  state.calendar.forEach(ev=>{ if(attendanceStatus(ev, crewId)==='absent') n++; });
  return n;
}
function attendanceChipForEvent(ev){
  if(!canTakeAttendance()) return '';
  const attendees = expectedAttendees(ev);
  if(!attendees.length) return '';
  const counts = {present:0, absent:0, excused:0};
  attendees.forEach(c=>{ const s = attendanceStatus(ev,c.id); if(s) counts[s]++; });
  return `<div class="chip-row" style="margin-top:6px;"><span class="chip outline">Attendance: ✓${counts.present} ✕${counts.absent} ⊘${counts.excused} / ${attendees.length}</span></div>`;
}

function logChange(message, scope){
  state.changelog.unshift({ id:cryptoId(), timestamp:new Date().toISOString(), message, scope: scope || {type:'all'} });
  if(state.changelog.length > 300) state.changelog = state.changelog.slice(0, 300);
}
function canSeeNotification(n){
  if(isDirector()) return true;
  const scope = n.scope || {type:'all'};
  const u = currentUser();
  if(scope.type==='all') return true;
  if(scope.type==='crew') return !!(u && u.id===scope.crewId);
  if(scope.type==='department') return !!(u && (u.departments||[]).includes(scope.dept));
  if(scope.type==='deptClass') return !!(u && (u.departments||[]).includes(scope.dept) && u.classPeriod===scope.classPeriod);
  if(scope.type==='directorOnly') return false;
  if(scope.type==='event'){
    if(!scope.isSpecificCall) return true;
    if(!u) return true;
    if((scope.calledStudentIds||[]).includes(u.id)) return true;
    return (u.departments||[]).some(d=>(scope.calledDepartments||[]).includes(d));
  }
  return true;
}
function eventScope(ev){ return { type:'event', isSpecificCall: !!ev.isSpecificCall, calledDepartments: ev.calledDepartments||[], calledStudentIds: ev.calledStudentIds||[] }; }

// ---------------- HEADER ----------------
function renderHeader(){
  const email = activeEmail();
  document.getElementById('signedOutWrap').style.display = email ? 'none' : 'flex';
  document.getElementById('signedInWrap').style.display = email ? 'flex' : 'none';
  if(email){
    document.getElementById('authUserName').textContent = authUser ? (authUser.displayName || authUser.email) : email;
    let roleLabel = 'Loading…';
    if(state){
      const u = currentUser();
      if(isDirector()) roleLabel = 'Director (verified)';
      else if(u) roleLabel = `${escapeHtml(u.name)} · ${deptInfo((u.departments||[])[0]||'').label || 'No team yet'}`;
      else roleLabel = 'Not on the roster yet';
    }
    document.getElementById('authUserRole').textContent = roleLabel;
    const switchBtn = document.getElementById('switchShowBtn');
    const myMatches = (globalState && globalState.rosterIndex) ? (globalState.rosterIndex[email.toLowerCase()]||[]) : [];
    switchBtn.style.display = (!isDirector() && myMatches.length > 1) ? 'inline-block' : 'none';
  }
  if(!state) return; // production data hasn't loaded yet (e.g. still signing in) — nothing more to show
  document.getElementById('prodName').value = state.productionName;
  document.getElementById('prodName').disabled = !isDirector();
  document.getElementById('todayDate').textContent = new Date().toLocaleDateString(undefined,{weekday:'long', month:'short', day:'numeric'});
  document.getElementById('crewCount').textContent = state.crew.length;
}

// ---------------- DASHBOARD ----------------
function deptCompletionPct(deptKey){
  const tasks = state.departments[deptKey].tasks;
  if(!tasks.length) return 0;
  return Math.round(tasks.filter(t=>t.status==='done').length / tasks.length * 100);
}
function approvedConflictCount(crewId){ return state.conflicts.filter(c=>c.crewId===crewId && c.status==='approved').length; }
function attendanceAlerts(){
  const threshold = globalState.attendanceAlertThreshold || 3;
  return state.crew.filter(c=>c.role==='student').map(c=>({ crew:c, count: absentCount(c.id) })).filter(x=>x.count>=threshold).sort((a,b)=>b.count-a.count);
}
// ---------------- PRODUCTIONS ----------------
function renderProductionsView(){
  const authorized = isDirector();
  document.getElementById('productionsLockedMsg').style.display = authorized ? 'none' : 'block';
  document.getElementById('productionsAuthorizedWrap').style.display = authorized ? 'block' : 'none';
  if(!authorized) return;

  const copySel = document.getElementById('copyFromProduction');
  copySel.innerHTML = '<option value="">Start with an empty roster</option>' +
    globalState.productions.map(p=>`<option value="${p.id}">Copy crew from "${escapeHtml(p.name)}"</option>`).join('');

  const list = document.getElementById('productionsList');
  list.innerHTML = '';
  if(!globalState.productions.length){ list.innerHTML = `<div class="empty-state">No productions yet.</div>`; return; }
  [...globalState.productions].sort((a,b)=>b.createdAt.localeCompare(a.createdAt)).forEach(p=>{
    const isActive = p.id === currentProductionId;
    const row = document.createElement('div');
    row.className = 'cal-event';
    row.innerHTML = `
      <div class="cal-event-top">
        <div><div class="cal-title">${escapeHtml(p.name)}${isActive?' <span class="tag" style="margin-left:6px; color:var(--sage); border-color:var(--sage);">Currently viewing</span>':''}</div>
        <div class="cal-meta">Created ${fmtDate(p.createdAt.slice(0,10))}</div></div>
        <span style="display:flex; gap:8px;">
          ${!isActive?`<button class="btn ghost small" data-switch="${p.id}">Switch to this production</button>`:''}
          ${!isActive?`<button class="btn danger small" data-delete="${p.id}">Delete</button>`:''}
        </span>
      </div>
    `;
    list.appendChild(row);
  });
  list.querySelectorAll('[data-switch]').forEach(btn=>btn.addEventListener('click', async ()=>{
    await switchToProduction(btn.dataset.switch);
    toast(`Switched to "${globalState.productions.find(p=>p.id===btn.dataset.switch).name}"`);
  }));
  list.querySelectorAll('[data-delete]').forEach(btn=>btn.addEventListener('click', async ()=>{
    const p = globalState.productions.find(x=>x.id===btn.dataset.delete);
    if(!p) return;
    if(!confirm(`Permanently delete "${p.name}"? This removes ALL of its data — tasks, calendar, grades, attendance, behavior records, and workspace boards — with no undo. Download a backup first (Setup → Data) if you want to keep anything from it.`)) return;
    btn.disabled = true; btn.textContent = 'Deleting…';
    try{
      await deleteProductionCompletely(p.id);
      globalState.productions = globalState.productions.filter(x=>x.id!==p.id);
      if(globalState.rosterIndex){
        Object.keys(globalState.rosterIndex).forEach(email=>{
          globalState.rosterIndex[email] = globalState.rosterIndex[email].filter(m=>m.prodId!==p.id);
          if(!globalState.rosterIndex[email].length) delete globalState.rosterIndex[email];
        });
      }
      await saveGlobalState();
      renderProductionsView();
      toast(`"${p.name}" deleted`);
    }catch(e){
      toast('Delete failed — check your connection and try again');
      btn.disabled = false; btn.textContent = 'Delete';
    }
  }));
}
async function deleteProductionCompletely(prodId){
  if(!window.__fb) throw new Error('Firebase not ready');
  const data = await fsGet(PROD_COLLECTION, prodId).catch(()=>null);
  await window.__fb.deleteDoc(window.__fb.doc(window.__fb.db, 'productions', prodId));
  await Promise.all(DEPARTMENTS.map(d=>
    window.__fb.deleteDoc(window.__fb.doc(window.__fb.db, 'workspaces', prodId+'_'+d.key)).catch(()=>{})
  ));
  await window.__fb.deleteDoc(window.__fb.doc(window.__fb.db, 'stagedesigns', prodId)).catch(()=>{});
  await window.__fb.deleteDoc(window.__fb.doc(window.__fb.db, 'blocking_boards', prodId)).catch(()=>{});
  if(data && Array.isArray(data.sandboxList)){
    await Promise.all(data.sandboxList.map(sbx=>
      window.__fb.deleteDoc(window.__fb.doc(window.__fb.db, 'stagedesign_sandboxes', prodId+'_sbx_'+sbx.id)).catch(()=>{})
    ));
  }
}

function renderAnnouncements(){
  document.getElementById('announcementPostWrap').style.display = isDirector() ? 'block' : 'none';
  const list = document.getElementById('announcementList');
  const items = [...(state.announcements||[])].sort((a,b)=>b.postedAt.localeCompare(a.postedAt));
  if(!items.length){
    list.innerHTML = isDirector() ? `<div class="empty-state">Nothing posted yet — write something above.</div>` : `<div class="empty-state">No announcements right now.</div>`;
    return;
  }
  list.innerHTML = items.map(a=>{
    if(ui.editingAnnouncementId === a.id && isDirector()){
      return `
        <div class="announcement-item">
          <textarea class="full-width edit-announcement-text" style="min-height:60px;">${escapeHtml(a.text)}</textarea>
          <div class="cal-actions">
            <button class="btn small" data-save-announcement="${a.id}">Save</button>
            <button class="btn ghost small" data-discard-announcement="${a.id}">Discard</button>
          </div>
        </div>
      `;
    }
    return `
      <div class="announcement-item">
        <div class="announcement-top">
          <div class="announcement-text">${escapeHtml(a.text).replace(/\n/g,'<br>')}</div>
          ${isDirector()?`<span style="display:flex; gap:6px; flex-shrink:0;"><button class="btn ghost small" data-edit-announcement="${a.id}">Edit</button><button class="task-del" data-del-announcement="${a.id}">✕</button></span>`:''}
        </div>
        <div class="announcement-meta">${a.postedBy} · ${fmtDateTime(a.postedAt)}${a.editedAt?' · edited':''}</div>
      </div>
    `;
  }).join('');
  list.querySelectorAll('[data-del-announcement]').forEach(btn=>btn.addEventListener('click', async ()=>{
    if(!confirm('Delete this announcement?')) return;
    state.announcements = state.announcements.filter(a=>a.id!==btn.dataset.delAnnouncement);
    await saveState(); renderAnnouncements();
    toast('Announcement deleted');
  }));
  list.querySelectorAll('[data-edit-announcement]').forEach(btn=>btn.addEventListener('click', ()=>{
    ui.editingAnnouncementId = btn.dataset.editAnnouncement;
    renderAnnouncements();
  }));
  list.querySelectorAll('[data-discard-announcement]').forEach(btn=>btn.addEventListener('click', ()=>{
    ui.editingAnnouncementId = null;
    renderAnnouncements();
  }));
  list.querySelectorAll('[data-save-announcement]').forEach(btn=>btn.addEventListener('click', async ()=>{
    const a = state.announcements.find(x=>x.id===btn.dataset.saveAnnouncement);
    if(!a) return;
    const card = btn.closest('.announcement-item');
    const newText = card.querySelector('.edit-announcement-text').value.trim();
    if(!newText){ toast('Announcement can\'t be empty'); return; }
    a.text = newText;
    a.editedAt = new Date().toISOString();
    ui.editingAnnouncementId = null;
    await saveState(); renderAnnouncements();
    toast('Announcement updated');
  }));
}
async function postAnnouncement(){
  if(!isDirector()){ toast('Only the Director can post announcements'); return; }
  const text = document.getElementById('announcementText').value.trim();
  if(!text){ toast('Write something first'); return; }
  const postedBy = currentUser()?.name || authUser?.displayName || 'Director';
  state.announcements.push({ id:cryptoId(), text, postedBy, postedAt:new Date().toISOString() });
  await saveState();
  document.getElementById('announcementText').value = '';
  renderAnnouncements();
  toast('Announcement posted');
}

function renderDashboard(){
  renderAnnouncements();
  const tiles = document.getElementById('dashDeptTiles');
  tiles.innerHTML = '';
  DEPARTMENTS.forEach(dep=>{
    const pct = deptCompletionPct(dep.key);
    const tile = document.createElement('div');
    tile.className = 'dept-tile';
    tile.style.setProperty('--tile-color', dep.color);
    tile.innerHTML = `<div class="name stencil">${dep.label}</div><div class="bar"><div class="bar-fill" style="width:${pct}%"></div></div><div class="pct">${pct}% complete</div>`;
    tile.addEventListener('click', ()=>{ ui.activeDept = dep.key; switchView('departments'); });
    tiles.appendChild(tile);
  });

  const upcoming = document.getElementById('dashUpcoming');
  const future = [...state.calendar].filter(e=>e.date >= todayISO()).sort((a,b)=>a.date.localeCompare(b.date)).slice(0,5);
  upcoming.innerHTML = future.length ? future.map(e=>`<div class="list-item"><span>${escapeHtml(e.title)}${e.isSpecificCall?' <span class="tag" style="margin-left:4px;">specific call</span>':''}</span><span class="d">${fmtDate(e.date)} · ${e.startTime||'TBD'}</span></div>`).join('')
    : `<div class="empty-state">No upcoming calls scheduled.</div>`;

  const attn = document.getElementById('dashAttention');
  const pendingConflicts = state.conflicts.filter(c=>c.status==='pending').length;
  let reportsToGrade = 0;
  if(isDirector()){
    DEPARTMENTS.forEach(d=>{ reportsToGrade += state.departments[d.key].reports.filter(r=>r.status==='submitted'||r.status==='verified').length; });
  }
  const items = [];
  if(pendingConflicts) items.push(`<div class="list-item"><span>⚠ ${pendingConflicts} conflict(s) awaiting a decision</span><span class="d">Conflicts tab</span></div>`);
  if(reportsToGrade) items.push(`<div class="list-item"><span>📋 ${reportsToGrade} report(s) awaiting review/grade</span><span class="d">Departments tab</span></div>`);
  if(isDirector() && (globalState.pendingApprovals||[]).length) items.push(`<div class="list-item" style="color:var(--amber);"><span>🔔 ${globalState.pendingApprovals.length} account(s) awaiting approval</span><span class="d">Setup tab</span></div>`);
  if(isDirectorOrStageMgmt()){
    attendanceAlerts().forEach(a=>{
      items.push(`<div class="list-item" style="color:var(--red);"><span>🚩 ${escapeHtml(a.crew.name)} has ${a.count} unexcused absence${a.count!==1?'s':''}</span><span class="d">${a.crew.classPeriod||''}</span></div>`);
    });
  }
  attn.innerHTML = items.length ? items.join('') : `<div class="empty-state">Nothing needs attention right now.</div>`;
}

// ---------------- CALENDAR ----------------
function renderCalendarForm(){
  document.getElementById('calAddCard').style.display = isDirectorOrStageMgmt() ? 'block' : 'none';
  document.getElementById('groupmeAnnounceCard').style.display = isDirectorOrStageMgmt() ? 'block' : 'none';
  const wrap = document.getElementById('calDeptChecks');
  wrap.innerHTML = DEPARTMENTS.map(d=>`<label><input type="checkbox" value="${d.key}"> ${d.label}</label>`).join('');
  const specific = document.getElementById('calSpecific');
  specific.onchange = ()=>{ document.getElementById('calStudentPickWrap').style.display = specific.checked ? 'block' : 'none'; };
  const studentWrap = document.getElementById('calStudentChecks');
  studentWrap.innerHTML = state.crew.filter(c=>c.role==='student').map(c=>`<label><input type="checkbox" value="${c.id}"> ${escapeHtml(c.name)}</label>`).join('') || '<span style="font-size:12px;color:var(--paper-dim)">No students on roster yet.</span>';
}
function visibleToCurrentUser(ev){
  if(isDirector() || !ev.isSpecificCall) return true;
  const u = currentUser();
  if(!u) return true;
  if(ev.calledStudentIds.includes(u.id)) return true;
  return (u.departments||[]).some(d=>ev.calledDepartments.includes(d));
}

function switchCalSub(sub){
  ui.calSub = sub;
  document.querySelectorAll('#calSubtabs button').forEach(b=>b.classList.toggle('active', b.dataset.calSub===sub));
  document.getElementById('calMonthView').style.display = sub==='month' ? 'block' : 'none';
  document.getElementById('calAgendaView').style.display = sub==='agenda' ? 'block' : 'none';
  if(sub==='month') renderCalMonth(); else renderCalendar();
}

// Collects everything happening on a given date: rehearsal events + task due dates across all departments
function itemsForDate(dateISO){
  const events = state.calendar.filter(e=>e.date===dateISO && visibleToCurrentUser(e));
  const dueTasks = [];
  DEPARTMENTS.forEach(dep=>{
    state.departments[dep.key].tasks.forEach(t=>{
      if(t.dueDate===dateISO) dueTasks.push({ dept:dep, task:t });
    });
  });
  const approvedConflicts = state.conflicts.filter(c=>c.eventDate===dateISO && c.status==='approved');
  return { events, dueTasks, approvedConflicts };
}

// Renders the "Approved conflicts" block for a specific event, shown under its calendar card
function conflictsBlockForEvent(ev){
  const approved = state.conflicts.filter(c=>c.eventId===ev.id && c.status==='approved');
  if(!approved.length) return '';
  return `<div class="chip-row" style="margin-top:8px;">
    <span class="chip" style="background:var(--red);">⚠ ${approved.length} approved conflict${approved.length!==1?'s':''}</span>
    ${approved.map(c=>`<span class="chip outline">${c.crewName}${c.department?' ('+c.department+')':''}</span>`).join('')}
  </div>`;
}

function renderCalLegend(){
  const legend = document.getElementById('calLegend');
  legend.innerHTML = `<span><span class="sw" style="background:var(--amber)"></span>Rehearsal / build call</span>` +
    `<span><span class="sw" style="background:var(--red)"></span>Approved conflict</span>` +
    DEPARTMENTS.map(d=>`<span><span class="sw" style="background:${d.color}"></span>${d.label} task due</span>`).join('');
}

function renderCalMonth(){
  renderCalLegend();
  const cursor = ui.calMonthCursor;
  const year = cursor.getFullYear(), month = cursor.getMonth();
  document.getElementById('calMonthLabel').textContent = cursor.toLocaleDateString(undefined,{month:'long', year:'numeric'});

  const grid = document.getElementById('calGrid');
  grid.innerHTML = '';
  ['Sun','Mon','Tue','Wed','Thu','Fri','Sat'].forEach(d=>{
    const el = document.createElement('div'); el.className='cal-dow'; el.textContent = d; grid.appendChild(el);
  });

  const firstOfMonth = new Date(year, month, 1);
  const startOffset = firstOfMonth.getDay();
  const gridStart = new Date(year, month, 1 - startOffset);
  const today = todayISO();

  for(let i=0;i<42;i++){
    const cellDate = new Date(gridStart.getFullYear(), gridStart.getMonth(), gridStart.getDate()+i);
    const cellISO = cellDate.toISOString().slice(0,10);
    const inMonth = cellDate.getMonth()===month;
    const { events, dueTasks, approvedConflicts } = itemsForDate(cellISO);

    const cell = document.createElement('div');
    cell.className = 'cal-day' + (inMonth?'':' other-month') + (cellISO===today?' is-today':'') + (cellISO===ui.calSelectedDate?' is-selected':'');
    const dots = events.map(()=>`<span class="cal-day-dot" style="background:var(--amber)"></span>`).join('') +
      (approvedConflicts.length ? `<span class="cal-day-dot" style="background:var(--red)"></span>` : '') +
      dueTasks.slice(0, Math.max(0,5-events.length)).map(dt=>`<span class="cal-day-dot" style="background:${dt.dept.color}"></span>`).join('');
    const totalItems = events.length + dueTasks.length + (approvedConflicts.length?1:0);
    const shown = Math.min(6, totalItems);
    cell.innerHTML = `<span class="daynum">${cellDate.getDate()}</span><div class="cal-day-dots">${dots}</div>${totalItems>shown?`<span class="cal-day-more">+${totalItems-shown} more</span>`:''}`;
    cell.addEventListener('click', ()=>{ ui.calSelectedDate = cellISO; renderCalMonth(); renderCalDayDetail(cellISO); });
    grid.appendChild(cell);
  }
  renderCalDayDetail(ui.calSelectedDate);
}

function escapeAttr(s){ return String(s||'').replace(/&/g,'&amp;').replace(/"/g,'&quot;'); }
function escapeHtml(s){ return String(s||'').replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;').replace(/'/g,'&#39;'); }

// ---------------- PRINTING ----------------
// Opens a clean, white-background, ink-friendly version in a new tab rather than trying to
// print the app's dark interactive screen directly — checklists get expanded, buttons/toolbars
// disappear, and it reads like a real handout instead of a screenshot.
function openPrintWindow(title, bodyHtml){
  const win = window.open('', '_blank');
  if(!win){ toast('Your browser blocked the print window — allow pop-ups for this site and try again'); return; }
  win.document.write(`<!DOCTYPE html><html><head><meta charset="UTF-8"><title>${escapeHtml(title)}</title><style>
    body{ font-family: Arial, Helvetica, sans-serif; color:#111; padding:24px; max-width:800px; margin:0 auto; }
    h1{ font-size:20px; margin:0 0 2px; }
    .meta{ font-size:11px; color:#555; margin-bottom:18px; border-bottom:2px solid #333; padding-bottom:10px; }
    table{ width:100%; border-collapse:collapse; margin-bottom:14px; font-size:12px; }
    th, td{ text-align:left; padding:6px 8px; border-bottom:1px solid #ddd; vertical-align:top; }
    th{ background:#f0f0f0; }
    ul.checklist{ list-style:none; padding-left:2px; margin:5px 0 0; }
    ul.checklist li{ font-size:11.5px; padding:2px 0; }
    ul.checklist li.done::before{ content:"\\2611  "; }
    ul.checklist li:not(.done)::before{ content:"\\2610  "; }
    .note-item{ margin-bottom:10px; padding-bottom:10px; border-bottom:1px solid #eee; font-size:12.5px; }
    .note-meta{ font-size:10.5px; color:#777; }
    @media print{ body{ padding:0; } }
  </style></head><body>${bodyHtml}</body></html>`);
  win.document.close();
  win.focus();
  setTimeout(()=>{ win.print(); }, 300);
}
function printCalendar(){
  const events = [...state.calendar].filter(visibleToCurrentUser).sort((a,b)=>a.date.localeCompare(b.date));
  const rows = events.map(e=>{
    const who = e.isSpecificCall
      ? escapeHtml([...(e.calledDepartments||[]).map(k=>deptInfo(k).label), ...(e.calledStudentIds||[]).map(id=>{ const c=state.crew.find(x=>x.id===id); return c?c.name:''; })].filter(Boolean).join(', ') || 'Specific call')
      : 'Everyone';
    return `<tr><td>${fmtDate(e.date)}</td><td>${e.startTime||''}${e.endTime?'–'+e.endTime:''}</td><td>${escapeHtml(e.title)}</td><td>${escapeHtml(e.location||'')}</td><td>${who}</td><td>${escapeHtml(e.notes||'')}</td></tr>`;
  }).join('');
  const body = `<h1>${escapeHtml(state.productionName)}</h1><div class="meta">Rehearsal / Build Calendar — Printed ${new Date().toLocaleDateString()}</div>
    <table><thead><tr><th>Date</th><th>Time</th><th>Call</th><th>Location</th><th>Who's Called</th><th>Notes</th></tr></thead>
    <tbody>${rows || '<tr><td colspan="6">No calls scheduled.</td></tr>'}</tbody></table>`;
  openPrintWindow(`${state.productionName} — Calendar`, body);
}
function printTaskList(deptKey){
  const dep = deptInfo(deptKey);
  const depState = state.departments[deptKey];
  const rows = depState.tasks.map(t=>{
    const checklistHtml = (t.checklist && t.checklist.length) ? `<ul class="checklist">${t.checklist.map(i=>`<li class="${i.done?'done':''}">${escapeHtml(i.text)}</li>`).join('')}</ul>` : '';
    return `<tr><td style="font-size:14px;">${t.status==='done'?'\u2611':'\u2610'}</td><td>
      <b>${escapeHtml(t.title)}</b><br>
      <span style="font-size:10.5px;color:#666;">${escapeHtml(t.workType)} \u00b7 ${escapeHtml(t.priority)} priority \u00b7 Due ${t.dueDate} \u00b7 ${t.estimatedHours}h${t.assignedTo?' \u00b7 Assigned to '+escapeHtml(t.assignedTo):''}</span>
      ${t.description?`<div style="font-size:11px; margin-top:4px;">${escapeHtml(t.description)}</div>`:''}
      ${checklistHtml}
    </td></tr>`;
  }).join('');
  const body = `<h1>${escapeHtml(state.productionName)}</h1><div class="meta">${escapeHtml(dep.label)} Task List — Printed ${new Date().toLocaleDateString()}</div>
    <table><thead><tr><th style="width:26px;"></th><th>Task</th></tr></thead>
    <tbody>${rows || '<tr><td colspan="2">No tasks yet.</td></tr>'}</tbody></table>`;
  openPrintWindow(`${dep.label} Task List`, body);
}
function printTeamNotes(deptKey){
  const dep = deptInfo(deptKey);
  const notes = workspaceCache.teamNotes || [];
  const rows = notes.map(n=>`<div class="note-item"><div><b>${escapeHtml(n.authorName)}</b>${n.isStaff?' (Staff)':''}</div><div>${escapeHtml(n.text).replace(/\n/g,'<br>')}</div><div class="note-meta">${fmtDateTime(n.timestamp)}</div></div>`).join('');
  const body = `<h1>${escapeHtml(state.productionName)}</h1><div class="meta">${escapeHtml(dep.label)} \u2014 Team Notes \u2014 Printed ${new Date().toLocaleDateString()}</div>
    ${rows || '<p>No notes yet.</p>'}`;
  openPrintWindow(`${dep.label} Team Notes`, body);
}

// Builds one calendar event card as a DOM element — either its normal display,
// or (if the Director has clicked Edit on it) a full inline edit form.
function buildEventCardEl(ev){
  const card = document.createElement('div');
  card.className = 'cal-event';
  card.style.marginBottom = '8px';

  if(ui.editingEventId === ev.id && isDirectorOrStageMgmt()){
    card.innerHTML = `
      <div class="form-grid">
        <input type="text" class="edit-title" value="${escapeAttr(ev.title)}" placeholder="Title">
        <input type="date" class="edit-date" value="${ev.date}">
      </div>
      <div class="form-grid">
        <input type="time" class="edit-start" value="${ev.startTime||''}" placeholder="Start">
        <input type="time" class="edit-end" value="${ev.endTime||''}" placeholder="End">
        <input type="text" class="edit-location" value="${escapeAttr(ev.location||'')}" placeholder="Location">
      </div>
      <label style="display:flex; align-items:center; gap:8px; font-size:12.5px; margin-bottom:10px;">
        <input type="checkbox" class="edit-specific" ${ev.isSpecificCall?'checked':''}> Specific call (only selected departments/students see it)
      </label>
      <div class="checkbox-row edit-dept-checks" style="margin-bottom:10px;">
        ${DEPARTMENTS.map(d=>`<label><input type="checkbox" value="${d.key}" ${(ev.calledDepartments||[]).includes(d.key)?'checked':''}> ${d.label}</label>`).join('')}
      </div>
      <div class="edit-student-wrap" style="display:${ev.isSpecificCall?'block':'none'}; margin-bottom:10px;">
        <div class="checkbox-row edit-student-checks">
          ${state.crew.filter(c=>c.role==='student').map(c=>`<label><input type="checkbox" value="${c.id}" ${(ev.calledStudentIds||[]).includes(c.id)?'checked':''}> ${escapeHtml(c.name)}</label>`).join('') || '<span style="font-size:12px;color:var(--paper-dim)">No students on roster yet.</span>'}
        </div>
      </div>
      <textarea class="edit-notes full-width" style="min-height:50px;" placeholder="Notes">${ev.notes||''}</textarea>
      <div class="cal-actions">
        <button class="btn small edit-save-btn">Save Changes</button>
        <button class="btn ghost small edit-discard-btn">Discard</button>
      </div>
    `;
    const specCb = card.querySelector('.edit-specific');
    const studentWrap = card.querySelector('.edit-student-wrap');
    specCb.addEventListener('change', ()=>{ studentWrap.style.display = specCb.checked ? 'block' : 'none'; });
    card.querySelector('.edit-discard-btn').addEventListener('click', ()=>{
      ui.editingEventId = null; renderCalMonth(); renderCalendar();
    });
    card.querySelector('.edit-save-btn').addEventListener('click', async ()=>{
      const newTitle = card.querySelector('.edit-title').value.trim();
      if(!newTitle){ toast('Title is required'); return; }
      const before = { title:ev.title, date:ev.date, startTime:ev.startTime };
      ev.title = newTitle;
      ev.date = card.querySelector('.edit-date').value || ev.date;
      ev.startTime = card.querySelector('.edit-start').value;
      ev.endTime = card.querySelector('.edit-end').value;
      ev.location = card.querySelector('.edit-location').value.trim();
      ev.notes = card.querySelector('.edit-notes').value.trim();
      ev.isSpecificCall = specCb.checked;
      ev.calledDepartments = Array.from(card.querySelectorAll('.edit-dept-checks input:checked')).map(i=>i.value);
      ev.calledStudentIds = Array.from(card.querySelectorAll('.edit-student-checks input:checked')).map(i=>i.value);
      const moved = before.date!==ev.date || before.startTime!==ev.startTime;
      if(moved) ev.reminderSent = false;
      logChange(`Edited: "${escapeHtml(before.title)}"${moved ? ` — moved from ${fmtDate(before.date)} ${before.startTime||''} to ${fmtDate(ev.date)} ${ev.startTime||''}` : ` (${fmtDate(ev.date)})`}.`, eventScope(ev));
      ui.editingEventId = null;
      await saveState(); renderCalMonth(); renderCalendar(); renderNotifications(); renderDashboard();
      if(moved) sendGroupMe(`🔄 "${escapeHtml(ev.title)}" has been rescheduled to ${fmtDate(ev.date)}${ev.startTime?' at '+ev.startTime:''}${ev.location?' — '+ev.location:''}${groupMeCallInfo(ev)}.`);
      toast('Call updated — crew notified');
    });
    return card;
  }

  card.innerHTML = `
    <div class="cal-event-top">
      <div><div class="cal-title">${escapeHtml(ev.title)}</div><div class="cal-meta">${fmtDate(ev.date)} · ${ev.startTime||'TBD'}${ev.endTime?'–'+ev.endTime:''} ${ev.location?'· '+ev.location:''}</div></div>
      <span class="cal-date-chip">${ev.date}</span>
    </div>
    ${ev.isSpecificCall ? `<div class="chip-row">
      <span class="chip outline">Specific call</span>
      ${(ev.calledDepartments||[]).map(k=>`<span class="chip" style="background:${deptInfo(k).color}">${deptInfo(k).label}</span>`).join('')}
      ${(ev.calledStudentIds||[]).map(id=>{ const c=state.crew.find(x=>x.id===id); return c?`<span class="chip outline">${escapeHtml(c.name)}</span>`:''; }).join('')}
    </div>` : ''}
    ${ev.notes?`<div class="cal-notes">${escapeHtml(ev.notes)}</div>`:''}
    ${conflictsBlockForEvent(ev)}
    ${attendanceChipForEvent(ev)}
    ${isDirectorOrStageMgmt() ? `<div class="cal-actions"><button class="btn ghost small edit-btn">Edit</button><button class="btn danger small del-btn">Cancel</button></div>` : ''}
  `;
  if(isDirectorOrStageMgmt()){
    card.querySelector('.edit-btn').addEventListener('click', ()=>{
      ui.editingEventId = ev.id; renderCalMonth(); renderCalendar();
    });
    card.querySelector('.del-btn').addEventListener('click', async ()=>{
      if(!confirm(`Cancel "${escapeHtml(ev.title)}" on ${fmtDate(ev.date)}?`)) return;
      state.calendar = state.calendar.filter(e=>e.id!==ev.id);
      logChange(`Cancelled: "${escapeHtml(ev.title)}" originally set for ${fmtDate(ev.date)}.`, eventScope(ev));
      await saveState(); renderCalMonth(); renderCalendar(); renderNotifications(); renderDashboard();
      sendGroupMe(`❌ "${escapeHtml(ev.title)}" originally set for ${fmtDate(ev.date)} has been cancelled${groupMeCallInfo(ev)}.`);
      toast('Call cancelled — crew notified');
    });
  }
  return card;
}

function renderCalDayDetail(dateISO){
  document.getElementById('calDayDetailTitle').textContent = fmtDate(dateISO);
  const { events, dueTasks } = itemsForDate(dateISO);
  const wrap = document.getElementById('calDayDetail');
  wrap.innerHTML = '';
  if(!events.length && !dueTasks.length){ wrap.innerHTML = `<div class="empty-state">Nothing scheduled or due this day.</div>`; return; }
  events.forEach(ev=>wrap.appendChild(buildEventCardEl(ev)));
  if(dueTasks.length){
    const due = document.createElement('div');
    due.style.marginTop = '6px';
    due.innerHTML = `<h3>Tasks due</h3>` + dueTasks.map(dt=>`
      <div class="list-item"><span><span class="chip" style="background:${dt.dept.color}; margin-right:6px;">${dt.dept.label}</span>${escapeHtml(dt.task.title)}</span><span class="d">${dt.task.status.replace('_',' ')}</span></div>
    `).join('');
    wrap.appendChild(due);
  }
}

function renderCalendar(){
  const list = document.getElementById('calList');
  list.innerHTML = '';
  if(!isDirectorOrStageMgmt()){
    const lock = document.createElement('div'); lock.className = 'lockmsg';
    lock.textContent = 'Viewing as Team — only the Director or Stage Management can add or edit calendar entries.';
    list.appendChild(lock);
  }
  const sorted = [...state.calendar].filter(visibleToCurrentUser).sort((a,b)=>a.date.localeCompare(b.date));
  if(!sorted.length){ list.innerHTML += `<div class="empty-state"><div class="lamp">🗓️</div>No calls scheduled yet.</div>`; return; }
  sorted.forEach(ev=>list.appendChild(buildEventCardEl(ev)));
}
async function addCalendarEvent(){
  if(!isDirectorOrStageMgmt()){ toast('Only the Director or Stage Management can add calendar entries'); return; }
  const date = document.getElementById('calDate').value, title = document.getElementById('calTitle').value.trim();
  if(!date || !title){ toast('Add a date and title first'); return; }
  const startTime = document.getElementById('calStart').value, endTime = document.getElementById('calEnd').value;
  const location = document.getElementById('calLocation').value.trim(), notes = document.getElementById('calNotes').value.trim();
  const isSpecificCall = document.getElementById('calSpecific').checked;
  const calledDepartments = Array.from(document.querySelectorAll('#calDeptChecks input:checked')).map(i=>i.value);
  const calledStudentIds = Array.from(document.querySelectorAll('#calStudentChecks input:checked')).map(i=>i.value);
  state.calendar.push({ id:cryptoId(), title, date, startTime, endTime, location, notes, isSpecificCall, calledDepartments, calledStudentIds, reminderSent:false });
  logChange(`New call added: "${title}" on ${fmtDate(date)}${startTime?' at '+startTime:''}.`, eventScope({isSpecificCall, calledDepartments, calledStudentIds}));
  await saveState();
  ['calTitle','calLocation','calNotes','calStart','calEnd'].forEach(id=>document.getElementById(id).value='');
  document.getElementById('calSpecific').checked=false;
  document.querySelectorAll('#calDeptChecks input, #calStudentChecks input').forEach(i=>i.checked=false);
  renderCalendarForm(); renderCalendar(); renderNotifications(); renderDashboard();
  if(ui.calSub==='month') renderCalMonth();
  sendGroupMe(`🗓️ New call added: "${title}" on ${fmtDate(date)}${startTime?' at '+startTime:''}${location?' — '+location:''}${groupMeCallInfo({isSpecificCall, calledDepartments, calledStudentIds})}.`);
  toast('Added to calendar');
}
async function bulkImportCalendar(){
  if(!isDirectorOrStageMgmt()){ toast('Only the Director or Stage Management can import calendar entries'); return; }
  const raw = document.getElementById('calBulkImportText').value;
  const lines = raw.split('\n').map(l=>l.trim()).filter(Boolean);
  if(!lines.length){ toast('Paste at least one line first'); return; }
  let count = 0, errors = [];
  lines.forEach((line, idx)=>{
    const parts = line.split('|').map(p=>p.trim());
    const [date, startTime='', endTime='', title='', location='', deptsStr='', notes=''] = parts;
    if(!/^\d{4}-\d{2}-\d{2}$/.test(date||'')){ errors.push(`Line ${idx+1}: bad date "${date||''}" (need YYYY-MM-DD)`); return; }
    if(!title){ errors.push(`Line ${idx+1}: missing title`); return; }
    const calledDepartments = deptsStr ? deptsStr.split(',').map(s=>s.trim().toLowerCase()).map(name=>{
      const match = DEPARTMENTS.find(d=>d.label.toLowerCase()===name || d.key===name);
      return match ? match.key : null;
    }).filter(Boolean) : [];
    const isSpecificCall = calledDepartments.length > 0;
    state.calendar.push({ id:cryptoId(), title, date, startTime, endTime, location, notes, isSpecificCall, calledDepartments, calledStudentIds:[], reminderSent:false });
    count++;
  });
  if(count){
    logChange(`Bulk imported ${count} calendar call(s).`, {type:'all'});
    await saveState();
    document.getElementById('calBulkImportText').value = '';
    renderCalendarForm(); renderCalendar(); renderNotifications(); renderDashboard();
    if(ui.calSub==='month') renderCalMonth();
  }
  if(errors.length){ toast(`Imported ${count}, skipped ${errors.length} — check console for details`); console.warn('Bulk calendar import issues:\n'+errors.join('\n')); }
  else toast(`Imported ${count} call(s)`);
}

// ---------------- CONFLICTS ----------------
function renderConflictForm(){
  const hasCrew = state.crew.length > 0;
  const future = [...state.calendar].filter(e=>e.date>=todayISO()).sort((a,b)=>a.date.localeCompare(b.date));
  const hasEvents = future.length > 0;
  const director = isDirector();
  const cu = currentUser();

  document.getElementById('conflictNoCrewMsg').style.display = hasCrew ? 'none' : 'block';
  document.getElementById('conflictNoEventMsg').style.display = (hasCrew && !hasEvents) ? 'block' : 'none';
  document.getElementById('conflictNotSignedInMsg').style.display = (hasCrew && hasEvents && !director && !activeEmail()) ? 'block' : 'none';
  document.getElementById('conflictNotOnRosterMsg').style.display = (hasCrew && hasEvents && !director && activeEmail() && !cu) ? 'block' : 'none';
  const canShowForm = hasCrew && hasEvents && (director || cu);
  document.getElementById('conflictFormWrap').style.display = canShowForm ? 'block' : 'none';
  if(!canShowForm) return;

  const whoSel = document.getElementById('conflictWho');
  if(director){
    whoSel.disabled = false;
    whoSel.innerHTML = state.crew.map(c=>`<option value="${c.id}">${escapeHtml(c.name)}</option>`).join('');
    if(cu) whoSel.value = cu.id;
  } else {
    whoSel.disabled = true;
    whoSel.innerHTML = `<option value="${cu.id}">${escapeHtml(cu.name)} (you)</option>`;
  }

  const sel = document.getElementById('conflictEvent');
  sel.innerHTML = future.map(e=>`<option value="${e.id}">${escapeHtml(e.title)} — ${fmtDate(e.date)}</option>`).join('');
}
function renderConflicts(){
  renderConflictForm();
  const list = document.getElementById('conflictList'); list.innerHTML = '';
  if(!state.conflicts.length){ list.innerHTML = `<div class="empty-state">No conflicts submitted yet.</div>`; return; }
  const byEvent = {};
  state.conflicts.forEach(c=>{ (byEvent[c.eventTitle+'|'+c.eventDate] = byEvent[c.eventTitle+'|'+c.eventDate]||[]).push(c); });
  Object.keys(byEvent).sort((a,b)=>a.split('|')[1].localeCompare(b.split('|')[1])).forEach(key=>{
    const [title, date] = key.split('|');
    const h = document.createElement('div'); h.className='conflict-group-title'; h.textContent = `${title} — ${fmtDate(date)}`;
    list.appendChild(h);
    byEvent[key].forEach(c=>{
      const card = document.createElement('div'); card.className = 'conflict-card';
      card.innerHTML = `
        <div class="conflict-top"><span>${c.crewName} <span style="color:var(--paper-dim); font-weight:400;">(${c.department||'Unassigned'})</span></span><span class="status-chip ${c.status}">${c.status}</span></div>
        <div class="conflict-meta">Submitted ${fmtDateTime(c.submittedAt)}</div>
        <div class="conflict-reason">${escapeHtml(c.reason)}${c.notes?' — '+escapeHtml(c.notes):''}</div>
        ${isDirector() && c.status==='pending' ? `<div class="cal-actions"><button class="btn small" data-act="approve" data-id="${c.id}">Approve</button><button class="btn danger small" data-act="deny" data-id="${c.id}">Deny</button></div>` : ''}
      `;
      list.appendChild(card);
    });
  });
  list.querySelectorAll('[data-act=approve]').forEach(b=>b.addEventListener('click', async ()=>{ await setConflictStatus(b.dataset.id,'approved'); }));
  list.querySelectorAll('[data-act=deny]').forEach(b=>b.addEventListener('click', async ()=>{ await setConflictStatus(b.dataset.id,'denied'); }));
}
async function setConflictStatus(id, status){
  const c = state.conflicts.find(x=>x.id===id); if(!c) return;
  c.status = status;
  if(status==='approved') logChange(`Conflict approved: ${c.crewName} — ${c.eventTitle} (${fmtDate(c.eventDate)}).`, {type:'crew', crewId:c.crewId});
  await saveState(); renderConflicts(); renderNotifications(); renderDashboard();
  if(ui.calSub==='month') renderCalMonth(); else renderCalendar();
  toast(`Conflict ${status}`);
}
// ---------------- ATTENDANCE ----------------
// ---------------- ATTENDANCE ----------------
function switchAttendanceSub(sub){
  ui.attendanceSub = sub;
  document.querySelectorAll('#attendanceSubtabs button').forEach(b=>b.classList.toggle('active', b.dataset.attSub===sub));
  document.getElementById('attendanceMarkView').style.display = sub==='mark' ? 'block' : 'none';
  document.getElementById('attendanceGridView').style.display = sub==='grid' ? 'block' : 'none';
  if(sub==='mark') renderAttendanceMarkView(); else renderAttendanceGrid();
}
function renderAttendanceView(){
  const authorized = canTakeAttendance();
  document.getElementById('attendanceLockedMsg').style.display = authorized ? 'none' : 'block';
  document.getElementById('attendanceAuthorizedWrap').style.display = authorized ? 'block' : 'none';
  if(!authorized) return;
  switchAttendanceSub(ui.attendanceSub || 'mark');
}
function renderAttendanceMarkView(){
  const list = document.getElementById('attendanceList');
  list.innerHTML = '';
  const sorted = [...state.calendar].sort((a,b)=>b.date.localeCompare(a.date));
  if(!sorted.length){ list.innerHTML = `<div class="empty-state"><div class="lamp">🗓️</div>No rehearsal calls on the calendar yet.</div>`; return; }
  sorted.forEach(ev=>{
    const attendees = expectedAttendees(ev).sort((a,b)=>a.name.localeCompare(b.name));
    const counts = {present:0, absent:0, excused:0, unmarked:0};
    attendees.forEach(c=>{ counts[attendanceStatus(ev,c.id) || 'unmarked']++; });
    const card = document.createElement('div'); card.className = 'card';
    card.innerHTML = `
      <div style="display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:8px;">
        <div><h3 style="margin:0;">${escapeHtml(ev.title)}</h3><div class="cal-meta">${fmtDate(ev.date)}${ev.startTime?' · '+ev.startTime:''}</div></div>
        <span class="mono" style="font-size:12px; color:var(--paper-dim);">✓${counts.present} · ✕${counts.absent} · ⊘${counts.excused}${counts.unmarked?' · '+counts.unmarked+' unmarked':''}</span>
      </div>
      <div id="att-rows-${ev.id}" style="margin-top:12px;"></div>
    `;
    list.appendChild(card);
    const rowsWrap = card.querySelector(`#att-rows-${ev.id}`);
    if(!attendees.length){ rowsWrap.innerHTML = `<div class="empty-state">No students expected for this call — assign departments/students to it in Calendar, or add crew in Setup.</div>`; return; }
    attendees.forEach(c=>{
      const status = attendanceStatus(ev, c.id);
      const row = document.createElement('div'); row.className = 'list-item';
      row.innerHTML = `
        <span>${escapeHtml(c.name)}</span>
        <span style="display:flex; gap:5px;">
          <button class="btn small" style="border:1px solid var(--sage); background:${status==='present'?'var(--sage)':'transparent'}; color:${status==='present'?'var(--ink)':'var(--paper)'};" data-att="present" data-event="${ev.id}" data-crew="${c.id}">Present</button>
          <button class="btn small" style="border:1px solid var(--red); background:${status==='absent'?'var(--red)':'transparent'}; color:${status==='absent'?'#fff':'var(--paper)'};" data-att="absent" data-event="${ev.id}" data-crew="${c.id}">Absent</button>
          <button class="btn small" style="border:1px solid var(--amber); background:${status==='excused'?'var(--amber)':'transparent'}; color:${status==='excused'?'var(--ink)':'var(--paper)'};" data-att="excused" data-event="${ev.id}" data-crew="${c.id}">Excused</button>
        </span>
      `;
      rowsWrap.appendChild(row);
    });
  });
  list.querySelectorAll('[data-att]').forEach(btn=>btn.addEventListener('click', async ()=>{
    const ev = state.calendar.find(e=>e.id===btn.dataset.event); if(!ev) return;
    await setAttendance(ev, btn.dataset.crew, btn.dataset.att);
    renderAttendanceMarkView(); renderDashboard(); renderNotifications();
    if(ui.calSub==='month') renderCalMonth(); else renderCalendar();
  }));
}

function renderAttendanceGrid(){
  const gridLocked = document.getElementById('attendanceGridLockedMsg');
  const gridWrap = document.getElementById('attendanceGridWrap');
  if(!isDirector()){
    gridLocked.style.display = 'block';
    gridWrap.innerHTML = '';
    return;
  }
  gridLocked.style.display = 'none';

  const events = [...state.calendar].sort((a,b)=>a.date.localeCompare(b.date));
  const crewList = [...state.crew].filter(c=>c.role==='student').sort((a,b)=>{
    const da = (a.departments||[])[0] || '', db = (b.departments||[])[0] || '';
    return da.localeCompare(db) || a.name.localeCompare(b.name);
  });

  if(!crewList.length){ gridWrap.innerHTML = `<div class="empty-state">No students on the roster yet — add crew in Setup.</div>`; return; }
  if(!events.length){ gridWrap.innerHTML = `<div class="empty-state">No rehearsal calls on the calendar yet.</div>`; return; }

  const cellMark = { present:['P','att-cell-p'], absent:['A','att-cell-a'], excused:['E','att-cell-e'] };

  let html = `
    <div class="att-legend">
      <span><b class="att-cell-p">P</b> Present</span>
      <span><b class="att-cell-a">A</b> Absent</span>
      <span><b class="att-cell-e">E</b> Excused</span>
      <span><b class="att-cell-blank">·</b> Not called for this rehearsal</span>
    </div>
    <div class="att-grid-scroll">
    <table class="att-table">
      <thead><tr>
        <th class="att-name-th">Cast / Crew</th>
        ${events.map(ev=>`<th title="${escapeHtml(ev.title)}">${new Date(ev.date+'T00:00').toLocaleDateString(undefined,{month:'numeric',day:'numeric'})}</th>`).join('')}
        <th class="att-total-th">Absences</th>
      </tr></thead>
      <tbody>
        ${crewList.map(c=>{
          const cells = events.map(ev=>{
            const expected = expectedAttendees(ev).some(x=>x.id===c.id);
            if(!expected) return `<td class="att-cell-blank">·</td>`;
            const status = attendanceStatus(ev, c.id);
            if(!status) return `<td class="att-cell-blank">—</td>`;
            const [label, cls] = cellMark[status];
            return `<td class="${cls}">${label}</td>`;
          }).join('');
          return `<tr><td class="att-name-cell">${escapeHtml(c.name)}<br><span style="font-size:10px;color:var(--paper-dim);">${(c.departments||[]).map(d=>deptInfo(d).label).join(', ')||'No team'} · ${c.classPeriod||''}</span></td>${cells}<td class="att-total-cell" style="color:${absentCount(c.id)>=(globalState.attendanceAlertThreshold||3)?'var(--red)':'var(--paper)'}">${absentCount(c.id)}</td></tr>`;
        }).join('')}
      </tbody>
    </table>
    </div>
  `;
  gridWrap.innerHTML = html;
}

// ---------------- BEHAVIOR TRACKING ----------------
function weekStartISO(dateStr){
  const d = new Date(dateStr+'T00:00:00');
  const day = d.getDay(); // 0=Sun..6=Sat
  const diff = (day===0 ? -6 : 1-day); // shift back to Monday
  d.setDate(d.getDate()+diff);
  return d.toISOString().slice(0,10);
}
function weekEndISO(weekStart){
  const d = new Date(weekStart+'T00:00:00');
  d.setDate(d.getDate()+6);
  return d.toISOString().slice(0,10);
}
function fmtWeekLabel(weekStart){
  const end = weekEndISO(weekStart);
  return `Week of ${fmtDate(weekStart)} – ${new Date(end+'T00:00').toLocaleDateString(undefined,{month:'short', day:'numeric'})}`;
}
// Weekly score for one student: full marks minus, for each day that had at least
// one infraction, that day's deductions (capped so one bad day can't cost more than
// its own share of the week).
function behaviorScoreForStudent(crewId, weekStart){
  const cfg = state.behaviorConfig || { pointsPerDay:20, daysPerWeek:5 };
  const weekEnd = weekEndISO(weekStart);
  const incidents = state.behaviorIncidents.filter(i=>i.crewId===crewId && i.date>=weekStart && i.date<=weekEnd);
  const byDate = {};
  incidents.forEach(i=>{ byDate[i.date] = (byDate[i.date]||0) + (i.deduction||cfg.pointsPerDay); });
  let totalDeduction = 0;
  Object.values(byDate).forEach(dayTotal=>{ totalDeduction += Math.min(dayTotal, cfg.pointsPerDay); });
  const maxScore = cfg.pointsPerDay * cfg.daysPerWeek;
  return { score: Math.max(0, maxScore - totalDeduction), maxScore, incidentCount: incidents.length, incidents };
}

// Participation grade for one student, in one department, for one week — same math as
// behavior: everyone starts at full marks (pointsPerDay × daysPerWeek); a day only gets
// docked if that student was actually flagged as a non-contributor on a report that date.
// Days with no report at all simply aren't touched, same as days with no logged infraction.
function participationScoreForStudent(crewId, deptKey, weekStart){
  const cfg = state.participationConfig || { pointsPerDay:20, daysPerWeek:5 };
  const weekEnd = weekEndISO(weekStart);
  const depState = state.departments[deptKey];
  const reportsThisWeek = (depState?depState.reports:[]).filter(r=>
    r.date>=weekStart && r.date<=weekEnd && (r.teamMemberIds||[]).includes(crewId));
  const dockedDates = [];
  reportsThisWeek.forEach(r=>{ if((r.nonContributorIds||[]).includes(crewId) && !dockedDates.includes(r.date)) dockedDates.push(r.date); });
  const maxScore = cfg.pointsPerDay * cfg.daysPerWeek;
  const deduction = Math.min(dockedDates.length * cfg.pointsPerDay, maxScore);
  return { score: Math.max(0, maxScore - deduction), maxScore, dockedDates, reportsCount: reportsThisWeek.length };
}
// A student's overall weekly participation across every department they're actually on,
// averaged to a 0-100 percentage. A department with zero reports still counts at full
// credit (same "no flag = full marks" rule as the per-department score) — this is what
// makes it safe to skip reports on a light week without students' averages coming up blank.
function combinedParticipationForStudent(crewId, weekStart){
  const crewMember = state.crew.find(c=>c.id===crewId);
  const memberDepts = crewMember ? (crewMember.departments||[]) : [];
  let earned = 0, max = 0;
  memberDepts.forEach(deptKey=>{
    if(!DEPARTMENTS.some(d=>d.key===deptKey)) return;
    const r = participationScoreForStudent(crewId, deptKey, weekStart);
    earned += r.score; max += r.maxScore;
  });
  return { earned, max, pct: max ? Math.round(earned/max*100) : null };
}

function switchBehaviorSub(sub){
  if(sub==='summary' && !isDirector()){ sub = 'log'; } // Weekly Summary is grades/behavior viewing — Director only
  ui.behaviorSub = sub;
  document.querySelectorAll('#behaviorSubtabs button').forEach(b=>b.classList.toggle('active', b.dataset.behSub===sub));
  document.getElementById('behSummaryTabBtn').style.display = isDirector() ? 'inline-block' : 'none';
  document.getElementById('behaviorLogView').style.display = sub==='log' ? 'block' : 'none';
  document.getElementById('behaviorSummaryView').style.display = sub==='summary' ? 'block' : 'none';
  if(sub==='log') renderBehaviorLog(); else renderBehaviorSummary();
}
function renderBehaviorView(){
  const authorized = canManageBehavior();
  document.getElementById('behaviorAuthorizedWrap').style.display = authorized ? 'block' : 'none';
  if(authorized){
    document.getElementById('behaviorNotSignedInMsg').style.display = 'none';
    document.getElementById('behaviorNotOnRosterMsg').style.display = 'none';
    document.getElementById('behaviorMyViewWrap').style.display = 'none';
    if(!ui.behaviorWeekCursor) ui.behaviorWeekCursor = weekStartISO(todayISO());
    switchBehaviorSub(ui.behaviorSub || 'log');
    return;
  }
  const u = currentUser();
  if(!activeEmail()){
    document.getElementById('behaviorNotSignedInMsg').style.display = 'block';
    document.getElementById('behaviorNotOnRosterMsg').style.display = 'none';
    document.getElementById('behaviorMyViewWrap').style.display = 'none';
    return;
  }
  if(!u){
    document.getElementById('behaviorNotSignedInMsg').style.display = 'none';
    document.getElementById('behaviorNotOnRosterMsg').style.display = 'block';
    document.getElementById('behaviorMyViewWrap').style.display = 'none';
    return;
  }
  document.getElementById('behaviorNotSignedInMsg').style.display = 'none';
  document.getElementById('behaviorNotOnRosterMsg').style.display = 'none';
  document.getElementById('behaviorMyViewWrap').style.display = 'block';
  if(!ui.myBehaviorWeekCursor) ui.myBehaviorWeekCursor = weekStartISO(todayISO());
  renderMyBehaviorView();
}

function renderMyBehaviorView(){
  const u = currentUser();
  if(!u) return;
  document.getElementById('myBehWeekLabel').textContent = fmtWeekLabel(ui.myBehaviorWeekCursor);
  const r = behaviorScoreForStudent(u.id, ui.myBehaviorWeekCursor);
  const pct = r.maxScore ? r.score / r.maxScore : 1;
  const cls = pct>=0.9 ? 'behavior-score-good' : pct>=0.6 ? 'behavior-score-mid' : 'behavior-score-bad';
  document.getElementById('myBehaviorScoreWrap').innerHTML = `
    <div class="stencil ${cls}" style="font-size:34px; font-weight:700;">${r.score}/${r.maxScore}</div>
    <div class="mono" style="font-size:12px; color:var(--paper-dim); margin-top:4px;">${r.incidentCount} infraction${r.incidentCount!==1?'s':''} this week</div>
  `;
  const list = document.getElementById('myBehaviorIncidentList');
  if(!r.incidents.length){
    list.innerHTML = `<div class="empty-state">No infractions logged for you this week. 🎉</div>`;
    return;
  }
  list.innerHTML = [...r.incidents].sort((a,b)=>b.date.localeCompare(a.date)).map(i=>`
    <div class="incident-card">
      <div class="incident-top"><span>${fmtDate(i.date)}</span><span class="mono" style="color:var(--red);">−${i.deduction} pts</span></div>
      <div class="incident-meta">${i.reason||'No reason given'}</div>
      ${i.notes?`<div class="incident-note">${escapeHtml(i.notes)}</div>`:''}
    </div>
  `).join('');
}

function renderBehaviorLog(){
  const cfg = state.behaviorConfig || { pointsPerDay:20, daysPerWeek:5 };
  document.getElementById('behPtsLabel').textContent = cfg.pointsPerDay;
  document.getElementById('behDeduction').value = cfg.pointsPerDay;
  document.getElementById('behDate').value = document.getElementById('behDate').value || todayISO();

  const studentSel = document.getElementById('behStudent');
  const students = state.crew.filter(c=>c.role==='student').sort((a,b)=>a.name.localeCompare(b.name));
  studentSel.innerHTML = students.map(c=>`<option value="${c.id}">${escapeHtml(c.name)}${(c.departments||[]).includes('cast') && c.castRole ? ' as '+c.castRole : ''} — ${(c.departments||[]).map(d=>deptInfo(d).label).join(', ')||'No team'}</option>`).join('') || '<option value="">No students on roster</option>';

  document.getElementById('behRecentCard').style.display = isDirector() ? 'block' : 'none';
  if(!isDirector()) return; // Stage Management can log infractions but cannot view existing ones
  const list = document.getElementById('behaviorIncidentList');
  const sorted = [...state.behaviorIncidents].sort((a,b)=>b.date.localeCompare(a.date) || b.timestamp.localeCompare(a.timestamp));
  if(!sorted.length){ list.innerHTML = `<div class="empty-state">No infractions logged yet.</div>`; return; }
  list.innerHTML = sorted.slice(0,50).map(i=>`
    <div class="incident-card">
      <div class="incident-top"><span><b>${i.crewName}</b></span><span class="mono" style="color:var(--red);">−${i.deduction} pts</span></div>
      <div class="incident-meta">${fmtDate(i.date)} · ${i.reason||'No reason given'} · logged by ${i.loggedBy||'someone'}</div>
      ${i.notes?`<div class="incident-note">${escapeHtml(i.notes)}</div>`:''}
      ${isDirector()?`<button class="btn danger small" style="margin-top:8px;" data-del-incident="${i.id}">Delete</button>`:''}
    </div>
  `).join('');
  list.querySelectorAll('[data-del-incident]').forEach(btn=>btn.addEventListener('click', async ()=>{
    if(!confirm('Delete this infraction record?')) return;
    state.behaviorIncidents = state.behaviorIncidents.filter(i=>i.id!==btn.dataset.delIncident);
    await saveState(); renderBehaviorLog();
    toast('Infraction deleted');
  }));
}
async function logInfraction(){
  if(!canManageBehavior()){ toast('Only Director or Stage Management can log infractions'); return; }
  const crewId = document.getElementById('behStudent').value;
  const student = state.crew.find(c=>c.id===crewId);
  if(!student){ toast('Choose a student'); return; }
  const date = document.getElementById('behDate').value || todayISO();
  const reason = document.getElementById('behReason').value;
  if(!reason){ toast('Select a reason'); return; }
  const deduction = parseInt(document.getElementById('behDeduction').value) || (state.behaviorConfig||{pointsPerDay:20}).pointsPerDay;
  const notes = document.getElementById('behNotes').value.trim();
  const loggedBy = currentUser()?.name || authUser?.displayName || activeEmail() || 'someone';
  const incident = {
    id:cryptoId(), crewId, crewName:student.name, date, reason, deduction, notes,
    loggedBy, loggedByEmail: activeEmail()||'', timestamp:new Date().toISOString()
  };
  state.behaviorIncidents.push(incident);
  logChange(`Behavior infraction logged for ${escapeHtml(student.name)} (${fmtDate(date)}).`, {type:'crew', crewId});
  await saveState(); renderBehaviorLog(); renderNotifications();
  sendBehaviorInfractionEmail(student, incident);
  document.getElementById('behReason').value = ''; document.getElementById('behNotes').value = '';
  toast('Infraction logged');
}

function renderBehaviorSummary(){
  if(!isDirector()){
    const wrap = document.getElementById('behaviorSummaryWrap');
    if(wrap) wrap.innerHTML = `<div class="empty-state"><div class="lamp">🔒</div>Weekly behavior summaries are Director-only.</div>`;
    return;
  }
  document.getElementById('behWeekLabel').textContent = fmtWeekLabel(ui.behaviorWeekCursor);
  const cfg = state.behaviorConfig || { pointsPerDay:20, daysPerWeek:5 };
  const students = state.crew.filter(c=>c.role==='student').sort((a,b)=>a.name.localeCompare(b.name));
  const wrap = document.getElementById('behaviorSummaryWrap');
  if(!students.length){ wrap.innerHTML = `<div class="empty-state">No students on the roster yet.</div>`; return; }

  const rows = students.map(c=>{
    const r = behaviorScoreForStudent(c.id, ui.behaviorWeekCursor);
    const pct = r.maxScore ? r.score / r.maxScore : 1;
    const cls = pct>=0.9 ? 'behavior-score-good' : pct>=0.6 ? 'behavior-score-mid' : 'behavior-score-bad';
    const isCast = (c.departments||[]).includes('cast');
    return { name: c.name + (isCast && c.castRole ? ` (${escapeHtml(c.castRole)})` : ''), classPeriod:c.classPeriod||'', departments:(c.departments||[]).map(d=>deptInfo(d).label).join(', ')||'No team', score:r.score, maxScore:r.maxScore, incidentCount:r.incidentCount, cls };
  });

  wrap.innerHTML = `
    <div class="att-grid-scroll">
    <table class="att-table" style="white-space:normal;">
      <thead><tr>
        <th class="att-name-th">Student</th><th>Class</th><th>Team</th><th># Infractions</th><th>Score</th>
      </tr></thead>
      <tbody>
        ${rows.map(r=>`<tr>
          <td class="att-name-cell">${escapeHtml(r.name)}</td>
          <td>${r.classPeriod}</td>
          <td>${r.departments}</td>
          <td>${r.incidentCount}</td>
          <td class="${r.cls}" style="font-weight:700;">${r.score}/${r.maxScore}</td>
        </tr>`).join('')}
      </tbody>
    </table>
    </div>
  `;
  ui._behaviorRowsCache = rows;
}
function downloadCsvRows(headerRow, dataRows, filename){
  const esc = v => `"${String(v??'').replace(/"/g,'""')}"`;
  const lines = [headerRow.map(esc).join(',')];
  dataRows.forEach(row=>lines.push(row.map(esc).join(',')));
  const blob = new Blob([lines.join('\n')], {type:'text/csv'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = filename;
  document.body.appendChild(a); a.click(); document.body.removeChild(a);
  URL.revokeObjectURL(url);
  toast('CSV downloaded');
}
function exportBehaviorCsv(){
  if(!isDirector()){ toast('Only the Director can export behavior grades'); return; }
  const rows = ui._behaviorRowsCache || [];
  if(!rows.length){ toast('Nothing to export'); return; }
  const weekLabel = ui.behaviorWeekCursor;
  const dataRows = rows.map(r=>[r.name, r.classPeriod, r.departments, r.incidentCount, r.score, r.maxScore, weekLabel]);
  downloadCsvRows(['Student','Class Period','Team','Infractions','Score','Max Score','Week Of'], dataRows, `behavior-grades-${weekLabel}.csv`);
}

// One row per student per report: uses that student's individual grade adjustment if the
// Director set one, otherwise the whole team's grade for that report.
function deptGradeRowsFor(deptKeys){
  const rows = [];
  deptKeys.forEach(deptKey=>{
    const dep = deptInfo(deptKey);
    const depState = state.departments[deptKey];
    depState.reports.forEach(r=>{
      const teamIds = (r.teamMemberIds && r.teamMemberIds.length) ? r.teamMemberIds : [];
      if(!teamIds.length){
        rows.push([dep.label, r.date, r.classPeriod||'', r.shift||'', '(whole team — no members listed)', r.grade, r.gradeLetter, r.status, '', r.teacherFeedback||'']);
        return;
      }
      teamIds.forEach(crewId=>{
        const student = state.crew.find(c=>c.id===crewId);
        const name = student ? student.name : '(removed student)';
        const adj = (r.individualAdjustments||[]).find(a=>a.crewId===crewId);
        const grade = adj ? adj.adjustedGrade : r.grade;
        const letter = adj ? letterFor(adj.adjustedGrade) : r.gradeLetter;
        const nonContrib = (r.nonContributorIds||[]).includes(crewId) ? 'Yes' : '';
        const feedback = (adj && adj.teacherNote) ? adj.teacherNote : (r.teacherFeedback||'');
        rows.push([dep.label, r.date, r.classPeriod||'', r.shift||'', name, grade, letter, r.status, nonContrib, feedback]);
      });
    });
  });
  return rows;
}
const DEPT_GRADE_CSV_HEADER = ['Department','Date','Class Period','Shift','Student','Grade','Letter','Status','Flagged Non-Contributor','Feedback'];
function exportDeptGradesCsv(deptKey){
  const dep = deptInfo(deptKey);
  const rows = deptGradeRowsFor([deptKey]);
  if(!rows.length){ toast('No reports to export for this department yet'); return; }
  downloadCsvRows(DEPT_GRADE_CSV_HEADER, rows, `${dep.label.replace(/\s+/g,'-').toLowerCase()}-grades.csv`);
}
function exportAllDeptGradesCsv(){
  const rows = deptGradeRowsFor(DEPARTMENTS.map(d=>d.key));
  if(!rows.length){ toast('No reports to export yet'); return; }
  downloadCsvRows(DEPT_GRADE_CSV_HEADER, rows, `all-department-grades.csv`);
}

async function submitConflict(){
  const whoId = document.getElementById('conflictWho').value;
  const user = state.crew.find(c=>c.id===whoId);
  if(!user){ toast('Choose who this conflict is for'); return; }
  const eventId = document.getElementById('conflictEvent').value;
  const ev = state.calendar.find(e=>e.id===eventId);
  if(!ev){ toast('Pick an upcoming call'); return; }
  const reason = document.getElementById('conflictReason').value;
  if(!reason){ toast('Select a reason'); return; }
  const notes = document.getElementById('conflictNotes').value.trim();
  state.conflicts.push({ id:cryptoId(), eventId:ev.id, eventTitle:ev.title, eventDate:ev.date, crewId:user.id, crewName:user.name,
    department:(user.departments||[]).map(k=>deptInfo(k).label).join(', '), reason, notes, status:'pending', submittedAt:new Date().toISOString() });
  await saveState();
  document.getElementById('conflictNotes').value=''; document.getElementById('conflictReason').value='';
  renderConflicts(); toast('Conflict submitted');
}

// ---------------- DEPARTMENTS: pills + subtabs ----------------
function renderDeptPills(){
  const wrap = document.getElementById('deptPills'); wrap.innerHTML = '';
  DEPARTMENTS.forEach(dep=>{
    const p = document.createElement('button'); p.className = 'pill' + (ui.activeDept===dep.key?' active':'');
    p.style.setProperty('--pill-color', dep.color); p.textContent = dep.label;
    p.addEventListener('click', ()=>{ ui.activeDept = dep.key; renderDepartments(); });
    wrap.appendChild(p);
  });
  document.querySelectorAll('#deptSubtabs button').forEach(b=>{
    b.classList.toggle('active', b.dataset.sub===ui.activeSub);
    b.style.setProperty('--dept-color', deptInfo(ui.activeDept).color);
  });
}

function renderDepartments(){
  renderDeptPills();
  const content = document.getElementById('deptContent'); content.innerHTML = '';
  const dep = deptInfo(ui.activeDept), depState = state.departments[ui.activeDept];

  if(ui.activeSub !== 'workspace') stopWorkspaceListener();

  if(ui.activeSub === 'tasks') renderTasksSub(content, dep, depState);
  if(ui.activeSub === 'templates') renderTemplatesSub(content, dep, depState);
  if(ui.activeSub === 'report') renderReportFormSub(content, dep, depState);
  if(ui.activeSub === 'reports') renderReportsHistorySub(content, dep, depState);
  if(ui.activeSub === 'participation') renderParticipationSub(content, dep, depState);
  if(ui.activeSub === 'workspace') renderWorkspaceSub(content, dep, depState);
}

function renderTasksSub(content, dep, depState){
  if(!canViewDept(dep.key) && !isDirectorOrStageMgmt()){
    content.innerHTML = `<div class="empty-state"><div class="lamp">🔒</div>${dep.label} tasks are only visible to ${dep.label} crew and the Director.</div>`;
    return;
  }
  const total = depState.tasks.length, done = depState.tasks.filter(t=>t.status==='done').length;
  const deptCrewOptions = state.crew.filter(c=>(c.departments||[]).includes(dep.key));
  content.innerHTML = `
    <div class="progress-wrap">
      <div class="progress-top"><span class="stencil" style="font-size:17px;">${dep.label} Tasks</span><span class="mono" style="font-size:12px;color:var(--paper-dim);">${done} / ${total} done</span></div>
      <div class="bar"><div class="bar-fill" style="width:${total?done/total*100:0}%; background:${dep.color}"></div></div>
    </div>
    <div style="display:flex; justify-content:flex-end; margin-bottom:8px;">
      <button class="btn ghost small" id="printTaskListBtn">🖨 Print Task List</button>
    </div>
    <div class="task-row" id="taskRow"></div>
    <div class="add-form" id="addForm">
      <div class="add-form-row">
        <input type="text" name="title" placeholder="Task title">
      </div>
      <div class="add-form-row">
        <select name="workType">${WORK_TYPES.map(w=>`<option>${w}</option>`).join('')}</select>
        <select name="priority">${PRIORITIES.map(p=>`<option value="${p}">${p}</option>`).join('')}</select>
        <input type="date" name="dueDate" value="${todayISO()}">
        <input type="number" name="hours" placeholder="Hrs" value="1" min="0.5" step="0.5" style="width:70px;">
        <select name="assignedToCrewId">
          <option value="">— Unassigned —</option>
          ${deptCrewOptions.map(c=>`<option value="${c.id}">${escapeHtml(c.name)}${c.email?'':' (no email on file)'}</option>`).join('')}
        </select>
      </div>
      <textarea name="description" placeholder="Instructions / notes for whoever picks this up (optional)" style="width:100%; min-height:54px; background:var(--ink); border:1px solid var(--line); color:var(--paper); border-radius:3px; padding:8px 10px; font-size:13px; font-family:'Inter',sans-serif; resize:vertical;"></textarea>
      <div><button class="btn small" id="addTaskConfirm">Add Task</button></div>
    </div>
    <button class="btn ghost small" id="toggleAddForm" style="margin-top:10px;">+ Add task</button>
  `;
  const row = document.getElementById('taskRow');
  if(!depState.tasks.length){ row.innerHTML = `<div class="empty-state"><div class="lamp">🔦</div>Nothing on the ${dep.label} board yet.</div>`; }
  depState.tasks.forEach(t=>row.appendChild(renderTaskCard(t, dep, depState)));
  document.getElementById('toggleAddForm').addEventListener('click', ()=>document.getElementById('addForm').classList.toggle('open'));
  document.getElementById('printTaskListBtn').addEventListener('click', ()=>printTaskList(dep.key));
  document.getElementById('addTaskConfirm').addEventListener('click', async ()=>{
    const form = document.getElementById('addForm');
    const title = form.querySelector('[name=title]').value.trim();
    if(!title){ toast('Give the task a title'); return; }
    const assignedToCrewId = form.querySelector('[name=assignedToCrewId]').value || null;
    const assignedPerson = assignedToCrewId ? state.crew.find(c=>c.id===assignedToCrewId) : null;
    depState.tasks.push({
      id:cryptoId(), title, description: form.querySelector('[name=description]').value.trim(),
      workType: form.querySelector('[name=workType]').value, priority: form.querySelector('[name=priority]').value,
      dueDate: form.querySelector('[name=dueDate]').value || todayISO(),
      estimatedHours: parseFloat(form.querySelector('[name=hours]').value)||1,
      status:'todo', assignedTo: assignedPerson?assignedPerson.name:'', assignedToCrewId, deadlineAlertSent:false, checklist:[]
    });
    await saveState(); renderDepartments(); renderDashboard();
    toast('Task added');
  });
}

function cycleStatus(t){
  const order = ['todo','in_progress','done'];
  t.status = order[(order.indexOf(t.status)+1)%order.length];
}
function renderTaskCard(t, dep, depState){
  if(!t.checklist) t.checklist = [];
  const card = document.createElement('div');
  card.className = 'task-card' + (t.status==='done'?' done':'');
  card.style.setProperty('--dept-color', dep.color);

  const statusBtn = document.createElement('button');
  statusBtn.className = 'status-btn ' + t.status;
  statusBtn.textContent = t.status.replace('_',' ');
  statusBtn.addEventListener('click', async ()=>{ cycleStatus(t); await saveState(); renderDepartments(); renderDashboard(); });
  card.appendChild(statusBtn);

  const body = document.createElement('div'); body.className = 'task-body';
  const clDone = t.checklist.filter(i=>i.done).length;
  body.innerHTML = `
    <div style="display:flex; align-items:center; gap:8px; flex-wrap:wrap;">
      <div class="task-title" style="margin-bottom:0;">${escapeHtml(t.title)}</div>
      ${t.checklist.length ? `<span class="task-checklist-progress">☑ ${clDone}/${t.checklist.length}</span>` : ''}
    </div>
  `;
  const metaRow = document.createElement('div'); metaRow.className = 'task-meta-row';
  metaRow.style.marginTop = '6px';
  metaRow.innerHTML = `
    <span class="tag">${t.workType}</span>
    <span class="tag priority-${t.priority}">${t.priority}</span>
    <span class="tag">Due ${t.dueDate}</span>
    <span class="tag">${t.estimatedHours}h</span>
  `;
  const assignee = document.createElement('select');
  assignee.className = 'assignee';
  const deptCrewOptions = state.crew.filter(c=>(c.departments||[]).includes(dep.key));
  assignee.innerHTML = `<option value="">— Unassigned —</option>` + deptCrewOptions.map(c=>`<option value="${c.id}" ${c.id===t.assignedToCrewId?'selected':''}>${escapeHtml(c.name)}</option>`).join('');
  assignee.addEventListener('change', async ()=>{
    const person = state.crew.find(c=>c.id===assignee.value);
    t.assignedToCrewId = assignee.value || null;
    t.assignedTo = person ? person.name : '';
    t.deadlineAlertSent = false; // reassigning re-arms the deadline alert for the new person
    await saveState();
  });
  metaRow.appendChild(assignee);
  if(isDirectorOrStageMgmt()){
    const del = document.createElement('button'); del.className='task-del'; del.textContent='✕';
    del.addEventListener('click', async ()=>{ depState.tasks = depState.tasks.filter(x=>x.id!==t.id); await saveState(); renderDepartments(); renderDashboard(); });
    metaRow.appendChild(del);
  }
  body.appendChild(metaRow);

  const expanded = ui.expandedTasks.has(t.id);
  const expandBtn = document.createElement('button');
  expandBtn.className = 'task-expand-btn';
  expandBtn.style.marginTop = '8px';
  expandBtn.innerHTML = `${expanded?'▾':'▸'} Instructions &amp; checklist`;
  expandBtn.addEventListener('click', ()=>{
    if(ui.expandedTasks.has(t.id)) ui.expandedTasks.delete(t.id); else ui.expandedTasks.add(t.id);
    renderDepartments();
  });
  body.appendChild(expandBtn);

  if(expanded){
    const panel = document.createElement('div');
    panel.className = 'task-detail-panel';

    const descArea = document.createElement('textarea');
    descArea.className = 'task-desc-input';
    descArea.placeholder = 'Instructions or notes for this task...';
    descArea.value = t.description || '';
    descArea.addEventListener('change', async ()=>{ t.description = descArea.value; await saveState(); });
    panel.appendChild(descArea);

    const clLabel = document.createElement('label');
    clLabel.className = 'checklist-label';
    clLabel.textContent = 'Checklist (saves as you go — pick up where you left off)';
    panel.appendChild(clLabel);

    t.checklist.forEach(item=>{
      const row = document.createElement('div');
      row.className = 'checklist-item' + (item.done?' checked':'');
      const cb = document.createElement('input'); cb.type = 'checkbox'; cb.checked = item.done;
      cb.addEventListener('change', async ()=>{ item.done = cb.checked; await saveState(); renderDepartments(); });
      row.appendChild(cb);
      const span = document.createElement('span'); span.textContent = item.text;
      row.appendChild(span);
      const del = document.createElement('button'); del.className = 'cl-del'; del.textContent = '✕';
      del.addEventListener('click', async ()=>{ t.checklist = t.checklist.filter(x=>x.id!==item.id); await saveState(); renderDepartments(); });
      row.appendChild(del);
      panel.appendChild(row);
    });

    const addRow = document.createElement('div');
    addRow.className = 'checklist-add-row';
    const addInput = document.createElement('input');
    addInput.type = 'text'; addInput.placeholder = 'Add a checklist step...';
    addRow.appendChild(addInput);
    const addBtn = document.createElement('button');
    addBtn.className = 'btn small'; addBtn.textContent = 'Add';
    addBtn.addEventListener('click', async ()=>{
      const text = addInput.value.trim(); if(!text) return;
      t.checklist.push({ id:cryptoId(), text, done:false });
      await saveState(); renderDepartments();
    });
    addInput.addEventListener('keydown', (e)=>{ if(e.key==='Enter'){ e.preventDefault(); addBtn.click(); } });
    addRow.appendChild(addBtn);
    panel.appendChild(addRow);

    body.appendChild(panel);
  }

  card.appendChild(body);
  return card;
}

function renderTemplatesSub(content, dep, depState){
  if(!canViewDept(dep.key) && !isDirectorOrStageMgmt()){
    content.innerHTML = `<div class="empty-state"><div class="lamp">🔒</div>${dep.label} templates are only visible to ${dep.label} crew and the Director.</div>`;
    return;
  }
  const canManage = isDirectorOrStageMgmt();
  content.innerHTML = `
    <div class="lockmsg" style="display:${canManage?'none':'block'}">Viewing as Team — only the Director or Stage Management can create or apply templates.</div>
    <div id="tplList"></div>
    ${canManage ? `
    <div class="card" style="margin-top:14px;">
      <h3>New Template</h3>
      <input type="text" id="tplName" placeholder="Template name (e.g. Standard Build Week)" class="full-width" style="background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:9px 10px; border-radius:3px;">
      <div id="tplItemsWrap"></div>
      <button class="btn ghost small" id="tplAddItemBtn">+ Add item</button>
      <div style="margin-top:12px;"><button class="btn small" id="tplSaveBtn">Save Template</button></div>
    </div>` : ''}
  `;
  const list = document.getElementById('tplList');
  if(!depState.templates.length){ list.innerHTML = `<div class="empty-state">No templates saved for ${dep.label} yet.</div>`; }
  else depState.templates.forEach(tpl=>{
    const card = document.createElement('div'); card.className = 'card'; card.style.marginBottom='10px';
    card.innerHTML = `<h3 style="margin-bottom:6px;">${escapeHtml(tpl.name)}</h3><p style="font-size:12.5px;color:var(--paper-dim);margin:0 0 10px;">${tpl.items.length} task(s)</p>
      <button class="btn small" data-act="apply" data-id="${tpl.id}">Apply — creates ${tpl.items.length} tasks</button>
      ${canManage?`<button class="btn danger small" data-act="del" data-id="${tpl.id}" style="margin-left:8px;">Delete</button>`:''}`;
    list.appendChild(card);
  });
  list.querySelectorAll('[data-act=apply]').forEach(b=>b.addEventListener('click', async ()=>{
    const tpl = depState.templates.find(t=>t.id===b.dataset.id); if(!tpl) return;
    const due = prompt('Due date for these tasks (YYYY-MM-DD):', todayISO()); if(!due) return;
    tpl.items.forEach(it=>depState.tasks.push({ id:cryptoId(), title:it.title, description:it.description||'', workType:it.workType||'Other', priority:it.priority||'medium', dueDate:due, estimatedHours:it.estimatedHours||1, status:'todo', assignedTo:'', assignedToCrewId:null, deadlineAlertSent:false, checklist:[] }));
    await saveState(); renderDepartments(); renderDashboard();
    toast(`${tpl.items.length} tasks created from template`);
  }));
  list.querySelectorAll('[data-act=del]').forEach(b=>b.addEventListener('click', async ()=>{
    depState.templates = depState.templates.filter(t=>t.id!==b.dataset.id);
    await saveState(); renderDepartments(); toast('Template deleted');
  }));

  if(canManage){
    let items = [{title:'', workType:'Build', priority:'medium', hours:1}];
    function renderItems(){
      const wrap = document.getElementById('tplItemsWrap'); wrap.innerHTML='';
      items.forEach((it,idx)=>{
        const row = document.createElement('div'); row.className='add-form-row'; row.style.marginTop='8px';
        row.innerHTML = `
          <input type="text" value="${escapeHtml(it.title)}" placeholder="Item title" data-f="title" data-i="${idx}" style="flex:2; min-width:160px; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:3px;">
          <select data-f="workType" data-i="${idx}" style="background:var(--ink); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:3px;">${WORK_TYPES.map(w=>`<option ${w===it.workType?'selected':''}>${w}</option>`).join('')}</select>
          <select data-f="priority" data-i="${idx}" style="background:var(--ink); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:3px;">${PRIORITIES.map(p=>`<option value="${p}" ${p===it.priority?'selected':''}>${p}</option>`).join('')}</select>
          <input type="number" value="${it.hours}" min="0.5" step="0.5" data-f="hours" data-i="${idx}" style="width:60px; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:3px;">
        `;
        wrap.appendChild(row);
      });
      wrap.querySelectorAll('[data-f]').forEach(inp=>inp.addEventListener('change', ()=>{
        const i = parseInt(inp.dataset.i), f = inp.dataset.f;
        items[i][f] = f==='hours' ? parseFloat(inp.value) : inp.value;
      }));
    }
    renderItems();
    document.getElementById('tplAddItemBtn').addEventListener('click', ()=>{ items.push({title:'', workType:'Build', priority:'medium', hours:1}); renderItems(); });
    document.getElementById('tplSaveBtn').addEventListener('click', async ()=>{
      const name = document.getElementById('tplName').value.trim();
      const validItems = items.filter(it=>it.title.trim());
      if(!name || !validItems.length){ toast('Give the template a name and at least one item'); return; }
      depState.templates.push({ id:cryptoId(), name, items: validItems.map(it=>({title:it.title, description:'', workType:it.workType, priority:it.priority, estimatedHours:it.hours})) });
      await saveState(); renderDepartments();
      toast('Template saved');
    });
  }
}

function renderReportFormSub(content, dep, depState){
  const u = currentUser();
  const staffLike = isDirectorOrStageMgmt();
  if(!staffLike && !u){
    content.innerHTML = `<div class="empty-state">Sign in with Google or Email &amp; Password (top right) to submit a report — reports and attendance are tied to your class period, so we need to know who's submitting. If you've done that and still see this, ask the Director to add your email to the roster.</div>`;
    return;
  }
  if(!staffLike && !canViewDept(dep.key)){
    content.innerHTML = `<div class="empty-state"><div class="lamp">🔒</div>Only ${dep.label} crew and the Director can submit a report for this department.</div>`;
    return;
  }
  const classOptions = ['Class A','Class B','Class C','Class D'];
  const effectiveClass = staffLike ? (ui.reportClassPeriod || classOptions[0]) : u.classPeriod;
  const deptCrew = state.crew.filter(c=>(c.departments||[]).includes(ui.activeDept) && c.classPeriod===effectiveClass);
  content.innerHTML = `
    <div class="card">
      <h3>${dep.label} — Submit Today's Report</h3>
      <div class="form-grid">
        <input type="date" id="repDate" value="${todayISO()}">
        <select id="repShift">${SHIFTS.map(s=>`<option>${s}</option>`).join('')}</select>
        ${staffLike
          ? `<select id="repClassPeriod">${classOptions.map(cp=>`<option ${cp===effectiveClass?'selected':''}>${cp}</option>`).join('')}</select>`
          : `<span class="tag" style="align-self:center;">${u.classPeriod}</span>`}
      </div>
      <div style="margin:12px 0;">
        <label style="font-size:11.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em; display:block; margin-bottom:6px;">Team present today (${effectiveClass})</label>
        <div class="checkbox-row" id="repTeamChecks">${deptCrew.map(c=>`<label><input type="checkbox" value="${c.id}" checked> ${escapeHtml(c.name)}</label>`).join('') || '<span style="font-size:12px;color:var(--paper-dim)">No crew assigned to this department/class period yet — add them in Setup.</span>'}</div>
        <label style="font-size:11.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em; display:block; margin:10px 0 6px;">Flag non-contributors</label>
        <div class="checkbox-row" id="repNonContribChecks">${deptCrew.map(c=>`<label><input type="checkbox" value="${c.id}"> ${escapeHtml(c.name)}</label>`).join('')}</div>
      </div>
      <label style="font-size:11.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em;">Daily / Weekly Goal</label>
      <textarea id="repGoal" class="notes-field" placeholder="What's the team aiming to accomplish today or this week?"></textarea>
      <label style="font-size:11.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em;">Completed tasks</label>
      <textarea id="repCompleted" class="notes-field">${depState.tasks.filter(t=>t.status==='done').map(t=>'• '+t.title).join('\\n')}</textarea>
      <label style="font-size:11.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em;">In progress</label>
      <textarea id="repInProgress" class="notes-field">${depState.tasks.filter(t=>t.status==='in_progress').map(t=>'• '+t.title).join('\\n')}</textarea>
      <label style="font-size:11.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em;">Notes & reflections</label>
      <textarea id="repNotes" class="notes-field" placeholder="What went well, what you learned..."></textarea>
      <label style="font-size:11.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em;">Challenges encountered</label>
      <textarea id="repChallenges" class="notes-field" placeholder="Obstacles, delays, material shortages..."></textarea>
      <button class="btn" id="submitReportBtn">Submit Report</button>
    </div>
  `;
  if(staffLike){
    document.getElementById('repClassPeriod').addEventListener('change', (e)=>{
      ui.reportClassPeriod = e.target.value;
      renderReportFormSub(content, dep, depState);
    });
  }
  document.getElementById('submitReportBtn').addEventListener('click', async ()=>{
    const teamIds = Array.from(document.querySelectorAll('#repTeamChecks input:checked')).map(i=>i.value);
    const nonContribIds = Array.from(document.querySelectorAll('#repNonContribChecks input:checked')).map(i=>i.value);
    const classPeriod = staffLike ? document.getElementById('repClassPeriod').value : u.classPeriod;
    const doneCount = depState.tasks.filter(t=>t.status==='done').length;
    const totalCount = depState.tasks.length;
    const suggestedGrade = totalCount ? Math.round(doneCount/totalCount*100) : 0;
    const report = {
      id:cryptoId(), date: document.getElementById('repDate').value, shift: document.getElementById('repShift').value, classPeriod,
      teamMemberIds: teamIds, nonContributorIds: nonContribIds,
      goal: document.getElementById('repGoal').value.trim(),
      completedTasks: document.getElementById('repCompleted').value.trim(),
      inProgressTasks: document.getElementById('repInProgress').value.trim(),
      notes: document.getElementById('repNotes').value.trim(), challenges: document.getElementById('repChallenges').value.trim(),
      tasksCompletedCount: doneCount, status:'submitted', grade: suggestedGrade, gradeLetter: letterFor(suggestedGrade),
      teacherFeedback:'', verifiedBy:'', verifiedDate:'', individualAdjustments:[], failingGradeEmailedIds:[]
    };
    depState.reports.unshift(report);
    logChange(`${dep.label} (${classPeriod}) submitted a daily report for ${fmtDate(report.date)} (suggested grade ${suggestedGrade}).`, {type:'deptClass', dept:dep.key, classPeriod});
    await saveState(); renderDepartments(); renderNotifications();
    toast('Report submitted for teacher review');
  });
}

function letterFor(score){ return score>=90?'A':score>=80?'B':score>=70?'C':score>=60?'D':'F'; }

function renderReportsHistorySub(content, dep, depState){
  if(!canViewDept(dep.key)){
    content.innerHTML = `<div class="empty-state"><div class="lamp">🔒</div>Grades and reports for ${dep.label} are only visible to ${dep.label} crew and the Director.</div>`;
    return;
  }
  const u = currentUser();
  const visibleReports = isDirector() ? depState.reports : depState.reports.filter(r=>!r.classPeriod || (u && r.classPeriod===u.classPeriod));
  if(!visibleReports.length){ content.innerHTML = `<div class="empty-state"><div class="lamp">👻</div>No ${dep.label} reports filed yet${!isDirector() && u ? ' for '+u.classPeriod : ''}.</div>`; return; }
  content.innerHTML = '';
  if(isDirector()){
    const exportBtn = document.createElement('button');
    exportBtn.className = 'btn ghost small';
    exportBtn.style.marginBottom = '14px';
    exportBtn.textContent = 'Download CSV for gradebook';
    exportBtn.addEventListener('click', ()=>exportDeptGradesCsv(dep.key));
    content.appendChild(exportBtn);
  }
  visibleReports.forEach(r=>{
    const item = document.createElement('div'); item.className = 'hist-item';
    const steps = ['submitted','verified','graded'];
    item.innerHTML = `
      <div class="hist-top">
        <span class="mono">${fmtDate(r.date)} · ${r.shift}${r.classPeriod?' · '+r.classPeriod:''}</span>
        <span style="font-family:'Oswald',sans-serif; font-weight:700; color:${r.grade>=70?'var(--sage)':'var(--red)'}">${r.gradeLetter} · ${r.grade}</span>
      </div>
      <div class="pipeline">
        ${['submitted','verified','graded'].map(s=>`<span class="step ${r.status===s?'active':(steps.indexOf(r.status)>steps.indexOf(s) && r.status!=='returned' ?'past':'')}">${s}</span>`).join('')}
        ${r.status==='returned'?'<span class="step active">returned for revision</span>':''}
      </div>
      <div class="hist-detail" id="detail-${r.id}"></div>
    `;
    item.addEventListener('click', (e)=>{
      if(e.target.closest('button')) return;
      item.classList.toggle('open');
      if(item.classList.contains('open')) renderReportDetail(r, dep, depState, document.getElementById(`detail-${r.id}`));
    });
    content.appendChild(item);
  });
}

function renderParticipationSub(content, dep, depState){
  if(!canViewDept(dep.key)){
    content.innerHTML = `<div class="empty-state"><div class="lamp">🔒</div>Participation grades for ${dep.label} are only visible to ${dep.label} crew and the Director.</div>`;
    return;
  }
  if(!ui.participationWeekCursor) ui.participationWeekCursor = weekStartISO(todayISO());
  const cfg = state.participationConfig || { pointsPerDay:20, daysPerWeek:5 };

  if(isDirector()){
    const teamCrew = state.crew.filter(c=>c.role==='student' && (c.departments||[]).includes(dep.key)).sort((a,b)=>a.name.localeCompare(b.name));
    content.innerHTML = `
      <div class="cal-nav">
        <button class="btn ghost small" id="partPrevWeekBtn">‹ Prev Week</button>
        <span class="stencil" id="partWeekLabel" style="font-size:16px;"></span>
        <button class="btn ghost small" id="partThisWeekBtn">This Week</button>
        <button class="btn ghost small" id="partNextWeekBtn">Next Week ›</button>
      </div>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-4px;">Each day is worth ${cfg.pointsPerDay} points (${cfg.pointsPerDay*cfg.daysPerWeek}/week). Everyone starts at full marks — a day only gets docked for a student flagged as a non-contributor on that day's report.</p>
      <div style="display:flex; justify-content:flex-end; margin:10px 0;">
        <button class="btn ghost small" id="partExportCsvBtn">Download CSV for gradebook</button>
      </div>
      <div id="participationTableWrap"></div>
    `;
    document.getElementById('partWeekLabel').textContent = fmtWeekLabel(ui.participationWeekCursor);
    const tableWrap = document.getElementById('participationTableWrap');
    if(!teamCrew.length){
      tableWrap.innerHTML = `<div class="empty-state">No students assigned to ${dep.label} yet.</div>`;
    } else {
      tableWrap.innerHTML = `
        <div class="att-grid-scroll">
        <table class="att-table" style="white-space:normal;">
          <thead><tr><th class="att-name-th">Student</th><th>Reports This Week</th><th>Docked Days</th><th>Score</th></tr></thead>
          <tbody>
            ${teamCrew.map(c=>{
              const r = participationScoreForStudent(c.id, dep.key, ui.participationWeekCursor);
              const pct = r.maxScore ? r.score/r.maxScore : 1;
              const cls = pct>=0.9?'behavior-score-good':pct>=0.6?'behavior-score-mid':'behavior-score-bad';
              return `<tr><td class="att-name-cell">${escapeHtml(c.name)}</td><td>${r.reportsCount}</td><td>${r.dockedDates.length}</td><td class="${cls}" style="font-weight:700;">${r.score}/${r.maxScore}</td></tr>`;
            }).join('')}
          </tbody>
        </table>
        </div>
      `;
    }
    document.getElementById('partPrevWeekBtn').addEventListener('click', ()=>{
      const d = new Date(ui.participationWeekCursor+'T00:00'); d.setDate(d.getDate()-7);
      ui.participationWeekCursor = d.toISOString().slice(0,10); renderParticipationSub(content, dep, depState);
    });
    document.getElementById('partNextWeekBtn').addEventListener('click', ()=>{
      const d = new Date(ui.participationWeekCursor+'T00:00'); d.setDate(d.getDate()+7);
      ui.participationWeekCursor = d.toISOString().slice(0,10); renderParticipationSub(content, dep, depState);
    });
    document.getElementById('partThisWeekBtn').addEventListener('click', ()=>{
      ui.participationWeekCursor = weekStartISO(todayISO()); renderParticipationSub(content, dep, depState);
    });
    document.getElementById('partExportCsvBtn').addEventListener('click', ()=>{
      const weekLabel = ui.participationWeekCursor;
      const rows = teamCrew.map(c=>{
        const r = participationScoreForStudent(c.id, dep.key, weekLabel);
        return [dep.label, c.name, c.classPeriod||'', r.reportsCount, r.dockedDates.length, r.score, r.maxScore, weekLabel];
      });
      if(!rows.length){ toast('No students to export'); return; }
      downloadCsvRows(['Department','Student','Class Period','Reports This Week','Docked Days','Score','Max Score','Week Of'], rows, `${dep.label.replace(/\s+/g,'-').toLowerCase()}-participation-${weekLabel}.csv`);
    });
    return;
  }

  // Team member (not Director): show only their own participation record for this department.
  const u = currentUser();
  if(!u){
    content.innerHTML = `<div class="empty-state">Sign in with Google or Email &amp; Password (top right) to see your participation grade.</div>`;
    return;
  }
  const r = participationScoreForStudent(u.id, dep.key, ui.participationWeekCursor);
  const pct = r.maxScore ? r.score/r.maxScore : 1;
  const cls = pct>=0.9?'behavior-score-good':pct>=0.6?'behavior-score-mid':'behavior-score-bad';
  content.innerHTML = `
    <div class="card">
      <div class="cal-nav">
        <button class="btn ghost small" id="partPrevWeekBtn">‹ Prev Week</button>
        <span class="stencil" id="partWeekLabel" style="font-size:16px;"></span>
        <button class="btn ghost small" id="partThisWeekBtn">This Week</button>
        <button class="btn ghost small" id="partNextWeekBtn">Next Week ›</button>
      </div>
      <p style="font-size:12px;color:var(--paper-dim); text-align:center;">This is your own ${dep.label} participation grade — nobody else's is shown here.</p>
      <div style="text-align:center; margin:14px 0;">
        <div class="stencil ${cls}" style="font-size:34px; font-weight:700;">${r.score}/${r.maxScore}</div>
        <div class="mono" style="font-size:12px; color:var(--paper-dim); margin-top:4px;">${r.reportsCount} report${r.reportsCount!==1?'s':''} this week${r.dockedDates.length?' · '+r.dockedDates.length+' day(s) docked':''}</div>
      </div>
    </div>
  `;
  document.getElementById('partWeekLabel').textContent = fmtWeekLabel(ui.participationWeekCursor);
  document.getElementById('partPrevWeekBtn').addEventListener('click', ()=>{
    const d = new Date(ui.participationWeekCursor+'T00:00'); d.setDate(d.getDate()-7);
    ui.participationWeekCursor = d.toISOString().slice(0,10); renderParticipationSub(content, dep, depState);
  });
  document.getElementById('partNextWeekBtn').addEventListener('click', ()=>{
    const d = new Date(ui.participationWeekCursor+'T00:00'); d.setDate(d.getDate()+7);
    ui.participationWeekCursor = d.toISOString().slice(0,10); renderParticipationSub(content, dep, depState);
  });
  document.getElementById('partThisWeekBtn').addEventListener('click', ()=>{
    ui.participationWeekCursor = weekStartISO(todayISO()); renderParticipationSub(content, dep, depState);
  });
}

// ---------------- TEAM WORKSPACE (cross-class-period notes + whiteboard, LIVE) ----------------
// Lives in its own Firestore collection ("workspaces"), separate from the main production
// document, with a real-time listener active only while this exact tab is open.
const STICKY_COLORS = ['#E8C468','#8FBF9F','#E8A7A0','#9BB8D3','#D9B3E8'];
const PEN_COLORS = ['#F2EFE9','#E8A33D','#C1443C','#6B8F71','#4C7A93','#14161A'];
const PEN_WIDTHS = [2,4,8];
function canModerateWorkspace(){ return isDirector() || canViewDept('stage_mgmt'); }

let workspaceUnsubscribe = null;
let workspaceListenerDeptKey = null;
let workspaceCache = { teamNotes:[], stickyNotes:[], strokes:[], images:[], activityLog:[] };
let workspaceDraggingId = null;
let workspaceDrawingActive = false;
let workspacePendingRerender = false;

function workspaceDocId(deptKey){ return currentProductionId + '_' + deptKey; }
function normalizeWorkspaceCache(){
  if(!workspaceCache.teamNotes) workspaceCache.teamNotes = [];
  if(!workspaceCache.stickyNotes) workspaceCache.stickyNotes = [];
  if(!workspaceCache.strokes) workspaceCache.strokes = [];
  if(!workspaceCache.images) workspaceCache.images = [];
  if(!workspaceCache.activityLog) workspaceCache.activityLog = [];
}
function logWorkspaceActivity(type, detail){
  const author = currentUser()?.name || authUser?.displayName || 'Someone';
  workspaceCache.activityLog.unshift({ id:cryptoId(), type, detail, authorName:author, authorId: currentUser()?.id||null, timestamp:new Date().toISOString() });
  if(workspaceCache.activityLog.length > 100) workspaceCache.activityLog = workspaceCache.activityLog.slice(0,100);
}

function stopWorkspaceListener(){
  if(workspaceUnsubscribe){ workspaceUnsubscribe(); workspaceUnsubscribe = null; }
  workspaceListenerDeptKey = null;
}

async function startWorkspaceListener(dep, depState){
  stopWorkspaceListener();
  if(!window.__fb) return;
  workspaceListenerDeptKey = dep.key;
  const ref = window.__fb.doc(window.__fb.db, 'workspaces', workspaceDocId(dep.key));
  workspaceUnsubscribe = window.__fb.onSnapshot(ref, (snap)=>{
    workspaceCache = snap.exists() ? snap.data() : { teamNotes:[], stickyNotes:[], strokes:[], images:[] };
    normalizeWorkspaceCache();
    if(ui.activeDept===dep.key && ui.activeSub==='workspace'){
      renderWorkspaceFromCache(dep, depState);
    }
  }, (err)=>{
    console.warn('Workspace live listener failed (check Firestore rules include "workspaces")', err);
  });
}
async function saveWorkspaceData(){
  try{
    if(window.__fb && workspaceListenerDeptKey){
      const ref = window.__fb.doc(window.__fb.db, 'workspaces', workspaceDocId(workspaceListenerDeptKey));
      await window.__fb.setDoc(ref, JSON.parse(JSON.stringify(workspaceCache)));
    }
  }catch(e){ console.warn('Workspace save failed — a pasted image may be too large. Try a smaller image or a URL instead.', e); }
}

function renderWorkspaceSub(content, dep, depState){
  if(!canViewDept(dep.key)){
    stopWorkspaceListener();
    content.innerHTML = `<div class="empty-state"><div class="lamp">🔒</div>The ${dep.label} workspace is only visible to ${dep.label} crew and the Director.</div>`;
    return;
  }
  if(depState.workspaceRestricted===undefined) depState.workspaceRestricted = false;
  if(!ui.whiteboardTool) ui.whiteboardTool = 'select';
  if(!ui.whiteboardColor) ui.whiteboardColor = PEN_COLORS[0];
  if(!ui.whiteboardWidth) ui.whiteboardWidth = PEN_WIDTHS[1];
  const staff = canModerateWorkspace();
  const drawingLocked = depState.workspaceRestricted && !staff;
  if(drawingLocked) ui.whiteboardTool = 'select';

  content.innerHTML = `
    <div style="display:flex; justify-content:space-between; align-items:flex-start; gap:10px; flex-wrap:wrap; margin-top:-6px;">
      <p style="font-size:12px;color:var(--paper-dim); margin:0;">Shared live by everyone on ${dep.label} — across every class period, not just yours. Updates appear automatically while this tab is open, no refresh needed.</p>
      <button class="btn danger small" id="wbReportBtn" style="white-space:nowrap;">🚩 Report inappropriate content</button>
    </div>
    <div class="card">
      <h2>Team Notes</h2>
      <textarea id="teamNoteText" class="full-width" style="min-height:54px;" placeholder="Leave a note for the rest of the team — what you're working on, what's blocking you, questions for other class periods..."></textarea>
      <button class="btn small" id="teamNotePostBtn">Post Note</button>
      <button class="btn ghost small" id="printTeamNotesBtn">🖨 Print Notes</button>
      <div id="teamNotesList" style="margin-top:14px;"></div>
    </div>
    <div class="card">
      <h2>Whiteboard</h2>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Sticky notes are always open to the team. Pasted images are compressed and there's a practical limit (a shared board, not unlimited storage) — for anything you found online with its own address, paste the URL instead, which has no size limit.</p>
      ${staff ? `
      <label class="lockmsg" style="display:flex; align-items:center; gap:8px; cursor:pointer;">
        <input type="checkbox" id="wbRestrictToggle" ${depState.workspaceRestricted?'checked':''}>
        Restrict Pen, Eraser, and image adding to Director/Stage Management only (sticky notes and text notes stay open to everyone)
      </label>` : (drawingLocked ? `<div class="lockmsg">Drawing and images are currently staff-only on this board. Sticky notes and Team Notes are still open to you.</div>` : '')}
      <div class="wb-toolbar" id="wbToolbar" style="display:${drawingLocked?'none':'flex'};">
        <button class="wb-tool-btn active" data-tool="select">🖐 Select / Move</button>
        <button class="wb-tool-btn" data-tool="pen">✏️ Pen</button>
        <button class="wb-tool-btn" data-tool="eraser">🧹 Eraser</button>
        <span class="wb-sep"></span>
        <span class="wb-color-row" id="wbColorRow"></span>
        <span class="wb-width-row" id="wbWidthRow"></span>
      </div>
      <div class="wb-toolbar" style="display:flex; margin-top:${drawingLocked?'0':'8px'};">
        <button class="btn ghost small" id="addStickyBtn">+ Sticky note</button>
        <button class="btn danger small" id="clearBoardBtn" style="display:${staff?'inline-block':'none'};">Clear Board</button>
      </div>
      <div class="wb-image-add-row" style="display:${drawingLocked?'none':'flex'};">
        <input type="text" id="wbImageUrl" placeholder="Paste an image URL (e.g. copied 'image address')...">
        <button class="btn ghost small" id="wbAddImageUrlBtn">Add Image from URL</button>
        <span style="font-size:11px;color:var(--paper-dim);">— or click the board and press Ctrl+V (Cmd+V) to paste a copied image directly</span>
      </div>
      <div class="whiteboard-wrap" id="whiteboardWrap" tabindex="0">
        <div class="whiteboard-canvas" id="whiteboardCanvas"></div>
        <canvas id="wbDrawCanvas" width="1400" height="900"></canvas>
      </div>
    </div>
    ${staff ? `<div class="card"><div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:6px;"><h2 style="margin:0;">Activity Log</h2>${isDirector()?'<button class="btn danger small" id="wbClearLogBtn">Clear Log</button>':''}</div><p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Who added what, and when — visible only to Director and Stage Management, so you can check even if nobody was watching live.</p><div id="wbActivityLog"></div></div>` : ''}
  `;

  document.getElementById('wbReportBtn').addEventListener('click', ()=>{
    const reporter = currentUser()?.name || authUser?.displayName || activeEmail() || 'Someone';
    logChange(`🚩 ${reporter} reported something inappropriate on the ${dep.label} workspace — please review it.`, {type:'directorOnly'});
    toast('Reported — Director and Stage Management have been notified');
  });
  if(isDirector()){
    document.getElementById('wbClearLogBtn').addEventListener('click', async ()=>{
      if(!confirm('Clear this workspace\'s activity log? This only clears the log itself — sticky notes, sketches, images, and Team Notes are not affected.')) return;
      workspaceCache.activityLog = [];
      renderWorkspaceActivityLog();
      await saveWorkspaceData();
      toast('Activity log cleared');
    });
  }

  if(staff){
    document.getElementById('wbRestrictToggle').addEventListener('change', async (e)=>{
      depState.workspaceRestricted = e.target.checked;
      await saveState();
      renderWorkspaceSub(content, dep, depState);
      toast(depState.workspaceRestricted ? 'Drawing and images locked to staff only' : 'Drawing and images opened back up to the team');
    });
  }

  // --- toolbar wiring ---
  const drawCanvas = document.getElementById('wbDrawCanvas');
  const ctx = drawCanvas.getContext('2d');
  function updateCanvasInteractivity(){
    drawCanvas.style.pointerEvents = ui.whiteboardTool==='select' ? 'none' : 'auto';
    drawCanvas.style.cursor = ui.whiteboardTool==='eraser' ? 'cell' : ui.whiteboardTool==='pen' ? 'crosshair' : 'default';
  }
  document.querySelectorAll('.wb-tool-btn').forEach(btn=>btn.addEventListener('click', ()=>{
    ui.whiteboardTool = btn.dataset.tool;
    document.querySelectorAll('.wb-tool-btn').forEach(b=>b.classList.toggle('active', b===btn));
    updateCanvasInteractivity();
  }));
  const colorRow = document.getElementById('wbColorRow');
  colorRow.innerHTML = PEN_COLORS.map((c,i)=>`<div class="sticky-color-swatch ${c===ui.whiteboardColor?'active':''}" style="background:${c}" data-color="${c}"></div>`).join('');
  colorRow.querySelectorAll('.sticky-color-swatch').forEach(sw=>sw.addEventListener('click', ()=>{
    ui.whiteboardColor = sw.dataset.color;
    colorRow.querySelectorAll('.sticky-color-swatch').forEach(s=>s.classList.remove('active'));
    sw.classList.add('active');
  }));
  const widthRow = document.getElementById('wbWidthRow');
  const widthLabels = {2:'Thin',4:'Med',8:'Thick'};
  widthRow.innerHTML = PEN_WIDTHS.map(w=>`<button class="btn ghost small wb-width-btn ${w===ui.whiteboardWidth?'active':''}" data-width="${w}">${widthLabels[w]}</button>`).join('');
  widthRow.querySelectorAll('.wb-width-btn').forEach(btn=>btn.addEventListener('click', ()=>{
    ui.whiteboardWidth = parseInt(btn.dataset.width);
    widthRow.querySelectorAll('.wb-width-btn').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
  }));
  updateCanvasInteractivity();

  function redrawStrokesCanvas(){
    ctx.clearRect(0,0,drawCanvas.width,drawCanvas.height);
    (workspaceCache.strokes||[]).forEach(s=>{
      if(!s.points || s.points.length<2) return;
      ctx.beginPath();
      ctx.strokeStyle = s.color; ctx.lineWidth = s.width; ctx.lineCap='round'; ctx.lineJoin='round';
      ctx.moveTo(s.points[0].x, s.points[0].y);
      for(let i=1;i<s.points.length;i++) ctx.lineTo(s.points[i].x, s.points[i].y);
      ctx.stroke();
    });
  }
  function canvasPointFromEvent(e){
    const rect = drawCanvas.getBoundingClientRect();
    return { x: e.clientX-rect.left, y: e.clientY-rect.top };
  }
  function eraseAtPoint(p){
    const threshold = 14;
    const before = workspaceCache.strokes.length;
    workspaceCache.strokes = workspaceCache.strokes.filter(s=> !s.points.some(pt => Math.hypot(pt.x-p.x, pt.y-p.y) < threshold));
    if(workspaceCache.strokes.length !== before) redrawStrokesCanvas();
  }
  let currentStrokePoints = null;
  drawCanvas.addEventListener('pointerdown', (e)=>{
    if(ui.whiteboardTool==='select' || drawingLocked) return;
    const p = canvasPointFromEvent(e);
    workspaceDrawingActive = true;
    drawCanvas.setPointerCapture(e.pointerId);
    if(ui.whiteboardTool==='pen') currentStrokePoints = [p];
    else if(ui.whiteboardTool==='eraser') eraseAtPoint(p);
  });
  drawCanvas.addEventListener('pointermove', (e)=>{
    if(!workspaceDrawingActive) return;
    const p = canvasPointFromEvent(e);
    if(ui.whiteboardTool==='pen' && currentStrokePoints){
      const prev = currentStrokePoints[currentStrokePoints.length-1];
      currentStrokePoints.push(p);
      ctx.beginPath(); ctx.strokeStyle=ui.whiteboardColor; ctx.lineWidth=ui.whiteboardWidth; ctx.lineCap='round'; ctx.lineJoin='round';
      ctx.moveTo(prev.x, prev.y); ctx.lineTo(p.x, p.y); ctx.stroke();
    } else if(ui.whiteboardTool==='eraser'){
      eraseAtPoint(p);
    }
  });
  drawCanvas.addEventListener('pointerup', async ()=>{
    if(!workspaceDrawingActive) return;
    workspaceDrawingActive = false;
    if(ui.whiteboardTool==='pen' && currentStrokePoints && currentStrokePoints.length>1){
      workspaceCache.strokes.push({ id:cryptoId(), points:currentStrokePoints, color:ui.whiteboardColor, width:ui.whiteboardWidth, authorId: currentUser()?.id||null });
      logWorkspaceActivity('draw', 'drew on the board');
    }
    currentStrokePoints = null;
    await saveWorkspaceData();
    if(workspacePendingRerender){ workspacePendingRerender=false; renderWorkspaceFromCache(dep, depState); }
  });

  // --- images: add via URL or clipboard paste ---
  function addImageToBoard(src, natW, natH){
    const author = currentUser()?.name || authUser?.displayName || 'Someone';
    const maxW = 260;
    let width = natW||300, height = natH||200;
    if(width > maxW){ const scale = maxW/width; width = Math.round(width*scale); height = Math.round(height*scale); }
    workspaceCache.images.push({
      id:cryptoId(), src, x: 30+Math.round(Math.random()*250), y: 30+Math.round(Math.random()*150),
      width, height, authorId: currentUser()?.id||null, authorName:author, timestamp:new Date().toISOString()
    });
    logWorkspaceActivity('image', 'added an image');
    renderWhiteboard(dep, depState);
    saveWorkspaceData();
  }
  function resizeImageFile(file){
    return new Promise((resolve, reject)=>{
      const reader = new FileReader();
      reader.onload = (e)=>{
        const img = new Image();
        img.onload = ()=>{
          const maxDim = 700;
          let w = img.naturalWidth, h = img.naturalHeight;
          if(w > maxDim || h > maxDim){ const scale = maxDim/Math.max(w,h); w = Math.round(w*scale); h = Math.round(h*scale); }
          const c = document.createElement('canvas'); c.width=w; c.height=h;
          c.getContext('2d').drawImage(img, 0, 0, w, h);
          resolve({ dataUrl: c.toDataURL('image/jpeg', 0.72), width:w, height:h });
        };
        img.onerror = reject;
        img.src = e.target.result;
      };
      reader.onerror = reject;
      reader.readAsDataURL(file);
    });
  }
  document.getElementById('wbAddImageUrlBtn').addEventListener('click', ()=>{
    if(drawingLocked){ toast('Images are locked to Director/Stage Management right now'); return; }
    const url = document.getElementById('wbImageUrl').value.trim();
    if(!url){ toast('Paste an image URL first'); return; }
    const testImg = new Image();
    testImg.onload = ()=>{ addImageToBoard(url, testImg.naturalWidth, testImg.naturalHeight); document.getElementById('wbImageUrl').value=''; toast('Image added'); };
    testImg.onerror = ()=>{ addImageToBoard(url, 300, 200); document.getElementById('wbImageUrl').value=''; toast('Added — if it looks broken, that site may block outside embedding'); };
    testImg.src = url;
  });
  document.getElementById('whiteboardWrap').addEventListener('paste', async (e)=>{
    if(drawingLocked) return;
    const items = e.clipboardData && e.clipboardData.items;
    if(!items) return;
    for(const item of items){
      if(item.type.indexOf('image')===0){
        e.preventDefault();
        const file = item.getAsFile();
        try{
          const { dataUrl, width, height } = await resizeImageFile(file);
          addImageToBoard(dataUrl, width, height);
          toast('Pasted image added');
        }catch(err){ console.warn('Paste image failed', err); toast('Could not read that pasted image'); }
        return;
      }
    }
  });

  document.getElementById('teamNotePostBtn').addEventListener('click', async ()=>{
    const text = document.getElementById('teamNoteText').value.trim();
    if(!text){ toast('Write something first'); return; }
    const author = currentUser()?.name || authUser?.displayName || 'Someone';
    workspaceCache.teamNotes.unshift({ id:cryptoId(), text, authorName:author, authorId: currentUser()?.id||null, isStaff:canModerateWorkspace(), timestamp:new Date().toISOString() });
    logWorkspaceActivity('note', 'posted a team note');
    document.getElementById('teamNoteText').value = '';
    renderTeamNotes(dep, depState);
    await saveWorkspaceData();
    toast('Note posted');
  });
  document.getElementById('printTeamNotesBtn').addEventListener('click', ()=>printTeamNotes(dep.key));

  document.getElementById('addStickyBtn').addEventListener('click', async ()=>{
    const author = currentUser()?.name || authUser?.displayName || 'Someone';
    workspaceCache.stickyNotes.push({
      id:cryptoId(), text:'', color:STICKY_COLORS[Math.floor(Math.random()*STICKY_COLORS.length)],
      x: 20 + Math.round(Math.random()*300), y: 20 + Math.round(Math.random()*200),
      authorName:author, authorId: currentUser()?.id||null, timestamp:new Date().toISOString()
    });
    logWorkspaceActivity('sticky', 'added a sticky note');
    renderWhiteboard(dep, depState);
    await saveWorkspaceData();
  });

  document.getElementById('clearBoardBtn').addEventListener('click', async ()=>{
    if(!canModerateWorkspace()) return;
    if(!confirm('Clear every sticky note, sketch, and image from this whiteboard? Team Notes are not affected.')) return;
    workspaceCache.stickyNotes = []; workspaceCache.strokes = []; workspaceCache.images = [];
    logWorkspaceActivity('clear', 'cleared the whole board');
    renderWhiteboard(dep, depState); redrawStrokesCanvas();
    await saveWorkspaceData();
    toast('Board cleared');
  });

  startWorkspaceListener(dep, depState);
  renderTeamNotes(dep, depState);
  renderWhiteboard(dep, depState);
  redrawStrokesCanvas();
  renderWorkspaceActivityLog();
  // Expose for the live-update path (defined below) to redraw ink without re-declaring the canvas.
  content._redrawStrokesCanvas = redrawStrokesCanvas;
}

function renderWorkspaceActivityLog(){
  const list = document.getElementById('wbActivityLog');
  if(!list) return; // not rendered for non-staff viewers
  const entries = workspaceCache.activityLog || [];
  if(!entries.length){ list.innerHTML = `<div class="empty-state">No activity yet.</div>`; return; }
  const icons = { sticky:'📝', image:'🖼️', note:'💬', draw:'✏️', clear:'🗑️' };
  list.innerHTML = entries.slice(0,40).map(a=>`
    <div class="list-item"><span>${icons[a.type]||'•'} <b>${a.authorName}</b> ${a.detail}</span><span class="d">${fmtDateTime(a.timestamp)}</span></div>
  `).join('');
}

// Called when a live update arrives while this exact tab is open. Skips touching the DOM
// for whatever the user is actively doing right now (typing, dragging, drawing/erasing) so
// an incoming update never yanks something out from under them — it just catches up after.
function renderWorkspaceFromCache(dep, depState){
  const activeIsSticky = document.activeElement && document.activeElement.matches && document.activeElement.matches('.sticky-note textarea');
  if(activeIsSticky || workspaceDraggingId || workspaceDrawingActive){
    workspacePendingRerender = true;
    return;
  }
  renderTeamNotes(dep, depState);
  renderWhiteboard(dep, depState);
  const content = document.getElementById('deptContent');
  if(content && content._redrawStrokesCanvas) content._redrawStrokesCanvas();
  renderWorkspaceActivityLog();
}

function renderTeamNotes(dep, depState){
  const list = document.getElementById('teamNotesList');
  if(!list) return;
  const notes = workspaceCache.teamNotes || [];
  if(!notes.length){ list.innerHTML = `<div class="empty-state">No notes yet — be the first to post one.</div>`; return; }
  const u = currentUser();
  list.innerHTML = notes.map(n=>{
    const canEdit = canModerateWorkspace() || (u && n.authorId===u.id);
    return `
      <div class="team-note-item ${n.isStaff?'staff':''}">
        <div class="team-note-top">
          <span class="author">${n.authorName}${n.isStaff?'<span class="staff-tag">STAFF</span>':''}</span>
          ${canEdit?`<span style="display:flex; gap:6px;"><button class="btn ghost small" data-edit-note="${n.id}">Edit</button><button class="task-del" data-del-note="${n.id}">✕</button></span>`:''}
        </div>
        <div class="team-note-text" id="noteText-${n.id}">${escapeHtml(n.text).replace(/\n/g,'<br>')}</div>
        <div class="team-note-meta">${fmtDateTime(n.timestamp)}${n.editedAt?' · edited':''}</div>
      </div>
    `;
  }).join('');
  list.querySelectorAll('[data-del-note]').forEach(btn=>btn.addEventListener('click', async ()=>{
    if(!confirm('Delete this note?')) return;
    workspaceCache.teamNotes = workspaceCache.teamNotes.filter(n=>n.id!==btn.dataset.delNote);
    renderTeamNotes(dep, depState);
    await saveWorkspaceData();
    toast('Note deleted');
  }));
  list.querySelectorAll('[data-edit-note]').forEach(btn=>btn.addEventListener('click', ()=>{
    const n = workspaceCache.teamNotes.find(x=>x.id===btn.dataset.editNote);
    if(!n) return;
    const textEl = document.getElementById(`noteText-${n.id}`);
    const ta = document.createElement('textarea');
    ta.className = 'full-width'; ta.style.minHeight = '54px'; ta.value = n.text;
    textEl.replaceWith(ta);
    btn.textContent = 'Save';
    btn.replaceWith(btn.cloneNode(true));
    const newBtn = list.querySelector(`[data-edit-note="${n.id}"]`);
    newBtn.textContent = 'Save';
    newBtn.addEventListener('click', async ()=>{
      const val = ta.value.trim();
      if(!val){ toast("Note can't be empty"); return; }
      n.text = val; n.editedAt = new Date().toISOString();
      renderTeamNotes(dep, depState);
      await saveWorkspaceData();
      toast('Note updated');
    });
  }));
}

function renderWhiteboard(dep, depState){
  const canvas = document.getElementById('whiteboardCanvas');
  if(!canvas) return;
  canvas.innerHTML = '';
  const u = currentUser();

  (workspaceCache.stickyNotes||[]).forEach(sn=>{
    const canEditText = canModerateWorkspace() || (u && sn.authorId===u.id);
    const el = document.createElement('div');
    el.className = 'sticky-note';
    el.style.left = sn.x+'px'; el.style.top = sn.y+'px'; el.style.background = sn.color;
    el.innerHTML = `
      <div class="sn-handle" title="Drag to move">⠿⠿ drag</div>
      <textarea placeholder="Write something..." ${canEditText?'':'readonly'}>${escapeHtml(sn.text)}</textarea>
      <div class="sn-meta">${sn.authorName}</div>
      ${canEditText?`<button class="sn-del">✕</button>`:''}
    `;
    canvas.appendChild(el);
    const handle = el.querySelector('.sn-handle');
    const ta = el.querySelector('textarea');
    if(canEditText){
      ta.addEventListener('blur', async ()=>{
        sn.text = ta.value;
        await saveWorkspaceData();
        if(workspacePendingRerender){ workspacePendingRerender = false; renderWorkspaceFromCache(dep, depState); }
      });
    }
    const delBtn = el.querySelector('.sn-del');
    if(delBtn){
      delBtn.addEventListener('click', async (e)=>{
        e.stopPropagation();
        workspaceCache.stickyNotes = workspaceCache.stickyNotes.filter(x=>x.id!==sn.id);
        renderWhiteboard(dep, depState);
        await saveWorkspaceData();
      });
    }
    let dragging = false, startX=0, startY=0, origX=sn.x, origY=sn.y;
    handle.addEventListener('pointerdown', (e)=>{
      dragging = true; workspaceDraggingId = sn.id; el.classList.add('dragging');
      startX = e.clientX; startY = e.clientY; origX = sn.x; origY = sn.y;
      handle.setPointerCapture(e.pointerId);
    });
    handle.addEventListener('pointermove', (e)=>{
      if(!dragging) return;
      const dx = e.clientX - startX, dy = e.clientY - startY;
      const newX = Math.max(0, origX + dx), newY = Math.max(0, origY + dy);
      el.style.left = newX+'px'; el.style.top = newY+'px';
      sn._pendingX = newX; sn._pendingY = newY;
    });
    handle.addEventListener('pointerup', async ()=>{
      if(!dragging) return;
      dragging = false; workspaceDraggingId = null; el.classList.remove('dragging');
      if(sn._pendingX!==undefined){ sn.x = sn._pendingX; sn.y = sn._pendingY; delete sn._pendingX; delete sn._pendingY; }
      await saveWorkspaceData();
      if(workspacePendingRerender){ workspacePendingRerender = false; renderWorkspaceFromCache(dep, depState); }
    });
  });

  (workspaceCache.images||[]).forEach(im=>{
    const canEdit = canModerateWorkspace() || (u && im.authorId===u.id);
    const el = document.createElement('div');
    el.className = 'wb-image';
    el.style.left = im.x+'px'; el.style.top = im.y+'px'; el.style.width = im.width+'px'; el.style.height = im.height+'px';
    el.innerHTML = `
      ${canEdit?`<div class="sn-handle" title="Drag to move">⠿⠿ drag</div>`:''}
      <img src="${escapeAttr(im.src)}" draggable="false">
      ${canEdit?`<button class="sn-del">✕</button><div class="img-resize-handle" title="Drag to resize"></div>`:''}
    `;
    canvas.appendChild(el);
    if(!canEdit) return;
    const handle = el.querySelector('.sn-handle');
    const delBtn = el.querySelector('.sn-del');
    delBtn.addEventListener('click', async (e)=>{
      e.stopPropagation();
      workspaceCache.images = workspaceCache.images.filter(x=>x.id!==im.id);
      renderWhiteboard(dep, depState);
      await saveWorkspaceData();
    });
    let dragging = false, startX=0, startY=0, origX=im.x, origY=im.y;
    handle.addEventListener('pointerdown', (e)=>{
      dragging = true; workspaceDraggingId = im.id; el.classList.add('dragging');
      startX = e.clientX; startY = e.clientY; origX = im.x; origY = im.y;
      handle.setPointerCapture(e.pointerId);
    });
    handle.addEventListener('pointermove', (e)=>{
      if(!dragging) return;
      const dx = e.clientX - startX, dy = e.clientY - startY;
      const newX = Math.max(0, origX + dx), newY = Math.max(0, origY + dy);
      el.style.left = newX+'px'; el.style.top = newY+'px';
      im._pendingX = newX; im._pendingY = newY;
    });
    handle.addEventListener('pointerup', async ()=>{
      if(!dragging) return;
      dragging = false; workspaceDraggingId = null; el.classList.remove('dragging');
      if(im._pendingX!==undefined){ im.x = im._pendingX; im.y = im._pendingY; delete im._pendingX; delete im._pendingY; }
      await saveWorkspaceData();
      if(workspacePendingRerender){ workspacePendingRerender = false; renderWorkspaceFromCache(dep, depState); }
    });

    const resizeHandle = el.querySelector('.img-resize-handle');
    const aspect = im.height / im.width;
    let resizing = false, rStartX=0, origW=im.width;
    resizeHandle.addEventListener('pointerdown', (e)=>{
      e.stopPropagation();
      resizing = true; workspaceDraggingId = im.id;
      rStartX = e.clientX; origW = im.width;
      resizeHandle.setPointerCapture(e.pointerId);
    });
    resizeHandle.addEventListener('pointermove', (e)=>{
      if(!resizing) return;
      const dx = e.clientX - rStartX;
      const newW = Math.max(60, origW + dx), newH = Math.round(newW*aspect);
      el.style.width = newW+'px'; el.style.height = newH+'px';
      im._pendingW = newW; im._pendingH = newH;
    });
    resizeHandle.addEventListener('pointerup', async ()=>{
      if(!resizing) return;
      resizing = false; workspaceDraggingId = null;
      if(im._pendingW!==undefined){ im.width = im._pendingW; im.height = im._pendingH; delete im._pendingW; delete im._pendingH; }
      await saveWorkspaceData();
      if(workspacePendingRerender){ workspacePendingRerender = false; renderWorkspaceFromCache(dep, depState); }
    });
  });
}
// ---------------- 3D SET DESIGN STUDIO (Three.js, live-synced) ----------------
const PIECE_TYPES = {
  // Standard 32-piece UIL One-Act Play scale set — the same piece list used by the scale
  // model kits schools buy for exactly this purpose. Dimensions match the standard kit.
  uil_platform_4x8: { label:'4×8 Platform',    defaultW:8,    defaultD:4,   defaultH:1, color:'#8B7355', uil:true },
  uil_platform_4x4: { label:'4×4 Platform',    defaultW:4,    defaultD:4,   defaultH:1, color:'#8B7355', uil:true },
  uil_platform_1x1: { label:'1×1 Platform',    defaultW:1,    defaultD:1,   defaultH:1, color:'#8B7355', uil:true },
  uil_ramp_4x4:     { label:'4×4 Ramp',        defaultW:4,    defaultD:4,   defaultH:1, color:'#A78B6C', uil:true },
  uil_step_4:       { label:"4' Step Unit",    defaultW:4,    defaultD:2,   defaultH:1, color:'#6E7B8B', uil:true },
  uil_step_2:       { label:"2' Step Unit",    defaultW:2,    defaultD:2,   defaultH:1, color:'#6E7B8B', uil:true },
  uil_pylon_8:      { label:"8' Pylon",        defaultW:1,    defaultD:1,   defaultH:8, color:'#4C7A93', uil:true },
  uil_pylon_6:      { label:"6' Pylon",        defaultW:1,    defaultD:1,   defaultH:6, color:'#4C7A93', uil:true },
  uil_pylon_4:      { label:"4' Pylon",        defaultW:1,    defaultD:1,   defaultH:4, color:'#4C7A93', uil:true },
  uil_door:         { label:'Door Unit',       defaultW:3.58, defaultD:0.3, defaultH:8, color:'#C97B84', uil:true },
  uil_window:       { label:'Window Unit',     defaultW:3,    defaultD:0.3, defaultH:8, color:'#9BB8D3', uil:true },
  uil_french_door:  { label:'French Door',     defaultW:4.96, defaultD:0.3, defaultH:8, color:'#C97B84', uil:true },
  uil_bifold_flat:  { label:'Bifold Flat',     defaultW:4,    defaultD:0.3, defaultH:8, color:'#D9D2C5', uil:true },
  uil_trifold_flat: { label:'Trifold Flat',    defaultW:6,    defaultD:0.3, defaultH:8, color:'#D9D2C5', uil:true },
  // General furniture / extras — not part of the standard UIL set, but useful for blocking.
  platform: { label:'Platform',  defaultW:6,   defaultD:4,   defaultH:1,   color:'#8B7355' },
  flat:     { label:'Flat',      defaultW:4,   defaultD:0.3, defaultH:8,   color:'#D9D2C5' },
  stairs:   { label:'Stairs',    defaultW:3,   defaultD:3,   defaultH:2,   color:'#6E7B8B' },
  riser:    { label:'Riser',     defaultW:2,   defaultD:2,   defaultH:1,   color:'#4C7A93' },
  table:    { label:'Table',     defaultW:3,   defaultD:2,   defaultH:2.5, color:'#7A5C3E' },
  chair:    { label:'Chair',     defaultW:1.5, defaultD:1.5, defaultH:3,   color:'#9C5A88' },
  bench:    { label:'Bench',     defaultW:4,   defaultD:1.2, defaultH:1.5, color:'#6B8F71' },
  door:     { label:'Doorway',   defaultW:3,   defaultD:0.3, defaultH:7,   color:'#C97B84' },
};
function canViewStageDesign(){ return isDirector() || !!currentUser(); }
function canEditStageDesign(){ return isDirectorOrStageMgmt() || canViewDept('set_design'); }

// Practice sandboxes are separate boards from the real production set — same 3D engine and
// piece palette, but their own Firestore doc, so groups can practice without colliding with
// the real show's plan OR with each other. Sandboxes are freely creatable and named (e.g. one
// per group), NOT tied to a single login identity — anyone who selects the same sandbox is
// collaborating on it live, same as the real Production Set. sdBoardMode picks which board is
// currently loaded into the (already-running) scene; switching swaps the data source only.
let sdBoardMode = 'production'; // 'production' | 'sandbox'
let sdSandboxId = null;         // which sandbox doc is loaded, when sdBoardMode==='sandbox'
function stageDesignCollectionName(){ return sdBoardMode==='sandbox' ? 'stagedesign_sandboxes' : 'stagedesigns'; }
function canEditActiveBoard(){
  if(sdBoardMode==='production') return canEditStageDesign();
  return isDirectorOrStageMgmt() || !!currentUser(); // any signed-in team member can edit a sandbox — that's the whole point, group collaboration
}

let stageDesignUnsubscribe = null;
let stageDesignCache = { pieces:[] };
let stageDesignDraggingId = null;
let stageDesignPendingRerender = false;
function stageDesignDocId(){ return sdBoardMode==='sandbox' ? currentProductionId+'_sbx_'+sdSandboxId : currentProductionId; }
function normalizeStageDesignCache(){ if(!stageDesignCache.pieces) stageDesignCache.pieces = []; }
function stopStageDesignListener(){ if(stageDesignUnsubscribe){ stageDesignUnsubscribe(); stageDesignUnsubscribe = null; } }
async function startStageDesignListener(){
  stopStageDesignListener();
  if(!window.__fb || !currentProductionId) return;
  const ref = window.__fb.doc(window.__fb.db, stageDesignCollectionName(), stageDesignDocId());
  stageDesignUnsubscribe = window.__fb.onSnapshot(ref, (snap)=>{
    stageDesignCache = snap.exists() ? snap.data() : { pieces:[] };
    normalizeStageDesignCache();
    if(!document.getElementById('view-stagedesign').classList.contains('active')) return;
    if(stageDesignDraggingId){ stageDesignPendingRerender = true; return; }
    syncStageDesignScene(); renderSdPieceList(); renderSdPropsPanel();
  }, (err)=>console.warn('Stage design live listener failed (check Firestore rules include "'+stageDesignCollectionName()+'")', err));
}
async function saveStageDesignData(){
  try{
    if(window.__fb && currentProductionId){
      const ref = window.__fb.doc(window.__fb.db, stageDesignCollectionName(), stageDesignDocId());
      await window.__fb.setDoc(ref, JSON.parse(JSON.stringify(stageDesignCache)));
    }
  }catch(e){ console.warn('Stage design save failed', e); }
}
// Creates a brand new, empty, freely-named sandbox (e.g. "Group 1") and switches into it.
// Uses an atomic array append so two groups creating sandboxes at the same moment can't
// clobber each other's entry in the shared sandbox list.
async function createNewSandbox(){
  if(!currentUser() && !isDirectorOrStageMgmt()){ toast('Sign in to create a sandbox'); return; }
  const name = prompt('Name this sandbox (e.g. "Group 1", "2nd Period Team A"):', '');
  if(name===null) return;
  const trimmed = name.trim();
  if(!trimmed){ toast('Give it a name'); return; }
  const pin = prompt('Optional: set a PIN so other groups can\'t get into this one (leave blank for no PIN):', '');
  const trimmedPin = pin ? pin.trim() : '';
  const entry = { id:cryptoId(), name:trimmed, pin: trimmedPin||null, createdBy: currentUser()?.name || authUser?.displayName || 'Someone', createdAt:new Date().toISOString() };
  if(!state.sandboxList) state.sandboxList = [];
  state.sandboxList.push(entry);
  sdUnlockedSandboxes.add(entry.id); // the creator doesn't need to re-enter their own PIN
  try{
    if(window.__fb){
      const ref = window.__fb.doc(window.__fb.db, PROD_COLLECTION, currentProductionId);
      await window.__fb.updateDoc(ref, { sandboxList: window.__fb.arrayUnion(entry) });
    } else {
      await saveState();
    }
  }catch(e){ console.warn('Sandbox creation failed to sync, falling back to full save', e); await saveState(); }
  await switchStageDesignBoard('sandbox', entry.id);
  renderStageDesignView();
  toast(trimmedPin ? `Created "${trimmed}" — share the name AND PIN with your group` : `Created "${trimmed}" — share this name with your group`);
}
// Returns true if the person may proceed into this sandbox — staff always can (they need to
// review any group's work), the sandbox's own creator never has to re-enter their PIN, and
// once anyone enters a PIN correctly it's remembered for the rest of this browser session so
// they're not re-prompted every time they switch back to it.
function sdCheckSandboxAccess(sandboxId){
  const sbx = (state.sandboxList||[]).find(s=>s.id===sandboxId);
  if(!sbx || !sbx.pin) return true;
  if(isDirectorOrStageMgmt()) return true;
  if(sdUnlockedSandboxes.has(sandboxId)) return true;
  const entered = prompt(`"${sbx.name}" is protected with a PIN. Enter it to continue:`);
  if(entered===null) return false;
  if(entered.trim() === sbx.pin){ sdUnlockedSandboxes.add(sandboxId); return true; }
  toast('Wrong PIN');
  return false;
}
// Switches which board is loaded into the (already-running) 3D scene — stops the old live
// listener, clears the currently-rendered pieces, and starts a fresh listener on the new one.
async function switchStageDesignBoard(mode, sandboxId){
  stopStageDesignListener();
  sdBoardMode = mode; sdSandboxId = sandboxId || null;
  sdSelectedId = null;
  sdUndoStack = [];
  const undoBtn = document.getElementById('sdUndoBtn');
  if(undoBtn) undoBtn.disabled = true;
  stageDesignCache = { pieces:[] };
  if(sdScene) syncStageDesignScene();
  document.getElementById('sdToolbarWrap').style.display = canEditActiveBoard() ? 'block' : 'none';
  await startStageDesignListener();
  renderSdPieceList(); renderSdPropsPanel();
}

let THREE_MOD=null, OrbitControlsClass=null;
let sdScene=null, sdCamera=null, sdRenderer=null, sdControls=null, sdRaycaster=null;
let sdPieceMeshes={}, sdAnimFrame=null, sdResizeObs=null, sdSelectedId=null;
// Undo is a local, in-memory, per-board safety net for destructive actions (Clear All,
// Delete Piece) — not a synced/persisted history. It resets whenever you switch boards, since
// undoing "into" a different board's history wouldn't make sense.
let sdUndoStack = [];
const SD_UNDO_MAX = 15;
let sdUnlockedSandboxes = new Set(); // sandbox IDs whose PIN has already been verified this session

async function ensureThreeLoaded(){
  if(THREE_MOD) return;
  THREE_MOD = await import('three');
  const mod = await import('three/addons/controls/OrbitControls.js');
  OrbitControlsClass = mod.OrbitControls;
}
function tagPieceId(obj, id){ obj.userData.pieceId = id; obj.children.forEach(c=>tagPieceId(c, id)); }
function disposePieceGroup(group){ group.traverse(o=>{ if(o.geometry) o.geometry.dispose(); if(o.material) o.material.dispose(); }); }
// A real sloped wedge (not a box) — closed 6-vertex solid, low edge at +Z rising to the
// full platform height at -Z. DoubleSide on its material means even if a face winds the
// "wrong" way it still renders, so the ramp can't come out with an invisible face.
function buildRampGeometry(THREE, w, d, h){
  const hw=w/2, hd=d/2;
  const positions = new Float32Array([
    -hw,0,-hd,  hw,0,-hd,  hw,0,hd,  -hw,0,hd,  -hw,h,-hd,  hw,h,-hd
  ]);
  const indices = [
    0,1,2, 0,2,3,   // bottom
    0,1,5, 0,5,4,   // back vertical riser (high end)
    3,2,5, 3,5,4,   // ramp slope surface
    0,4,3,          // left end cap
    1,2,5           // right end cap
  ];
  const geo = new THREE.BufferGeometry();
  geo.setAttribute('position', new THREE.BufferAttribute(positions, 3));
  geo.setIndex(indices);
  geo.computeVertexNormals();
  return geo;
}
// Darkens/lightens a hex color by a flat amount per channel — used to make a door panel or
// window frame read as visually distinct from its surrounding flat, without needing a
// second color input per piece.
function shadeColor(hex, amount){
  const num = parseInt(String(hex).replace('#',''),16);
  let r=(num>>16)+amount, g=((num>>8)&0xff)+amount, b=(num&0xff)+amount;
  r=Math.max(0,Math.min(255,r)); g=Math.max(0,Math.min(255,g)); b=Math.max(0,Math.min(255,b));
  return '#'+((1<<24)+(r<<16)+(g<<8)+b).toString(16).slice(1);
}
function buildPieceMesh(THREE, type, w, d, h, color){
  const mat = new THREE.MeshStandardMaterial({ color, roughness:0.85, metalness:0.05 });
  const group = new THREE.Group();
  if(type==='stairs' || type==='uil_step_4' || type==='uil_step_2'){
    const steps=5, stepH=h/steps, stepD=d/steps;
    for(let i=0;i<steps;i++){
      const m = new THREE.Mesh(new THREE.BoxGeometry(w, stepH, stepD*(steps-i)), mat);
      m.position.set(0, stepH*i+stepH/2, -d/2+stepD*(steps-i)/2);
      group.add(m);
    }
  } else if(type==='uil_ramp_4x4' || type.indexOf('ramp')>=0){
    const rampMat = new THREE.MeshStandardMaterial({ color, roughness:0.85, metalness:0.05, side:THREE.DoubleSide });
    const m = new THREE.Mesh(buildRampGeometry(THREE, w, d, h), rampMat);
    group.add(m);
  } else if(type==='table'){
    const top = new THREE.Mesh(new THREE.BoxGeometry(w, h*0.12, d), mat);
    top.position.set(0, h*0.94, 0); group.add(top);
    const legR = Math.min(w,d)*0.05, legH = h*0.88;
    const legGeo = new THREE.CylinderGeometry(legR, legR, legH, 8);
    [[w/2-legR*2,d/2-legR*2],[-(w/2-legR*2),d/2-legR*2],[w/2-legR*2,-(d/2-legR*2)],[-(w/2-legR*2),-(d/2-legR*2)]].forEach(([lx,lz])=>{
      const leg = new THREE.Mesh(legGeo, mat); leg.position.set(lx, legH/2, lz); group.add(leg);
    });
  } else if(type==='chair'){
    const seat = new THREE.Mesh(new THREE.BoxGeometry(w, h*0.15, d), mat);
    seat.position.set(0, h*0.45, 0); group.add(seat);
    const back = new THREE.Mesh(new THREE.BoxGeometry(w, h*0.55, d*0.15), mat);
    back.position.set(0, h*0.72, -d/2+d*0.075); group.add(back);
    const legGeo = new THREE.BoxGeometry(w*0.08, h*0.45, d*0.08);
    [[w/2-w*0.06,d/2-d*0.06],[-(w/2-w*0.06),d/2-d*0.06],[w/2-w*0.06,-(d/2-d*0.06)],[-(w/2-w*0.06),-(d/2-d*0.06)]].forEach(([lx,lz])=>{
      const leg = new THREE.Mesh(legGeo, mat); leg.position.set(lx, h*0.225, lz); group.add(leg);
    });
  } else if(type==='uil_door' || type==='door'){
    const jambW = Math.min(0.25, w*0.1), headerH = Math.min(0.3, h*0.06);
    const leftJamb = new THREE.Mesh(new THREE.BoxGeometry(jambW,h,d), mat);
    leftJamb.position.set(-w/2+jambW/2, h/2, 0); group.add(leftJamb);
    const rightJamb = new THREE.Mesh(new THREE.BoxGeometry(jambW,h,d), mat);
    rightJamb.position.set(w/2-jambW/2, h/2, 0); group.add(rightJamb);
    const header = new THREE.Mesh(new THREE.BoxGeometry(w,headerH,d), mat);
    header.position.set(0, h-headerH/2, 0); group.add(header);
    const doorMat = new THREE.MeshStandardMaterial({ color: shadeColor(color,-30), roughness:0.7 });
    const panelW = w - jambW*2 - 0.1, panelH = h - headerH - 0.1;
    const panel = new THREE.Mesh(new THREE.BoxGeometry(panelW, panelH, Math.max(d*0.5, 0.08)), doorMat);
    panel.position.set(0, panelH/2+0.05, 0); group.add(panel);
    const knob = new THREE.Mesh(new THREE.SphereGeometry(Math.min(0.09,w*0.03),8,8), new THREE.MeshStandardMaterial({ color:0xC9A227, roughness:0.3, metalness:0.6 }));
    knob.position.set(panelW*0.32, panelH*0.35, d*0.35); group.add(knob);
  } else if(type==='uil_french_door'){
    const jambW = Math.min(0.2, w*0.06), headerH = Math.min(0.3, h*0.06), mullionW = jambW*0.85;
    const leftJamb = new THREE.Mesh(new THREE.BoxGeometry(jambW,h,d), mat);
    leftJamb.position.set(-w/2+jambW/2, h/2, 0); group.add(leftJamb);
    const rightJamb = new THREE.Mesh(new THREE.BoxGeometry(jambW,h,d), mat);
    rightJamb.position.set(w/2-jambW/2, h/2, 0); group.add(rightJamb);
    const header = new THREE.Mesh(new THREE.BoxGeometry(w,headerH,d), mat);
    header.position.set(0, h-headerH/2, 0); group.add(header);
    const doorH = h - headerH;
    const midMullion = new THREE.Mesh(new THREE.BoxGeometry(mullionW, doorH, d), mat);
    midMullion.position.set(0, doorH/2, 0); group.add(midMullion);
    const glassMat = new THREE.MeshStandardMaterial({ color:0xbfe3f0, transparent:true, opacity:0.4, roughness:0.15 });
    const paneW = (w - jambW*2 - mullionW)/2 - 0.08;
    [-1,1].forEach(side=>{
      const pane = new THREE.Mesh(new THREE.BoxGeometry(Math.max(paneW,0.1), doorH-0.2, Math.max(d*0.35,0.06)), glassMat);
      pane.position.set(side*(paneW/2+mullionW/2+0.04), doorH/2, 0); group.add(pane);
      const knob = new THREE.Mesh(new THREE.SphereGeometry(Math.min(0.07,w*0.02),8,8), new THREE.MeshStandardMaterial({ color:0xC9A227, roughness:0.3, metalness:0.6 }));
      knob.position.set(side*(mullionW/2+0.06), doorH*0.35, d*0.3); group.add(knob);
    });
  } else if(type==='uil_window'){
    const frameW = Math.min(0.2, w*0.07);
    const openTop = h*0.75, openBottom = h*0.45;
    const openH = openTop - openBottom;
    const top = new THREE.Mesh(new THREE.BoxGeometry(w, frameW, d), mat); top.position.set(0, openTop, 0); group.add(top);
    const bottom = new THREE.Mesh(new THREE.BoxGeometry(w, frameW, d), mat); bottom.position.set(0, openBottom, 0); group.add(bottom);
    const left = new THREE.Mesh(new THREE.BoxGeometry(frameW, openH, d), mat); left.position.set(-w/2+frameW/2, (openTop+openBottom)/2, 0); group.add(left);
    const right = new THREE.Mesh(new THREE.BoxGeometry(frameW, openH, d), mat); right.position.set(w/2-frameW/2, (openTop+openBottom)/2, 0); group.add(right);
    const wallBelow = new THREE.Mesh(new THREE.BoxGeometry(w, openBottom, d), mat); wallBelow.position.set(0, openBottom/2, 0); group.add(wallBelow);
    const wallAbove = new THREE.Mesh(new THREE.BoxGeometry(w, h-openTop, d), mat); wallAbove.position.set(0, (h+openTop)/2, 0); group.add(wallAbove);
    const glassMat = new THREE.MeshStandardMaterial({ color:0xbfe3f0, transparent:true, opacity:0.45, roughness:0.1 });
    const glass = new THREE.Mesh(new THREE.BoxGeometry(Math.max(w-frameW*2,0.1), Math.max(openH-frameW,0.1), Math.max(d*0.3,0.05)), glassMat);
    glass.position.set(0, (openTop+openBottom)/2, 0); group.add(glass);
    const muntinV = new THREE.Mesh(new THREE.BoxGeometry(0.06, Math.max(openH-frameW,0.1), Math.max(d*0.35,0.06)), mat);
    muntinV.position.set(0, (openTop+openBottom)/2, 0); group.add(muntinV);
    const muntinH = new THREE.Mesh(new THREE.BoxGeometry(Math.max(w-frameW*2,0.1), 0.06, Math.max(d*0.35,0.06)), mat);
    muntinH.position.set(0, (openTop+openBottom)/2, 0); group.add(muntinH);
  } else {
    const mesh = new THREE.Mesh(new THREE.BoxGeometry(w, h, d), mat);
    mesh.position.set(0, h/2, 0); group.add(mesh);
  }
  return group;
}

function sdAnimateFrame(){
  sdAnimFrame = requestAnimationFrame(sdAnimateFrame);
  if(sdControls) sdControls.update();
  if(sdRenderer && sdScene && sdCamera) sdRenderer.render(sdScene, sdCamera);
}

async function initStageDesignSceneIfNeeded(){
  await ensureThreeLoaded();
  if(sdScene) return;
  const THREE = THREE_MOD;
  const canvas = document.getElementById('sdCanvas');
  const wrap = document.getElementById('sdCanvasWrap');

  sdScene = new THREE.Scene();
  sdScene.background = new THREE.Color(0x0c0d10);
  sdCamera = new THREE.PerspectiveCamera(50, Math.max(wrap.clientWidth,1)/Math.max(wrap.clientHeight,1), 0.1, 500);
  sdCamera.position.set(0, 26, 34);
  sdRenderer = new THREE.WebGLRenderer({ canvas, antialias:true, preserveDrawingBuffer:true });
  sdRenderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
  sdRenderer.setSize(wrap.clientWidth, wrap.clientHeight);

  sdControls = new OrbitControlsClass(sdCamera, sdRenderer.domElement);
  sdControls.target.set(0,2,0);
  sdControls.enableDamping = true;
  sdControls.maxPolarAngle = Math.PI/2 - 0.03;
  sdControls.minDistance = 8; sdControls.maxDistance = 90;

  sdScene.add(new THREE.HemisphereLight(0xffffff, 0x33362e, 1.1));
  const dir = new THREE.DirectionalLight(0xffffff, 0.9); dir.position.set(15,25,10); sdScene.add(dir);

  const floor = new THREE.Mesh(new THREE.PlaneGeometry(50,34), new THREE.MeshStandardMaterial({ color:0x1b2a4a, roughness:1 }));
  floor.rotation.x = -Math.PI/2; sdScene.add(floor);
  const grid = new THREE.GridHelper(50, 50, 0xE8A33D, 0x30364a); grid.position.y = 0.01; sdScene.add(grid);

  sdRaycaster = new THREE.Raycaster();
  const dragPlane = new THREE.Plane(new THREE.Vector3(0,1,0), 0);
  const dragOffset = new THREE.Vector3();
  let dragPieceId = null;

  function ndc(e){
    const rect = canvas.getBoundingClientRect();
    return new THREE.Vector2(((e.clientX-rect.left)/rect.width)*2-1, -((e.clientY-rect.top)/rect.height)*2+1);
  }
  function pieceIdAt(e){
    sdRaycaster.setFromCamera(ndc(e), sdCamera);
    const hits = sdRaycaster.intersectObjects(Object.values(sdPieceMeshes), true);
    if(!hits.length) return null;
    let obj = hits[0].object;
    while(obj && !obj.userData.pieceId) obj = obj.parent;
    return obj ? obj.userData.pieceId : null;
  }
  canvas.addEventListener('pointerdown', (e)=>{
    if(!canEditActiveBoard()) return;
    const id = pieceIdAt(e);
    if(id){
      selectPiece(id);
      dragPieceId = id; stageDesignDraggingId = id;
      sdControls.enabled = false;
      sdRaycaster.setFromCamera(ndc(e), sdCamera);
      const hitPoint = new THREE.Vector3();
      sdRaycaster.ray.intersectPlane(dragPlane, hitPoint);
      const mesh = sdPieceMeshes[id];
      dragOffset.copy(mesh.position).sub(hitPoint);
      canvas.setPointerCapture(e.pointerId);
    } else {
      selectPiece(null);
    }
  });
  canvas.addEventListener('pointermove', (e)=>{
    if(!dragPieceId) return;
    sdRaycaster.setFromCamera(ndc(e), sdCamera);
    const hitPoint = new THREE.Vector3();
    if(sdRaycaster.ray.intersectPlane(dragPlane, hitPoint)){
      const mesh = sdPieceMeshes[dragPieceId];
      const nx = hitPoint.x+dragOffset.x, nz = hitPoint.z+dragOffset.z;
      mesh.position.x = nx; mesh.position.z = nz;
      const piece = stageDesignCache.pieces.find(p=>p.id===dragPieceId);
      if(piece){ piece._pendingX = nx; piece._pendingZ = nz; }
    }
  });
  canvas.addEventListener('pointerup', async ()=>{
    if(!dragPieceId) return;
    const piece = stageDesignCache.pieces.find(p=>p.id===dragPieceId);
    if(piece && piece._pendingX!==undefined){ piece.x = piece._pendingX; piece.z = piece._pendingZ; delete piece._pendingX; delete piece._pendingZ; }
    dragPieceId = null; stageDesignDraggingId = null;
    sdControls.enabled = true;
    await saveStageDesignData();
    if(stageDesignPendingRerender){ stageDesignPendingRerender=false; syncStageDesignScene(); renderSdPieceList(); }
  });

  sdResizeObs = new ResizeObserver(()=>{
    if(!wrap.clientWidth || !wrap.clientHeight || !sdCamera || !sdRenderer) return;
    sdCamera.aspect = wrap.clientWidth/wrap.clientHeight;
    sdCamera.updateProjectionMatrix();
    sdRenderer.setSize(wrap.clientWidth, wrap.clientHeight);
  });
  sdResizeObs.observe(wrap);
}
function pauseStageDesignScene(){
  if(sdAnimFrame){ cancelAnimationFrame(sdAnimFrame); sdAnimFrame=null; }
  stopStageDesignListener();
}
function syncStageDesignScene(){
  if(!sdScene) return;
  const THREE = THREE_MOD;
  const ids = new Set(stageDesignCache.pieces.map(p=>p.id));
  Object.keys(sdPieceMeshes).forEach(id=>{
    if(!ids.has(id)){ sdScene.remove(sdPieceMeshes[id]); disposePieceGroup(sdPieceMeshes[id]); delete sdPieceMeshes[id]; }
  });
  stageDesignCache.pieces.forEach(p=>{
    let group = sdPieceMeshes[p.id];
    const sig = `${p.type}|${p.width}|${p.depth}|${p.height}|${p.color}`;
    if(!group || group.userData.sig !== sig){
      if(group){ sdScene.remove(group); disposePieceGroup(group); }
      group = buildPieceMesh(THREE, p.type, p.width, p.depth, p.height, p.color);
      group.userData.sig = sig;
      tagPieceId(group, p.id);
      sdScene.add(group);
      sdPieceMeshes[p.id] = group;
    }
    group.position.set(p.x, p.y||0, p.z);
    group.rotation.y = (p.rotationY||0) * Math.PI/180;
    group.traverse(o=>{ if(o.material) o.material.emissive = new THREE.Color(p.id===sdSelectedId ? 0x333333 : 0x000000); });
  });
}
async function addStagePiece(type){
  if(!canEditActiveBoard()){ toast('Only Set Design, Stage Management, or the Director can edit the set'); return; }
  const def = PIECE_TYPES[type]; if(!def) return;
  const author = currentUser()?.name || authUser?.displayName || 'Someone';
  const piece = { id:cryptoId(), type, label:def.label, x:(Math.random()-0.5)*10, y:0, z:(Math.random()-0.5)*6,
    rotationY:0, width:def.defaultW, depth:def.defaultD, height:def.defaultH, color:def.color,
    authorName:author, authorId: currentUser()?.id||null };
  stageDesignCache.pieces.push(piece);
  syncStageDesignScene(); renderSdPieceList(); selectPiece(piece.id);
  await saveStageDesignData();
}
function selectPiece(id){
  sdSelectedId = id;
  syncStageDesignScene(); renderSdPieceList(); renderSdPropsPanel();
}
function renderSdPieceList(){
  const list = document.getElementById('sdPieceList'); if(!list) return;
  if(!stageDesignCache.pieces.length){ list.innerHTML = `<div class="empty-state" style="font-size:11.5px;">No pieces yet.</div>`; return; }
  list.innerHTML = stageDesignCache.pieces.map(p=>`
    <div class="sd-piece-row ${p.id===sdSelectedId?'active':''}" data-select-piece="${p.id}">
      <span><span class="sw" style="background:${p.color}"></span>${escapeHtml(p.label||PIECE_TYPES[p.type]?.label||p.type)}</span>
    </div>
  `).join('');
  list.querySelectorAll('[data-select-piece]').forEach(row=>row.addEventListener('click', ()=>selectPiece(row.dataset.selectPiece)));
}
function renderSdPropsPanel(){
  const panel = document.getElementById('sdPropsPanel'); if(!panel) return;
  const piece = stageDesignCache.pieces.find(p=>p.id===sdSelectedId);
  if(!piece || !canEditActiveBoard()){ panel.style.display='none'; return; }
  panel.style.display = 'block';
  document.getElementById('sdPropLabel').value = piece.label||'';
  document.getElementById('sdPropColor').value = piece.color||'#888888';
  document.getElementById('sdPropWidth').value = piece.width;
  document.getElementById('sdPropDepth').value = piece.depth;
  document.getElementById('sdPropHeight').value = piece.height;
  document.getElementById('sdPropRotation').value = piece.rotationY||0;
  document.getElementById('sdPropBaseY').value = piece.y||0;
}
async function updateSelectedPieceProp(field, value){
  const piece = stageDesignCache.pieces.find(p=>p.id===sdSelectedId); if(!piece) return;
  piece[field] = value;
  syncStageDesignScene(); renderSdPieceList();
  await saveStageDesignData();
}
function sdPushUndo(){
  sdUndoStack.push(JSON.parse(JSON.stringify(stageDesignCache.pieces)));
  if(sdUndoStack.length > SD_UNDO_MAX) sdUndoStack.shift();
  const btn = document.getElementById('sdUndoBtn');
  if(btn) btn.disabled = false;
}
async function sdUndo(){
  if(!sdUndoStack.length){ toast('Nothing to undo'); return; }
  if(!canEditActiveBoard()){ toast('Only editors of this board can undo'); return; }
  stageDesignCache.pieces = sdUndoStack.pop();
  sdSelectedId = null;
  syncStageDesignScene(); renderSdPieceList(); renderSdPropsPanel();
  await saveStageDesignData();
  const btn = document.getElementById('sdUndoBtn');
  if(btn) btn.disabled = !sdUndoStack.length;
  toast('Undone');
}
async function deleteSelectedPiece(){
  if(!sdSelectedId) return;
  sdPushUndo();
  stageDesignCache.pieces = stageDesignCache.pieces.filter(p=>p.id!==sdSelectedId);
  sdSelectedId = null;
  syncStageDesignScene(); renderSdPieceList(); renderSdPropsPanel();
  await saveStageDesignData();
}
function sdPieceManifestRows(){
  return stageDesignCache.pieces.map(p=>`<tr><td>${escapeHtml(p.label||PIECE_TYPES[p.type]?.label||p.type)}</td><td>${p.width.toFixed(2)}' × ${p.depth.toFixed(2)}' × ${p.height.toFixed(2)}'</td><td>${Math.round(p.x*10)/10}, ${Math.round(p.z*10)/10}</td><td>${p.rotationY||0}°</td></tr>`).join('');
}
function printStageDesignSnapshot(){
  if(!sdRenderer || !stageDesignCache.pieces.length){ toast('Add some pieces to the set first'); return; }
  sdRenderer.render(sdScene, sdCamera); // ensure the buffer reflects the current camera angle
  const dataUrl = sdRenderer.domElement.toDataURL('image/png');
  const boardLabel = sdBoardMode==='sandbox' ? 'Practice Sandbox' : 'Production Set';
  const body = `
    <h1>${escapeHtml(state.productionName)}</h1>
    <div class="meta">3D Set Design — ${boardLabel} — Printed ${new Date().toLocaleDateString()} (camera view at time of printing, not to scale)</div>
    <img src="${dataUrl}" style="width:100%; border:1px solid #999; margin-bottom:16px;">
    <h2>Pieces on Stage</h2>
    <table><thead><tr><th>Piece</th><th>Dimensions (W×D×H)</th><th>Position (X,Z ft from center)</th><th>Rotation</th></tr></thead>
    <tbody>${sdPieceManifestRows() || '<tr><td colspan="4">No pieces placed.</td></tr>'}</tbody></table>
  `;
  openPrintWindow(`${state.productionName} — 3D Set View`, body);
}
function printStageDesignGroundPlan(){
  if(!stageDesignCache.pieces.length){ toast('Add some pieces to the set first'); return; }
  const stageW = 50, stageD = 34; // matches the floor built in initStageDesignSceneIfNeeded
  const scale = 12; // px per foot on screen; printing at 100%/no-scaling keeps this proportionally accurate
  const svgW = stageW*scale, svgH = stageD*scale;
  const cx = svgW/2, cy = svgH/2;
  const toPx = (x,z)=>[cx + x*scale, cy + z*scale];
  const pieceShapes = stageDesignCache.pieces.map(p=>{
    const [px,pz] = toPx(p.x, p.z);
    const w = p.width*scale, d = p.depth*scale;
    const label = escapeHtml(p.label||PIECE_TYPES[p.type]?.label||p.type);
    return `
      <g transform="translate(${px},${pz}) rotate(${p.rotationY||0})">
        <rect x="${-w/2}" y="${-d/2}" width="${w}" height="${d}" fill="${p.color}" fill-opacity="0.55" stroke="#111" stroke-width="1.5"/>
        <text x="0" y="4" font-size="10" text-anchor="middle" fill="#111" font-family="Arial">${label}</text>
      </g>`;
  }).join('');
  const svg = `
    <svg width="${svgW}" height="${svgH}" viewBox="0 0 ${svgW} ${svgH}" xmlns="http://www.w3.org/2000/svg" style="background:#fff; border:2px solid #111;">
      <line x1="${cx}" y1="0" x2="${cx}" y2="${svgH}" stroke="#999" stroke-width="1" stroke-dasharray="4,4"/>
      <text x="${cx+4}" y="14" font-size="10" fill="#999" font-family="Arial">Center Line</text>
      <text x="8" y="${svgH-8}" font-size="10" fill="#333" font-family="Arial">Downstage / Audience →</text>
      ${pieceShapes}
      <g transform="translate(16,16)">
        <line x1="0" y1="0" x2="${scale*5}" y2="0" stroke="#111" stroke-width="2"/>
        <text x="0" y="-4" font-size="10" fill="#111" font-family="Arial">Scale: 5 ft (print at 100% / "Actual Size" for accuracy)</text>
      </g>
    </svg>`;
  const boardLabel = sdBoardMode==='sandbox' ? 'Practice Sandbox' : 'Production Set';
  const body = `
    <h1>${escapeHtml(state.productionName)}</h1>
    <div class="meta">Ground Plan — ${boardLabel} — Printed ${new Date().toLocaleDateString()} — Stage shown: ${stageW}' × ${stageD}'</div>
    ${svg}
    <h2 style="margin-top:16px;">Pieces on Stage</h2>
    <table><thead><tr><th>Piece</th><th>Dimensions (W×D×H)</th><th>Position (X,Z ft from center)</th><th>Rotation</th></tr></thead>
    <tbody>${sdPieceManifestRows()}</tbody></table>
    <p style="font-size:10.5px; color:#777; margin-top:10px;">Auto-generated top-down plan from the 3D layout — position/rotation are exact, but treat this as a working plan, not a substitute for a hand-checked technical drawing before building.</p>
  `;
  openPrintWindow(`${state.productionName} — Ground Plan`, body);
}
async function deleteSandbox(sandboxId){
  if(!isDirectorOrStageMgmt()) return;
  const sbx = (state.sandboxList||[]).find(s=>s.id===sandboxId);
  if(!sbx) return;
  if(!confirm(`Delete the sandbox "${sbx.name}"? This removes everything in it — no undo.`)) return;
  state.sandboxList = (state.sandboxList||[]).filter(s=>s.id!==sandboxId);
  try{
    if(window.__fb){
      const ref = window.__fb.doc(window.__fb.db, PROD_COLLECTION, currentProductionId);
      await window.__fb.updateDoc(ref, { sandboxList: window.__fb.arrayRemove(sbx) });
      await window.__fb.deleteDoc(window.__fb.doc(window.__fb.db, 'stagedesign_sandboxes', currentProductionId+'_sbx_'+sandboxId)).catch(()=>{});
    } else {
      await saveState();
    }
  }catch(e){ console.warn('Sandbox delete failed to sync', e); await saveState(); }
  await switchStageDesignBoard('production', null);
  document.querySelectorAll('#sdBoardTabs button').forEach(b=>b.classList.toggle('active', b.dataset.board==='production'));
  renderStageDesignView();
  toast(`"${sbx.name}" deleted`);
}
async function renderStageDesignView(){
  const canView = canViewStageDesign();
  document.getElementById('stageDesignLockedMsg').style.display = canView ? 'none' : 'block';
  document.getElementById('stageDesignWrap').style.display = canView ? 'block' : 'none';
  if(!canView){ pauseStageDesignScene(); return; }
  document.getElementById('sdToolbarWrap').style.display = canEditActiveBoard() ? 'block' : 'none';

  const sandboxTabBtn = document.querySelector('#sdBoardTabs [data-board="sandbox"]');
  const u = currentUser();
  sandboxTabBtn.style.display = (u || isDirectorOrStageMgmt()) ? 'inline-block' : 'none';
  document.getElementById('sdSandboxBrowseWrap').style.display = (sdBoardMode==='sandbox') ? 'flex' : 'none';
  document.getElementById('sdDeleteSandboxBtn').style.display = (sdBoardMode==='sandbox' && sdSandboxId && isDirectorOrStageMgmt()) ? 'inline-block' : 'none';
  if(sdBoardMode==='sandbox'){
    const sel = document.getElementById('sdSandboxBrowseSelect');
    const list = state.sandboxList || [];
    sel.innerHTML = list.length ? list.map(sbx=>`<option value="${sbx.id}" ${sbx.id===sdSandboxId?'selected':''}>${escapeHtml(sbx.name)}</option>`).join('')
      : `<option value="">No sandboxes yet — create one</option>`;
  }

  await initStageDesignSceneIfNeeded();
  if(!sdAnimFrame) sdAnimFrame = requestAnimationFrame(sdAnimateFrame);
  await startStageDesignListener();
  syncStageDesignScene(); renderSdPieceList(); renderSdPropsPanel();
}

// ---------------- LIGHTING LAB (Three.js virtual light board + digital stage) ----------------
// Practice tool, not tied to real production planning — open to any signed-in team member,
// same as viewing the 3D set. Live board position is per-session only (not saved); saved
// cues persist for the whole team via normal saveState(), no live-sync needed here.
// User's actual area numbering: 1-5 front row, 6-10 middle row, 11-15 back row (left to
// right in each row). Assuming 1-5 is downstage (closest to audience) since that's how it
// was written — flip LL_AREA_ROWS' z order below if that's backwards for your stage.
const LL_AREA_ROWS = [
  { z: 8,  nums:[1,2,3,4,5] },
  { z: 0,  nums:[6,7,8,9,10] },
  { z: -8, nums:[11,12,13,14,15] },
];
const LL_COL_X = [-12,-6,0,6,12];
const LL_AREA_DEFS = [];
LL_AREA_ROWS.forEach(row=>{ row.nums.forEach((num,colIdx)=>{ LL_AREA_DEFS.push({ key:'A'+num, label:String(num), x:LL_COL_X[colIdx], z:row.z, isCyc:false, num }); }); });
LL_AREA_DEFS.sort((a,b)=>a.num-b.num);
const LL_CYC_DEFS = [
  { key:'CYCL', label:'Cyc L', x:-10, z:-13, isCyc:true },
  { key:'CYCC', label:'Cyc C', x:0,   z:-13, isCyc:true },
  { key:'CYCR', label:'Cyc R', x:10,  z:-13, isCyc:true },
];
const LL_CHANNELS = [...LL_AREA_DEFS, ...LL_CYC_DEFS];
const LL_SWATCHES = ['#ffffff','#ffcf8a','#a6c8ff','#ff5c5c','#5cff8a','#c78aff','#ffe45c','#5cf0ff'];
const LL_INTENSITY_SCALE = 40; // best-effort brightness scale — tell me if it reads too dim/bright once you test it

function canUseLightingLab(){ return isDirector() || !!currentUser(); }

let llScene=null, llCamera=null, llRenderer=null, llControls=null, llResizeObs=null, llAnimFrame=null;
let llLights=[], llActors=[], llCycMesh=null, llFloorMesh=null;
let llCrossfade = null;
let llPieceMeshes = {}, llDraggingPieceId = null, llRaycaster = null;

function buildLLActorMesh(THREE, color){
  const mat = new THREE.MeshStandardMaterial({ color: color||0xcfc6b8, roughness:0.9 });
  const g = new THREE.Group();
  const body = new THREE.Mesh(new THREE.CylinderGeometry(0.55,0.65,3.2,10), mat);
  body.position.y = 1.9; body.castShadow = true; g.add(body);
  const head = new THREE.Mesh(new THREE.SphereGeometry(0.5,12,10), mat);
  head.position.y = 3.9; head.castShadow = true; g.add(head);
  return g;
}
function buildLLCubeMesh(THREE, color){
  const mesh = new THREE.Mesh(new THREE.BoxGeometry(2,2,2), new THREE.MeshStandardMaterial({ color: color||0x8a7c66, roughness:0.85 }));
  mesh.position.y = 1; mesh.castShadow = true;
  return mesh;
}
async function initLightingLabSceneIfNeeded(){
  await ensureThreeLoaded();
  if(llScene) return;
  const THREE = THREE_MOD;
  const canvas = document.getElementById('llCanvas');
  const wrap = document.getElementById('llCanvasWrap');

  llScene = new THREE.Scene();
  llScene.background = new THREE.Color(0x050608);
  llCamera = new THREE.PerspectiveCamera(50, Math.max(wrap.clientWidth,1)/Math.max(wrap.clientHeight,1), 0.1, 500);
  llCamera.position.set(0, 16, 24);
  llRenderer = new THREE.WebGLRenderer({ canvas, antialias:true, preserveDrawingBuffer:true });
  llRenderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
  llRenderer.setSize(wrap.clientWidth, wrap.clientHeight);
  llRenderer.shadowMap.enabled = true; // real shadows, toggled per-light below based on intensity

  llControls = new OrbitControlsClass(llCamera, llRenderer.domElement);
  llControls.target.set(0,3,-2);
  llControls.enableDamping = true;
  llControls.maxPolarAngle = Math.PI/2 - 0.02;
  llControls.minDistance = 8; llControls.maxDistance = 60;

  llScene.add(new THREE.AmbientLight(0xffffff, 0.06)); // faint ambient so blackout isn't pure void

  // Floor
  const floor = new THREE.Mesh(new THREE.PlaneGeometry(34,24), new THREE.MeshStandardMaterial({ color:0x2a2c33, roughness:1 }));
  floor.rotation.x = -Math.PI/2; floor.position.z = -1; floor.receiveShadow = true; llScene.add(floor);
  llFloorMesh = floor;
  const grid = new THREE.GridHelper(34, 34, 0x555555, 0x333333); grid.position.y=0.01; grid.position.z=-1; llScene.add(grid);

  // Cyclorama backdrop
  llCycMesh = new THREE.Mesh(new THREE.PlaneGeometry(30,14), new THREE.MeshStandardMaterial({ color:0x111111, roughness:1 }));
  llCycMesh.position.set(0, 7, -14); llCycMesh.receiveShadow = true; llScene.add(llCycMesh);

  // Stage starts blank — students use "+ Add Actor" / "+ Add Rehearsal Cube" to place
  // movable practice pieces themselves, rather than fixed figures cluttering every area.

  // One light per channel (areas get SpotLights aimed down at their area; cyc channels get
  // SpotLights aimed at the backdrop). decay:0 so brightness doesn't require guessing large
  // physically-based candela numbers — a straightforward 0-100% maps predictably to dark-to-bright.
  // Shadows are toggled on per-light only while that light is actually above 0%, and use a
  // modest shadow-map size, so a school laptop isn't rendering 18 shadow maps at once for
  // lights nobody's even brought up.
  llLights = LL_CHANNELS.map(def=>{
    const light = new THREE.SpotLight(0xffffff, 0, 40, def.isCyc ? Math.PI/5 : Math.PI/7, 0.5, 0);
    if(def.isCyc){ light.position.set(def.x, 3, -9); } else { light.position.set(def.x, 14, def.z+1); }
    light.castShadow = false;
    light.shadow.mapSize.set(512,512);
    light.shadow.camera.near = 1; light.shadow.camera.far = 30;
    light.shadow.bias = -0.0015;
    llScene.add(light);
    const target = new THREE.Object3D();
    target.position.set(def.x, def.isCyc?7:0, def.isCyc?-14:def.z);
    llScene.add(target);
    light.target = target;
    return light;
  });

  llResizeObs = new ResizeObserver(()=>{
    if(!wrap.clientWidth || !wrap.clientHeight || !llCamera || !llRenderer) return;
    llCamera.aspect = wrap.clientWidth/wrap.clientHeight;
    llCamera.updateProjectionMatrix();
    llRenderer.setSize(wrap.clientWidth, wrap.clientHeight);
  });
  llResizeObs.observe(wrap);

  // Movable practice pieces (actors/cubes) — drag on the floor plane to reposition, same
  // pattern as the Set Design tool. Dragging a piece temporarily disables camera orbit so
  // the two gestures don't fight each other.
  llRaycaster = new THREE.Raycaster();
  const dragPlane = new THREE.Plane(new THREE.Vector3(0,1,0), 0);
  const dragOffset = new THREE.Vector3();
  let dragId = null;
  function ndc(e){
    const rect = canvas.getBoundingClientRect();
    return new THREE.Vector2(((e.clientX-rect.left)/rect.width)*2-1, -((e.clientY-rect.top)/rect.height)*2+1);
  }
  function pieceIdAt(e){
    llRaycaster.setFromCamera(ndc(e), llCamera);
    const hits = llRaycaster.intersectObjects(Object.values(llPieceMeshes), true);
    if(!hits.length) return null;
    let obj = hits[0].object;
    while(obj && !obj.userData.llPieceId) obj = obj.parent;
    return obj ? obj.userData.llPieceId : null;
  }
  canvas.addEventListener('pointerdown', (e)=>{
    if(!canUseLightingLab()) return;
    const id = pieceIdAt(e);
    if(!id) return;
    dragId = id; llDraggingPieceId = id;
    llControls.enabled = false;
    llRaycaster.setFromCamera(ndc(e), llCamera);
    const hitPoint = new THREE.Vector3();
    llRaycaster.ray.intersectPlane(dragPlane, hitPoint);
    const mesh = llPieceMeshes[id];
    dragOffset.copy(mesh.position).sub(hitPoint);
    canvas.setPointerCapture(e.pointerId);
  });
  canvas.addEventListener('pointermove', (e)=>{
    if(!dragId) return;
    llRaycaster.setFromCamera(ndc(e), llCamera);
    const hitPoint = new THREE.Vector3();
    if(llRaycaster.ray.intersectPlane(dragPlane, hitPoint)){
      const mesh = llPieceMeshes[dragId];
      mesh.position.x = hitPoint.x + dragOffset.x;
      mesh.position.z = hitPoint.z + dragOffset.z;
      const piece = ui.llPieces.find(p=>p.id===dragId);
      if(piece){ piece.x = mesh.position.x; piece.z = mesh.position.z; }
    }
  });
  canvas.addEventListener('pointerup', ()=>{
    dragId = null; llDraggingPieceId = null;
    llControls.enabled = true;
  });
}
function llAnimateFrame(){
  llAnimFrame = requestAnimationFrame(llAnimateFrame);
  if(llControls) llControls.update();
  if(llCrossfade){
    const t = Math.min(1, (performance.now()-llCrossfade.startTime)/llCrossfade.duration);
    LL_CHANNELS.forEach((def,i)=>{
      const from = llCrossfade.fromIntensity[i], to = llCrossfade.toIntensity[i];
      ui.llState[i].intensity = Math.round(from + (to-from)*t);
      llLights[i].intensity = (ui.llState[i].intensity/100) * LL_INTENSITY_SCALE;
      llLights[i].castShadow = ui.llState[i].intensity > 0;
    });
    llRefreshChannelUI();
    if(t>=1) llCrossfade = null;
  }
  if(llRenderer && llScene && llCamera) llRenderer.render(llScene, llCamera);
}
function pauseLightingLabScene(){
  if(llAnimFrame){ cancelAnimationFrame(llAnimFrame); llAnimFrame=null; }
  llCrossfade = null;
}

function llApplyChannel(i){
  const st = ui.llState[i];
  if(llLights[i]){
    llLights[i].intensity = (st.intensity/100) * LL_INTENSITY_SCALE;
    llLights[i].color.set(st.color);
    llLights[i].castShadow = st.intensity > 0; // only lights that are actually on pay for a shadow map
  }
}
function llRefreshChannelUI(){
  LL_CHANNELS.forEach((def,i)=>{
    const fader = document.getElementById('llFader-'+i);
    const pct = document.getElementById('llPct-'+i);
    if(fader) fader.value = ui.llState[i].intensity;
    if(pct) pct.textContent = Math.round(ui.llState[i].intensity)+'%';
  });
}
function llChannelStripHtml(def, i){
  return `
    <div class="ll-channel">
      <div class="ll-channel-label">${escapeHtml(def.label)}</div>
      <div class="ll-fader-track"><input type="range" id="llFader-${i}" min="0" max="100" value="0"></div>
      <div class="ll-channel-pct" id="llPct-${i}">0%</div>
      <input type="color" class="ll-channel-color" id="llColor-${i}" value="#ffffff">
      <div class="ll-swatch-row">${LL_SWATCHES.map(c=>`<span class="ll-swatch" style="background:${c}" data-swatch="${i}" data-color="${c}"></span>`).join('')}</div>
    </div>
  `;
}
function renderLightingChannels(){
  const areaWrap = document.getElementById('llAreaChannels');
  const cycWrap = document.getElementById('llCycChannels');
  areaWrap.innerHTML = LL_AREA_DEFS.map((def,i)=>llChannelStripHtml(def,i)).join('');
  cycWrap.innerHTML = LL_CYC_DEFS.map((def,j)=>llChannelStripHtml(def, LL_AREA_DEFS.length+j)).join('');
  const editable = canUseLightingLab();
  LL_CHANNELS.forEach((def,i)=>{
    const fader = document.getElementById('llFader-'+i);
    const colorInput = document.getElementById('llColor-'+i);
    fader.disabled = !editable; colorInput.disabled = !editable;
    fader.value = ui.llState[i].intensity;
    colorInput.value = ui.llState[i].color;
    fader.addEventListener('input', ()=>{
      ui.llState[i].intensity = parseInt(fader.value);
      document.getElementById('llPct-'+i).textContent = fader.value+'%';
      llApplyChannel(i);
    });
    colorInput.addEventListener('input', ()=>{
      ui.llState[i].color = colorInput.value;
      llApplyChannel(i);
    });
  });
  document.querySelectorAll('[data-swatch]').forEach(sw=>sw.addEventListener('click', ()=>{
    if(!editable) return;
    const i = parseInt(sw.dataset.swatch);
    ui.llState[i].color = sw.dataset.color;
    document.getElementById('llColor-'+i).value = sw.dataset.color;
    llApplyChannel(i);
  }));
}
async function llSaveCue(){
  if(!canUseLightingLab()){ toast('Sign in to save a cue'); return; }
  const name = document.getElementById('llCueName').value.trim();
  if(!name){ toast('Give the cue a name'); return; }
  const author = currentUser()?.name || authUser?.displayName || 'Someone';
  state.lightingCues.push({
    id:cryptoId(), name, channels: JSON.parse(JSON.stringify(ui.llState)),
    authorName:author, createdAt:new Date().toISOString()
  });
  await saveState();
  document.getElementById('llCueName').value = '';
  renderLightingCueList();
  toast('Cue saved');
}
function exportLightingCuesCsv(){
  const cues = state.lightingCues || [];
  if(!cues.length){ toast('No cues to export yet'); return; }
  const rows = [];
  cues.forEach(c=>{
    LL_CHANNELS.forEach((def,i)=>{
      const ch = c.channels[i];
      if(!ch || ch.intensity<=0) return; // only channels actually contributing to the look
      rows.push([c.name, def.label, Math.round(ch.intensity), ch.color, c.authorName, fmtDateTime(c.createdAt), c.updatedAt?fmtDateTime(c.updatedAt):'']);
    });
  });
  if(!rows.length){ toast('Every saved cue is blackout — nothing to export'); return; }
  downloadCsvRows(['Cue Name','Channel','Intensity (%)','Color','Created By','Created At','Last Updated'], rows, `lighting-cues-${todayISO()}.csv`);
}
function printLightingLook(){
  if(!llRenderer || !ui.llState){ toast('Nothing to print yet'); return; }
  const active = LL_CHANNELS.filter((def,i)=>ui.llState[i] && ui.llState[i].intensity>0);
  if(!active.length){ toast('Board is at blackout — nothing to print'); return; }
  llRenderer.render(llScene, llCamera); // ensure the buffer reflects the current camera angle
  const dataUrl = llRenderer.domElement.toDataURL('image/png');
  const rows = LL_CHANNELS.map((def,i)=>{
    const st = ui.llState[i];
    if(!st || st.intensity<=0) return '';
    return `<tr><td>${escapeHtml(def.label)}</td><td>${Math.round(st.intensity)}%</td><td><span style="display:inline-block;width:14px;height:14px;border:1px solid #999;background:${st.color};vertical-align:middle; margin-right:6px;"></span>${st.color}</td></tr>`;
  }).join('');
  const body = `
    <h1>${escapeHtml(state.productionName)}</h1>
    <div class="meta">Lighting Lab — Current Look — Printed ${new Date().toLocaleDateString()}</div>
    <img src="${dataUrl}" style="width:100%; border:1px solid #999; margin-bottom:16px;">
    <h2>Active Channels</h2>
    <table><thead><tr><th>Channel</th><th>Intensity</th><th>Color</th></tr></thead><tbody>${rows}</tbody></table>
  `;
  openPrintWindow(`${state.productionName} — Lighting Look`, body);
}
function llCueChannelSummary(cue){
  const active = LL_CHANNELS.map((def,i)=>({ def, ch: cue.channels[i] })).filter(x=>x.ch && x.ch.intensity>0);
  if(!active.length) return `<div style="font-size:11px; color:var(--paper-dim); margin-top:4px;">All channels at 0% (blackout)</div>`;
  return `<div style="display:flex; flex-wrap:wrap; gap:5px; margin-top:6px;">${active.map(x=>
    `<span class="mono" style="font-size:10.5px; background:rgba(0,0,0,0.25); border:1px solid var(--line); border-radius:10px; padding:2px 8px; display:inline-flex; align-items:center; gap:5px;">
      <span style="width:8px;height:8px;border-radius:2px;background:${x.ch.color}; display:inline-block;"></span>${escapeHtml(x.def.label)}: ${Math.round(x.ch.intensity)}%
    </span>`
  ).join('')}</div>`;
}
function renderLightingCueList(){
  const list = document.getElementById('llCueList');
  const cues = state.lightingCues || [];
  if(!cues.length){ list.innerHTML = `<div class="empty-state">No cues saved yet.</div>`; return; }
  const editable = canUseLightingLab();
  list.innerHTML = [...cues].reverse().map(c=>`
    <div class="ll-cue-item" style="flex-direction:column; align-items:stretch;">
      <div style="display:flex; justify-content:space-between; align-items:flex-start; gap:8px;">
        <span><b>${escapeHtml(c.name)}</b><br><span class="mono" style="font-size:10.5px;color:var(--paper-dim);">${escapeHtml(c.authorName)} · ${fmtDateTime(c.createdAt)}${c.updatedAt?' · edited '+fmtDateTime(c.updatedAt):''}</span></span>
        <span style="display:flex; gap:6px; flex-wrap:wrap; justify-content:flex-end; flex-shrink:0;">
          ${editable?`<button class="btn ghost small" data-go="${c.id}">Go</button><button class="btn ghost small" data-snap="${c.id}">Snap</button><button class="btn ghost small" data-update="${c.id}">Update</button><button class="btn ghost small" data-rename="${c.id}">Rename</button><button class="btn danger small" data-delcue="${c.id}">✕</button>`:''}
        </span>
      </div>
      ${llCueChannelSummary(c)}
    </div>
  `).join('');
  list.querySelectorAll('[data-go]').forEach(btn=>btn.addEventListener('click', ()=>{
    const c = cues.find(x=>x.id===btn.dataset.go); if(!c) return;
    LL_CHANNELS.forEach((def,i)=>{ ui.llState[i].color = c.channels[i].color; llLights[i].color.set(c.channels[i].color); });
    llRefreshChannelUI();
    llCrossfade = { fromIntensity: ui.llState.map(s=>s.intensity), toIntensity: c.channels.map(s=>s.intensity), startTime:performance.now(), duration:2200 };
    toast(`Going to "${escapeHtml(c.name)}"…`);
  }));
  list.querySelectorAll('[data-snap]').forEach(btn=>btn.addEventListener('click', ()=>{
    const c = cues.find(x=>x.id===btn.dataset.snap); if(!c) return;
    LL_CHANNELS.forEach((def,i)=>{ ui.llState[i] = JSON.parse(JSON.stringify(c.channels[i])); llApplyChannel(i); });
    llRefreshChannelUI();
    toast(`Snapped to "${escapeHtml(c.name)}"`);
  }));
  list.querySelectorAll('[data-update]').forEach(btn=>btn.addEventListener('click', async ()=>{
    const c = cues.find(x=>x.id===btn.dataset.update); if(!c) return;
    if(!confirm(`Overwrite "${escapeHtml(c.name)}" with the board's current look? This replaces its saved levels and colors.`)) return;
    c.channels = JSON.parse(JSON.stringify(ui.llState));
    c.updatedAt = new Date().toISOString();
    await saveState(); renderLightingCueList();
    toast(`"${escapeHtml(c.name)}" updated to the current look`);
  }));
  list.querySelectorAll('[data-rename]').forEach(btn=>btn.addEventListener('click', async ()=>{
    const c = cues.find(x=>x.id===btn.dataset.rename); if(!c) return;
    const newName = prompt('Rename cue:', c.name);
    if(newName===null) return;
    const trimmed = newName.trim();
    if(!trimmed){ toast('Name cannot be empty'); return; }
    c.name = trimmed;
    await saveState(); renderLightingCueList();
    toast('Cue renamed');
  }));
  list.querySelectorAll('[data-delcue]').forEach(btn=>btn.addEventListener('click', async ()=>{
    if(!confirm('Delete this cue?')) return;
    state.lightingCues = state.lightingCues.filter(x=>x.id!==btn.dataset.delcue);
    await saveState(); renderLightingCueList();
    toast('Cue deleted');
  }));
}
function syncLightingPieces(){
  if(!llScene) return;
  const THREE = THREE_MOD;
  const ids = new Set((ui.llPieces||[]).map(p=>p.id));
  Object.keys(llPieceMeshes).forEach(id=>{
    if(!ids.has(id)){ llScene.remove(llPieceMeshes[id]); delete llPieceMeshes[id]; }
  });
  (ui.llPieces||[]).forEach(p=>{
    let mesh = llPieceMeshes[p.id];
    if(!mesh){
      mesh = p.type==='cube' ? buildLLCubeMesh(THREE, p.color) : buildLLActorMesh(THREE, p.color);
      mesh.userData.llPieceId = p.id;
      mesh.traverse(o=>{ o.userData.llPieceId = p.id; });
      llScene.add(mesh);
      llPieceMeshes[p.id] = mesh;
    }
    mesh.position.x = p.x; mesh.position.z = p.z;
  });
}
function addLightingPiece(type){
  if(!canUseLightingLab()){ toast('Sign in to add practice pieces'); return; }
  if(!ui.llPieces) ui.llPieces = [];
  ui.llPieces.push({ id:cryptoId(), type, x:(Math.random()-0.5)*10, z:(Math.random()-0.5)*6 });
  syncLightingPieces();
}
function clearLightingPieces(){
  ui.llPieces = [];
  syncLightingPieces();
}
async function renderLightingLabView(){
  const canView = canUseLightingLab();
  document.getElementById('lightingLabLockedMsg').style.display = canView ? 'none' : 'block';
  document.getElementById('lightingLabWrap').style.display = canView ? 'block' : 'none';
  if(!canView){ pauseLightingLabScene(); return; }
  if(!ui.llState) ui.llState = LL_CHANNELS.map(()=>({ intensity:0, color:'#ffffff' }));
  if(!ui.llPieces) ui.llPieces = [];
  await initLightingLabSceneIfNeeded();
  if(!llAnimFrame) llAnimFrame = requestAnimationFrame(llAnimateFrame);
  renderLightingChannels();
  LL_CHANNELS.forEach((def,i)=>llApplyChannel(i));
  syncLightingPieces();
  renderLightingCueList();
}

// ---------------- BLOCKING ASSISTANT (live 2D top-down board, Director + Stage Mgmt only) ----------------
function canUseBlockingAssistant(){ return isDirectorOrStageMgmt(); }
function bkDefaultColor(type){ return type==='door' ? '#8B5E3C' : type==='cube' ? '#9BB8D3' : type==='number' ? '#00BCD4' : '#E8A33D'; }
function bkNextNumber(){
  const nums = (blockingCache.pieces||[]).filter(p=>p.type==='number').map(p=>parseInt(p.label)||0);
  return (nums.length ? Math.max(...nums) : 0) + 1;
}
function bkCastRosterPrompt(currentLabel){
  const castMembers = (state.crew||[]).filter(c=>(c.departments||[]).includes('cast'));
  if(!castMembers.length) return prompt('Actor / character name:', currentLabel||'');
  const listText = castMembers.map((c,i)=>`${i+1}) ${escapeHtml(c.name)}${c.castRole?' as '+c.castRole:''}`).join('\n');
  const val = prompt(`Type a name, or type a number to pick from your Cast roster:\n${listText}`, currentLabel||'');
  if(val===null) return null;
  const trimmed = val.trim();
  const num = parseInt(trimmed);
  if(!isNaN(num) && String(num)===trimmed && castMembers[num-1]) return castMembers[num-1].castRole || castMembers[num-1].name;
  return trimmed;
}

let blockingUnsubscribe = null;
let blockingCache = { pieces:[], strokes:[] };
let blockingDraggingId = null;
let blockingPendingRerender = false;
let bkDrawCtx = null, bkDrawCanvasInitialized = false, bkDrawing = false, bkCurrentStroke = null;
const BK_PEN_COLORS = ['#1a1a1a','#c0392b','#2e6da4','#2e8b57','#ffffff'];

function blockingDocId(){ return currentProductionId; }
function normalizeBlockingCache(){ if(!blockingCache.pieces) blockingCache.pieces = []; if(!blockingCache.strokes) blockingCache.strokes = []; }
function stopBlockingListener(){ if(blockingUnsubscribe){ blockingUnsubscribe(); blockingUnsubscribe = null; } }
async function startBlockingListener(){
  stopBlockingListener();
  if(!window.__fb || !currentProductionId) return;
  const ref = window.__fb.doc(window.__fb.db, 'blocking_boards', blockingDocId());
  blockingUnsubscribe = window.__fb.onSnapshot(ref, (snap)=>{
    blockingCache = snap.exists() ? snap.data() : { pieces:[], strokes:[] };
    normalizeBlockingCache();
    if(!document.getElementById('view-blocking').classList.contains('active')) return;
    if(blockingDraggingId){ blockingPendingRerender = true; return; }
    renderBlockingBoard();
  }, (err)=>console.warn('Blocking board live listener failed (check Firestore rules include "blocking_boards")', err));
}
async function saveBlockingData(){
  try{ if(window.__fb && currentProductionId){ const ref = window.__fb.doc(window.__fb.db, 'blocking_boards', blockingDocId()); await window.__fb.setDoc(ref, JSON.parse(JSON.stringify(blockingCache))); } }
  catch(e){ console.warn('Blocking board save failed', e); }
}
function bkAddPiece(type){
  if(!canUseBlockingAssistant()) return;
  const author = currentUser()?.name || authUser?.displayName || 'Someone';
  const defaults = { actor:{label:'Actor', w:70, h:60}, cube:{label:'Cube', w:70, h:60}, door:{label:'Entrance', w:70, h:50}, number:{label:String(bkNextNumber()), w:50, h:44}, sticky:{label:'', w:120, h:80} };
  const d = defaults[type];
  const piece = {
    id:cryptoId(), type, label:d.label, text:'',
    x: 60+Math.round(Math.random()*300), y: 60+Math.round(Math.random()*200),
    authorName:author, authorId: currentUser()?.id||null
  };
  if(type!=='sticky') piece.color = bkDefaultColor(type);
  blockingCache.pieces.push(piece);
  renderBlockingBoard();
  saveBlockingData();
}
function renderBlockingBoard(){
  const stage = document.getElementById('bkStage');
  if(!stage) return;
  stage.querySelectorAll('.bk-marker, .bk-sticky').forEach(el=>el.remove());
  const editable = canUseBlockingAssistant();
  (blockingCache.pieces||[]).forEach(p=>{
    if(p.type==='sticky'){
      const el = document.createElement('div');
      el.className = 'sticky-note bk-sticky';
      el.style.left = p.x+'px'; el.style.top = p.y+'px'; el.style.background = '#E8C468';
      el.innerHTML = `
        <div class="sn-handle" title="Drag to move">⠿⠿ drag</div>
        <textarea placeholder="Note..." ${editable?'':'readonly'}>${escapeHtml(p.text||'')}</textarea>
        ${editable?`<button class="sn-del">✕</button>`:''}
      `;
      stage.appendChild(el);
      const ta = el.querySelector('textarea');
      if(editable) ta.addEventListener('change', async ()=>{ p.text = ta.value; await saveBlockingData(); });
      const delBtn = el.querySelector('.sn-del');
      if(delBtn) delBtn.addEventListener('click', async (e)=>{ e.stopPropagation(); blockingCache.pieces = blockingCache.pieces.filter(x=>x.id!==p.id); renderBlockingBoard(); await saveBlockingData(); });
      bkWireDrag(el, p, editable, el.querySelector('.sn-handle'));
    } else {
      const color = p.color || bkDefaultColor(p.type);
      const placeholder = p.type==='actor' ? 'Actor' : p.type==='door' ? 'Entrance' : p.type==='number' ? '1' : 'Cube';
      const el = document.createElement('div');
      el.className = 'bk-marker'; el.dataset.type = p.type;
      el.style.left = p.x+'px'; el.style.top = p.y+'px';
      el.innerHTML = `
        <div class="bk-marker-shape" title="Drag to move" style="background:${color};">${p.type==='door'?'🚪':escapeHtml((p.label||'').slice(0,4))}</div>
        <div class="bk-marker-label" title="Click to rename">${p.type==='number'?'':escapeHtml(p.label||placeholder)}</div>
        ${editable?`<input type="color" class="bk-marker-color" value="${color}" title="Change color">`:''}
        ${editable?`<button class="bk-marker-del">✕</button>`:''}
      `;
      stage.appendChild(el);
      const labelEl = el.querySelector('.bk-marker-label');
      if(editable){
        labelEl.addEventListener('click', async ()=>{
          let val;
          if(p.type==='actor') val = bkCastRosterPrompt(p.label);
          else if(p.type==='number') val = prompt('Sequence number:', p.label||'1');
          else if(p.type==='door') val = prompt('Entrance label (e.g. SR Door, US Entrance):', p.label||'');
          else val = prompt('What does this cube represent?', p.label||'');
          if(val===null) return;
          p.label = val.trim(); renderBlockingBoard(); await saveBlockingData();
        });
      }
      const colorInput = el.querySelector('.bk-marker-color');
      if(colorInput) colorInput.addEventListener('input', async ()=>{ p.color = colorInput.value; el.querySelector('.bk-marker-shape').style.background = colorInput.value; await saveBlockingData(); });
      const delBtn = el.querySelector('.bk-marker-del');
      if(delBtn) delBtn.addEventListener('click', async (e)=>{ e.stopPropagation(); blockingCache.pieces = blockingCache.pieces.filter(x=>x.id!==p.id); renderBlockingBoard(); await saveBlockingData(); });
      bkWireDrag(el, p, editable, el.querySelector('.bk-marker-shape'));
    }
  });
  bkRedrawStrokes();
}
function bkWireDrag(el, piece, editable, handle){
  if(!editable || !handle) return;
  let dragging=false, startX=0, startY=0, origX=piece.x, origY=piece.y;
  handle.addEventListener('pointerdown', (e)=>{
    if(ui.bkTool && ui.bkTool!=='select') return; // pen/eraser active — don't drag markers
    dragging = true; blockingDraggingId = piece.id; el.classList.add('dragging');
    startX = e.clientX; startY = e.clientY; origX = piece.x; origY = piece.y;
    handle.setPointerCapture(e.pointerId);
  });
  handle.addEventListener('pointermove', (e)=>{
    if(!dragging) return;
    const dx = e.clientX-startX, dy = e.clientY-startY;
    const nx = Math.max(0, origX+dx), ny = Math.max(0, origY+dy);
    el.style.left = nx+'px'; el.style.top = ny+'px';
    piece._pendingX = nx; piece._pendingY = ny;
  });
  handle.addEventListener('pointerup', async ()=>{
    if(!dragging) return;
    dragging = false; blockingDraggingId = null; el.classList.remove('dragging');
    if(piece._pendingX!==undefined){ piece.x = piece._pendingX; piece.y = piece._pendingY; delete piece._pendingX; delete piece._pendingY; }
    await saveBlockingData();
    if(blockingPendingRerender){ blockingPendingRerender=false; renderBlockingBoard(); }
  });
}
// ---- Pen/eraser drawing on the blocking board (movement paths, circles, arrows, etc.) ----
function bkRedrawStrokes(){
  const canvas = document.getElementById('bkDrawCanvas');
  if(!canvas) return;
  if(!bkDrawCtx) bkDrawCtx = canvas.getContext('2d');
  bkDrawCtx.clearRect(0,0,canvas.width,canvas.height);
  (blockingCache.strokes||[]).forEach(s=>{
    if(!s.points || s.points.length<2) return;
    bkDrawCtx.beginPath();
    bkDrawCtx.strokeStyle = s.color; bkDrawCtx.lineWidth = s.width; bkDrawCtx.lineCap='round'; bkDrawCtx.lineJoin='round';
    bkDrawCtx.moveTo(s.points[0].x, s.points[0].y);
    for(let i=1;i<s.points.length;i++) bkDrawCtx.lineTo(s.points[i].x, s.points[i].y);
    bkDrawCtx.stroke();
  });
}
function bkEraseAtPoint(p){
  const threshold = 10;
  const before = (blockingCache.strokes||[]).length;
  blockingCache.strokes = (blockingCache.strokes||[]).filter(s=> !s.points.some(pt => Math.hypot(pt.x-p.x, pt.y-p.y) < threshold));
  if(blockingCache.strokes.length !== before) bkRedrawStrokes();
}
function initBkDrawCanvasIfNeeded(){
  if(bkDrawCanvasInitialized) return;
  bkDrawCanvasInitialized = true;
  const canvas = document.getElementById('bkDrawCanvas');
  bkDrawCtx = canvas.getContext('2d');
  function canvasPoint(e){
    const rect = canvas.getBoundingClientRect();
    const scaleX = canvas.width/rect.width, scaleY = canvas.height/rect.height;
    return { x:(e.clientX-rect.left)*scaleX, y:(e.clientY-rect.top)*scaleY };
  }
  canvas.addEventListener('pointerdown', (e)=>{
    if(!canUseBlockingAssistant() || !ui.bkTool || ui.bkTool==='select') return;
    bkDrawing = true;
    const p = canvasPoint(e);
    if(ui.bkTool==='eraser'){ bkEraseAtPoint(p); }
    else {
      bkCurrentStroke = { points:[p], color: ui.bkPenColor||BK_PEN_COLORS[0], width: ui.bkPenWidth||3 };
      bkDrawCtx.beginPath(); bkDrawCtx.strokeStyle=bkCurrentStroke.color; bkDrawCtx.lineWidth=bkCurrentStroke.width; bkDrawCtx.lineCap='round'; bkDrawCtx.lineJoin='round';
      bkDrawCtx.moveTo(p.x,p.y);
    }
    canvas.setPointerCapture(e.pointerId);
  });
  canvas.addEventListener('pointermove', (e)=>{
    if(!bkDrawing) return;
    const p = canvasPoint(e);
    if(ui.bkTool==='eraser'){ bkEraseAtPoint(p); }
    else if(bkCurrentStroke){ bkCurrentStroke.points.push(p); bkDrawCtx.lineTo(p.x,p.y); bkDrawCtx.stroke(); }
  });
  canvas.addEventListener('pointerup', async ()=>{
    bkDrawing = false;
    if(bkCurrentStroke && bkCurrentStroke.points.length>1){
      if(!blockingCache.strokes) blockingCache.strokes = [];
      blockingCache.strokes.push(bkCurrentStroke);
      await saveBlockingData();
    }
    bkCurrentStroke = null;
  });
}
function bkSetTool(tool){
  ui.bkTool = tool;
  const canvas = document.getElementById('bkDrawCanvas');
  if(canvas) canvas.style.pointerEvents = tool==='select' ? 'none' : 'auto';
  document.querySelectorAll('#bkDrawToolbar [data-bktool]').forEach(b=>b.classList.toggle('active', b.dataset.bktool===tool));
  document.getElementById('bkPenOptionsRow').style.display = (tool==='pen') ? 'flex' : 'none';
}
function bkPiecesSnapshotHtml(pieces, strokes){
  return `<div style="position:relative; width:900px; height:560px; border:2px solid #111; background:#eef2f7; margin:0 auto 16px;">
    <div style="position:absolute; left:450px; top:0; bottom:0; width:1px; border-left:1px dashed #999;"></div>
    ${(pieces||[]).map(p=>{
      if(p.type==='sticky') return `<div style="position:absolute; left:${p.x}px; top:${p.y}px; width:110px; background:#fbe08a; border:1px solid #997; padding:5px; font-size:10px; white-space:pre-wrap;">${escapeHtml(p.text||'')}</div>`;
      const color = p.color || bkDefaultColor(p.type);
      const radius = (p.type==='actor' || p.type==='number') ? '50%' : '4px';
      const size = p.type==='number' ? 32 : 60;
      const fontSize = p.type==='number' ? 14 : 9;
      const label = p.type==='door' ? '🚪 '+(p.label||'') : (p.label||'');
      return `<div style="position:absolute; left:${p.x}px; top:${p.y}px; width:${size}px; height:${p.type==='number'?size:50}px; display:flex; align-items:center; justify-content:center; text-align:center; font-size:${fontSize}px; font-weight:${p.type==='number'?700:400}; border:2px solid #111; border-radius:${radius}; background:${color};">${escapeHtml(label)}</div>`;
    }).join('')}
    <svg style="position:absolute; left:0; top:0; width:900px; height:560px; pointer-events:none;">
      ${(strokes||[]).map(s=>{
        if(!s.points || s.points.length<2) return '';
        const d = s.points.map((pt,i)=>(i===0?'M':'L')+pt.x+','+pt.y).join(' ');
        return `<path d="${d}" fill="none" stroke="${s.color}" stroke-width="${s.width}" stroke-linecap="round" stroke-linejoin="round"/>`;
      }).join('')}
    </svg>
  </div>`;
}
function printBlockingBoard(pieces, title, strokes){
  const body = `<h1>${escapeHtml(state.productionName)}</h1><div class="meta">${escapeHtml(title)} — Printed ${new Date().toLocaleDateString()}</div>${bkPiecesSnapshotHtml(pieces, strokes)}`;
  openPrintWindow(`${state.productionName} — ${title}`, body);
}
function bkSortedNotes(){ return [...(state.blockingNotes||[])].sort((a,b)=>(a.pageNumber||'').localeCompare(b.pageNumber||'', undefined, {numeric:true})); }
async function bkSaveNote(){
  if(!canUseBlockingAssistant()) return;
  const pageNumber = document.getElementById('bkPageNumber').value.trim();
  const sceneLabel = document.getElementById('bkSceneLabel').value.trim();
  if(!pageNumber && !sceneLabel){ toast('Give it a page number or scene label'); return; }
  if(!(blockingCache.pieces||[]).length && !(blockingCache.strokes||[]).length){ toast('Add something to the board first'); return; }
  const notesText = document.getElementById('bkNoteText').value.trim();
  if(ui.bkEditingNoteId){
    const n = (state.blockingNotes||[]).find(x=>x.id===ui.bkEditingNoteId);
    if(n){
      n.pageNumber = pageNumber; n.sceneLabel = sceneLabel; n.notes = notesText;
      n.pieces = JSON.parse(JSON.stringify(blockingCache.pieces));
      n.strokes = JSON.parse(JSON.stringify(blockingCache.strokes||[]));
      n.updatedAt = new Date().toISOString();
      await saveState();
      toast('Blocking note updated');
    }
    bkCancelEdit();
  } else {
    const author = currentUser()?.name || authUser?.displayName || 'Someone';
    state.blockingNotes.push({
      id:cryptoId(), pageNumber, sceneLabel, notes: notesText,
      pieces: JSON.parse(JSON.stringify(blockingCache.pieces)),
      strokes: JSON.parse(JSON.stringify(blockingCache.strokes||[])),
      authorName:author, createdAt:new Date().toISOString()
    });
    await saveState();
    document.getElementById('bkPageNumber').value=''; document.getElementById('bkSceneLabel').value=''; document.getElementById('bkNoteText').value='';
    toast('Blocking note saved');
  }
  renderBlockingNoteList();
}
function bkCancelEdit(){
  ui.bkEditingNoteId = null;
  document.getElementById('bkFormTitle').textContent = 'Save as Blocking Note';
  document.getElementById('bkSaveNoteBtn').textContent = 'Save This Layout as a Blocking Note';
  document.getElementById('bkCancelEditBtn').style.display = 'none';
  document.getElementById('bkPageNumber').value=''; document.getElementById('bkSceneLabel').value=''; document.getElementById('bkNoteText').value='';
}
function renderBlockingNoteList(){
  const list = document.getElementById('bkNoteList');
  const notes = bkSortedNotes();
  if(!notes.length){ list.innerHTML = `<div class="empty-state">No blocking notes saved yet.</div>`; return; }
  list.innerHTML = notes.map(n=>`
    <div class="bk-note-item">
      <div style="display:flex; justify-content:space-between; align-items:flex-start; gap:8px;">
        <span><b>${n.pageNumber?'Pg '+escapeHtml(n.pageNumber)+' — ':''}${escapeHtml(n.sceneLabel||'Untitled')}</b><br><span class="mono" style="font-size:10.5px;color:var(--paper-dim);">${escapeHtml(n.authorName)} · ${fmtDateTime(n.createdAt)}${n.updatedAt?' · edited '+fmtDateTime(n.updatedAt):''}</span></span>
        <span style="display:flex; gap:6px; flex-wrap:wrap; justify-content:flex-end;">
          <button class="btn ghost small" data-loadnote="${n.id}">Load to Board</button>
          <button class="btn ghost small" data-editnote="${n.id}">Edit</button>
          <button class="btn ghost small" data-duplicatenote="${n.id}">Duplicate</button>
          <button class="btn ghost small" data-printnote="${n.id}">🖨 Print</button>
          <button class="btn danger small" data-delnote="${n.id}">✕</button>
        </span>
      </div>
      ${n.notes?`<div style="font-size:12px; margin-top:5px; color:var(--paper-dim);">${escapeHtml(n.notes)}</div>`:''}
    </div>
  `).join('');
  list.querySelectorAll('[data-loadnote]').forEach(btn=>btn.addEventListener('click', async ()=>{
    const n = notes.find(x=>x.id===btn.dataset.loadnote); if(!n) return;
    if(((blockingCache.pieces||[]).length || (blockingCache.strokes||[]).length) && !confirm('Replace the current board with this saved layout?')) return;
    blockingCache.pieces = JSON.parse(JSON.stringify(n.pieces));
    blockingCache.strokes = JSON.parse(JSON.stringify(n.strokes||[]));
    ui.bkCurrentNoteIndex = notes.findIndex(x=>x.id===n.id);
    renderBlockingBoard();
    renderBkPageNav();
    await saveBlockingData();
    toast(`Loaded "${n.sceneLabel||n.pageNumber}" to the live board`);
  }));
  list.querySelectorAll('[data-editnote]').forEach(btn=>btn.addEventListener('click', async ()=>{
    const n = notes.find(x=>x.id===btn.dataset.editnote); if(!n) return;
    if(((blockingCache.pieces||[]).length || (blockingCache.strokes||[]).length) && !confirm('Load this note onto the live board for editing? Current board contents will be replaced.')) return;
    blockingCache.pieces = JSON.parse(JSON.stringify(n.pieces));
    blockingCache.strokes = JSON.parse(JSON.stringify(n.strokes||[]));
    ui.bkEditingNoteId = n.id;
    document.getElementById('bkFormTitle').textContent = 'Editing: '+(n.sceneLabel||('Pg '+n.pageNumber)||'Blocking Note');
    document.getElementById('bkSaveNoteBtn').textContent = 'Update Blocking Note';
    document.getElementById('bkCancelEditBtn').style.display = 'inline-block';
    document.getElementById('bkPageNumber').value = n.pageNumber||'';
    document.getElementById('bkSceneLabel').value = n.sceneLabel||'';
    document.getElementById('bkNoteText').value = n.notes||'';
    renderBlockingBoard();
    await saveBlockingData();
    document.getElementById('bkFormTitle').scrollIntoView({behavior:'smooth', block:'center'});
    toast('Adjust the board and/or the details below, then click "Update Blocking Note"');
  }));
  list.querySelectorAll('[data-duplicatenote]').forEach(btn=>btn.addEventListener('click', ()=>{
    const n = notes.find(x=>x.id===btn.dataset.duplicatenote); if(!n) return;
    bkCancelEdit();
    document.getElementById('bkSceneLabel').value = (n.sceneLabel||'') + ' (copy)';
    document.getElementById('bkNoteText').value = n.notes||'';
    if(((blockingCache.pieces||[]).length || (blockingCache.strokes||[]).length) && !confirm('Load this note\'s layout onto the board to start your copy? Current board contents will be replaced.')) return;
    blockingCache.pieces = JSON.parse(JSON.stringify(n.pieces));
    blockingCache.strokes = JSON.parse(JSON.stringify(n.strokes||[]));
    renderBlockingBoard();
    saveBlockingData();
    toast('Loaded as a starting point — adjust it, then save with a new page number');
  }));
  list.querySelectorAll('[data-printnote]').forEach(btn=>btn.addEventListener('click', ()=>{
    const n = notes.find(x=>x.id===btn.dataset.printnote); if(!n) return;
    printBlockingBoard(n.pieces, `Blocking — Pg ${n.pageNumber||'?'} — ${n.sceneLabel||''}`, n.strokes);
  }));
  list.querySelectorAll('[data-delnote]').forEach(btn=>btn.addEventListener('click', async ()=>{
    if(!confirm('Delete this blocking note?')) return;
    if(ui.bkEditingNoteId===btn.dataset.delnote) bkCancelEdit();
    state.blockingNotes = state.blockingNotes.filter(x=>x.id!==btn.dataset.delnote);
    await saveState(); renderBlockingNoteList(); renderBkPageNav();
    toast('Blocking note deleted');
  }));
  renderBkPageNav();
}
function renderBkPageNav(){
  const nav = document.getElementById('bkPageNav');
  const notes = bkSortedNotes();
  if(notes.length < 2){ nav.style.display = 'none'; return; }
  nav.style.display = 'flex';
  const idx = (ui.bkCurrentNoteIndex!=null && ui.bkCurrentNoteIndex>=0 && ui.bkCurrentNoteIndex<notes.length) ? ui.bkCurrentNoteIndex : null;
  document.getElementById('bkPageNavLabel').textContent = idx!=null
    ? `Pg ${notes[idx].pageNumber||'?'} — ${notes[idx].sceneLabel||'Untitled'} (${idx+1} of ${notes.length})`
    : `${notes.length} saved pages — use Prev/Next to step through them`;
  document.getElementById('bkPrevPageBtn').disabled = idx===0;
  document.getElementById('bkNextPageBtn').disabled = idx===notes.length-1;
}
async function bkGoToNoteByIndex(idx){
  const notes = bkSortedNotes();
  if(idx<0 || idx>=notes.length) return;
  const n = notes[idx];
  if(((blockingCache.pieces||[]).length || (blockingCache.strokes||[]).length) && !confirm(`Load Pg ${n.pageNumber||'?'} — ${n.sceneLabel||'Untitled'}? Current board contents will be replaced.`)) return;
  blockingCache.pieces = JSON.parse(JSON.stringify(n.pieces));
  blockingCache.strokes = JSON.parse(JSON.stringify(n.strokes||[]));
  ui.bkCurrentNoteIndex = idx;
  bkCancelEdit();
  renderBlockingBoard();
  await saveBlockingData();
  renderBkPageNav();
  toast(`Viewing: Pg ${n.pageNumber||'?'} — ${n.sceneLabel||''}`);
}
function pauseBlockingScene(){ stopBlockingListener(); }
async function renderBlockingView(){
  const canView = canUseBlockingAssistant();
  document.getElementById('blockingLockedMsg').style.display = canView ? 'none' : 'block';
  document.getElementById('blockingWrap').style.display = canView ? 'block' : 'none';
  if(!canView){ pauseBlockingScene(); return; }
  if(!ui.bkTool) ui.bkTool = 'select';
  if(!ui.bkPenColor) ui.bkPenColor = BK_PEN_COLORS[0];
  if(!ui.bkPenWidth) ui.bkPenWidth = 3;
  if(ui.bkEditingNoteId===undefined) ui.bkEditingNoteId = null;
  if(ui.bkCurrentNoteIndex===undefined) ui.bkCurrentNoteIndex = null;
  initBkDrawCanvasIfNeeded();
  bkSetTool(ui.bkTool);
  await startBlockingListener();
  renderBlockingBoard();
  renderBlockingNoteList();
}

function renderReportDetail(r, dep, depState, container){
  const teamNames = r.teamMemberIds.map(id=>{ const c=state.crew.find(x=>x.id===id); return c?c.name:'—'; });
  const nonContribNames = r.nonContributorIds.map(id=>{ const c=state.crew.find(x=>x.id===id); return c?c.name:'—'; });
  container.innerHTML = `
    <div class="summary-line" style="display:flex; justify-content:space-between; padding:6px 0; border-bottom:1px dashed var(--line); font-size:13px;"><span>Team present</span><span class="mono">${teamNames.join(', ')||'—'}</span></div>
    ${(nonContribNames.length && isDirector())?`<div class="summary-line" style="display:flex; justify-content:space-between; padding:6px 0; border-bottom:1px dashed var(--line); font-size:13px; color:var(--red);"><span>Flagged non-contributors</span><span class="mono">${nonContribNames.join(', ')}</span></div>`:''}
    ${r.goal?`<p style="font-size:13px; margin-top:10px;"><b>Goal:</b> ${escapeHtml(r.goal)}</p>`:''}
    ${r.completedTasks?`<p style="font-size:13px; margin-top:10px;"><b>Completed:</b><br>${escapeHtml(r.completedTasks).replace(/\\n/g,'<br>')}</p>`:''}
    ${r.inProgressTasks?`<p style="font-size:13px;"><b>In progress:</b><br>${escapeHtml(r.inProgressTasks).replace(/\\n/g,'<br>')}</p>`:''}
    ${r.notes?`<p style="font-size:13px;"><b>Notes:</b> ${escapeHtml(r.notes)}</p>`:''}
    ${r.challenges?`<p style="font-size:13px;"><b>Challenges:</b> ${escapeHtml(r.challenges)}</p>`:''}
    ${r.teacherFeedback?`<div class="card" style="margin-top:10px;"><h3>Teacher Feedback</h3><p style="font-size:13px;">${escapeHtml(r.teacherFeedback)}</p></div>`:''}
    <div id="adjWrap-${r.id}"></div>
    <div id="actionsWrap-${r.id}" style="margin-top:12px;"></div>
  `;
  const adjWrap = container.querySelector(`#adjWrap-${r.id}`);
  if(isDirector() && r.individualAdjustments.length){
    adjWrap.innerHTML = `<h3 style="margin-top:14px;">Individual Grade Adjustments</h3>` + r.individualAdjustments.map(a=>`
      <div class="adjustment-row"><span style="flex:1;">${a.crewName}</span><span class="mono">${a.adjustedGrade}</span><span style="color:var(--paper-dim); flex:2;">${a.teacherNote||''}</span></div>
    `).join('');
  } else if(!isDirector()){
    const u = currentUser();
    const mine = u ? r.individualAdjustments.find(a=>a.crewId===u.id) : null;
    if(mine){
      adjWrap.innerHTML = `<div class="card" style="margin-top:10px; border-color:var(--amber);"><h3>Your Grade on This Report</h3>
        <p style="font-size:13px;">Adjusted to <b class="mono">${mine.adjustedGrade}</b>${mine.teacherNote?' — '+mine.teacherNote:''}</p></div>`;
    }
  }
  const actions = container.querySelector(`#actionsWrap-${r.id}`);
  if(!isDirector()) return;
  let html = '';
  if(r.status==='submitted') html += `<button class="btn ghost small" data-act="verify">Verify</button> `;
  if(r.status==='submitted' || r.status==='verified') html += `<button class="btn small" data-act="grade">Enter Final Grade & Feedback</button> <button class="btn danger small" data-act="return">Return for Revision</button> `;
  html += `<button class="btn ghost small" data-act="adjust" style="margin-top:8px;">+ Individual Grade Adjustment</button>`;
  actions.innerHTML = html;

  actions.querySelector('[data-act=verify]')?.addEventListener('click', async ()=>{
    r.status='verified'; r.verifiedBy = isDirector() ? (currentUser()?.name || 'Teacher') : ''; r.verifiedDate = new Date().toISOString();
    await saveState(); renderDepartments();
    toast('Report verified');
  });
  actions.querySelector('[data-act=grade]')?.addEventListener('click', async ()=>{
    const g = prompt('Final grade (0-100):', r.grade); if(g===null) return;
    const fb = prompt('Feedback for the team:', r.teacherFeedback||'') || '';
    r.grade = Math.max(0, Math.min(100, parseFloat(g)||0)); r.gradeLetter = letterFor(r.grade); r.teacherFeedback = fb; r.status='graded';
    logChange(`${dep.label} report for ${fmtDate(r.date)} graded: ${r.gradeLetter} (${r.grade}).`, {type:'deptClass', dept:dep.key, classPeriod:r.classPeriod});
    await saveState(); renderDepartments(); renderNotifications();
    await checkAndSendFailingGradeEmails(r, dep);
    await saveState();
    toast('Grade finalized');
  });
  actions.querySelector('[data-act=return]')?.addEventListener('click', async ()=>{
    r.status='returned';
    logChange(`${dep.label} report for ${fmtDate(r.date)} returned for revision.`, {type:'deptClass', dept:dep.key, classPeriod:r.classPeriod});
    await saveState(); renderDepartments(); renderNotifications();
    toast('Returned to team for revision');
  });
  actions.querySelector('[data-act=adjust]')?.addEventListener('click', async ()=>{
    if(!r.teamMemberIds.length){ toast('No team members listed on this report'); return; }
    const names = r.teamMemberIds.map(id=>state.crew.find(c=>c.id===id)?.name).filter(Boolean);
    const who = prompt(`Adjust grade for which team member?\\n${names.join(', ')}`); if(!who) return;
    const crewMember = state.crew.find(c=>c.name.toLowerCase()===who.trim().toLowerCase());
    if(!crewMember){ toast("Name not found on this report's team"); return; }
    const g = prompt(`Adjusted grade for ${escapeHtml(crewMember.name)}:`, r.grade); if(g===null) return;
    const note = prompt('Note (optional):','') || '';
    r.individualAdjustments.push({ crewId: crewMember.id, crewName: crewMember.name, adjustedGrade: parseFloat(g)||0, teacherNote: note });
    await saveState(); renderDepartments();
    await checkAndSendFailingGradeEmails(r, dep);
    await saveState();
    toast('Adjustment added');
  });
}

// ---------------- COSTUMES ----------------
let costumeDraft = { pieces: [] };
function renderCostumeMeasureGrid(){
  document.getElementById('cMeasureGrid').innerHTML = MEASURE_FIELDS.map(m=>`
    <div><label>${m.label}</label><input type="text" data-m="${m.key}"></div>
  `).join('');
}
function renderCostumePieces(){
  document.getElementById('cPiecesList').innerHTML = costumeDraft.pieces.map((p,idx)=>`
    <span class="piece-chip">${escapeHtml(p.name)} <span class="piece-status" data-idx="${idx}">${p.status}</span> <button data-del="${idx}" style="background:none;border:none;color:var(--paper-dim);cursor:pointer;">✕</button></span>
  `).join('');
  document.querySelectorAll('.piece-status').forEach(el=>el.addEventListener('click', ()=>{
    const idx = parseInt(el.dataset.idx);
    const cur = PIECE_STATUSES.indexOf(costumeDraft.pieces[idx].status);
    costumeDraft.pieces[idx].status = PIECE_STATUSES[(cur+1)%PIECE_STATUSES.length];
    renderCostumePieces();
  }));
  document.querySelectorAll('[data-del]').forEach(el=>el.addEventListener('click', ()=>{
    costumeDraft.pieces.splice(parseInt(el.dataset.del),1); renderCostumePieces();
  }));
}
function renderCostumesView(){
  const authorized = canViewCostumes();
  document.getElementById('costumeLockedMsg').style.display = authorized ? 'none' : 'block';
  document.getElementById('costumeAuthorizedWrap').style.display = authorized ? 'block' : 'none';
  if(!authorized) return;
  const list = document.getElementById('costumeList'); list.innerHTML = '';
  if(!state.costumeRecords.length){ list.innerHTML = `<div class="empty-state"><div class="lamp">🧵</div>No measurement records yet.</div>`; return; }
  state.costumeRecords.forEach(rec=>{
    const card = document.createElement('div'); card.className = 'costume-card';
    card.innerHTML = `
      <div style="display:flex; justify-content:space-between; align-items:center;">
        <div><b>${escapeHtml(rec.name)}</b>${rec.email?` <span class="mono" style="font-size:11px;color:var(--paper-dim);">${rec.email}</span>`:''}</div>
        ${isDirector() ? `<button class="task-del" data-id="${rec.id}">✕</button>` : ''}
      </div>
      <div class="costume-grid">${MEASURE_FIELDS.map(m=>rec[m.key]?`<div><label>${m.label}</label>${rec[m.key]}</div>`:'').join('')}</div>
      ${rec.pieces.length?`<div>${rec.pieces.map(p=>`<span class="piece-chip">${escapeHtml(p.name)} <span class="piece-status">${p.status}</span></span>`).join('')}</div>`:''}
      ${rec.notes?`<p style="font-size:12.5px;color:var(--paper-dim);margin-top:8px;">${escapeHtml(rec.notes)}</p>`:''}
    `;
    if(isDirector()){
      card.querySelector('.task-del').addEventListener('click', async ()=>{
        state.costumeRecords = state.costumeRecords.filter(r=>r.id!==rec.id);
        await saveState(); renderCostumesView();
      });
    }
    list.appendChild(card);
  });
}

// ---------------- NOTIFICATIONS ----------------
function visibleChangelog(){ return state.changelog.filter(canSeeNotification); }
function unreadCount(){
  const visible = visibleChangelog();
  if(!device.lastSeenChangelog) return visible.length;
  return visible.filter(n=>n.timestamp > device.lastSeenChangelog).length;
}
function renderNotifBadge(){ const n = unreadCount(); const badge = document.getElementById('notifBadge'); if(n>0){ badge.style.display='flex'; badge.textContent = n>9?'9+':n; } else badge.style.display='none'; }
function renderNotifications(){
  renderNotifBadge();
  document.getElementById('clearAllNotifsBtn').style.display = isDirector() ? 'inline-block' : 'none';
  const list = document.getElementById('notifList'); list.innerHTML = '';
  const visible = visibleChangelog();
  if(!visible.length){ list.innerHTML = `<div class="empty-state">No changes yet.</div>`; return; }
  visible.forEach(n=>{
    const unread = !device.lastSeenChangelog || n.timestamp > device.lastSeenChangelog;
    const item = document.createElement('div'); item.className = 'notif-item ' + (unread?'unread':'read');
    item.style.display = 'flex'; item.style.justifyContent = 'space-between'; item.style.alignItems = 'flex-start'; item.style.gap = '8px';
    item.innerHTML = `<div style="display:flex; gap:10px;"><div class="notif-dot"></div><div><div>${escapeHtml(n.message)}</div><div class="notif-time">${fmtDateTime(n.timestamp)}</div></div></div>${isDirector()?`<button class="task-del" data-del-notif="${n.id}">✕</button>`:''}`;
    list.appendChild(item);
  });
  if(isDirector()){
    list.querySelectorAll('[data-del-notif]').forEach(btn=>btn.addEventListener('click', async ()=>{
      state.changelog = state.changelog.filter(n=>n.id!==btn.dataset.delNotif);
      await saveState(); renderNotifications();
    }));
  }
}

// ---------------- SETUP ----------------
function renderSetup(){
  const noDirectorsYet = !(globalState.directorEmails||[]).length;
  document.getElementById('claimDirectorCard').style.display = (noDirectorsYet && authUser) ? 'block' : 'none';
  document.getElementById('dirAccessCard').style.display = isDirector() ? 'block' : 'none';
  document.getElementById('dirRecoveryWarning').style.display = (globalState.directorEmails||[]).length < 2 ? 'block' : 'none';
  renderPendingApprovals();
  document.getElementById('attendanceThreshold').value = globalState.attendanceAlertThreshold || 3;
  const behCfg = state.behaviorConfig || { pointsPerDay:20, daysPerWeek:5 };
  document.getElementById('behPointsPerDay').value = behCfg.pointsPerDay;
  document.getElementById('behDaysPerWeek').value = behCfg.daysPerWeek;
  const partCfg = state.participationConfig || { pointsPerDay:20, daysPerWeek:5 };
  document.getElementById('partPointsPerDay').value = partCfg.pointsPerDay;
  document.getElementById('partDaysPerWeek').value = partCfg.daysPerWeek;

  document.getElementById('emailAlertsCard').style.display = isDirector() ? 'block' : 'none';
  const ejs = globalState.emailjs || { publicKey:'', serviceId:'', templateAbsence:'', templateDeadline:'', templateBehavior:'', templateFailingGrade:'' };
  document.getElementById('ejsPublicKey').value = ejs.publicKey;
  document.getElementById('ejsServiceId').value = ejs.serviceId;
  document.getElementById('ejsTemplateAbsence').value = ejs.templateAbsence;
  document.getElementById('ejsTemplateDeadline').value = ejs.templateDeadline;
  document.getElementById('ejsTemplateBehavior').value = ejs.templateBehavior || '';
  document.getElementById('ejsTemplateFailingGrade').value = ejs.templateFailingGrade || '';

  document.getElementById('groupmeCard').style.display = isDirector() ? 'block' : 'none';
  document.getElementById('groupmeBotId').value = (globalState.groupme && globalState.groupme.botId) || '';

  const dirList = document.getElementById('directorEmailsList');
  dirList.innerHTML = (globalState.directorEmails||[]).map(email=>`
    <div class="roster-row"><span class="rname">${email}</span><button class="task-del" data-remove-director="${email}">✕</button></div>
  `).join('') || `<div class="empty-state">No Directors yet.</div>`;
  dirList.querySelectorAll('[data-remove-director]').forEach(btn=>btn.addEventListener('click', async ()=>{
    if((globalState.directorEmails||[]).length<=1){ toast("Can't remove the last Director — add another first"); return; }
    globalState.directorEmails = globalState.directorEmails.filter(e=>e!==btn.dataset.removeDirector);
    await saveGlobalState(); renderSetup();
    toast('Director access removed');
  }));

  document.getElementById('rosterAddFormWrap').style.display = isDirector() ? 'block' : 'none';
  document.getElementById('rosterLockedMsg').style.display = isDirector() ? 'none' : 'block';
  document.getElementById('bulkImportCard').style.display = isDirector() ? 'block' : 'none';
  document.getElementById('exportGradesCard').style.display = isDirector() ? 'block' : 'none';
  document.getElementById('participationExportCard').style.display = isDirector() ? 'block' : 'none';
  if(isDirector()){
    if(!ui.allPartWeekCursor) ui.allPartWeekCursor = weekStartISO(todayISO());
    document.getElementById('allPartWeekLabel').textContent = fmtWeekLabel(ui.allPartWeekCursor);
  }
  document.getElementById('resetCard').style.display = isDirector() ? 'block' : 'none';
  document.getElementById('archiveCard').style.display = isDirector() ? 'block' : 'none';
  if(isDirector()){
    const sizeBytes = new Blob([JSON.stringify(state)]).size;
    const sizeKB = Math.round(sizeBytes/1024);
    const pct = Math.min(100, Math.round(sizeBytes/1024/1024*100));
    document.getElementById('dataSizeLabel').textContent = `${sizeKB} KB / 1024 KB (${pct}%)`;
    const bar = document.getElementById('dataSizeBar');
    bar.style.width = pct+'%';
    bar.style.background = pct>=80 ? 'var(--red)' : pct>=50 ? 'var(--amber)' : 'var(--sage)';
  }

  const deptWrap = document.getElementById('rosterDeptChecks');
  deptWrap.innerHTML = DEPARTMENTS.map(d=>`<label><input type="checkbox" value="${d.key}"> ${d.label}</label>`).join('');
  const prodWrap = document.getElementById('rosterProdChecks');
  prodWrap.innerHTML = (globalState.productions||[]).map(p=>`<label><input type="checkbox" value="${p.id}" ${p.id===currentProductionId?'checked':''}> ${escapeHtml(p.name)}</label>`).join('');
  const list = document.getElementById('rosterList'); list.innerHTML = '';
  if(!state.crew.length){ list.innerHTML = `<div class="empty-state">No cast/crew added yet.</div>`; }
  state.crew.forEach(c=>{
    const wrap = document.createElement('div');
    const row = document.createElement('div'); row.className = 'roster-row';
    const isCast = (c.departments||[]).includes('cast');
    row.innerHTML = `<span class="dot" style="background:${c.role==='teacher'?'var(--gold)':'var(--amber)'}"></span>
      <span class="rname">${escapeHtml(c.name)}${c.role==='teacher'?' <span class="mono" style="font-size:10px;color:var(--gold)">TEACHER</span>':''}${isCast && c.castRole?` <span class="mono" style="font-size:10.5px;color:var(--cast,#8C3B3B);">as ${escapeHtml(c.castRole)}</span>`:''}<br><span class="mono" style="font-size:10px;color:var(--paper-dim);">${c.email||'no sign-in email set'}</span></span>
      <span class="rmeta">${(c.departments||[]).map(k=>deptInfo(k).label).join(', ')||'No team yet'}</span>
      ${isDirector() ? `<select class="mono" style="background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); border-radius:3px; padding:4px 7px; font-size:11.5px;" data-classperiod="${c.id}">
        ${['Class A','Class B','Class C','Class D'].map(cp=>`<option ${cp===c.classPeriod?'selected':''}>${cp}</option>`).join('')}
      </select>` : `<span class="rmeta">${c.classPeriod||''}</span>`}
      ${isDirector() ? `${isCast?`<button class="btn ghost small" data-editrole="${c.id}">Edit Role</button>`:''}<button class="btn ghost small" data-editemail="${c.id}">Edit Email</button><button class="btn ghost small" data-edit="${c.id}">Edit Teams</button><button class="task-del" data-id="${c.id}">✕</button>` : ''}`;
    wrap.appendChild(row);
    if(isDirector()){
      row.querySelector('[data-classperiod]').addEventListener('change', async (e)=>{
        c.classPeriod = e.target.value;
        await saveState(); renderSetup();
        toast(`${escapeHtml(c.name)} moved to ${c.classPeriod}`);
      });
      row.querySelector('[data-editemail]').addEventListener('click', async ()=>{
        const val = prompt(`Sign-in email for ${escapeHtml(c.name)}:`, c.email||'');
        if(val===null) return;
        const oldEmail = c.email;
        c.email = val.trim();
        if(oldEmail) rosterIndexRemove(oldEmail, currentProductionId);
        if(c.email){ const prodMeta = globalState.productions.find(p=>p.id===currentProductionId); rosterIndexAdd(c.email, currentProductionId, prodMeta?prodMeta.name:'', c.id); }
        await saveState(); await saveGlobalState(); renderSetup();
        toast('Email updated');
      });
      if(isCast){
        row.querySelector('[data-editrole]').addEventListener('click', async ()=>{
          const val = prompt(`Cast role / character for ${escapeHtml(c.name)}:`, c.castRole||'');
          if(val===null) return;
          c.castRole = val.trim();
          await saveState(); renderSetup();
          toast('Cast role updated');
        });
      }
      const editRow = document.createElement('div');
      editRow.className = 'checkbox-row';
      editRow.style.display = 'none';
      editRow.style.marginBottom = '10px';
      editRow.innerHTML = DEPARTMENTS.map(d=>`<label><input type="checkbox" value="${d.key}" ${(c.departments||[]).includes(d.key)?'checked':''}> ${d.label}</label>`).join('');
      wrap.appendChild(editRow);
      row.querySelector('[data-edit]').addEventListener('click', ()=>{
        editRow.style.display = editRow.style.display==='none' ? 'flex' : 'none';
      });
      editRow.querySelectorAll('input').forEach(inp=>inp.addEventListener('change', async ()=>{
        c.departments = Array.from(editRow.querySelectorAll('input:checked')).map(i=>i.value);
        await saveState(); renderSetup(); renderHeader();
      }));
    }
    list.appendChild(wrap);
  });
  list.querySelectorAll('.task-del').forEach(btn=>btn.addEventListener('click', async ()=>{
    if(!isDirector()) return;
    const removed = state.crew.find(c=>c.id===btn.dataset.id);
    state.crew = state.crew.filter(c=>c.id!==btn.dataset.id);
    if(removed && removed.email){ rosterIndexRemove(removed.email, currentProductionId); await saveGlobalState(); }
    await saveState(); renderSetup(); renderHeader();
  }));
}
async function bulkImportCrew(){
  if(!isDirector()){ toast('Only the Director can import crew'); return; }
  const raw = document.getElementById('bulkImportText').value;
  const lines = raw.split('\n').map(l=>l.trim()).filter(Boolean);
  if(!lines.length){ toast('Paste at least one name'); return; }
  let count = 0;
  const prodMeta = globalState.productions.find(p=>p.id===currentProductionId);
  lines.forEach(line=>{
    const parts = line.split(',').map(p=>p.trim());
    const name = parts[0];
    if(!name) return;
    const email = parts[1] && parts[1].includes('@') ? parts[1] : '';
    const newMember = { id:cryptoId(), name, role:'student', classPeriod:'Class A', departments:[], castRole:'', email, parentEmail:'', parentPhone:'' };
    state.crew.push(newMember);
    if(email) rosterIndexAdd(email, currentProductionId, prodMeta?prodMeta.name:'', newMember.id);
    count++;
  });
  await saveState(); await saveGlobalState();
  document.getElementById('bulkImportText').value = '';
  renderSetup(); renderHeader();
  toast(`${count} student(s) imported — assign teams with "Edit Teams"`);
}
async function addCrew(){
  if(!isDirector()){ toast('Only the Director can edit the roster'); return; }
  const name = document.getElementById('rosterName').value.trim();
  if(!name){ toast('Enter a name'); return; }
  const role = document.getElementById('rosterRole').value;
  const classPeriod = document.getElementById('rosterClassPeriod').value;
  const departments = Array.from(document.querySelectorAll('#rosterDeptChecks input:checked')).map(i=>i.value);
  const castRole = document.getElementById('rosterCastRole').value.trim();
  const email = document.getElementById('rosterEmail').value.trim();
  const parentEmail = document.getElementById('rosterParentEmail').value.trim();
  const parentPhone = document.getElementById('rosterParentPhone').value.trim();
  let prodIds = Array.from(document.querySelectorAll('#rosterProdChecks input:checked')).map(i=>i.value);
  if(!prodIds.length) prodIds = [currentProductionId]; // safety net — always add to at least the current show

  for(const prodId of prodIds){
    const newMember = { id:cryptoId(), name, role, classPeriod, departments, castRole, email, parentEmail, parentPhone };
    if(prodId === currentProductionId){
      state.crew.push(newMember);
      await saveState();
    } else {
      const otherState = await loadProductionState(prodId);
      if(!otherState.crew) otherState.crew = [];
      otherState.crew.push(newMember);
      await fsSet(PROD_COLLECTION, prodId, otherState);
    }
    const prodMeta = globalState.productions.find(p=>p.id===prodId);
    if(email) rosterIndexAdd(email, prodId, prodMeta?prodMeta.name:'', newMember.id);
  }
  let removedPending = null;
  if(email && globalState.pendingApprovals && globalState.pendingApprovals.length){
    removedPending = globalState.pendingApprovals.find(p=>p.email.toLowerCase()===email.toLowerCase()) || null;
  }
  if(removedPending){
    try{
      if(window.__fb){
        const ref = window.__fb.doc(window.__fb.db, GLOBAL_DOC[0], GLOBAL_DOC[1]);
        await window.__fb.updateDoc(ref, { pendingApprovals: window.__fb.arrayRemove(removedPending) });
        // Refresh from the server right before the full save below, so that save's copy
        // of pendingApprovals is as fresh as possible — the full save still overwrites the
        // whole document, so this keeps the staleness window as small as it can be.
        const fresh = await fsGet(GLOBAL_DOC[0], GLOBAL_DOC[1]);
        if(fresh && fresh.pendingApprovals) globalState.pendingApprovals = fresh.pendingApprovals;
      }
    }catch(e){ console.warn('Pending approval cleanup failed to sync', e); }
  }
  await saveGlobalState();
  document.getElementById('rosterName').value=''; document.getElementById('rosterCastRole').value=''; document.getElementById('rosterEmail').value=''; document.getElementById('rosterParentEmail').value=''; document.getElementById('rosterParentPhone').value='';
  document.querySelectorAll('#rosterDeptChecks input').forEach(i=>i.checked=false);
  renderSetup(); renderHeader();
  toast(prodIds.length>1 ? `Crew member added to ${prodIds.length} productions` : 'Crew member added');
}

// ---------------- NAV ----------------
function switchView(view){
  document.querySelectorAll('.tabs button').forEach(b=>b.classList.toggle('active', b.dataset.view===view));
  document.querySelectorAll('.view').forEach(v=>v.classList.toggle('active', v.id==='view-'+view));
  if(view!=='departments') stopWorkspaceListener();
  if(view!=='stagedesign') pauseStageDesignScene();
  if(view!=='lightinglab') pauseLightingLabScene();
  if(view!=='blocking') pauseBlockingScene();
  if(view==='dashboard') renderDashboard();
  if(view==='productions') renderProductionsView();
  if(view==='calendar'){ renderCalendarForm(); switchCalSub(ui.calSub); }
  if(view==='conflicts') renderConflicts();
  if(view==='attendance') renderAttendanceView();
  if(view==='behavior') renderBehaviorView();
  if(view==='departments') renderDepartments();
  if(view==='costumes') renderCostumesView();
  if(view==='stagedesign') renderStageDesignView();
  if(view==='lightinglab') renderLightingLabView();
  if(view==='blocking') renderBlockingView();
  if(view==='notifications') renderNotifications();
  if(view==='setup') renderSetup();
}
function renderAll(){
  renderHeader(); renderCalendarForm();
  renderDashboard(); renderProductionsView(); renderCalMonth(); renderCalendar(); renderConflicts(); renderAttendanceView(); renderBehaviorView(); renderDepartments(); renderCostumesView(); renderNotifications(); renderSetup();
}

async function loadEverythingAndRender(){
  globalState = await loadOrMigrateGlobalState();
  if(!globalState.rosterIndex) globalState.rosterIndex = {};
  if(!globalState.pendingApprovals) globalState.pendingApprovals = [];
  if(!globalState.activeProductionId && globalState.productions.length){ globalState.activeProductionId = globalState.productions[0].id; }
  if(!globalState.emailjs) globalState.emailjs = { publicKey:'', serviceId:'', templateAbsence:'', templateDeadline:'', templateBehavior:'', templateFailingGrade:'' };
  if(globalState.emailjs.templateBehavior === undefined) globalState.emailjs.templateBehavior = '';
  if(globalState.emailjs.templateFailingGrade === undefined) globalState.emailjs.templateFailingGrade = '';
  if(!globalState.groupme) globalState.groupme = { botId:'' };
  await ensureRosterIndexBackfilled();

  // Directors manage whichever production they've switched to (Productions tab).
  // Everyone else lands in whichever show(s) their own email is actually rostered on —
  // never just whatever the Director happens to have open at the moment.
  const email = activeEmail();
  const isDirectorEmail = !!(email && globalState.directorEmails.includes(email));
  if(isDirectorEmail){
    currentProductionId = globalState.activeProductionId;
  } else if(email){
    const matches = globalState.rosterIndex[email.toLowerCase()] || [];
    if(matches.length === 1){
      currentProductionId = matches[0].prodId;
    } else if(matches.length > 1){
      showProductionPickerShell(matches);
      return;
    } else {
      currentProductionId = globalState.activeProductionId; // not on any roster — pending-approval path below handles this
    }
  } else {
    currentProductionId = globalState.activeProductionId;
  }

  state = await loadProductionState(currentProductionId);
  if(!state.costumeRecords) state.costumeRecords = [];
  if(!state.behaviorIncidents) state.behaviorIncidents = [];
  if(!state.behaviorConfig) state.behaviorConfig = { pointsPerDay:20, daysPerWeek:5 };
  if(!state.participationConfig) state.participationConfig = { pointsPerDay:20, daysPerWeek:5 };
  if(!state.announcements) state.announcements = [];
  if(!state.lightingCues) state.lightingCues = [];
  if(!state.sandboxList) state.sandboxList = [];
  if(!state.blockingNotes) state.blockingNotes = [];

  if(!isApprovedUser()){
    await registerPendingApproval();
    showPendingApprovalShell();
    return;
  }

  showAppShell();
  renderAll();
  renderCostumeMeasureGrid(); renderCostumePieces();
  initEmailJs();
  if(isDirector()){ checkAndSendDeadlineAlerts(); checkAndSendGroupMeReminders(); }
}

async function init(){
  device = loadDevice();
  await waitForFirebase();

  if(!window.__fb){
    toast('Sign-in service failed to load — try refreshing the page');
    showSignedOutShell();
  } else {
    // Resolve the very first auth check (including restoring any persisted session) before
    // attempting any Firestore reads, since reads now require request.auth != null — a read
    // fired before this resolves would incorrectly look "signed out" and get rejected.
    const firstUser = await new Promise(resolve=>{
      const unsub = window.__fb.onAuthStateChanged(window.__fb.auth, (user)=>{ unsub(); resolve(user); });
    });
    authUser = firstUser ? { email:firstUser.email, displayName:firstUser.displayName } : null;

    if(authUser) await loadEverythingAndRender();
    else showSignedOutShell();
    renderHeader();

    // Ongoing listener for sign-in/out events that happen after this initial load.
    window.__fb.onAuthStateChanged(window.__fb.auth, async (user)=>{
      const wasSignedIn = !!authUser;
      authUser = user ? { email:user.email, displayName:user.displayName } : null;
      const isSignedIn = !!authUser;
      if(!wasSignedIn && isSignedIn){
        await loadEverythingAndRender();
      } else if(wasSignedIn && !isSignedIn){
        stopWorkspaceListener();
        state = null; globalState = null; currentProductionId = null;
        showSignedOutShell();
      } else if(isSignedIn && state){
        renderDashboard(); renderProductionsView(); renderCalendarForm(); renderCalMonth(); renderCalendar();
        renderConflicts(); renderAttendanceView(); renderBehaviorView(); renderDepartments(); renderCostumesView(); renderNotifications(); renderSetup();
        if(isDirector()){ checkAndSendDeadlineAlerts(); checkAndSendGroupMeReminders(); }
      }
      renderHeader();
    });
  }

  document.getElementById('tabsScrollLeft').addEventListener('click', ()=>{ document.getElementById('mainTabs').scrollBy({left:-180, behavior:'smooth'}); });
  document.getElementById('tabsScrollRight').addEventListener('click', ()=>{ document.getElementById('mainTabs').scrollBy({left:180, behavior:'smooth'}); });
  document.getElementById('mainTabs').addEventListener('scroll', updateTabsScrollButtons);
  document.getElementById('mainTabs').addEventListener('wheel', (e)=>{
    if(Math.abs(e.deltaY) > Math.abs(e.deltaX)){ e.preventDefault(); document.getElementById('mainTabs').scrollLeft += e.deltaY; }
  }, {passive:false});
  window.addEventListener('resize', updateTabsScrollButtons);
  document.getElementById('googleSignInBtn').addEventListener('click', signInWithGoogle);
  document.getElementById('toggleEmailPwBtn').addEventListener('click', ()=>{
    const wrap = document.getElementById('emailPwWrap');
    wrap.style.display = wrap.style.display==='none' ? 'flex' : 'none';
  });
  document.getElementById('epSignInBtn').addEventListener('click', ()=>{
    const email = document.getElementById('epEmail').value.trim();
    const password = document.getElementById('epPassword').value;
    if(!email || !password){ toast('Enter both email and password'); return; }
    signInWithEmailPassword(email, password);
  });
  document.getElementById('epSignUpBtn').addEventListener('click', ()=>{
    const email = document.getElementById('epEmail').value.trim();
    const password = document.getElementById('epPassword').value;
    if(!email || !password){ toast('Enter both email and password'); return; }
    signUpWithEmailPassword(email, password);
  });
  document.getElementById('epForgotBtn').addEventListener('click', ()=>{
    const email = document.getElementById('epEmail').value.trim();
    if(!email){ toast('Enter your email above first, then click Forgot password'); return; }
    sendPasswordReset(email);
  });
  document.getElementById('signOutBtn').addEventListener('click', signOutUser);
  document.getElementById('switchShowBtn').addEventListener('click', ()=>{
    const email = activeEmail();
    if(!email) return;
    const matches = (globalState.rosterIndex[email.toLowerCase()]||[]).filter(m=>m.prodId!==currentProductionId);
    showProductionPickerShell(matches.length ? matches : (globalState.rosterIndex[email.toLowerCase()]||[]));
  });
  document.getElementById('claimDirectorBtn').addEventListener('click', async ()=>{
    if(!authUser) return;
    globalState.directorEmails = [authUser.email];
    await saveGlobalState();
    renderHeader(); renderSetup(); renderDashboard();
    toast('You are now the Director');
  });
  document.getElementById('addDirectorEmailBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only an existing Director can add another'); return; }
    const email = document.getElementById('newDirectorEmail').value.trim().toLowerCase();
    if(!email || !email.includes('@')){ toast('Enter a valid email'); return; }
    if(!globalState.directorEmails.includes(email)) globalState.directorEmails.push(email);
    await saveGlobalState();
    document.getElementById('newDirectorEmail').value = '';
    renderSetup();
    toast('Director added');
  });

  document.getElementById('prodName').addEventListener('change', async e=>{
    if(!isDirector()){ renderHeader(); return; }
    const newName = e.target.value.trim() || 'Untitled Production';
    state.productionName = newName;
    const entry = globalState.productions.find(p=>p.id===currentProductionId);
    if(entry) entry.name = newName;
    await saveState(); await saveGlobalState();
  });

  document.querySelectorAll('.tabs button').forEach(b=>b.addEventListener('click', ()=>switchView(b.dataset.view)));
  document.querySelectorAll('#deptSubtabs button').forEach(b=>b.addEventListener('click', ()=>{ ui.activeSub = b.dataset.sub; renderDepartments(); }));
  document.querySelectorAll('#attendanceSubtabs button').forEach(b=>b.addEventListener('click', ()=>switchAttendanceSub(b.dataset.attSub)));
  document.querySelectorAll('#behaviorSubtabs button').forEach(b=>b.addEventListener('click', ()=>switchBehaviorSub(b.dataset.behSub)));

  document.getElementById('calAddBtn').addEventListener('click', addCalendarEvent);
  document.getElementById('toggleCalBulkImport').addEventListener('click', ()=>{
    const wrap = document.getElementById('calBulkImportWrap');
    wrap.style.display = wrap.style.display==='none' ? 'block' : 'none';
  });
  document.getElementById('calBulkImportBtn').addEventListener('click', bulkImportCalendar);
  document.getElementById('calClearAllBtn').addEventListener('click', async ()=>{
    if(!isDirectorOrStageMgmt()){ toast('Only the Director or Stage Management can clear the calendar'); return; }
    const count = state.calendar.length;
    if(!count){ toast('Calendar is already empty'); return; }
    if(!confirm(`Permanently delete all ${count} calendar entries (including any attendance already recorded on them)? This cannot be undone — do this before re-importing corrected data, not after.`)) return;
    state.calendar = [];
    logChange(`Cleared all ${count} calendar entries.`, {type:'all'});
    await saveState();
    renderCalendarForm(); renderCalendar(); renderNotifications(); renderDashboard();
    if(ui.calSub==='month') renderCalMonth();
    toast(`Cleared ${count} calendar entries`);
  });
  document.getElementById('printCalendarBtn').addEventListener('click', printCalendar);
  document.getElementById('printCalendarBtn2').addEventListener('click', printCalendar);
  document.getElementById('groupmeSendAnnounceBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can post to GroupMe'); return; }
    if(!globalState.groupme || !globalState.groupme.botId){ toast('Add your GroupMe Bot ID in Setup first'); return; }
    const text = document.getElementById('groupmeAnnounceText').value.trim();
    if(!text){ toast('Write something first'); return; }
    await sendGroupMe(`📣 ${text}`);
    document.getElementById('groupmeAnnounceText').value = '';
    toast('Announcement sent to GroupMe');
  });
  document.getElementById('groupmeSendScheduleBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can post to GroupMe'); return; }
    if(!globalState.groupme || !globalState.groupme.botId){ toast('Add your GroupMe Bot ID in Setup first'); return; }
    const today = todayISO();
    const weekOut = new Date(today+'T00:00'); weekOut.setDate(weekOut.getDate()+7);
    const weekOutISO = weekOut.toISOString().slice(0,10);
    const upcoming = [...state.calendar].filter(e=>e.date>=today && e.date<=weekOutISO).sort((a,b)=>a.date.localeCompare(b.date));
    if(!upcoming.length){ toast('No calls in the next 7 days'); return; }
    const lines = upcoming.map(e=>`• ${fmtDate(e.date)}${e.startTime?' '+e.startTime:''} — ${escapeHtml(e.title)}${e.location?' @ '+e.location:''}${groupMeCallInfo(e)}`);
    await sendGroupMe(`🗓️ Upcoming Rehearsal Schedule:\n${lines.join('\n')}`);
    toast('Schedule sent to GroupMe');
  });
  document.getElementById('announcementPostBtn').addEventListener('click', postAnnouncement);
  document.querySelectorAll('#calSubtabs button').forEach(b=>b.addEventListener('click', ()=>switchCalSub(b.dataset.calSub)));
  document.getElementById('calPrevBtn').addEventListener('click', ()=>{ ui.calMonthCursor = new Date(ui.calMonthCursor.getFullYear(), ui.calMonthCursor.getMonth()-1, 1); renderCalMonth(); });
  document.getElementById('calNextBtn').addEventListener('click', ()=>{ ui.calMonthCursor = new Date(ui.calMonthCursor.getFullYear(), ui.calMonthCursor.getMonth()+1, 1); renderCalMonth(); });
  document.getElementById('calTodayBtn').addEventListener('click', ()=>{ const n=new Date(); ui.calMonthCursor = new Date(n.getFullYear(), n.getMonth(), 1); ui.calSelectedDate = todayISO(); renderCalMonth(); });
  document.getElementById('conflictAddBtn').addEventListener('click', submitConflict);
  document.getElementById('behLogBtn').addEventListener('click', logInfraction);
  document.getElementById('behPrevWeekBtn').addEventListener('click', ()=>{
    const d = new Date(ui.behaviorWeekCursor+'T00:00'); d.setDate(d.getDate()-7);
    ui.behaviorWeekCursor = d.toISOString().slice(0,10); renderBehaviorSummary();
  });
  document.getElementById('behNextWeekBtn').addEventListener('click', ()=>{
    const d = new Date(ui.behaviorWeekCursor+'T00:00'); d.setDate(d.getDate()+7);
    ui.behaviorWeekCursor = d.toISOString().slice(0,10); renderBehaviorSummary();
  });
  document.getElementById('behThisWeekBtn').addEventListener('click', ()=>{
    ui.behaviorWeekCursor = weekStartISO(todayISO()); renderBehaviorSummary();
  });
  document.getElementById('behExportCsvBtn').addEventListener('click', exportBehaviorCsv);
  document.getElementById('myBehPrevWeekBtn').addEventListener('click', ()=>{
    const d = new Date(ui.myBehaviorWeekCursor+'T00:00'); d.setDate(d.getDate()-7);
    ui.myBehaviorWeekCursor = d.toISOString().slice(0,10); renderMyBehaviorView();
  });
  document.getElementById('myBehNextWeekBtn').addEventListener('click', ()=>{
    const d = new Date(ui.myBehaviorWeekCursor+'T00:00'); d.setDate(d.getDate()+7);
    ui.myBehaviorWeekCursor = d.toISOString().slice(0,10); renderMyBehaviorView();
  });
  document.getElementById('myBehThisWeekBtn').addEventListener('click', ()=>{
    ui.myBehaviorWeekCursor = weekStartISO(todayISO()); renderMyBehaviorView();
  });
  document.getElementById('rosterAddBtn').addEventListener('click', addCrew);
  document.getElementById('attendanceThresholdSaveBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can change this'); return; }
    const val = parseInt(document.getElementById('attendanceThreshold').value) || 3;
    globalState.attendanceAlertThreshold = val;
    await saveGlobalState(); renderSetup(); renderDashboard();
    toast(`Attendance alert threshold set to ${val}`);
  });
  document.getElementById('behConfigSaveBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can change this'); return; }
    const pointsPerDay = parseInt(document.getElementById('behPointsPerDay').value) || 20;
    const daysPerWeek = parseInt(document.getElementById('behDaysPerWeek').value) || 5;
    state.behaviorConfig = { pointsPerDay, daysPerWeek };
    await saveState(); renderSetup(); renderBehaviorView();
    toast(`Behavior grading set to ${pointsPerDay} pts/day × ${daysPerWeek} days`);
  });
  document.getElementById('partConfigSaveBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can change this'); return; }
    const pointsPerDay = parseInt(document.getElementById('partPointsPerDay').value) || 20;
    const daysPerWeek = parseInt(document.getElementById('partDaysPerWeek').value) || 5;
    state.participationConfig = { pointsPerDay, daysPerWeek };
    await saveState(); renderSetup(); renderDepartments();
    toast(`Participation grading set to ${pointsPerDay} pts/day × ${daysPerWeek} days`);
  });
  document.getElementById('ejsConfigSaveBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can change this'); return; }
    globalState.emailjs = {
      publicKey: document.getElementById('ejsPublicKey').value.trim(),
      serviceId: document.getElementById('ejsServiceId').value.trim(),
      templateAbsence: document.getElementById('ejsTemplateAbsence').value.trim(),
      templateDeadline: document.getElementById('ejsTemplateDeadline').value.trim(),
      templateBehavior: document.getElementById('ejsTemplateBehavior').value.trim(),
      templateFailingGrade: document.getElementById('ejsTemplateFailingGrade').value.trim(),
    };
    await saveGlobalState();
    initEmailJs();
    toast('Email alert settings saved');
  });
  document.getElementById('groupmeConfigSaveBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can change this'); return; }
    globalState.groupme = { botId: document.getElementById('groupmeBotId').value.trim() };
    await saveGlobalState();
    toast('GroupMe bot saved');
  });
  document.getElementById('bulkImportBtn').addEventListener('click', bulkImportCrew);
  document.getElementById('markReadBtn').addEventListener('click', ()=>{ device.lastSeenChangelog = new Date().toISOString(); saveDevice(); renderNotifications(); toast('Marked read'); });
  document.getElementById('clearAllNotifsBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can clear notifications'); return; }
    if(!confirm('Clear all notifications? This only affects the notification log, not the underlying tasks/grades/records.')) return;
    state.changelog = [];
    await saveState(); renderNotifications();
    toast('Notifications cleared');
  });
  document.querySelectorAll('#sdToolbarWrap [data-piece]').forEach(btn=>btn.addEventListener('click', ()=>addStagePiece(btn.dataset.piece)));
  document.getElementById('sdUndoBtn').addEventListener('click', sdUndo);
  document.getElementById('sdClearBtn').addEventListener('click', async ()=>{
    if(!canEditActiveBoard()){ toast('Only Set Design, Stage Management, or the Director can edit the set'); return; }
    if(!stageDesignCache.pieces.length){ toast('Already empty'); return; }
    if(!confirm(`Remove all ${stageDesignCache.pieces.length} piece(s) from this board? You'll be able to click Undo right after if this was a mistake, but that only lasts until you leave this board — don't count on it later.`)) return;
    sdPushUndo();
    stageDesignCache.pieces = []; sdSelectedId = null;
    syncStageDesignScene(); renderSdPieceList(); renderSdPropsPanel();
    await saveStageDesignData();
    toast('Set cleared — click Undo if that was a mistake');
  });
  document.querySelectorAll('#sdBoardTabs button').forEach(btn=>btn.addEventListener('click', async ()=>{
    if(btn.dataset.board==='production'){
      document.querySelectorAll('#sdBoardTabs button').forEach(b=>b.classList.toggle('active', b===btn));
      await switchStageDesignBoard('production', null);
    } else {
      if(!currentUser() && !isDirectorOrStageMgmt()){ toast('Sign in to use a practice sandbox'); return; }
      const list = state.sandboxList || [];
      if(!list.length){ toast('No sandboxes exist yet — click "+ New Sandbox" to start one for your group'); return; }
      if(!sdCheckSandboxAccess(list[0].id)) return;
      document.querySelectorAll('#sdBoardTabs button').forEach(b=>b.classList.toggle('active', b===btn));
      await switchStageDesignBoard('sandbox', list[0].id);
    }
    renderStageDesignView();
  }));
  document.getElementById('sdSandboxBrowseSelect').addEventListener('change', async (e)=>{
    if(!e.target.value) return;
    if(!sdCheckSandboxAccess(e.target.value)){ renderStageDesignView(); return; } // revert dropdown to the actually-loaded sandbox
    await switchStageDesignBoard('sandbox', e.target.value);
    renderStageDesignView();
  });
  document.getElementById('sdNewSandboxBtn').addEventListener('click', createNewSandbox);
  document.getElementById('sdDeleteSandboxBtn').addEventListener('click', ()=>{ if(sdSandboxId) deleteSandbox(sdSandboxId); });
  document.getElementById('sdPropLabel').addEventListener('change', e=>updateSelectedPieceProp('label', e.target.value));
  document.getElementById('sdPropColor').addEventListener('input', e=>updateSelectedPieceProp('color', e.target.value));
  document.getElementById('sdPropWidth').addEventListener('change', e=>updateSelectedPieceProp('width', parseFloat(e.target.value)||1));
  document.getElementById('sdPropDepth').addEventListener('change', e=>updateSelectedPieceProp('depth', parseFloat(e.target.value)||1));
  document.getElementById('sdPropHeight').addEventListener('change', e=>updateSelectedPieceProp('height', parseFloat(e.target.value)||1));
  document.getElementById('sdPropRotation').addEventListener('change', e=>updateSelectedPieceProp('rotationY', parseFloat(e.target.value)||0));
  document.getElementById('sdPropBaseY').addEventListener('change', e=>updateSelectedPieceProp('y', parseFloat(e.target.value)||0));
  document.getElementById('sdDeletePieceBtn').addEventListener('click', deleteSelectedPiece);
  document.getElementById('sdPrintSnapshotBtn').addEventListener('click', printStageDesignSnapshot);
  document.getElementById('sdPrintGroundPlanBtn').addEventListener('click', printStageDesignGroundPlan);
  document.getElementById('llSaveCueBtn').addEventListener('click', llSaveCue);
  document.getElementById('llBlackoutBtn').addEventListener('click', ()=>{
    if(!canUseLightingLab()) return;
    if(!ui.llState) return;
    LL_CHANNELS.forEach((def,i)=>{ ui.llState[i].intensity = 0; llApplyChannel(i); });
    llRefreshChannelUI();
    toast('Blackout');
  });
  document.getElementById('llAddActorBtn').addEventListener('click', ()=>addLightingPiece('actor'));
  document.getElementById('llExportCuesBtn').addEventListener('click', exportLightingCuesCsv);
  document.getElementById('llPrintLookBtn').addEventListener('click', printLightingLook);
  document.getElementById('bkAddActorBtn').addEventListener('click', ()=>bkAddPiece('actor'));
  document.getElementById('bkAddCubeBtn').addEventListener('click', ()=>bkAddPiece('cube'));
  document.getElementById('bkAddDoorBtn').addEventListener('click', ()=>bkAddPiece('door'));
  document.getElementById('bkAddNumberBtn').addEventListener('click', ()=>bkAddPiece('number'));
  document.getElementById('bkAddStickyBtn').addEventListener('click', ()=>bkAddPiece('sticky'));
  document.querySelectorAll('#bkDrawToolbar [data-bktool]').forEach(btn=>btn.addEventListener('click', ()=>bkSetTool(btn.dataset.bktool)));
  const bkPenColorRow = document.getElementById('bkPenColorRow');
  bkPenColorRow.innerHTML = BK_PEN_COLORS.map(c=>`<div class="sticky-color-swatch ${c===ui.bkPenColor?'active':''}" style="background:${c}; border:1px solid #666;" data-color="${c}"></div>`).join('');
  bkPenColorRow.querySelectorAll('.sticky-color-swatch').forEach(sw=>sw.addEventListener('click', ()=>{
    ui.bkPenColor = sw.dataset.color;
    bkPenColorRow.querySelectorAll('.sticky-color-swatch').forEach(s=>s.classList.remove('active'));
    sw.classList.add('active');
  }));
  const bkPenWidthRow = document.getElementById('bkPenWidthRow');
  const bkWidthLabels = {2:'Thin',3:'Med',6:'Thick'};
  bkPenWidthRow.innerHTML = [2,3,6].map(w=>`<button class="btn ghost small wb-width-btn ${w===ui.bkPenWidth?'active':''}" data-width="${w}">${bkWidthLabels[w]}</button>`).join('');
  bkPenWidthRow.querySelectorAll('.wb-width-btn').forEach(btn=>btn.addEventListener('click', ()=>{
    ui.bkPenWidth = parseInt(btn.dataset.width);
    bkPenWidthRow.querySelectorAll('.wb-width-btn').forEach(b=>b.classList.remove('active'));
    btn.classList.add('active');
  }));
  document.getElementById('bkClearBoardBtn').addEventListener('click', async ()=>{
    if(!canUseBlockingAssistant()) return;
    if(((blockingCache.pieces||[]).length || (blockingCache.strokes||[]).length) && !confirm('Clear the live blocking board? This does not delete any saved blocking notes.')) return;
    blockingCache.pieces = []; blockingCache.strokes = [];
    renderBlockingBoard();
    await saveBlockingData();
    toast('Board cleared');
  });
  document.getElementById('bkSaveNoteBtn').addEventListener('click', bkSaveNote);
  document.getElementById('bkCancelEditBtn').addEventListener('click', ()=>{ bkCancelEdit(); renderBlockingBoard(); });
  document.getElementById('bkPrintCurrentBtn').addEventListener('click', ()=>{
    if(!(blockingCache.pieces||[]).length && !(blockingCache.strokes||[]).length){ toast('Board is empty — nothing to print'); return; }
    printBlockingBoard(blockingCache.pieces, 'Blocking — Current Live Board', blockingCache.strokes);
  });
  document.getElementById('bkPrevPageBtn').addEventListener('click', ()=>{
    const idx = ui.bkCurrentNoteIndex!=null ? ui.bkCurrentNoteIndex-1 : 0;
    bkGoToNoteByIndex(idx);
  });
  document.getElementById('bkNextPageBtn').addEventListener('click', ()=>{
    const idx = ui.bkCurrentNoteIndex!=null ? ui.bkCurrentNoteIndex+1 : 0;
    bkGoToNoteByIndex(idx);
  });
  document.getElementById('llAddCubeBtn').addEventListener('click', ()=>addLightingPiece('cube'));
  document.getElementById('llClearPiecesBtn').addEventListener('click', ()=>{
    if(!canUseLightingLab()) return;
    if((ui.llPieces||[]).length && !confirm('Remove all actors and rehearsal cubes from the stage?')) return;
    clearLightingPieces();
    toast('Cleared');
  });
  document.getElementById('exportAllGradesBtn').addEventListener('click', ()=>{
    if(!isDirector()){ toast('Only the Director can export grades'); return; }
    exportAllDeptGradesCsv();
  });
  document.getElementById('allPartPrevWeekBtn').addEventListener('click', ()=>{
    const d = new Date(ui.allPartWeekCursor+'T00:00'); d.setDate(d.getDate()-7);
    ui.allPartWeekCursor = d.toISOString().slice(0,10);
    document.getElementById('allPartWeekLabel').textContent = fmtWeekLabel(ui.allPartWeekCursor);
  });
  document.getElementById('allPartNextWeekBtn').addEventListener('click', ()=>{
    const d = new Date(ui.allPartWeekCursor+'T00:00'); d.setDate(d.getDate()+7);
    ui.allPartWeekCursor = d.toISOString().slice(0,10);
    document.getElementById('allPartWeekLabel').textContent = fmtWeekLabel(ui.allPartWeekCursor);
  });
  document.getElementById('allPartThisWeekBtn').addEventListener('click', ()=>{
    ui.allPartWeekCursor = weekStartISO(todayISO());
    document.getElementById('allPartWeekLabel').textContent = fmtWeekLabel(ui.allPartWeekCursor);
  });
  document.getElementById('allPartExportCsvBtn').addEventListener('click', ()=>{
    if(!isDirector()){ toast('Only the Director can export grades'); return; }
    const weekLabel = ui.allPartWeekCursor || weekStartISO(todayISO());
    const students = state.crew.filter(c=>c.role==='student').sort((a,b)=>a.name.localeCompare(b.name));
    const rows = students.map(c=>{
      const combined = combinedParticipationForStudent(c.id, weekLabel);
      const depts = (c.departments||[]).map(k=>deptInfo(k).label).join(', ') || 'No team';
      return [c.name, c.classPeriod||'', depts, combined.earned, combined.max, combined.pct===null?'':combined.pct, weekLabel];
    });
    if(!rows.length){ toast('No students to export'); return; }
    downloadCsvRows(['Student','Class Period','Teams','Points Earned','Points Possible','Average Score (%)','Week Of'], rows, `weekly-participation-averages-${weekLabel}.csv`);
  });
  document.getElementById('downloadBackupBtn').addEventListener('click', ()=>{
    if(!isDirector()){ toast('Only the Director can download a backup'); return; }
    const backup = { exportedAt:new Date().toISOString(), productionName:state.productionName, production:state, globalConfig:{ directorEmails:globalState.directorEmails, attendanceAlertThreshold:globalState.attendanceAlertThreshold } };
    const blob = new Blob([JSON.stringify(backup, null, 2)], {type:'application/json'});
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url; a.download = `${state.productionName.replace(/\s+/g,'-').toLowerCase()}-backup-${todayISO()}.json`;
    document.body.appendChild(a); a.click(); document.body.removeChild(a);
    URL.revokeObjectURL(url);
    toast('Backup downloaded');
  });
  document.getElementById('archiveDeleteBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can archive records'); return; }
    const cutoff = document.getElementById('archiveCutoffDate').value;
    if(!cutoff){ toast('Pick a cutoff date first'); return; }
    let reportCount = 0, incidentCount = 0;
    const keptReportsByDept = {};
    DEPARTMENTS.forEach(d=>{
      const dep = state.departments[d.key];
      const kept = dep.reports.filter(r=>r.date >= cutoff);
      reportCount += dep.reports.length - kept.length;
      keptReportsByDept[d.key] = kept;
    });
    const keptIncidents = state.behaviorIncidents.filter(i=>i.date >= cutoff);
    incidentCount = state.behaviorIncidents.length - keptIncidents.length;
    if(reportCount===0 && incidentCount===0){ toast('Nothing to delete before that date'); return; }
    if(!confirm(`This will permanently delete ${reportCount} report(s) and ${incidentCount} behavior record(s) dated before ${fmtDate(cutoff)}. Make sure you've already exported the CSV(s) you need. This cannot be undone. Continue?`)) return;
    DEPARTMENTS.forEach(d=>{ state.departments[d.key].reports = keptReportsByDept[d.key]; });
    state.behaviorIncidents = keptIncidents;
    await saveState();
    renderSetup(); renderDashboard();
    toast(`Deleted ${reportCount} report(s) and ${incidentCount} behavior record(s)`);
  });
  document.getElementById('resetAllBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can reset production data'); return; }
    if(confirm('This clears all data for the CURRENT production only (tasks, calendar, conflicts, costumes, crew). Other productions are unaffected. Continue?')){
      state = defaultProductionState(state.productionName, false);
      await saveState(); renderAll(); toast('Current production reset');
    }
  });
  document.getElementById('newProductionBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Switch to Director mode first'); return; }
    const nameInput = document.getElementById('newProductionName');
    const name = nameInput.value.trim();
    if(!name){ toast('Give the production a name first'); return; }
    const copySel = document.getElementById('copyFromProduction');
    const copyFromId = copySel && copySel.value ? copySel.value : null;
    const newId = await createProduction(name, copyFromId);
    await switchToProduction(newId);
    nameInput.value = '';
    toast(`"${name}" created${copyFromId?' — roster copied, teams cleared':''}`);
  });

  document.getElementById('toggleCostumeForm').addEventListener('click', ()=>{
    const el = document.getElementById('costumeAddForm'); el.style.display = el.style.display==='none' ? 'block' : 'none';
  });
  document.getElementById('cAddPiece').addEventListener('click', ()=>{
    const name = document.getElementById('cPieceName').value.trim(); if(!name) return;
    costumeDraft.pieces.push({ name, category: document.getElementById('cPieceCat').value, status:'Assigned' });
    document.getElementById('cPieceName').value=''; renderCostumePieces();
  });
  document.getElementById('cSaveBtn').addEventListener('click', async ()=>{
    if(!canViewCostumes()){ toast('Only the Costumes team can add measurement records'); return; }
    const name = document.getElementById('cName').value.trim();
    if(!name){ toast('Enter a name'); return; }
    const rec = { id:cryptoId(), name, email: document.getElementById('cEmail').value.trim(), department:'costumes', pieces: costumeDraft.pieces, notes: document.getElementById('cNotes').value.trim() };
    document.querySelectorAll('#cMeasureGrid input').forEach(inp=>{ rec[inp.dataset.m] = inp.value.trim(); });
    state.costumeRecords.push(rec);
    await saveState();
    document.getElementById('cName').value=''; document.getElementById('cEmail').value=''; document.getElementById('cNotes').value='';
    document.querySelectorAll('#cMeasureGrid input').forEach(inp=>inp.value='');
    costumeDraft.pieces = []; renderCostumePieces();
    document.getElementById('costumeAddForm').style.display='none';
    renderCostumesView();
    toast('Measurement record saved');
  });
}

init();

})();
</script>
</body>
</html>
