---
layout: post
title: "County Parks Trail Reporting (Group Project)"
date: 2026-01-14
categories: [projects, web, group-project]
tags: [flask, python, mysql, docker, docker-compose, incident-reporting, civic-tech]
excerpt: "A Flask + MySQL web app built as a team project to support structured trail condition and incident reporting for county parks."
featured: true
---

## Background

During the Spring 2025 semester, I took **Software Engineering (COSC 412)** at Towson University. As the capstone deliverable for the course, our team was tasked with designing and building a **deployable software project** and presenting it as a final demonstration. The goal was to practice core **software development life cycle (SDLC)** concepts in a realistic, team-based setting—requirements, planning, implementation, testing, iteration, and delivery.

I worked on a team of four, and we built our application from scratch. One teammate, who had experience with **Baltimore County Recreation and Parks**, proposed a trail reporting platform that would allow park patrons to submit trail occurrences—such as fallen trees, hazards, or sightings of plants and wildlife—so that other patrons and park staff could quickly view and respond to relevant updates.

Over the course of the semester, we developed the project through **biweekly sprint cycles**. I helped keep the team organized by setting up recurring sprint meetings to review progress, coordinate code reviews, and align on priorities and next steps.

## Overview

This repository is a **group-built web application** focused on **trail condition / incident reporting** for county parks (Baltimore County trails context). The core goal is to make it easy for users to submit consistent, actionable reports (hazards, maintenance needs, conditions, observations) and for staff to review and manage those submissions.

The project is structured as a full stack app with:

- a **Flask (Python)** backend
- a **MySQL** database for persistence
- a **Docker Compose** setup to run the full environment locally with consistent dependencies

---

## Problem

Trail issues are often reported in fragmented or unstructured ways:

- missing location/context
- inconsistent categories (hazard vs. maintenance vs. observation)
- no single place to review or prioritize reports
- limited visibility into what has already been reported

A usable reporting workflow needs to be fast for the person submitting it, and structured enough for staff to act on.

---

## What We Built

At a high level, the app supports:

- **Submitting trail reports** (condition issues, hazards, maintenance needs, observations)
- **Storing reports in a database** so they can be tracked over time
- **Review workflows** to support staff/admin visibility and management
- A clear project layout so the system can be extended (status tracking, attachments, analytics, etc.)

Because this was a team effort, a big focus was making the project **easy to run locally** for every contributor.

---

## Tech Stack

- **Backend:** Flask (Python)
- **Database:** MySQL
- **Local environment / deployment:** Docker + Docker Compose
- **Frontend:** Server-rendered templates + static assets (typical Flask pattern)

---

## How the System Fits Together (Local Dev)

In development, the app runs as multiple services orchestrated by Docker Compose:

1. **Web app service** (Flask)
2. **Database service** (MySQL)

Docker Compose handles:

- container networking between the web app and DB
- environment configuration
- repeatable startup (one command) for anyone on the team

This avoids dependency drift and makes onboarding smoother.

---

## Local Project Layout (High-Level)

The repo follows a common Flask layout pattern:

- `app.py` (or equivalent) — Flask entrypoint / route registration
- `templates/` — HTML templates for views (reporting pages, staff/admin pages)
- `static/` — CSS/JS/assets
- `uploads/` (optional) — stored user uploads (images/files), if enabled
- `requirements.txt` — Python dependencies
- `Dockerfile` — web container build definition
- `docker-compose.yml` — multi-service orchestration (web + MySQL)

If you want this post to name the **exact** route paths, page names, and DB schema tables, paste your `app.py` (or main Flask file) and the `docker-compose.yml` and I’ll update the post to match your implementation precisely.

---

## Running Locally (Docker Compose)

### Prerequisites

- Docker installed (Docker Desktop recommended)
- Docker Compose available

### Start the app

From the project directory that contains `docker-compose.yml`:

```bash
docker compose up -d --build
```

### Open the app

Your compose file will map a host port to the Flask container. Common examples look like:

- `http://localhost:5000`
- `http://localhost:5001`

If you’re unsure which port is used on your machine, check the `ports:` mapping in `docker-compose.yml`.

### Useful commands

Stop services:

```bash
docker compose down
```

See running containers:

```bash
docker compose ps
```

View logs:

```bash
docker compose logs -f
```

---

## My Contributions (Team Project)

In a group project, my focus was on shipping reliable, well-integrated pieces that fit the larger workflow:

- Helped implement core reporting flow pieces so submissions are consistent and usable
- Worked within the shared repo workflow (branches, pull requests, merges)
- Supported integration and testing so features worked end-to-end across services
- Contributed to structure and run-ability so teammates could launch locally without friction

(If you want, paste your specific areas of work—routes, templates, DB schema, UI pieces—and I’ll rewrite this section with exact bullets tied to what you built.)

---

## What I Learned

- How to collaborate effectively on a shared codebase (PR flow, merge conflicts, division of work)
- How to design reporting inputs that balance **simplicity** for users with **structured data** for staff
- How containerized dev environments (Docker Compose) reduce setup issues and speed up onboarding
- How to think about incident reporting systems as a lifecycle (submit → review → resolve → analyze)

---

## Next Steps (If Continuing the Project)

If this project were extended further, the most impactful additions would be:

- **Status workflow:** New → In Review → Scheduled → Resolved
- **Attachment support:** photos and optional metadata
- **Map-driven location capture:** pin drop / GPS on mobile
- **Admin tools:** filtering, exporting, deduping, assignment
- **Analytics:** trends by category, location hot-spots, seasonal patterns

---

## Repo

GitHub: `tkrief1/county-parks-jjmt`