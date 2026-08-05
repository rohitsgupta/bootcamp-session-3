# Cloud Architecture Overview

This document provides a simple system context view of the Todo monorepo runtime architecture.

## System Context Diagram

```mermaid
flowchart LR
    User[End User]
    FE[React Frontend\npackages/frontend]
    API[Express API\npackages/backend]
    DB[(In-Memory Store\nSQLite :memory:)]

    User -->|Uses browser UI| FE
    FE -->|HTTP /api/tasks| API
    API -->|Reads/Writes task data| DB
```

## Sequence Diagram: Create TODO

```mermaid
sequenceDiagram
    autonumber
    actor U as User
    participant FE as React Frontend
    participant API as Express API
    participant DB as In-Memory SQLite

    U->>FE: Enter title/due date and submit form
    FE->>FE: Validate title is not empty
    FE->>API: POST /api/tasks { title, description, due_date }
    API->>API: Validate payload
    API->>DB: INSERT task row
    DB-->>API: Insert result + task id
    API->>DB: SELECT task by id
    DB-->>API: New task record
    API-->>FE: 201 Created + task JSON
    FE->>API: GET /api/tasks
    API->>DB: SELECT tasks ORDER BY due_date, created_at
    DB-->>API: Task list
    API-->>FE: 200 OK + tasks JSON
    FE-->>U: Updated TODO list renders new item
```

## Notes

- The frontend and backend are developed in a single monorepo.
- The API persists task data in an in-memory SQLite database.
- Data is ephemeral and resets when the backend process restarts.
