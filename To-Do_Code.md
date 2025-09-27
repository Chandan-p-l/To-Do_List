<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>To‑Do List with Time (localStorage)</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root {
      --bg: #0b1020;
      --card: #11172a;
      --muted: #8b93a7;
      --text: #e8ecf6;
      --accent: #5b8cff;
      --accent-2: #22c55e;
      --danger: #ef4444;
      --border: #22304e;
      --shadow: 0 10px 30px rgba(0,0,0,0.35);
      --radius: 16px;
      --pad: clamp(14px, 1.8vw, 24px);
      --gap: clamp(8px, 1.6vw, 14px);
      --chip-font: clamp(12px, 1.6vw, 14px);
      --text-font: clamp(14px, 1.8vw, 16px);
      --title-font: clamp(18px, 2.4vw, 22px);
      color-scheme: dark light;
    }

    @media (prefers-color-scheme: light) {
      :root {
        --bg: #f6f8fc;
        --card: #ffffff;
        --muted: #5b6377;
        --text: #0f172a;
        --accent: #2563eb;
        --accent-2: #16a34a;
        --danger: #dc2626;
        --border: #e5e7eb;
        --shadow: 0 10px 25px rgba(2,6,23,0.08);
      }
    }

    * { box-sizing: border-box; }

    html, body {
      height: 100%;
    }

    body {
      margin: 0;
      font-family: Inter, system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
      background:
        radial-gradient(1200px 600px at 10% -10%, rgba(91,140,255,0.15), transparent 60%),
        radial-gradient(1000px 500px at 110% 10%, rgba(34,197,94,0.12), transparent 60%),
        var(--bg);
      color: var(--text);
      min-height: 100dvh;
      padding: var(--pad);
    }

    .app {
      max-width: min(960px, 100%);
      margin: 0 auto;
    }

    .panel {
      background: var(--card);
      border: 1px solid var(--border);
      border-radius: var(--radius);
      box-shadow: var(--shadow);
      overflow: clip;
    }

    header.appbar {
      padding: clamp(14px, 2vw, 18px) clamp(14px, 2vw, 20px);
      border-bottom: 1px solid var(--border);
      display: flex;
      align-items: center;
      gap: 10px;
    }
    header.appbar h1 {
      font-size: var(--title-font);
      margin: 0;
      letter-spacing: 0.2px;
    }

    /* Responsive controls: mobile-first single column, then grow */
    .controls {
      padding: var(--pad);
      display: grid;
      grid-template-columns: 1fr;
      gap: var(--gap);
    }

    /* Tablet: two columns */
    @media (min-width: 560px) {
      .controls {
        grid-template-columns: 1fr 1fr;
      }
      .controls .actions-inline {
        grid-column: 1 / -1;
        display: flex;
        gap: var(--gap);
        flex-wrap: wrap;
      }
    }

    /* Desktop: 1fr input | 220px datetime | auto buttons */
    @media (min-width: 820px) {
      .controls {
        grid-template-columns: 1fr 220px auto auto;
        align-items: start;
      }
      .controls .actions-inline {
        display: contents; /* revert to grid items */
      }
    }

    input[type="text"], input[type="datetime-local"] {
      width: 100%;
      padding: clamp(10px, 1.6vw, 12px);
      border-radius: 10px;
      border: 1px solid var(--border);
      background: transparent;
      color: var(--text);
      outline: none;
      font-size: var(--text-font);
      transition: border-color 0.2s ease, box-shadow 0.2s ease;
      min-height: 42px;
    }
    input[type="text"]::placeholder { color: var(--muted); }
    input[type="text"]:focus, input[type="datetime-local"]:focus {
      border-color: var(--accent);
      box-shadow: 0 0 0 3px color-mix(in oklab, var(--accent) 25%, transparent);
    }

    button {
      border: 0;
      border-radius: 10px;
      padding: clamp(10px, 1.6vw, 12px) clamp(12px, 2vw, 14px);
      cursor: pointer;
      font-weight: 600;
      font-size: var(--text-font);
      transition: transform 0.04s ease, filter 0.15s ease, background 0.2s ease;
      min-height: 42px;
      touch-action: manipulation;
    }
    button:active { transform: translateY(1px); }
    .btn-primary { background: var(--accent); color: white; }
    .btn-secondary { background: color-mix(in oklab, var(--accent) 12%, transparent); color: var(--text); border: 1px solid var(--border); }
    .btn-danger { background: var(--danger); color: white; }
    .btn-ghost { background: transparent; color: var(--text); border: 1px solid var(--border); }

    .filters {
      padding: calc(var(--pad) - 6px) var(--pad);
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: var(--gap);
      border-bottom: 1px solid var(--border);
      flex-wrap: wrap;
    }

    .chip-group { display: flex; gap: 6px; flex-wrap: wrap; }

    .chip {
      padding: 7px 10px;
      border-radius: 999px;
      border: 1px solid var(--border);
      background: transparent;
      color: var(--text);
      cursor: pointer;
      font-size: var(--chip-font);
      min-height: 34px;
    }
    .chip.active { border-color: var(--accent); background: color-mix(in oklab, var(--accent) 15%, transparent); }

    ul#todo-list {
      list-style: none;
      margin: 0;
      padding: calc(var(--pad) - 6px);
      display: grid;
      gap: 8px;
    }

    li.todo {
      display: grid;
      grid-template-columns: auto 1fr auto;
      gap: 10px;
      align-items: center;
      padding: clamp(10px, 1.6vw, 12px);
      border: 1px solid var(--border);
      border-radius: 12px;
      background: linear-gradient(180deg, color-mix(in oklab, var(--card) 92%, transparent), var(--card));
    }

    .toggle {
      appearance: none;
      width: 22px;
      height: 22px;
      border-radius: 6px;
      border: 2px solid var(--border);
      display: grid;
      place-items: center;
      background: transparent;
      cursor: pointer;
    }
    .toggle:checked {
      border-color: var(--accent-2);
      background: color-mix(in oklab, var(--accent-2) 20%, transparent);
    }
    .toggle:checked::after {
      content: "✓";
      color: var(--accent-2);
      font-weight: 900;
      font-size: 14px;
    }

    .title {
      display: flex;
      flex-direction: column;
      gap: 4px;
      min-width: 0;
    }
    .text {
      font-size: var(--text-font);
      line-height: 1.35;
      word-break: break-word;
    }
    .meta {
      font-size: clamp(11px, 1.5vw, 12px);
      color: var(--muted);
    }

    .todo.done .text { text-decoration: line-through; opacity: 0.6; }

    .actions {
      display: flex;
      gap: 6px;
      flex-wrap: wrap;
    }

    .empty {
      margin: 18px;
      padding: 16px;
      text-align: center;
      color: var(--muted);
      border: 1px dashed var(--border);
      border-radius: 12px;
    }

    /* Compact list padding on very small phones */
    @media (max-width: 380px) {
      .actions button { padding-inline: 10px; }
      .chip { padding-inline: 8px; }
    }
  </style>
