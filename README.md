
## Usage
- Enter a task title, optionally pick a due date/time, then click Add or press Enter.
- Toggle completion with the checkbox.
- Edit changes the title; Set time updates the due date/time.
- Delete removes a single task; Clear all removes all; Delete completed removes only completed tasks.
- Use chips to filter (All/Active/Completed) and to toggle Sort by time.

## Tech Stack
- HTML for structure
- CSS for modern, responsive styling
- Vanilla JavaScript for state, rendering, and storage

## Data Model
Each task looks like:

Stored under a versioned key (e.g., `todos.v3`) in `localStorage`.

## Notes
- `localStorage` is per‑origin and persists until site data is cleared; private browsing may not persist.
- `datetime-local` captures local date/time without timezone; value format: `YYYY-MM-DDTHH:MM`.

## Accessibility
- Keyboard add flow with Enter
- Focus styles on inputs
- High-contrast palette with dark/light awareness

## Roadmap
- Inline editing without prompts
- Due reminders/notifications
- Drag-and-drop reordering
- PWA (installable + offline)

## Screenshots
Place images here:
<img width="1919" height="979" alt="Screenshot 2025-09-27 144329" src="https://github.com/user-attachments/assets/18a89bd1-ba11-44c0-8686-349642d862cc" />


## Contributing
Issues and PRs welcome. For larger changes, open an issue first to discuss.


