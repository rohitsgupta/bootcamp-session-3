# Product Requirements Document (PRD) - Todo App Upgrade: Due Dates, Priority, and Date Filters

## 1. Overview

The current Todo app supports only a task title and completion status. This upgrade introduces lightweight planning features so users can better organize and act on urgent work while keeping the product simple and teachable. The MVP focuses on task metadata and filtering without any backend changes. More advanced presentation and ordering behavior are deferred to Post-MVP.

---

## 2. MVP Scope

- Add an optional dueDate field to each task using ISO format YYYY-MM-DD.
- Add a priority field with enum values P1, P2, and P3.
- Set default priority to P3 when no priority is provided.
- Add task filters: All, Today, and Overdue.
- Keep storage local only, with no backend or external storage integration.
- Preserve current simple app architecture and avoid backend changes.
- Enforce data rule: title is required.
- Enforce data rule: dueDate is optional.
- Enforce data rule: invalid dueDate values are ignored and treated as absent.

---

## 3. Post-MVP Scope

- Visually highlight overdue tasks so they stand out in the UI.
- Add sorting behavior: overdue tasks first.
- Add sorting behavior: then by priority from P1 to P3.
- Add sorting behavior: then by due date ascending.
- Add sorting behavior: tasks without due dates last.

---

## 4. Out of Scope

- Notifications.
- Recurring tasks.
- Multi-user support.
- Keyboard navigation enhancements.
- External storage or backend persistence changes.
