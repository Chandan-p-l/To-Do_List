# To‑Do (List/Card, Drag Reorder, Undo)

A fast, offline-first to‑do app in a single HTML file. Tasks persist in the browser via localStorage, support due date/time, drag‑and‑drop reordering, list/card views, filters, sort by due, bulk deletes, and one‑click undo.

## Demo

- Download or clone the repo.
- Open `index.html` directly in any modern browser (Chrome, Edge, Firefox, Safari).
- No server or localhost required.

## Features

- Add tasks with optional due date/time (native datetime picker).
- Toggle between List view and Card view instantly.
- Drag-and-drop reorder to place related tasks next to each other; order is saved.
- Filters: All, Active, Completed.
- Sort by time: order tasks by due date (toggle on/off).
- Edit task text and due time.
- Bulk actions: Delete completed and Delete all.
- Undo: restores the last destructive action (single delete, clear completed, delete all, reorder).
- Data persistence using browser localStorage.

## How To Use

- Add a task:
  - Type text, optionally pick a date/time, press Enter or click “Add”.
- Mark done:
  - Use the checkbox on each task.
- Edit text:
  - Click “Edit” on a task to change its label.
- Set time:
  - Click “Set time” to open the datetime picker, then Save.
- Reorder:
  - Drag a task and drop it above/below another task to rearrange.
- View modes:
  - Click “List view” or “Card view” chips to switch layout.
- Filters:
  - Use the All, Active, Completed chips.
- Sort by time:
  - Toggle the “Sort by time” chip to sort by due date.
- Bulk delete:
  - “Delete completed Task” removes all done items.
  - “Delete all” clears all tasks.
- Undo:
  - After a delete/clear/reorder, click “Undo” to restore the previous state.

## File Structure

- `index.html` — Single file containing:
  - Markup for controls, filters, task list, and a datetime modal.
  - Responsive CSS with light/dark support and card/list layouts.
  - Vanilla JS for state, drag-and-drop, undo, and localStorage.

## Data Model

Each task object:
