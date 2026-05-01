# Instructor Solution Guide

The app is intentionally broken, but it should feel like a real internal tool once the bugs are fixed.

## Coverage Summary

This project is suitable for software engineering or full-stack associate learners because it covers:

- Console-driven JavaScript failures.
- Form validation logic.
- Array filtering and derived state.
- CSS class and rendered UI mismatches.
- Browser storage persistence.
- Network request debugging.
- Event delegation and `data-*` attributes.
- Regression checking after state changes.
- Documentation and commit-message discipline.

It is intentionally frontend-first. It does not try to simulate backend debugging, but it does cover the browser-side debugging methods that junior SE and full-stack learners should already be able to use.

## Debugging Coverage Matrix

| Bug                        | Main Symptom                     | Best Tool           | Skill Practiced                                 |
| -------------------------- | -------------------------------- | ------------------- | ----------------------------------------------- |
| Submit handler typo        | Add check does nothing           | Console             | Read stack traces and undefined function errors |
| Validation logic           | Bad form input is accepted       | UI plus code        | Reason about boolean conditions                 |
| Search scope               | Expected checks do not appear    | UI plus code        | Inspect filter logic and user expectations      |
| Status filter mismatch     | Wrong rows show                  | UI plus code        | Compare selected value to the correct property  |
| Badge class slug           | `In Progress` badge has no style | Elements            | Match rendered classes to CSS selectors         |
| Fixed count bug            | Score stays wrong                | UI plus code        | Derive metrics from state correctly             |
| Due soon metric bug        | Count is reversed                | UI plus code        | Test date logic and edge conditions             |
| Delete action mismatch     | Delete button does nothing       | Elements plus code  | Trace event delegation and dataset keys         |
| Status change persistence  | UI changes but state is stale    | Application plus UI | Persist changes and refresh derived state       |
| Local storage key mismatch | Data disappears on refresh       | Application         | Compare write key and read key                  |
| Reset fetch path           | Reset demo fails                 | Network             | Read 404s and verify request paths              |
| CSV export title bug       | Exported file has blank titles   | Output plus code    | Validate generated output, not just UI          |

## Recommended Teaching Flow

1. Ask students to run the app and list broken behavior without editing.
2. Make them pick one bug.
3. Require evidence from DevTools before the fix.
4. Allow only one focused fix at a time.
5. Make them retest and write one report entry.
6. Make them commit with a clear message.

## Intentional Bugs and Fixes

### 1. Add check button does nothing

- Symptom: Submitting the add form throws a Console error.
- Tool: Console.
- File: `js/app.js`.
- Broken line:

```js
form.addEventListener("submit", (event) => handleAddChek(event));
```

- Fix:

```js
form.addEventListener("submit", (event) => handleAddCheck(event));
```

- Commit idea: `fix: connect launch check submit handler`

### 2. Required field validation is wrong

- Symptom: After the submit handler is fixed, a check can be added when only one required field is completed.
- Tool: UI and code inspection.
- Broken line:

```js
if (!title && !category) {
```

- Fix:

```js
if (!title || !category) {
```

- Commit idea: `fix: require title and category before adding checks`

### 3. Search only checks the owner field

- Symptom: Searching for "SEO", "SSL", "Critical", or "Fixed" does not return expected results unless the owner matches.
- Tool: Console logs or code inspection.
- Broken code:

```js
let filtered = checks.filter((check) =>
  check.owner.toLowerCase().includes(searchTerm),
);
```

- Fix:

```js
let filtered = checks.filter(
  (check) =>
    check.title.toLowerCase().includes(searchTerm) ||
    check.category.toLowerCase().includes(searchTerm) ||
    check.priority.toLowerCase().includes(searchTerm) ||
    check.status.toLowerCase().includes(searchTerm) ||
    check.owner.toLowerCase().includes(searchTerm),
);
```

- Commit idea: `fix: search across launch check fields`

### 4. Status filter compares the wrong field

- Symptom: Choosing `Fixed`, `Pending`, or `Blocked` in the status filter shows the wrong rows or no rows.
- Tool: Console and code inspection.
- Broken line:

```js
filtered = filtered.filter((check) => check.priority === selectedStatus);
```

- Fix:

```js
filtered = filtered.filter((check) => check.status === selectedStatus);
```

- Commit idea: `fix: filter checklist by status`

### 5. In Progress badge has no color

- Symptom: The `In Progress` status badge does not use the intended blue style.
- Tool: Elements tab.
- Cause: The rendered class is `status-in progress`, but CSS expects `status-in-progress`.
- Broken line:

```js
const statusClass = `status-${check.status.toLowerCase()}`;
```

- Fix:

```js
const statusClass = `status-${check.status.toLowerCase().replaceAll(" ", "-")}`;
```

- Commit idea: `fix: slug status badge class names`

### 6. Readiness score stays wrong

- Symptom: The score and fixed count do not reflect checks marked `Fixed`.
- Tool: UI, Console, code inspection.
- Broken line:

```js
const fixed = checks.filter((check) => check.status === "Complete").length;
```

- Fix:

