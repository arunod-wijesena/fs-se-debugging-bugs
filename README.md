# LaunchDesk Class Session - Student Debugging Exercise

The app in this folder is intentionally broken for the live class. Students should reproduce one bug, inspect the evidence, make one focused fix, test again, document it, then commit.

## Run the App

```bash
npm run dev
```

Open:

```text
http://localhost:5173
```

You can also run it without npm:

```bash
python3 -m http.server 5173
```

## Folder Structure

- `index.html`, `css/`, `js/`, `data/` - the student-facing broken app for the live session.
- `docs/student-debugging-report.md` - copyable student report template.
- `docs/instructor-solution-guide.md` - private instructor guide with bug map, coverage, fix guidance, and smoke test.
- `docs/private/app.fixed-reference.js` - corrected final version of the app logic for instructor use only.
- `docs/private/fixed-reference-readme.md` - short note about the fixed reference behavior.

## What the App Should Do When Fixed

- Show a launch checklist with starter checks.
- Add a new launch check with title, category, priority, status, owner, and due date.
- Validate required fields.
- Search by title, category, priority, status, and owner.
- Filter by status and priority.
- Update status from the checklist table.
- Delete a check.
- Calculate readiness score from checks marked `Fixed`.
- Save updates in browser local storage.
- Reload demo checks from `data/launch-checks.json`.
- Export the current checklist view as CSV.

## Student Rules

- Find bugs using DevTools before editing.
- Fix one issue at a time.
- Test the exact broken action after each fix.
- Write a debugging report entry for every fix.
- Use clear commit messages, for example:

```text
fix: connect launch check submit handler
fix: correct status filter comparison
docs: add debugging report
```

## Useful Files

- `index.html` - app structure.
- `css/styles.css` - visual styling and responsive layout.
- `js/app.js` - app behavior with intentional bugs.
- `data/launch-checks.json` - starter data used by the reset flow.
- `docs/instructor-solution-guide.md` - private instructor reference.
- `docs/student-debugging-report.md` - copyable student report template.
- `docs/private/app.fixed-reference.js` - corrected `app.js` reference for your post-fix target.

## Instructor Notes

- The app is designed to have a variety of bugs across different features, encouraging students to practice systematic debugging.
- The instructor solution guide provides a map of the bugs, their locations, and hints for fixing
- The debugging report template helps students document their process and reflect on their learning.
- Encourage students to share their debugging stories and insights during the class discussion.
