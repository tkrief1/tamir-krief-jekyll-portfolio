---
layout: post
title: "Chatbot Web App: Express + Python (GPT API) Bridge"
date: 2026-01-14
categories: [projects, ai, web]
tags: [nodejs, express, python, openai, gpt-3.5-turbo, api, fullstack]
excerpt: "A lightweight chat UI that posts messages to an Express API, which spawns a Python script (in a virtualenv) to call a GPT model and return the response."
featured: true
---

## Background

While working as an intern at the company Conversadocs, I was tasked with learning how API keys work and how HTTP requests on Postman are utilized to test servers, so I built this simple Chatbot web app from an older existing ChatGPT model **(gpt-3.5-turbo)** to help me understand the full stack process of software engineering that my supervisor taught me.

## Overview

This project is a simple **chatbot-style web app** that demonstrates a complete request/response loop from a browser UI to an AI model call and back.

Instead of calling the model directly from Node, I built a clean bridge:

- **Frontend (static)** sends user messages to a backend endpoint
- **Express backend** spawns a **Python script** inside a virtual environment
- Python prints the model response to stdout
- The backend returns a JSON payload back to the browser

Even though the original OpenAI API key used during testing is no longer active, the project is still a solid example of end-to-end integration and service design.

---

## How It Works

The application has three moving parts: a static frontend, an Express backend, and a Python script that performs the model call.

### 1) Frontend: capture input + POST to the backend

The browser UI listens for the chat form submission, prevents the default page refresh, and reads the user’s text from the input field. It immediately adds the user’s message into the chat window so the UI feels responsive.

Then it sends a request to the backend endpoint using `fetch()`:

- `POST /chat`
- `Content-Type: application/json`
- Request body: `{ "message": "<user message>" }`

When the backend returns the response, the UI appends the AI response to the chat window and scrolls to the most recent message.

### 2) Backend: Express endpoint that spawns Python

The Node server provides an API endpoint:

- `POST /chat`

When the endpoint receives a request, it:

1. Reads `req.body.message` as the input
2. Logs the message for debugging
3. Spawns a Python process using an explicit interpreter path inside a virtual environment
4. Collects `stdout` into a response string
5. Logs `stderr` output for error visibility
6. When the Python process exits, returns the final output as JSON to the frontend

The backend intentionally runs the Python script via an explicit interpreter path (instead of whatever `python` happens to be on the system), which helps ensure consistent dependency behavior across environments.

If the Python script prints nothing to `stdout`, the backend returns a fallback response string so the UI doesn’t hang or display a blank message.

### 3) Python: model call + print to stdout

The Python script is executed with the user’s message passed as a command-line argument. The script is responsible for:

- reading the user message argument
- calling the GPT model provider (OpenAI) with the appropriate prompt/messages
- printing the final model response to stdout

The Node server treats whatever is printed to stdout as the chatbot response, trims it, and sends it back to the browser.

---

## Why I Built It This Way

I built this project as a practical demonstration of a common real-world integration pattern:

- keep model/provider secrets off the frontend
- keep the UI simple and responsive
- isolate AI logic in a Python environment (where AI tooling is often fastest to iterate)
- use a backend layer to control request/response structure and error handling

This approach also makes it straightforward to replace the Python script with a more robust service later (or to swap model providers) while keeping the frontend contract the same.

---

## Technical Highlights

- **Express static hosting**: Serves the chat UI from `/public`
- **JSON request parsing**: Accepts `{ message: "..." }` payloads cleanly
- **Child process execution**: Uses Node’s `spawn()` to run Python safely without blocking the server
- **Virtualenv interpreter targeting**: Calls `myenv/bin/python3` to ensure the right Python dependencies are used
- **Streaming output handling**: Collects `stdout` incrementally and returns it when the process finishes
- **Frontend chat UX**: Appends messages to the DOM and auto-scrolls to keep the latest response visible

---

## What I Learned

- How to design a clean API boundary between a browser UI and model execution
- How to reliably run Python from a Node/Express server using child processes
- Why virtual environments matter for reproducibility (and how to explicitly target them)
- How important it is to handle empty output and stderr logging for debugging

---

## How To Run Locally (Typical Workflow)

The exact commands can vary slightly depending on machine and environment, but a typical setup looks like this:

1. Create a Python virtual environment (e.g., `myenv`) and install dependencies
2. Install Node dependencies (`npm install`)
3. Start the server (`node server.js`)
4. Visit `http://localhost:3000` in a browser
5. Type a message and submit — the UI posts to `/chat`, the server runs the Python script, and the response is returned to the page

To re-enable live GPT responses today, you would add a new API key via environment variables (recommended) rather than hardcoding it.

---

## Repo

GitHub: `tkrief1/tkrief1-APIkeytest`

---
