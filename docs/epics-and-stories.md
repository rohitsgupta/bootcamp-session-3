# Epics and Stories for Todo App Upgrade

Based on requirements in docs/prd-todo.md and structured using docs/templates/epic-and-stories-template.md.
Technical requirements below are derived from acceptance criteria and reference the current implementation in packages/frontend and packages/backend.

## MVP

- Epic: Task Data Model Enhancements
  - Story: Add optional dueDate field to task model
    - Acceptance Criteria:
      - A task can be created without a dueDate.
      - A task can store a dueDate in ISO YYYY-MM-DD format.
    - Technical Requirements:
      - Keep using due_date as the persisted field name in backend payloads and database rows for compatibility with existing API responses.
      - Ensure create and update requests allow due_date to be omitted and store null when not provided.
      - Keep frontend form due date input mapped to due_date in request payloads.
  - Story: Add priority field with values P1, P2, and P3
    - Acceptance Criteria:
      - A task supports only the priority values P1, P2, or P3.
      - Priority values outside P1, P2, and P3 are not persisted as valid priority values.
    - Technical Requirements:
      - Add priority column to the tasks table in backend startup schema.
      - Update POST and PUT handlers to accept priority and validate against P1, P2, P3.
      - Update frontend task form to provide a priority input bound to allowed values only.
  - Story: Set default priority to P3 for new tasks
    - Acceptance Criteria:
      - When priority is not provided for a new task, stored priority is P3.
    - Technical Requirements:
      - Set backend default for priority to P3 when missing from POST payload.
      - Ensure response payload from create endpoint includes priority with value P3 when omitted by client.
      - Initialize frontend create flow with default priority selection set to P3.

- Epic: Task Validation Rules
  - Story: Require title when creating a task
    - Acceptance Criteria:
      - A task cannot be created with an empty or missing title.
    - Technical Requirements:
      - Keep frontend validation that blocks submit when title is blank or whitespace.
      - Keep backend validation in POST and PUT endpoints that returns 400 for missing or empty title.
      - Add or update API tests to assert 400 responses for invalid title payloads.
  - Story: Treat invalid dueDate values as absent
    - Acceptance Criteria:
      - If dueDate is not in ISO YYYY-MM-DD format, it is ignored.
      - Invalid dueDate values are treated as if dueDate was not provided.
    - Technical Requirements:
      - Add backend date format validation for due_date using strict YYYY-MM-DD matching.
      - Normalize invalid due_date values to null before insert or update.
      - Ensure frontend edit mode date normalization does not send non-ISO values to the API.
  - Story: Preserve optional dueDate behavior in task workflows
    - Acceptance Criteria:
      - Tasks without dueDate remain valid and usable in task operations.
    - Technical Requirements:
      - Keep due_date nullable in database and API contract.
      - Ensure task list rendering supports items with no due_date without formatting errors.
      - Verify create, edit, complete toggle, and delete flows work for undated tasks.

- Epic: Date-Based Task Filtering
  - Story: Add All filter tab for task list
    - Acceptance Criteria:
      - Users can switch to an All filter view.
      - All filter is available alongside Today and Overdue filters.
    - Technical Requirements:
      - Add filter state in frontend task list or parent container with values all, today, overdue.
      - Render tab controls for All, Today, and Overdue in the task list view.
      - Map All selection to unfiltered task retrieval.
  - Story: Add Today filter tab for task list
    - Acceptance Criteria:
      - Users can switch to a Today filter view.
      - Today filter is available alongside All and Overdue filters.
    - Technical Requirements:
      - Implement date comparison logic using local date boundaries for today.
      - In Today view, show only incomplete tasks with due_date equal to today.
      - Reuse existing task fetch flow and apply filter either via query params or client-side filtering.
  - Story: Add Overdue filter tab for task list
    - Acceptance Criteria:
      - Users can switch to an Overdue filter view.
      - Overdue filter is available alongside All and Today filters.
    - Technical Requirements:
      - Implement overdue logic where due_date is earlier than today and completed is false.
      - In Overdue view, hide completed tasks by filter definition.
      - Keep filter behavior consistent after create, edit, complete, and delete actions.

- Epic: Local-Only Persistence and MVP Constraints
  - Story: Keep task persistence in local storage only
    - Acceptance Criteria:
      - Task data is persisted locally on the client.
      - No external storage is required for task persistence.
    - Technical Requirements:
      - Do not introduce remote services, cloud storage, or third-party data APIs.
      - Keep persistence within existing local app boundary and repository runtime.
      - Maintain current frontend to backend local API communication path without external dependencies.
  - Story: Avoid backend integration changes for MVP
    - Acceptance Criteria:
      - MVP delivery does not require backend API or database changes.
    - Technical Requirements:
      - Do not add new external backend systems or deployment dependencies.
      - Keep task operations on existing /api/tasks endpoints and current Express app structure.
      - If any MVP field additions are required, scope them to existing codebase modules only.

## Post-MVP

- Epic: Overdue Task Visibility
  - Story: Add visual highlighting for overdue tasks
    - Acceptance Criteria:
      - Overdue tasks are visually distinguishable from non-overdue tasks.
    - Technical Requirements:
      - Add overdue status calculation in task list render path.
      - Apply distinct styling token for overdue items while preserving completed task styling rules.
      - Ensure overdue highlight appears in All and Overdue views for incomplete overdue tasks.

- Epic: Advanced Task Sorting
  - Story: Sort tasks with overdue items first
    - Acceptance Criteria:
      - In sorted views, overdue tasks appear before non-overdue tasks.
    - Technical Requirements:
      - Implement sort comparator or backend ORDER BY logic that prioritizes overdue status first.
      - Ensure overdue-first ordering is applied consistently after any task mutation.
  - Story: Sort tasks by priority from P1 to P3
    - Acceptance Criteria:
      - Within applicable groups, tasks are ordered by priority from P1 to P3.
    - Technical Requirements:
      - Define deterministic priority ranking map where P1 ranks above P2 and P3.
      - Apply priority sorting after overdue grouping in the sort pipeline.
  - Story: Sort tasks by due date in ascending order
    - Acceptance Criteria:
      - Within applicable groups, tasks with due dates are ordered from earliest to latest.
    - Technical Requirements:
      - Apply due date ascending ordering after overdue and priority ordering stages.
      - Use ISO date string comparison or normalized date objects for stable ordering.
  - Story: Place tasks without due dates at the end
    - Acceptance Criteria:
      - Within applicable groups, tasks without due dates are ordered after tasks with due dates.
    - Technical Requirements:
      - Add explicit null due date handling in sort logic to force undated tasks last.
      - Keep undated-last behavior consistent with existing backend pattern where null due dates are ordered last.
