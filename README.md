# Team Todo (self-hosted)

Simple self-hosted team todo list with user accounts, teams, task assignment, and in-app notifications when tasks get assigned. Runs as a single Node/Express service with SQLite storage and a lightweight HTML front-end.

## Features
- Email/password auth with JWT sessions.
- Create or join teams via invite codes.
- Create tasks for the current team, assign to teammates, and update status.
- Notifications generated on assignment; users can mark them as read.
- All-in-one container image (serves API and static UI).

## Quick start (Docker)
```bash
# build image
cd "Todo Application"
docker build -t team-todo .

# run
docker run -p 3000:3000 -e JWT_SECRET="your-strong-secret" -v $(pwd)/data:/app/data team-todo
```
- The SQLite file will be persisted in the `data` folder on your host (mounted into the container). Remove `-v ...` if you want ephemeral storage.
- Open http://localhost:3000 to register the first user and create your team.

## Local development (without Docker)
```bash
npm install
JWT_SECRET=dev-secret node server.js
```

## API overview
- `POST /api/auth/register` `{ name, email, password, teamName? }` → creates a new team (or uses provided name) and returns JWT.
- `POST /api/auth/login` `{ email, password }` → returns JWT.
- `GET /api/me` → current user and team info.
- `POST /api/teams` `{ name }` → create and switch to a new team.
- `POST /api/teams/join` `{ inviteCode }` → join an existing team by code.
- `GET /api/users` → teammates in current team.
- `GET /api/tasks` → tasks for the team (with assignee info).
- `POST /api/tasks` `{ title, description?, assignedTo? }` → create task; assignment triggers a notification.
- `PUT /api/tasks/:id` `{ title?, description?, status?, assignedTo? }` → update/assign task (new assignee gets a notification).
- `GET /api/notifications` → notifications for current user.
- `POST /api/notifications/:id/read` → mark notification as read.

## Configuration
- `PORT` (default 3000)
- `JWT_SECRET` (required for production)
- `DB_PATH` (optional path to the SQLite database file; defaults to `data.sqlite` in the app root)

## Notes
- Default task statuses are `open`, `in_progress`, `done` (editable in UI).
- To invite teammates, share the invite code shown under the team card.