```js
const fixed = checks.filter((check) => check.status === "Fixed").length;
```

- Commit idea: `fix: calculate readiness from fixed checks`

### 7. Due this week metric is reversed

- Symptom: `Due this week` counts far-future items instead of items due soon.
- Tool: UI and code inspection.
- Broken line:

```js
const dueSoon = checks.filter((check) => daysUntil(check.dueDate) > 7).length;
```

- Fix:

```js
const dueSoon = checks.filter((check) => {
  const days = daysUntil(check.dueDate);
  return days >= 0 && days <= 7;
}).length;
```

- Commit idea: `fix: count checks due within seven days`

### 8. Delete button does not work

- Symptom: Clicking the delete button does nothing.
- Tool: Elements tab and event listener inspection.
- Cause: HTML uses `data-remove-id`, but JavaScript looks for `data-delete-id`.
- Broken line:

```js
const deleteButton = event.target.closest("[data-delete-id]");
```

- Fix option A:

```js
const deleteButton = event.target.closest("[data-remove-id]");
const id = Number(deleteButton.dataset.removeId);
```

- Fix option B: change the rendered button to use `data-delete-id`.
- Commit idea: `fix: wire delete action to row button`

### 9. Status changes do not update the full app state

- Symptom: Changing a row status updates the visible row but the score does not update, filters may become stale, and refresh loses the change.
- Tool: UI, Application tab, code inspection.
- Broken area:

```js
check.status = statusSelect.value;
renderRows(currentView);
logActivity(`Changed "${check.title}" to ${check.status}.`);
```

- Fix:

```js
check.status = statusSelect.value;
saveChecks();
applyFilters();
logActivity(`Changed "${check.title}" to ${check.status}.`);
```

- Commit idea: `fix: persist status changes and refresh metrics`

### 10. Local storage uses different save and load keys

- Symptom: Added or changed checks disappear after refresh.
- Tool: Application tab.
- Broken constants:

```js
const STORAGE_SAVE_KEY = "launchdesk-v1-items";
const STORAGE_LOAD_KEY = "launchdesk-items-v1";
```

- Fix:

```js
const STORAGE_KEY = "launchdesk-v1-items";
```

Then use `STORAGE_KEY` in both `getItem` and `setItem`.

- Commit idea: `fix: use one local storage key`

### 11. Reset demo data fetches the wrong JSON file

- Symptom: Clicking `Reset demo` shows a message and Console error. Network tab shows a 404.
- Tool: Network tab.
- Broken line:

```js
const response = await fetch("data/launch-seed.json");
```

- Fix:

```js
const response = await fetch("data/launch-checks.json");
```

- Commit idea: `fix: load demo checks from correct json file`

### 12. CSV export has a blank title column

- Symptom: Exported CSV downloads, but the Title column is empty.
- Tool: Inspect downloaded CSV and code.
- Broken line:

```js
check.name,
```

- Fix:

```js
check.title,
```

- Commit idea: `fix: export launch check titles`

## Suggested Student Fix Order

1. Submit handler.
2. Validation.
3. Readiness score.
4. Status badge.
5. Search.
6. Status filter.
7. Delete button.
8. Status update persistence.
9. Local storage key.
10. Reset JSON path.
11. Due this week metric.
12. CSV export.

## Expected Final Working State

After the intended fixes are applied, the app should behave like a complete internal tool for this teaching scope:

- Starter launch checks are visible on first load.
- Adding a valid check creates a new row immediately.
- Missing title or category is blocked with a message.
- Search works across title, category, priority, status, and owner.
- Status filter and priority filter can be combined.
- `In Progress` badges are visibly styled.
- Changing a row status updates the row, score, and metric cards.
- Delete removes the row and updates metrics.
- Refresh preserves changes through local storage.
- `Reset demo` reloads the seed JSON correctly.
- CSV export contains the expected titles and fields.

## Instructor Smoke Test

1. Open the app and confirm demo rows render.
2. Add one valid check and confirm it appears at the top.
3. Try submitting with a blank title and confirm validation blocks it.
4. Search for `SEO`, `Blocked`, and one owner name.
5. Filter by `Fixed`, then by `Critical`.
6. Change one row to `Fixed` and confirm score changes.
7. Delete one row and confirm counts drop.
8. Refresh the page and confirm changes persist.
9. Click `Reset demo` and confirm seed data reloads.
10. Export CSV and confirm the Title column is populated.

If all ten pass, the project is class-ready and the post-fix app is functionally complete for this session.

## Example Completed Report Entry

### Problem

The Add check button did not add a new launch check.

### Steps to Reproduce

1. Opened the app.
2. Filled the add check form.
3. Clicked `Add check`.

### Evidence

The browser Console showed that `handleAddChek` was not defined.

### Cause

The submit event listener called a misspelled function name.

### Fix Applied

Changed the event listener to call `handleAddCheck`.

### Test Result

After refreshing and submitting the form again, the form handler ran. The next validation bug became visible.

### Commit Message

`fix: connect launch check submit handler`

### Learning

Function names must match exactly. Console errors are useful clues.
