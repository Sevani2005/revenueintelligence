# README – Revenue Intelligence Inbox

## Overview
This is a single‑page, **offline‑ready** web application that implements the Volopay recruitment assignment. All logic runs client‑side (HTML + CSS + JS) and stores state in the browser's `localStorage`.

---

## 1. Prerequisites
- A modern browser (Chrome / Edge / Firefox) that supports ES2020 JavaScript.
- **Because the app loads JSON files via `fetch()`, it must be served over HTTP** – opening `app.html` directly with a `file://` URL will trigger a CORS error.
- You can run a very lightweight static server. Two common ways on Windows:
  1. **Python (if installed)** – open a PowerShell terminal in the project folder and run:
     ```powershell
     python -m http.server 8000
     ```
     Then point the browser to `http://localhost:8000/app.html`.
  2. **Node http‑server** – if you have Node installed:
     ```powershell
     npx http-server -p 8000
     ```
- Alternatively, double‑click `index.html` – it contains the same app code and loads the data via relative URLs **when served**.

---

## 2. Running the Application
1. Start the static server as described above.
2. Open your browser to `http://localhost:8000/app.html` (or the equivalent URL for your server).
3. You will see the **Dashboard** view with placeholder metrics.
4. Click the **Demo Load** button on the top‑right toolbar – this runs the full import‑replay sequence:
   - Loads `data/01_initial.json` (revision 1, 44 records, 17 usable transcripts).
   - Loads `data/02_update.json` (revision 2, updates M07 to provide the 18th transcript).
   - Shows a toast log of each step and updates the UI counters.
5. Switch between **Dashboard**, **Inbox**, **Import**, and **Export** using the sidebar.

---

## 3. Import Replay Steps (what the demo does)
| Step | Action | Expected Log Message |
|------|--------|----------------------|
| 1 | Load initial batch | `Batch: 01_initial.json | Revision: 1 | Records: 44` |
| 2 | Insert 44 records → `added = 44` |
| 3 | Load update batch | `Batch: 02_update.json | Revision: 2 | Records: 1` |
| 4 | Update M07 (rev 2) – transcript becomes usable → `Updated rev 1→2 [TRANSCRIPT NOW AVAILABLE]` |
| 5 | No duplicate records, total stays **44** |
| 6 | UI badges update – Inbox badge shows **44**, Dashboard badge shows **21 Aug 2026** |

All operations are idempotent; re‑running the demo does **not** change counts.

---

## 4. Metric Definitions (used on the Dashboard)
- **Total Records** – number of distinct `(source_type, source_id)` pairs stored.
- **Calls / Meetings** – counts broken out by `source_type`.
- **Usable Transcripts** – records whose `transcript` field (calls) or `turns` array (meetings) is non‑empty.
- **Coverage %** – `usable / total × 100`.
- **Routing Summary** – how many records are tagged with each of the seven routing categories.
- **Review Queue** – records whose automatic routing is `review` (or missing transcript) and need human attention.

All numbers are computed **live** from the current `STATE` object; they reflect any manual route overrides you have applied and persisted via `localStorage`.

---

## 5. Persistence Details
- The entire application state (`STATE`) is serialized to `localStorage` under the key `RI_STATE` after every mutation (import, route change, review status change).
- On page load the app calls `loadState()`; if no stored state exists it starts with an empty inbox.
- Because `localStorage` is scoped to the origin (e.g., `http://localhost:8000`), clearing the browser data or switching to a different port will reset the app.

---

## 6. Export Instructions
1. Navigate to **Export** via the sidebar.
2. Click **Export JSON** – a `revenue_intel_export_YYYYMMDD_HHMMSS.json` file will be downloaded containing an array of all records with their current route, manual‑override flag, and revision.
3. Click **Export CSV** – a similar CSV file is produced with the same columns.
4. Both files are placed in the browser's download folder; there is no server‑side file creation.

---

## 7. Manual Route Override Persistence Demo
1. In the **Inbox** view, open any record (e.g., `M07`).
2. Toggle a routing checkbox (e.g., add `support`).
3. The UI shows a little “✏️ manual” badge.
4. Refresh the page or close/re‑open the browser – the override remains because it is saved in `localStorage`.
5. Re‑run the **Demo Load** – the manual flag is retained; the import engine respects it and does not overwrite the route list.

---

## 8. Known Limitations / Gotchas
- **Local‑file loading**: opening `app.html` directly will fail to fetch the JSON files. Always use a local HTTP server.
- The **Inbox badge** (`44`) is hard‑coded in the HTML; it updates after the first successful import. If you start the app without importing, it will still show `44` which is misleading – the toast log will clarify the real state.
- No AI‑driven live analysis – all findings are pre‑generated and shipped in the `PRE_GENERATED_ANALYSIS` object.
- The app does not enforce authentication; it is intended for offline review only.

---

## 9. How to Run the Automated Tests (optional)
The checklist items under **Part 10 – Tests** have been verified manually. If you wish to repeat them:
```powershell
# 1. Start the server
python -m http.server 8000
# 2. Open the app, click Demo Load, and observe the toast log.
# 3. Check the badge numbers, refresh, and re‑run Demo Load – counts should stay the same.
# 4. Export JSON/CSV and open the files – they should contain 44 records.
```

---

*Prepared on 2026‑10‑22 (IST).*
