<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SetDesk — Production Manager</title>
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

  .tabs{ max-width:1240px; margin:0 auto; display:flex; gap:2px; padding:0 24px; border-bottom:1px solid var(--line); overflow-x:auto; }
  .tabs button{ background:none; border:none; color:var(--paper-dim); padding:13px 14px; cursor:pointer; font-family:'Oswald',sans-serif;
    letter-spacing:0.05em; text-transform:uppercase; font-size:12.5px; border-bottom:3px solid transparent; white-space:nowrap; position:relative; }
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
</head>
<body>

<div class="callsheet-header">
  <div class="header-row">
    <div class="header-left">
      <div class="eyebrow">SetDesk // Full Production Manager</div>
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
          <button class="btn ghost small" id="toggleManualBtn">Can't sign in?</button>
        </div>
        <div id="emailPwWrap" style="display:none; gap:6px; flex-wrap:wrap; justify-content:flex-end;">
          <input type="email" id="epEmail" placeholder="email" style="background:rgba(0,0,0,0.25); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:14px; font-size:12px; width:140px;">
          <input type="password" id="epPassword" placeholder="password" style="background:rgba(0,0,0,0.25); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:14px; font-size:12px; width:120px;">
          <button class="btn small" id="epSignInBtn">Sign In</button>
          <button class="btn ghost small" id="epSignUpBtn">Create Account</button>
        </div>
        <div id="manualWrap" style="display:none; gap:6px; flex-wrap:wrap; justify-content:flex-end; align-items:center;">
          <span style="font-size:10.5px; color:var(--paper-dim); max-width:220px; text-align:right;">Not verified — only use if sign-in truly doesn't work for you:</span>
          <input type="text" id="manualEmailInput" placeholder="your email" style="background:rgba(0,0,0,0.25); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:14px; font-size:12px; width:140px;">
          <button class="btn ghost small" id="manualEmailBtn">Continue</button>
        </div>
      </div>
      <div id="signedInWrap" style="display:none; align-items:center; gap:10px;">
        <div style="text-align:right; line-height:1.3;">
          <div id="authUserName" style="font-size:13px; font-weight:600;"></div>
          <div id="authUserRole" class="mono" style="font-size:11px; color:var(--paper-dim);"></div>
        </div>
        <button class="btn ghost small" id="signOutBtn">Sign out</button>
      </div>
    </div>
  </div>
</div>

<nav class="tabs">
  <button data-view="dashboard" class="active">Dashboard</button>
  <button data-view="productions">Productions</button>
  <button data-view="calendar">Calendar</button>
  <button data-view="conflicts">Conflicts</button>
  <button data-view="attendance">Attendance</button>
  <button data-view="behavior">Behavior</button>
  <button data-view="departments">Departments</button>
  <button data-view="costumes">Costumes</button>
  <button data-view="notifications">Notifications<span class="badge" id="notifBadge" style="display:none;">0</span></button>
  <button data-view="setup">Roster / Setup</button>
</nav>

<main>

  <section class="view active" id="view-dashboard">
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
      </div>
      <div id="calList"></div>
    </div>
  </section>

  <section class="view" id="view-conflicts">
    <div class="card" id="conflictAddCard">
      <h2>Report a Conflict</h2>
      <div id="conflictNoCrewMsg" class="empty-state" style="display:none;">No crew on the roster yet — add crew members in <b>Roster / Setup</b> first, then come back here to submit a conflict.</div>
      <div id="conflictNoEventMsg" class="empty-state" style="display:none;">No upcoming calls on the calendar yet — add one in the <b>Calendar</b> tab first.</div>
      <div id="conflictNotSignedInMsg" class="empty-state" style="display:none;">Sign in with Google, or enter your email (top right), to submit a conflict.</div>
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
    <div id="behaviorLockedMsg" class="empty-state" style="display:none;">
      <div class="lamp">🔒</div>Behavior tracking is only visible to the Director and Stage Management team.
    </div>
    <div id="behaviorAuthorizedWrap">
      <div class="subtabs" id="behaviorSubtabs">
        <button data-beh-sub="log" class="active">Log Infraction</button>
        <button data-beh-sub="summary">Weekly Summary</button>
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
        <div class="card">
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

  <section class="view" id="view-notifications">
    <div class="card">
      <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:14px;">
        <h2 style="margin:0;">Schedule & Grading Changes</h2>
        <button class="btn ghost small" id="markReadBtn">Mark all read</button>
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
    <div class="card" id="dirAccessCard" style="display:none;">
      <h2>Director Access</h2>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Anyone signed in with one of these Google accounts gets Director access. Real Google sign-in enforces this — it isn't just a UI toggle.</p>
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
    </div>
    <div class="card" id="emailAlertsCard">
      <h2>Email Alerts</h2>
      <p style="font-size:12px;color:var(--paper-dim); margin-top:-6px;">Sends real emails using <a href="https://www.emailjs.com" target="_blank" style="color:var(--amber);">EmailJS</a> (free, no server needed). Create a free account there, connect an email service, make two templates, and paste the IDs below. Unexcused absences email the student and parent immediately; task deadlines email the assignee about 2 days before due, checked whenever the Director opens the app.</p>
      <div class="form-grid" style="max-width:460px;">
        <input type="text" id="ejsPublicKey" placeholder="Public Key">
        <input type="text" id="ejsServiceId" placeholder="Service ID">
      </div>
      <div class="form-grid" style="max-width:460px;">
        <input type="text" id="ejsTemplateAbsence" placeholder="Template ID — Absence">
        <input type="text" id="ejsTemplateDeadline" placeholder="Template ID — Deadline">
      </div>
      <p style="font-size:11.5px;color:var(--paper-dim);">Suggested template variables — Absence: <code>to_email, student_name, event_title, event_date, production_name</code>. Deadline: <code>to_email, student_name, task_title, due_date, department</code>.</p>
      <button class="btn small" id="ejsConfigSaveBtn">Save</button>
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
    <div class="card" id="resetCard">
      <h2>Data</h2>
      <p style="font-size:12.5px;color:var(--paper-dim);">All production data is shared and saved automatically to this app. Note: this app does not send real emails or sync with Google Classroom — those need a live server connection, which a standalone file can't provide.</p>
      <button class="btn danger small" id="resetAllBtn">Reset All Production Data</button>
    </div>
  </section>

