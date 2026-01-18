---
layout: post
title: "County Parks Trail Reporting (Flask + MySQL + Docker Compose)"
date: 2026-01-14
categories: [projects, web, group-project]
tags: [flask, python, mysql, sqlalchemy, docker, docker-compose, incident-reporting, civic-tech]
excerpt: "A Flask + MySQL web app built as a team project to support structured trail incident reporting, staff review, and basic admin management—packaged with Docker Compose for repeatable local deployment."
featured: true
---

## Overview

This project is a **trail incident reporting** web application built as part of a collaborative team effort. It provides a lightweight workflow for park patrons to submit trail-related incidents (hazards, maintenance needs, observations, etc.), and for staff/admin users to review, manage, and triage the submitted reports.

The application is built with **Flask**, persists data in **MySQL** via **SQLAlchemy**, supports **photo uploads**, and is deployed locally using **Docker Compose** for consistent development and demos.

---

## Key Features

- **Public reporting form**: submit new trail reports (park, trail, category, description, contact info)
- **Photo uploads**: optional image upload stored on disk and served back via a dedicated route
- **Staff dashboard**: view incidents in reverse chronological order and see report details at a glance
- **Admin view**: view all incidents and delete entries
- **Persistence**: incidents are stored in MySQL and created via SQLAlchemy model definitions
- **Repeatable local deployment**: single-command startup using Docker Compose

---

## Tech Stack

- **Backend:** Flask (Python)
- **ORM / Data layer:** SQLAlchemy (Flask-SQLAlchemy)
- **Database:** MySQL 8
- **Containerization:** Docker + Docker Compose
- **Uploads:** local filesystem volume mounted into the app container
- **Templates:** server-rendered HTML (`home.html`, `staff_dashboard.html`, `admin.html`)

---

## Data Model

The core database table is **`Incidents`**, represented by the `Incident` SQLAlchemy model. Each report captures:

- `incidentNum` (primary key)
- `ParkName`, `TrailName`
- `SubmiterName`, `Contact`
- `Date` (timestamp; defaults to UTC now)
- `Category`, `Description`
- `PhotoURL` (path to the served upload)
- `Status` (initialized as `"New"`)
- `StaffAssign` (string field, initialized empty)
- `Priority` (defaults to `"Medium"` if not provided)

On application startup, the database schema is created automatically through `db.create_all()` (within the Flask app context).

---

## Routes / Endpoints

### Public-facing routes

- `GET /`
  - Loads the home page and displays the **10 most recent incidents** (ordered by `incidentNum` descending).
  - Template: `home.html`

- `POST /submit_report`
  - Accepts form fields:
    - `park`, `trail`, `submiter`, `contact`, `category`, `description`
    - `priority` (optional; defaults to `"Medium"`)
  - Accepts optional file upload:
    - `photo` (png/jpg/jpeg)
  - Creates a new `Incident` record with:
    - `Status = "New"`
    - `StaffAssign = ""`
  - Saves uploaded photos to `uploads/` with a UUID-based filename and stores a `PhotoURL` that points to the served upload route.
  - Redirects back to `/` and shows a success flash message.

- `GET /uploads/<filename>`
  - Serves uploaded images from the `uploads/` directory.

### Staff/admin routes

- `GET /dashboard`
  - Displays all incidents ordered by `Date` descending.
  - Includes a safety check that verifies whether a referenced upload file exists; if not, it clears `PhotoURL` to avoid broken image links.
  - Template: `staff_dashboard.html`

- `GET /admin`
  - Admin view of all incidents.
  - Template: `admin.html`

- `POST /delete_incident/<incident_id>`
  - Deletes the incident by primary key (`incidentNum`) and redirects back to `/admin` with a flash confirmation.

---

## File Upload Handling

Photo uploads are optional:

- Stored under `uploads/`
- Saved using a unique UUID filename to prevent collisions
- Served through the app via `GET /uploads/<filename>`
- Uploaded directory is created automatically if it doesn’t exist

In Docker, uploads persist through a volume mount:

- Host: `./uploads`
- Container: `/app/uploads`

This makes uploads survive container restarts and keeps demo data stable.

---

## Docker Compose Deployment

The stack is defined as two services on a shared Docker network:

### `parks-db` (MySQL 8)

- Image: `mysql:8`
- Container name: `parks-db`
- Exposes: `3307` on the host → `3306` in container
- Persists data via Docker volume: `parks_data`
- Initializes database and credentials with environment variables:
  - Database: `county_parks`
  - User: `parks_auth`

### `parks-app` (Flask)

- Build: from the project `Dockerfile`
- Container name: `county-parks`
- Exposes: `5001` on host → `5000` in container
- Connects to MySQL via env vars:
  - `DATABASE_HOST=parks-db`
  - `DATABASE_USER=parks_auth`
  - `DATABASE_PASSWORD=...`
  - `DATABASE_NAME=county_parks`
- Depends on `parks-db` to ensure the DB container starts first
- Mounts uploads directory for persistence

---

## Running Locally

From the directory containing `docker-compose.yml`:

```bash
docker compose up -d --build
```

Open the application:

- `http://localhost:5001/`

Stop the stack:

```bash
docker compose down
```

---

## My Contributions (Group Project)

- **Sprint leadership / cadence:** Initiated and facilitated **biweekly sprint meetings** to review progress, align priorities, and keep deliverables moving toward demo-ready milestones.
- **Team communication + coordination:** Acted as the primary communication hub between teammates—tracking blockers, confirming ownership of tasks, and ensuring updates were shared consistently across the group.
- **Project planning & delivery support:** Helped translate requirements into actionable work items, coordinated lightweight code reviews, and supported integration/testing to keep the project stable as features were merged.
- **Hands-on engineering:** Contributed directly to the build by implementing and refining core application components (routes/templates, form handling, database interactions), plus troubleshooting bugs and improving overall usability.

---

## Next Steps (If We Were to Continue the Project)

If this project were extended beyond the current MVP, the most impactful improvements would be:

- **Status workflow:** Expand incident lifecycle tracking beyond the initial `"New"` state (e.g., **New → In Review → Scheduled → Resolved**) with timestamps and basic audit history.
- **Richer attachments:** Support multiple photos per incident, optional metadata (notes/captions), and stronger file validation/thumbnail previews in the UI.
- **Map-based location capture:** Add a map interface (pin drop) and/or mobile GPS capture so each incident can be tied to a specific trail segment or coordinate.
- **Admin productivity tools:** Add filtering/search, export (CSV), duplicate detection, and assignment features to streamline staff triage and workload management.
- **Analytics & reporting:** Build dashboards for trends over time (by category, park/trail, priority), hotspot identification, and seasonal pattern insights.

---

## Repo

GitHub: `tkrief1/county-parks-jjmt`

---