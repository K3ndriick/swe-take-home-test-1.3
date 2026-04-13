# Frontend Summary

## Tech Stack

- **Framework:** React 19 with TypeScript
- **Build Tool:** Vite 7
- **Module System:** ESM
- **Styling:** Plain CSS (no library)
- **No testing framework** — no frontend tests provided

## Project Structure

```
frontend/
├── package.json
├── tsconfig.json
├── vite.config.ts
└── src/
    ├── main.tsx               # Entry point — renders App
    ├── App.tsx                # Main layout — CheckForm + CheckHistory + ToastContainer
    ├── App.css                # All styles (form, history, toasts, layout)
    ├── CheckForm.tsx          # Inspection form — where most tasks live
    ├── CheckHistory.tsx       # Displays past inspections with filters
    ├── api.ts                 # API client (fetch wrapper for backend)
    ├── types.ts               # TypeScript interfaces (mirrors backend types)
    ├── Toast.tsx              # Toast notification component (provided, ready to use)
    ├── useToast.ts            # Toast hook — showToast(message, type)
    ├── index.css              # Base CSS reset
    └── assets/                # Static assets
```

## Component Architecture

```
App
├── CheckForm          — Left panel: submit new inspection
├── CheckHistory       — Right panel: view past inspections
└── ToastContainer     — Fixed position toast notifications
```

## Key Files

### App.tsx
- Two-column layout (form left, history right)
- `refreshKey` state triggers CheckHistory to refetch after form submission
- Toast system initialized via `useToast()` hook but NOT passed to child components
- `ToastContainer` rendered at root level

### CheckForm.tsx (Most tasks are here)
- Fetches vehicles on mount for the dropdown
- Form fields: vehicle select, odometer input, checklist items
- Submits via `api.createCheck()`, resets form on success
- Displays validation errors from backend

### CheckHistory.tsx
- Fetches and displays past inspections for a selected vehicle
- Filters by vehicle ID (required) and hasIssue flag (optional)
- Sorts by creation date (newest first)
- Displays check cards with status badges, checklist items, and notes

### api.ts
- `api.getVehicles()` — GET /vehicles
- `api.createCheck(data)` — POST /checks
- `api.getChecks(vehicleId, hasIssue?)` — GET /checks?vehicleId=...
- No delete endpoint defined yet

### Toast.tsx + useToast.ts
- `showToast(message, "success" | "error")` — adds a toast
- `dismissToast(id)` — removes a toast
- Toasts auto-dismiss after 4 seconds
- Slide-in animation, positioned top-right
- CSS already provided in App.css

## Data Models (types.ts)

Mirrors backend types exactly:
- `Vehicle` — id, registration, make, model, year
- `CheckItem` — key (CheckItemKey), status ("OK" | "FAIL")
- `Check` — id, vehicleId, odometerKm, items, note?, hasIssue, createdAt
- `CreateCheckRequest` — vehicleId, odometerKm, items, note?
- `ErrorResponse` — error.code, error.message, error.details[]

## Bugs & Issues (Pre-fix State)

### CheckForm.tsx Bugs
1. **Line 5:** `CHECK_ITEMS` only has 3 items (`TYRES, BRAKES, LIGHTS`) — missing `OIL` and `COOLANT`. Backend requires all 5.
2. **Line 16:** Status initialized as `true as unknown as "OK"` — stores booleans instead of `"OK"/"FAIL"` strings. Same casting hack on lines 30 and 53.
3. **Line 106:** Odometer input is `type="text"` — allows non-numeric input.
4. **Lines 120-127:** Checklist dropdowns use `"true"/"false"` values instead of `"OK"/"FAIL"`.

### Missing Features
- No notes textarea field
- No toast notifications on success/failure
- No delete functionality
- Toast system is set up in App.tsx but `showToast` is not passed to CheckForm

## Tasks to Complete

### Task 1: Fix Form Input (CheckForm.tsx)
- Add missing check items (OIL, COOLANT)
- Change odometer input type to "number"
- Fix boolean-to-string casting for status values
- Update handleItemStatusChange to use CheckItemStatus type

### Task 2: Change Dropdown to Radio Buttons/Checkboxes (CheckForm.tsx:114-131)
- Replace `<select>` dropdowns for checklist items with radio buttons or checkboxes
- Radio buttons recommended (OK/FAIL are mutually exclusive)

### Task 3: Add Notes Field (CheckForm.tsx)
- Add textarea for optional notes (max 300 characters)
- Optional: display character counter (e.g., "45/300")
- Include in API request (`note` field in CreateCheckRequest)
- Reset after submission

### Task 4: Toast Notifications (CheckForm.tsx + App.tsx)
- Pass `showToast` from App.tsx down to CheckForm
- Show success toast on successful submission
- Show error toast on failure
- Optional: include meaningful error message in toast

### Task 5: (Optional) Delete Inspection Record
- Add `deleteCheck` to api.ts
- Add delete UI to CheckHistory.tsx
- Requires backend Task 3 (DELETE /checks/:id) to be completed

## Styling Notes

- CSS is in App.css — all styles in one file
- `.radio-group` styles already exist (lines 130-146) — ready for Task 2
- `.toast-*` styles already exist (lines 334-404) — ready for Task 4
- `.check-note` styles already exist (lines 315-332) — ready for Task 3
- Responsive: grid switches to single column at 1024px

## Scripts

| Command          | Description                |
|------------------|----------------------------|
| `npm run dev`    | Start Vite dev server      |
| `npm run build`  | Type-check + build for prod|
| `npm run lint`   | Run ESLint                 |
| `npm run preview`| Preview production build   |