</main>

<div class="toast" id="toast"></div>

<script type="text/javascript" src="https://cdn.jsdelivr.net/npm/@emailjs/browser@4/dist/email.min.js"></script>

<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-app.js";
  import { getFirestore, doc, getDoc, setDoc } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore.js";
  import { getAuth, GoogleAuthProvider, signInWithPopup, signOut, onAuthStateChanged, createUserWithEmailAndPassword, signInWithEmailAndPassword } from "https://www.gstatic.com/firebasejs/10.12.2/firebase-auth.js";

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

  window.__fb = { db, doc, getDoc, setDoc, auth, googleProvider, signInWithPopup, signOut, onAuthStateChanged, createUserWithEmailAndPassword, signInWithEmailAndPassword };
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
function defaultDeptState(seed){ return { tasks: seedTasks(seed), templates:[], reports:[] }; }

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
    departments: freshDepartments(seeded)
  };
}
// Production defaults are created via defaultProductionState(name, seeded) above.

const DEFAULT_GLOBAL = { directorEmails:[], attendanceAlertThreshold:3, productions:[], activeProductionId:null, emailjs:{ publicKey:'', serviceId:'', templateAbsence:'', templateDeadline:'' } };

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
    prodState.crew = (src.crew||[]).map(c=>({...c, departments:[]}));
  }
  await saveProductionStateFor(prodId, prodState);
  globalState.productions.push({ id:prodId, name, createdAt:new Date().toISOString() });
  return prodId;
}
async function switchToProduction(id){
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

let ui = { activeDept:'set_design', activeSub:'tasks', calSub:'month', calMonthCursor:new Date(new Date().getFullYear(), new Date().getMonth(), 1), calSelectedDate: todayISO(), reportClassPeriod:null, attendanceSub:'mark', expandedTasks:new Set(), behaviorSub:'log', behaviorWeekCursor:null, editingEventId:null };
let authUser = null; // { email, displayName } once signed in via Google, else null

// Identity resolves two ways: real Google sign-in (authUser) is verified;
// device.manualEmail is a self-typed fallback for accounts Google blocks
// (e.g. locked-down school Workspace accounts) and is NOT verified.
function activeEmail(){ return authUser ? authUser.email : (device.manualEmail || null); }
function isVerified(){ return !!authUser; }
function currentUser(){
  const email = activeEmail();
  if(!email) return null;
  const lower = email.toLowerCase();
  return state.crew.find(c=>(c.email||'').toLowerCase()===lower) || null;
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
async function signOutUser(){
  try{ if(window.__fb && authUser) await window.__fb.signOut(window.__fb.auth); }catch(e){ console.warn('Sign-out failed', e); }
  device.manualEmail = '';
  saveDevice();
  renderHeader(); renderDashboard(); renderProductionsView(); renderCalendarForm(); renderCalMonth(); renderCalendar();
  renderConflicts(); renderAttendanceView(); renderBehaviorView(); renderDepartments(); renderCostumesView(); renderNotifications(); renderSetup();
}
function setManualEmail(email){
  device.manualEmail = email.trim();
  saveDevice();
  renderHeader(); renderDashboard(); renderProductionsView(); renderCalendarForm(); renderCalMonth(); renderCalendar();
  renderConflicts(); renderAttendanceView(); renderBehaviorView(); renderDepartments(); renderCostumesView(); renderNotifications(); renderSetup();
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
  return kind==='absence' ? !!cfg.templateAbsence : !!cfg.templateDeadline;
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

function logChange(message, scope){ state.changelog.unshift({ id:cryptoId(), timestamp:new Date().toISOString(), message, scope: scope || {type:'all'} }); }
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
  document.getElementById('prodName').value = state.productionName;
  document.getElementById('prodName').disabled = !isDirector();
  document.getElementById('todayDate').textContent = new Date().toLocaleDateString(undefined,{weekday:'long', month:'short', day:'numeric'});
  document.getElementById('crewCount').textContent = state.crew.length;

  const email = activeEmail();
  document.getElementById('signedOutWrap').style.display = email ? 'none' : 'flex';
  document.getElementById('signedInWrap').style.display = email ? 'flex' : 'none';
  if(email){
    const u = currentUser();
    document.getElementById('authUserName').textContent = authUser ? (authUser.displayName || authUser.email) : email;
    let roleLabel;
    if(isDirector()) roleLabel = 'Director (verified)';
    else if(u) roleLabel = `${u.name} · ${deptInfo((u.departments||[])[0]||'').label || 'No team yet'}${isVerified()?'':' · unverified'}`;
    else roleLabel = `Not on the roster yet${isVerified()?'':' · unverified'}`;
    document.getElementById('authUserRole').textContent = roleLabel;
  }
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
    globalState.productions.map(p=>`<option value="${p.id}">Copy crew from "${p.name}"</option>`).join('');

  const list = document.getElementById('productionsList');
  list.innerHTML = '';
  if(!globalState.productions.length){ list.innerHTML = `<div class="empty-state">No productions yet.</div>`; return; }
  [...globalState.productions].sort((a,b)=>b.createdAt.localeCompare(a.createdAt)).forEach(p=>{
    const isActive = p.id === currentProductionId;
    const row = document.createElement('div');
    row.className = 'cal-event';
    row.innerHTML = `
      <div class="cal-event-top">
        <div><div class="cal-title">${p.name}${isActive?' <span class="tag" style="margin-left:6px; color:var(--sage); border-color:var(--sage);">Currently viewing</span>':''}</div>
        <div class="cal-meta">Created ${fmtDate(p.createdAt.slice(0,10))}</div></div>
        ${!isActive?`<button class="btn ghost small" data-switch="${p.id}">Switch to this production</button>`:''}
      </div>
    `;
    list.appendChild(row);
  });
  list.querySelectorAll('[data-switch]').forEach(btn=>btn.addEventListener('click', async ()=>{
    await switchToProduction(btn.dataset.switch);
    toast(`Switched to "${globalState.productions.find(p=>p.id===btn.dataset.switch).name}"`);
  }));
}

function renderDashboard(){
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
  upcoming.innerHTML = future.length ? future.map(e=>`<div class="list-item"><span>${e.title}${e.isSpecificCall?' <span class="tag" style="margin-left:4px;">specific call</span>':''}</span><span class="d">${fmtDate(e.date)} · ${e.startTime||'TBD'}</span></div>`).join('')
    : `<div class="empty-state">No upcoming calls scheduled.</div>`;

  const attn = document.getElementById('dashAttention');
  const pendingConflicts = state.conflicts.filter(c=>c.status==='pending').length;
  let reportsToGrade = 0;
  DEPARTMENTS.forEach(d=>{ reportsToGrade += state.departments[d.key].reports.filter(r=>r.status==='submitted'||r.status==='verified').length; });
  const items = [];
  if(pendingConflicts) items.push(`<div class="list-item"><span>⚠ ${pendingConflicts} conflict(s) awaiting a decision</span><span class="d">Conflicts tab</span></div>`);
  if(reportsToGrade) items.push(`<div class="list-item"><span>📋 ${reportsToGrade} report(s) awaiting review/grade</span><span class="d">Departments tab</span></div>`);
  if(isDirector()){
    attendanceAlerts().forEach(a=>{
      items.push(`<div class="list-item" style="color:var(--red);"><span>🚩 ${a.crew.name} has ${a.count} unexcused absence${a.count!==1?'s':''}</span><span class="d">${a.crew.classPeriod||''}</span></div>`);
    });
  }
  attn.innerHTML = items.length ? items.join('') : `<div class="empty-state">Nothing needs attention right now.</div>`;
}

// ---------------- CALENDAR ----------------
function renderCalendarForm(){
  document.getElementById('calAddCard').style.display = isDirector() ? 'block' : 'none';
  const wrap = document.getElementById('calDeptChecks');
  wrap.innerHTML = DEPARTMENTS.map(d=>`<label><input type="checkbox" value="${d.key}"> ${d.label}</label>`).join('');
  const specific = document.getElementById('calSpecific');
  specific.onchange = ()=>{ document.getElementById('calStudentPickWrap').style.display = specific.checked ? 'block' : 'none'; };
  const studentWrap = document.getElementById('calStudentChecks');
  studentWrap.innerHTML = state.crew.filter(c=>c.role==='student').map(c=>`<label><input type="checkbox" value="${c.id}"> ${c.name}</label>`).join('') || '<span style="font-size:12px;color:var(--paper-dim)">No students on roster yet.</span>';
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

// Builds one calendar event card as a DOM element — either its normal display,
// or (if the Director has clicked Edit on it) a full inline edit form.
function buildEventCardEl(ev){
  const card = document.createElement('div');
  card.className = 'cal-event';
  card.style.marginBottom = '8px';

  if(ui.editingEventId === ev.id && isDirector()){
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
          ${state.crew.filter(c=>c.role==='student').map(c=>`<label><input type="checkbox" value="${c.id}" ${(ev.calledStudentIds||[]).includes(c.id)?'checked':''}> ${c.name}</label>`).join('') || '<span style="font-size:12px;color:var(--paper-dim)">No students on roster yet.</span>'}
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
      logChange(`Edited: "${before.title}"${moved ? ` — moved from ${fmtDate(before.date)} ${before.startTime||''} to ${fmtDate(ev.date)} ${ev.startTime||''}` : ` (${fmtDate(ev.date)})`}.`, eventScope(ev));
      ui.editingEventId = null;
      await saveState(); renderCalMonth(); renderCalendar(); renderNotifications(); renderDashboard();
      toast('Call updated — crew notified');
    });
    return card;
  }

  card.innerHTML = `
    <div class="cal-event-top">
      <div><div class="cal-title">${ev.title}</div><div class="cal-meta">${fmtDate(ev.date)} · ${ev.startTime||'TBD'}${ev.endTime?'–'+ev.endTime:''} ${ev.location?'· '+ev.location:''}</div></div>
      <span class="cal-date-chip">${ev.date}</span>
    </div>
    ${ev.isSpecificCall ? `<div class="chip-row">
      <span class="chip outline">Specific call</span>
      ${(ev.calledDepartments||[]).map(k=>`<span class="chip" style="background:${deptInfo(k).color}">${deptInfo(k).label}</span>`).join('')}
      ${(ev.calledStudentIds||[]).map(id=>{ const c=state.crew.find(x=>x.id===id); return c?`<span class="chip outline">${c.name}</span>`:''; }).join('')}
    </div>` : ''}
    ${ev.notes?`<div class="cal-notes">${ev.notes}</div>`:''}
    ${conflictsBlockForEvent(ev)}
    ${attendanceChipForEvent(ev)}
    ${isDirector() ? `<div class="cal-actions"><button class="btn ghost small edit-btn">Edit</button><button class="btn danger small del-btn">Cancel</button></div>` : ''}
  `;
  if(isDirector()){
    card.querySelector('.edit-btn').addEventListener('click', ()=>{
      ui.editingEventId = ev.id; renderCalMonth(); renderCalendar();
    });
    card.querySelector('.del-btn').addEventListener('click', async ()=>{
      if(!confirm(`Cancel "${ev.title}" on ${fmtDate(ev.date)}?`)) return;
      state.calendar = state.calendar.filter(e=>e.id!==ev.id);
      logChange(`Cancelled: "${ev.title}" originally set for ${fmtDate(ev.date)}.`, eventScope(ev));
      await saveState(); renderCalMonth(); renderCalendar(); renderNotifications(); renderDashboard();
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
      <div class="list-item"><span><span class="chip" style="background:${dt.dept.color}; margin-right:6px;">${dt.dept.label}</span>${dt.task.title}</span><span class="d">${dt.task.status.replace('_',' ')}</span></div>
    `).join('');
    wrap.appendChild(due);
  }
}

function renderCalendar(){
  const list = document.getElementById('calList');
  list.innerHTML = '';
  if(!isDirector()){
    const lock = document.createElement('div'); lock.className = 'lockmsg';
    lock.textContent = 'Viewing as Team — only the Teacher/Director can add or edit calendar entries.';
    list.appendChild(lock);
  }
  const sorted = [...state.calendar].filter(visibleToCurrentUser).sort((a,b)=>a.date.localeCompare(b.date));
  if(!sorted.length){ list.innerHTML += `<div class="empty-state"><div class="lamp">🗓️</div>No calls scheduled yet.</div>`; return; }
  sorted.forEach(ev=>list.appendChild(buildEventCardEl(ev)));
}
async function addCalendarEvent(){
  const date = document.getElementById('calDate').value, title = document.getElementById('calTitle').value.trim();
  if(!date || !title){ toast('Add a date and title first'); return; }
  const startTime = document.getElementById('calStart').value, endTime = document.getElementById('calEnd').value;
  const location = document.getElementById('calLocation').value.trim(), notes = document.getElementById('calNotes').value.trim();
  const isSpecificCall = document.getElementById('calSpecific').checked;
  const calledDepartments = Array.from(document.querySelectorAll('#calDeptChecks input:checked')).map(i=>i.value);
  const calledStudentIds = Array.from(document.querySelectorAll('#calStudentChecks input:checked')).map(i=>i.value);
  state.calendar.push({ id:cryptoId(), title, date, startTime, endTime, location, notes, isSpecificCall, calledDepartments, calledStudentIds });
  logChange(`New call added: "${title}" on ${fmtDate(date)}${startTime?' at '+startTime:''}.`, eventScope({isSpecificCall, calledDepartments, calledStudentIds}));
  await saveState();
  ['calTitle','calLocation','calNotes','calStart','calEnd'].forEach(id=>document.getElementById(id).value='');
  document.getElementById('calSpecific').checked=false;
  document.querySelectorAll('#calDeptChecks input, #calStudentChecks input').forEach(i=>i.checked=false);
  renderCalendarForm(); renderCalendar(); renderNotifications(); renderDashboard();
  if(ui.calSub==='month') renderCalMonth();
  toast('Added to calendar');
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
    whoSel.innerHTML = state.crew.map(c=>`<option value="${c.id}">${c.name}</option>`).join('');
    if(cu) whoSel.value = cu.id;
  } else {
    whoSel.disabled = true;
    whoSel.innerHTML = `<option value="${cu.id}">${cu.name} (you)</option>`;
  }

  const sel = document.getElementById('conflictEvent');
  sel.innerHTML = future.map(e=>`<option value="${e.id}">${e.title} — ${fmtDate(e.date)}</option>`).join('');
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
        <div class="conflict-reason">${c.reason}${c.notes?' — '+c.notes:''}</div>
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
        <div><h3 style="margin:0;">${ev.title}</h3><div class="cal-meta">${fmtDate(ev.date)}${ev.startTime?' · '+ev.startTime:''}</div></div>
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
        <span>${c.name}</span>
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
        ${events.map(ev=>`<th title="${ev.title}">${new Date(ev.date+'T00:00').toLocaleDateString(undefined,{month:'numeric',day:'numeric'})}</th>`).join('')}
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
          return `<tr><td class="att-name-cell">${c.name}<br><span style="font-size:10px;color:var(--paper-dim);">${(c.departments||[]).map(d=>deptInfo(d).label).join(', ')||'No team'} · ${c.classPeriod||''}</span></td>${cells}<td class="att-total-cell" style="color:${absentCount(c.id)>=(globalState.attendanceAlertThreshold||3)?'var(--red)':'var(--paper)'}">${absentCount(c.id)}</td></tr>`;
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

function switchBehaviorSub(sub){
  ui.behaviorSub = sub;
  document.querySelectorAll('#behaviorSubtabs button').forEach(b=>b.classList.toggle('active', b.dataset.behSub===sub));
  document.getElementById('behaviorLogView').style.display = sub==='log' ? 'block' : 'none';
  document.getElementById('behaviorSummaryView').style.display = sub==='summary' ? 'block' : 'none';
  if(sub==='log') renderBehaviorLog(); else renderBehaviorSummary();
}
function renderBehaviorView(){
  const authorized = canManageBehavior();
  document.getElementById('behaviorLockedMsg').style.display = authorized ? 'none' : 'block';
  document.getElementById('behaviorAuthorizedWrap').style.display = authorized ? 'block' : 'none';
  if(!authorized) return;
  if(!ui.behaviorWeekCursor) ui.behaviorWeekCursor = weekStartISO(todayISO());
  switchBehaviorSub(ui.behaviorSub || 'log');
}

function renderBehaviorLog(){
  const cfg = state.behaviorConfig || { pointsPerDay:20, daysPerWeek:5 };
  document.getElementById('behPtsLabel').textContent = cfg.pointsPerDay;
  document.getElementById('behDeduction').value = cfg.pointsPerDay;
  document.getElementById('behDate').value = document.getElementById('behDate').value || todayISO();

  const studentSel = document.getElementById('behStudent');
  const students = state.crew.filter(c=>c.role==='student').sort((a,b)=>a.name.localeCompare(b.name));
  studentSel.innerHTML = students.map(c=>`<option value="${c.id}">${c.name}${(c.departments||[]).includes('cast') && c.castRole ? ' as '+c.castRole : ''} — ${(c.departments||[]).map(d=>deptInfo(d).label).join(', ')||'No team'}</option>`).join('') || '<option value="">No students on roster</option>';

  const list = document.getElementById('behaviorIncidentList');
  const sorted = [...state.behaviorIncidents].sort((a,b)=>b.date.localeCompare(a.date) || b.timestamp.localeCompare(a.timestamp));
  if(!sorted.length){ list.innerHTML = `<div class="empty-state">No infractions logged yet.</div>`; return; }
  list.innerHTML = sorted.slice(0,50).map(i=>`
    <div class="incident-card">
      <div class="incident-top"><span><b>${i.crewName}</b></span><span class="mono" style="color:var(--red);">−${i.deduction} pts</span></div>
      <div class="incident-meta">${fmtDate(i.date)} · ${i.reason||'No reason given'} · logged by ${i.loggedBy||'someone'}</div>
      ${i.notes?`<div class="incident-note">${i.notes}</div>`:''}
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
  state.behaviorIncidents.push({
    id:cryptoId(), crewId, crewName:student.name, date, reason, deduction, notes,
    loggedBy, loggedByEmail: activeEmail()||'', timestamp:new Date().toISOString()
  });
  logChange(`Behavior infraction logged for ${student.name} (${fmtDate(date)}).`, {type:'crew', crewId});
  await saveState(); renderBehaviorLog(); renderNotifications();
  document.getElementById('behReason').value = ''; document.getElementById('behNotes').value = '';
  toast('Infraction logged');
}

function renderBehaviorSummary(){
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
    return { name: c.name + (isCast && c.castRole ? ` (${c.castRole})` : ''), classPeriod:c.classPeriod||'', departments:(c.departments||[]).map(d=>deptInfo(d).label).join(', ')||'No team', score:r.score, maxScore:r.maxScore, incidentCount:r.incidentCount, cls };
  });

  wrap.innerHTML = `
    <div class="att-grid-scroll">
    <table class="att-table" style="white-space:normal;">
      <thead><tr>
        <th class="att-name-th">Student</th><th>Class</th><th>Team</th><th># Infractions</th><th>Score</th>
      </tr></thead>
      <tbody>
        ${rows.map(r=>`<tr>
          <td class="att-name-cell">${r.name}</td>
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

  if(ui.activeSub === 'tasks') renderTasksSub(content, dep, depState);
  if(ui.activeSub === 'templates') renderTemplatesSub(content, dep, depState);
  if(ui.activeSub === 'report') renderReportFormSub(content, dep, depState);
  if(ui.activeSub === 'reports') renderReportsHistorySub(content, dep, depState);
}

function renderTasksSub(content, dep, depState){
  if(!canViewDept(dep.key)){
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
          ${deptCrewOptions.map(c=>`<option value="${c.id}">${c.name}${c.email?'':' (no email on file)'}</option>`).join('')}
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
      <div class="task-title" style="margin-bottom:0;">${t.title}</div>
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
  assignee.innerHTML = `<option value="">— Unassigned —</option>` + deptCrewOptions.map(c=>`<option value="${c.id}" ${c.id===t.assignedToCrewId?'selected':''}>${c.name}</option>`).join('');
  assignee.addEventListener('change', async ()=>{
    const person = state.crew.find(c=>c.id===assignee.value);
    t.assignedToCrewId = assignee.value || null;
    t.assignedTo = person ? person.name : '';
    t.deadlineAlertSent = false; // reassigning re-arms the deadline alert for the new person
    await saveState();
  });
  metaRow.appendChild(assignee);
  if(isDirector()){
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
  if(!canViewDept(dep.key)){
    content.innerHTML = `<div class="empty-state"><div class="lamp">🔒</div>${dep.label} templates are only visible to ${dep.label} crew and the Director.</div>`;
    return;
  }
  content.innerHTML = `
    <div class="lockmsg" style="display:${isDirector()?'none':'block'}">Viewing as Team — only the Teacher/Director can create or apply templates.</div>
    <div id="tplList"></div>
    ${isDirector() ? `
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
    card.innerHTML = `<h3 style="margin-bottom:6px;">${tpl.name}</h3><p style="font-size:12.5px;color:var(--paper-dim);margin:0 0 10px;">${tpl.items.length} task(s)</p>
      <button class="btn small" data-act="apply" data-id="${tpl.id}">Apply — creates ${tpl.items.length} tasks</button>
      ${isDirector()?`<button class="btn danger small" data-act="del" data-id="${tpl.id}" style="margin-left:8px;">Delete</button>`:''}`;
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

  if(isDirector()){
    let items = [{title:'', workType:'Build', priority:'medium', hours:1}];
    function renderItems(){
      const wrap = document.getElementById('tplItemsWrap'); wrap.innerHTML='';
      items.forEach((it,idx)=>{
        const row = document.createElement('div'); row.className='add-form-row'; row.style.marginTop='8px';
        row.innerHTML = `
          <input type="text" value="${it.title}" placeholder="Item title" data-f="title" data-i="${idx}" style="flex:2; min-width:160px; background:rgba(0,0,0,0.2); border:1px solid var(--line); color:var(--paper); padding:7px 9px; border-radius:3px;">
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
  if(!isDirector() && !u){
    content.innerHTML = `<div class="empty-state">Sign in with Google, or enter your email (top right), to submit a report — reports and attendance are tied to your class period, so we need to know who's submitting. If you've done that and still see this, ask the Director to add your email to the roster.</div>`;
    return;
  }
  if(!isDirector() && !canViewDept(dep.key)){
    content.innerHTML = `<div class="empty-state"><div class="lamp">🔒</div>Only ${dep.label} crew and the Director can submit a report for this department.</div>`;
    return;
  }
  const classOptions = ['Class A','Class B','Class C','Class D'];
  const effectiveClass = isDirector() ? (ui.reportClassPeriod || classOptions[0]) : u.classPeriod;
  const deptCrew = state.crew.filter(c=>(c.departments||[]).includes(ui.activeDept) && c.classPeriod===effectiveClass);
  content.innerHTML = `
    <div class="card">
      <h3>${dep.label} — Submit Today's Report</h3>
      <div class="form-grid">
        <input type="date" id="repDate" value="${todayISO()}">
        <select id="repShift">${SHIFTS.map(s=>`<option>${s}</option>`).join('')}</select>
        ${isDirector()
          ? `<select id="repClassPeriod">${classOptions.map(cp=>`<option ${cp===effectiveClass?'selected':''}>${cp}</option>`).join('')}</select>`
          : `<span class="tag" style="align-self:center;">${u.classPeriod}</span>`}
      </div>
      <div style="margin:12px 0;">
        <label style="font-size:11.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em; display:block; margin-bottom:6px;">Team present today (${effectiveClass})</label>
        <div class="checkbox-row" id="repTeamChecks">${deptCrew.map(c=>`<label><input type="checkbox" value="${c.id}" checked> ${c.name}</label>`).join('') || '<span style="font-size:12px;color:var(--paper-dim)">No crew assigned to this department/class period yet — add them in Setup.</span>'}</div>
        <label style="font-size:11.5px; color:var(--paper-dim); text-transform:uppercase; letter-spacing:0.05em; display:block; margin:10px 0 6px;">Flag non-contributors</label>
        <div class="checkbox-row" id="repNonContribChecks">${deptCrew.map(c=>`<label><input type="checkbox" value="${c.id}"> ${c.name}</label>`).join('')}</div>
      </div>
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
  if(isDirector()){
    document.getElementById('repClassPeriod').addEventListener('change', (e)=>{
      ui.reportClassPeriod = e.target.value;
      renderReportFormSub(content, dep, depState);
    });
  }
  document.getElementById('submitReportBtn').addEventListener('click', async ()=>{
    const teamIds = Array.from(document.querySelectorAll('#repTeamChecks input:checked')).map(i=>i.value);
    const nonContribIds = Array.from(document.querySelectorAll('#repNonContribChecks input:checked')).map(i=>i.value);
    const classPeriod = isDirector() ? document.getElementById('repClassPeriod').value : u.classPeriod;
    const doneCount = depState.tasks.filter(t=>t.status==='done').length;
    const totalCount = depState.tasks.length;
    const suggestedGrade = totalCount ? Math.round(doneCount/totalCount*100) : 0;
    const report = {
      id:cryptoId(), date: document.getElementById('repDate').value, shift: document.getElementById('repShift').value, classPeriod,
      teamMemberIds: teamIds, nonContributorIds: nonContribIds,
      completedTasks: document.getElementById('repCompleted').value.trim(),
      inProgressTasks: document.getElementById('repInProgress').value.trim(),
      notes: document.getElementById('repNotes').value.trim(), challenges: document.getElementById('repChallenges').value.trim(),
      tasksCompletedCount: doneCount, status:'submitted', grade: suggestedGrade, gradeLetter: letterFor(suggestedGrade),
      teacherFeedback:'', verifiedBy:'', verifiedDate:'', individualAdjustments:[]
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

function renderReportDetail(r, dep, depState, container){
  const teamNames = r.teamMemberIds.map(id=>{ const c=state.crew.find(x=>x.id===id); return c?c.name:'—'; });
  const nonContribNames = r.nonContributorIds.map(id=>{ const c=state.crew.find(x=>x.id===id); return c?c.name:'—'; });
  container.innerHTML = `
    <div class="summary-line" style="display:flex; justify-content:space-between; padding:6px 0; border-bottom:1px dashed var(--line); font-size:13px;"><span>Team present</span><span class="mono">${teamNames.join(', ')||'—'}</span></div>
    ${(nonContribNames.length && isDirector())?`<div class="summary-line" style="display:flex; justify-content:space-between; padding:6px 0; border-bottom:1px dashed var(--line); font-size:13px; color:var(--red);"><span>Flagged non-contributors</span><span class="mono">${nonContribNames.join(', ')}</span></div>`:''}
    ${r.completedTasks?`<p style="font-size:13px; margin-top:10px;"><b>Completed:</b><br>${r.completedTasks.replace(/\\n/g,'<br>')}</p>`:''}
    ${r.inProgressTasks?`<p style="font-size:13px;"><b>In progress:</b><br>${r.inProgressTasks.replace(/\\n/g,'<br>')}</p>`:''}
    ${r.notes?`<p style="font-size:13px;"><b>Notes:</b> ${r.notes}</p>`:''}
    ${r.challenges?`<p style="font-size:13px;"><b>Challenges:</b> ${r.challenges}</p>`:''}
    ${r.teacherFeedback?`<div class="card" style="margin-top:10px;"><h3>Teacher Feedback</h3><p style="font-size:13px;">${r.teacherFeedback}</p></div>`:''}
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
    const g = prompt(`Adjusted grade for ${crewMember.name}:`, r.grade); if(g===null) return;
    const note = prompt('Note (optional):','') || '';
    r.individualAdjustments.push({ crewId: crewMember.id, crewName: crewMember.name, adjustedGrade: parseFloat(g)||0, teacherNote: note });
    await saveState(); renderDepartments();
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
    <span class="piece-chip">${p.name} <span class="piece-status" data-idx="${idx}">${p.status}</span> <button data-del="${idx}" style="background:none;border:none;color:var(--paper-dim);cursor:pointer;">✕</button></span>
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
        <div><b>${rec.name}</b>${rec.email?` <span class="mono" style="font-size:11px;color:var(--paper-dim);">${rec.email}</span>`:''}</div>
        ${isDirector() ? `<button class="task-del" data-id="${rec.id}">✕</button>` : ''}
      </div>
      <div class="costume-grid">${MEASURE_FIELDS.map(m=>rec[m.key]?`<div><label>${m.label}</label>${rec[m.key]}</div>`:'').join('')}</div>
      ${rec.pieces.length?`<div>${rec.pieces.map(p=>`<span class="piece-chip">${p.name} <span class="piece-status">${p.status}</span></span>`).join('')}</div>`:''}
      ${rec.notes?`<p style="font-size:12.5px;color:var(--paper-dim);margin-top:8px;">${rec.notes}</p>`:''}
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
  const list = document.getElementById('notifList'); list.innerHTML = '';
  const visible = visibleChangelog();
  if(!visible.length){ list.innerHTML = `<div class="empty-state">No changes yet.</div>`; return; }
  visible.forEach(n=>{
    const unread = !device.lastSeenChangelog || n.timestamp > device.lastSeenChangelog;
    const item = document.createElement('div'); item.className = 'notif-item ' + (unread?'unread':'read');
    item.innerHTML = `<div class="notif-dot"></div><div><div>${n.message}</div><div class="notif-time">${fmtDateTime(n.timestamp)}</div></div>`;
    list.appendChild(item);
  });
}

// ---------------- SETUP ----------------
function renderSetup(){
  const noDirectorsYet = !(globalState.directorEmails||[]).length;
  document.getElementById('claimDirectorCard').style.display = (noDirectorsYet && authUser) ? 'block' : 'none';
  document.getElementById('dirAccessCard').style.display = isDirector() ? 'block' : 'none';
  document.getElementById('attendanceThreshold').value = globalState.attendanceAlertThreshold || 3;
  const behCfg = state.behaviorConfig || { pointsPerDay:20, daysPerWeek:5 };
  document.getElementById('behPointsPerDay').value = behCfg.pointsPerDay;
  document.getElementById('behDaysPerWeek').value = behCfg.daysPerWeek;

  document.getElementById('emailAlertsCard').style.display = isDirector() ? 'block' : 'none';
  const ejs = globalState.emailjs || { publicKey:'', serviceId:'', templateAbsence:'', templateDeadline:'' };
  document.getElementById('ejsPublicKey').value = ejs.publicKey;
  document.getElementById('ejsServiceId').value = ejs.serviceId;
  document.getElementById('ejsTemplateAbsence').value = ejs.templateAbsence;
  document.getElementById('ejsTemplateDeadline').value = ejs.templateDeadline;

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
  document.getElementById('resetCard').style.display = isDirector() ? 'block' : 'none';

  const deptWrap = document.getElementById('rosterDeptChecks');
  deptWrap.innerHTML = DEPARTMENTS.map(d=>`<label><input type="checkbox" value="${d.key}"> ${d.label}</label>`).join('');
  const list = document.getElementById('rosterList'); list.innerHTML = '';
  if(!state.crew.length){ list.innerHTML = `<div class="empty-state">No cast/crew added yet.</div>`; }
  state.crew.forEach(c=>{
    const wrap = document.createElement('div');
    const row = document.createElement('div'); row.className = 'roster-row';
    const isCast = (c.departments||[]).includes('cast');
    row.innerHTML = `<span class="dot" style="background:${c.role==='teacher'?'var(--gold)':'var(--amber)'}"></span>
      <span class="rname">${c.name}${c.role==='teacher'?' <span class="mono" style="font-size:10px;color:var(--gold)">TEACHER</span>':''}${isCast && c.castRole?` <span class="mono" style="font-size:10.5px;color:var(--cast,#8C3B3B);">as ${c.castRole}</span>`:''}<br><span class="mono" style="font-size:10px;color:var(--paper-dim);">${c.email||'no sign-in email set'}</span></span>
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
        toast(`${c.name} moved to ${c.classPeriod}`);
      });
      row.querySelector('[data-editemail]').addEventListener('click', async ()=>{
        const val = prompt(`Sign-in email for ${c.name}:`, c.email||'');
        if(val===null) return;
        c.email = val.trim();
        await saveState(); renderSetup();
        toast('Email updated');
      });
      if(isCast){
        row.querySelector('[data-editrole]').addEventListener('click', async ()=>{
          const val = prompt(`Cast role / character for ${c.name}:`, c.castRole||'');
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
    state.crew = state.crew.filter(c=>c.id!==btn.dataset.id);
    await saveState(); renderSetup(); renderHeader();
  }));
}
async function bulkImportCrew(){
  if(!isDirector()){ toast('Only the Director can import crew'); return; }
  const raw = document.getElementById('bulkImportText').value;
  const lines = raw.split('\n').map(l=>l.trim()).filter(Boolean);
  if(!lines.length){ toast('Paste at least one name'); return; }
  let count = 0;
  lines.forEach(line=>{
    const parts = line.split(',').map(p=>p.trim());
    const name = parts[0];
    if(!name) return;
    const email = parts[1] && parts[1].includes('@') ? parts[1] : '';
    state.crew.push({ id:cryptoId(), name, role:'student', classPeriod:'Class A', departments:[], castRole:'', email, parentEmail:'', parentPhone:'' });
    count++;
  });
  await saveState();
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
  state.crew.push({ id:cryptoId(), name, role, classPeriod, departments, castRole, email, parentEmail, parentPhone });
  await saveState();
  document.getElementById('rosterName').value=''; document.getElementById('rosterCastRole').value=''; document.getElementById('rosterEmail').value=''; document.getElementById('rosterParentEmail').value=''; document.getElementById('rosterParentPhone').value='';
  document.querySelectorAll('#rosterDeptChecks input').forEach(i=>i.checked=false);
  renderSetup(); renderHeader();
  toast('Crew member added');
}

// ---------------- NAV ----------------
function switchView(view){
  document.querySelectorAll('.tabs button').forEach(b=>b.classList.toggle('active', b.dataset.view===view));
  document.querySelectorAll('.view').forEach(v=>v.classList.toggle('active', v.id==='view-'+view));
  if(view==='dashboard') renderDashboard();
  if(view==='productions') renderProductionsView();
  if(view==='calendar'){ renderCalendarForm(); switchCalSub(ui.calSub); }
  if(view==='conflicts') renderConflicts();
  if(view==='attendance') renderAttendanceView();
  if(view==='behavior') renderBehaviorView();
  if(view==='departments') renderDepartments();
  if(view==='costumes') renderCostumesView();
  if(view==='notifications') renderNotifications();
  if(view==='setup') renderSetup();
}
function renderAll(){
  renderHeader(); renderCalendarForm();
  renderDashboard(); renderProductionsView(); renderCalMonth(); renderCalendar(); renderConflicts(); renderAttendanceView(); renderBehaviorView(); renderDepartments(); renderCostumesView(); renderNotifications(); renderSetup();
}

async function init(){
  globalState = await loadOrMigrateGlobalState();
  device = loadDevice();
  if(!globalState.activeProductionId && globalState.productions.length){ globalState.activeProductionId = globalState.productions[0].id; }
  currentProductionId = globalState.activeProductionId;
  state = await loadProductionState(currentProductionId);
  if(!state.costumeRecords) state.costumeRecords = [];
  if(!state.behaviorIncidents) state.behaviorIncidents = [];
  if(!state.behaviorConfig) state.behaviorConfig = { pointsPerDay:20, daysPerWeek:5 };
  if(!globalState.emailjs) globalState.emailjs = { publicKey:'', serviceId:'', templateAbsence:'', templateDeadline:'' };
  renderAll();
  renderCostumeMeasureGrid(); renderCostumePieces();
  initEmailJs();

  await waitForFirebase();
  if(window.__fb){
    window.__fb.onAuthStateChanged(window.__fb.auth, (user)=>{
      authUser = user ? { email:user.email, displayName:user.displayName } : null;
      if(authUser && device.manualEmail){ device.manualEmail = ''; saveDevice(); } // real sign-in takes over
      renderHeader(); renderDashboard(); renderProductionsView(); renderCalendarForm(); renderCalMonth(); renderCalendar();
      renderConflicts(); renderAttendanceView(); renderBehaviorView(); renderDepartments(); renderCostumesView(); renderNotifications(); renderSetup();
      if(isDirector()) checkAndSendDeadlineAlerts();
    });
  }
  document.getElementById('googleSignInBtn').addEventListener('click', signInWithGoogle);
  document.getElementById('toggleEmailPwBtn').addEventListener('click', ()=>{
    const wrap = document.getElementById('emailPwWrap');
    wrap.style.display = wrap.style.display==='none' ? 'flex' : 'none';
    document.getElementById('manualWrap').style.display = 'none';
  });
  document.getElementById('toggleManualBtn').addEventListener('click', ()=>{
    const wrap = document.getElementById('manualWrap');
    wrap.style.display = wrap.style.display==='none' ? 'flex' : 'none';
    document.getElementById('emailPwWrap').style.display = 'none';
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
  document.getElementById('manualEmailBtn').addEventListener('click', ()=>{
    const val = document.getElementById('manualEmailInput').value.trim();
    if(!val || !val.includes('@')){ toast('Enter a valid email'); return; }
    setManualEmail(val);
    document.getElementById('manualEmailInput').value = '';
    toast("Continuing as " + val + " (unverified)");
  });
  document.getElementById('signOutBtn').addEventListener('click', signOutUser);
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
  document.getElementById('ejsConfigSaveBtn').addEventListener('click', async ()=>{
    if(!isDirector()){ toast('Only the Director can change this'); return; }
    globalState.emailjs = {
      publicKey: document.getElementById('ejsPublicKey').value.trim(),
      serviceId: document.getElementById('ejsServiceId').value.trim(),
      templateAbsence: document.getElementById('ejsTemplateAbsence').value.trim(),
      templateDeadline: document.getElementById('ejsTemplateDeadline').value.trim(),
    };
    await saveGlobalState();
    initEmailJs();
    toast('Email alert settings saved');
  });
  document.getElementById('bulkImportBtn').addEventListener('click', bulkImportCrew);
  document.getElementById('markReadBtn').addEventListener('click', ()=>{ device.lastSeenChangelog = new Date().toISOString(); saveDevice(); renderNotifications(); toast('Marked read'); });
  document.getElementById('exportAllGradesBtn').addEventListener('click', ()=>{
    if(!isDirector()){ toast('Only the Director can export grades'); return; }
    exportAllDeptGradesCsv();
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