</head>
<body>
  <div class="app">
    <div class="panel">
      <header class="appbar">
        <h1>Tasks</h1>
      </header>

      <div class="controls">
        <input id="todo-input" type="text" placeholder="What needs to be done?" />
        <input id="todo-due" type="datetime-local" />
        <div class="actions-inline">
          <button id="add-btn" class="btn-primary">Add</button>
          <button id="clear-btn" class="btn-ghost">Clear all</button>
        </div>
      </div>

      <div class="filters">
        <div class="chip-group">
          <button class="chip active" data-filter="all">All</button>
          <button class="chip" data-filter="active">Active</button>
          <button class="chip" data-filter="done">Completed</button>
          <button class="chip" data-sort="due">Sort by time</button>
        </div>
        <div class="chip-group">
          <button id="clear-done" class="chip">Delete completed</button>
        </div>
      </div>

      <ul id="todo-list"></ul>
      <div id="empty" class="empty" hidden>No tasks yet. Add the first one!</div>
    </div>
  </div>

  <script>
    const STORAGE_KEY = 'todos.v3';
    let todos = [];
    let currentFilter = 'all';
    let sortByDue = false;

    const $ = (s) => document.querySelector(s);
    const $$ = (s) => document.querySelectorAll(s);
    const input = $('#todo-input');
    const due = $('#todo-due');
    const addBtn = $('#add-btn');
    const clearBtn = $('#clear-btn');
    const list = $('#todo-list');
    const empty = $('#empty');
    const clearDone = $('#clear-done');

    const uid = () => crypto.randomUUID ? crypto.randomUUID() :
      (Date.now() + Math.random().toString(16).slice(2));

    function save() { localStorage.setItem(STORAGE_KEY, JSON.stringify(todos)); }
    function load() {
      try { todos = JSON.parse(localStorage.getItem(STORAGE_KEY)) || []; }
      catch { todos = []; }
    }

    function fmt(dtStr) {
      if (!dtStr) return '';
      try {
        const d = new Date(dtStr);
        return new Intl.DateTimeFormat(undefined, {
          year: 'numeric', month: 'short', day: '2-digit',
          hour: '2-digit', minute: '2-digit'
        }).format(d);
      } catch { return dtStr; }
    }

    function getView() {
      let arr = [...todos];
      if (currentFilter === 'active') arr = arr.filter(t => !t.done);
      if (currentFilter === 'done') arr = arr.filter(t => t.done);
      if (sortByDue) {
        arr.sort((a, b) => {
          const ax = a.dueAt ? new Date(a.dueAt).getTime() : Infinity;
          const bx = b.dueAt ? new Date(b.dueAt).getTime() : Infinity;
          return ax - bx;
        });
      } else {
        arr.sort((a, b) => b.createdAt - a.createdAt);
      }
      return arr;
    }

    function render() {
      const data = getView();
      list.innerHTML = '';
      empty.hidden = data.length > 0;

      for (const t of data) {
        const li = document.createElement('li');
        li.className = 'todo' + (t.done ? ' done' : '');

        const toggle = document.createElement('input');
        toggle.type = 'checkbox';
        toggle.className = 'toggle';
        toggle.checked = t.done;

        const title = document.createElement('div');
        title.className = 'title';

        const text = document.createElement('div');
        text.className = 'text';
        text.textContent = t.text;

        const meta = document.createElement('div');
        meta.className = 'meta';
        meta.textContent = t.dueAt ? `Due ${fmt(t.dueAt)}` : 'No due time';

        title.append(text, meta);

        const actions = document.createElement('div');
        actions.className = 'actions';

        const editBtn = document.createElement('button');
        editBtn.textContent = 'Edit';
        editBtn.className = 'btn-secondary';

        const dateBtn = document.createElement('button');
        dateBtn.textContent = 'Set time';
        dateBtn.className = 'btn-secondary';

        const delBtn = document.createElement('button');
        delBtn.textContent = 'Delete';
        delBtn.className = 'btn-danger';

        actions.append(editBtn, dateBtn, delBtn);
        li.append(toggle, title, actions);
        list.append(li);

        toggle.addEventListener('change', () => { t.done = toggle.checked; save(); render(); });
        editBtn.addEventListener('click', () => {
          const next = prompt('Edit task:', t.text);
          if (next && next.trim()) { t.text = next.trim(); save(); render(); }
        });
        dateBtn.addEventListener('click', () => {
          const next = prompt('Set due (YYYY-MM-DDTHH:MM):', t.dueAt || '');
          if (next && /^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}$/.test(next)) { t.dueAt = next; save(); render(); }
        });
        delBtn.addEventListener('click', () => {
          todos = todos.filter(x => x.id !== t.id);
          save(); render();
        });
      }
    }

    function addTask() {
      const text = input.value.trim();
      const dueAt = due.value.trim(); // datetime-local produces "YYYY-MM-DDTHH:MM"
      if (!text) return;
      todos.unshift({ id: uid(), text, done: false, createdAt: Date.now(), dueAt: dueAt || null });
      input.value = '';
      due.value = '';
      save(); render();
    }

    addBtn.addEventListener('click', addTask);
    input.addEventListener('keydown', (e) => { if (e.key === 'Enter') addTask(); });
    due.addEventListener('keydown', (e) => { if (e.key === 'Enter') addTask(); });

    clearBtn.addEventListener('click', () => {
      if (todos.length && confirm('Clear all tasks?')) { todos = []; save(); render(); }
    });

    clearDone.addEventListener('click', () => {
      const before = todos.length;
      todos = todos.filter(t => !t.done);
      if (todos.length !== before) { save(); render(); }
    });

    $$('.chip[data-filter]').forEach(chip => {
      chip.addEventListener('click', () => {
        $$('.chip[data-filter]').forEach(c => c.classList.remove('active'));
        chip.classList.add('active');
        currentFilter = chip.dataset.filter;
        render();
      });
    });

    const sortChip = $('.chip[data-sort="due"]');
    sortChip.addEventListener('click', () => {
      sortByDue = !sortByDue;
      sortChip.classList.toggle('active', sortByDue);
      render();
    });

    load(); render();
  </script>
</body>
</html>
