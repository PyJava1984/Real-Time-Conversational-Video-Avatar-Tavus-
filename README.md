# Real-Time Conversational Video Avatar (Tavus)

Create an interactive, lifelike video avatar that represents you in real time. You ask a question by typing, and a talking-head video responds instantly in a natural voice and personality. The system is powered by a large language model (LLM) that is grounded in a short personal bio you provide.

This project uses the Tavus Conversational Video Interface (CVI). For deeper technical implementation details, refer to `TAVUS.md`.

---

## Overview: Three Development Stages

The project is designed in progressive phases so you can validate functionality before investing in custom media or paid features.

### Phase 1 — Initial Prototype (Free Tier, Stock Avatar)

Build a working end-to-end pipeline at no cost. You input text, the LLM responds based on your personal context, and a live talking-head video is generated using a default stock avatar provided by Tavus. This phase focuses on validating functionality.

### Phase 2 — Full Web Application

Develop a complete user interface using a React-based frontend (`avatar/`) with a lightweight Node.js backend. The interface supports multiple interaction modes such as typing, voice input, and live face-to-face communication. The project can be run locally using `npm install` and `npm run dev`.

### Phase 3 — Personalized Digital Twin (Paid Upgrade)

Upgrade to a paid plan and create a custom video avatar trained on your own recorded footage. After uploading a short training clip, the system generates a personalized visual and voice model. Once configured, the same application runs with your own digital likeness.

---

## Phase 1 — Prototype Setup

### Requirements

* A Tavus account (free tier is sufficient): [https://www.tavus.io](https://www.tavus.io)
* [`uv`](https://docs.astral.sh/uv/) installed

  * The project uses Python 3.12 and auto-manages the environment via `uv run`

---

### Step 1 — Create Your Personal Profile File

Create a file named:

```
knowledge/ME.md
```

This file defines your identity for the avatar system. It acts as the foundation of your digital persona.

Guidelines:

* Begin with a heading using your full name (e.g., `# John Doe`)
* Describe your background, profession, communication style, and key details
* Keep the writing concise and structured (around 1–2 pages recommended)
* Avoid excessive length (ideally under ~3000 words)

---

### Step 2 — Obtain API Credentials

In your Tavus developer dashboard:

* Navigate to API Keys
* Generate a new key
* Copy and store it securely (it will only be shown once)

---

### Step 3 — Configure Environment Variables

Copy the sample environment file:

```bash
cp .env.example .env
```

Then add your API key:

```
TAVUS_API_KEY=your_api_key_here
```

Other settings can remain at default values unless customization is needed.

---

### Step 4 — Run the Setup Script

Execute:

```bash
uv run setup_demo.py
```

This script:

* Reads your `ME.md` profile
* Builds a structured system prompt
* Creates a Tavus persona and live session
* Generates a `demo.html` file

It will also output a conversation link and local file path.

---

### Step 5 — Interact with Your Avatar

Open `demo.html` in a browser. The avatar will join a live session.

You can:

* Type a question
* Receive a spoken video response in real time
* View live captions of the response

This demonstrates the full pipeline:
**text input → LLM reasoning → real-time video response**

---

### Step 6 — Persona Management

A persona is generated from your `ME.md` file and stored for reuse.

Behavior:

* First run: a new persona is created automatically
* Subsequent runs: the same persona is reused
* If `ME.md` changes:

  ```bash
  uv run setup_demo.py --update-persona
  ```

To change the avatar appearance, set a new replica ID in `.env` and rerun the setup. The system applies updates immediately without manual reconfiguration.

---

### Notes and Limitations

* Each session is temporary; once ended, it cannot be resumed
* Free tier usage is limited (approximately 25 minutes total runtime)
* Each session is capped to prevent accidental usage overages
* Default avatar is a stock model until custom training is completed in Phase 3

---

## Phase 2 — Web Application

The full application resides in the `avatar/` directory and consists of:

* A React + TypeScript frontend (Vite-based)
* A minimal Node.js backend for API handling

The system supports three interaction modes:

* **Text Mode** — type questions, receive video responses
* **Voice Mode** — speak naturally using microphone input
* **Face-to-Face Mode** — live audio + video interaction with full camera support

---

### Requirements

* Node.js 20+
* Phase 1 completed (API key + persona configured)

---

### Run Instructions

```bash
cd avatar
npm install
npm run dev
```

Open:

```
http://localhost:5173
```

Start a session and choose your preferred mode.

---

### Architecture Overview

* Backend (`avatar/backend/server.mjs`)

  * Lightweight Node server with no external dependencies
  * Handles Tavus API communication securely using environment variables

* Frontend (`avatar/`)

  * React-based UI built with Vite
  * Manages session UI, transcription, and interaction modes
  * Communicates with backend via API endpoints

---

### Avatar Configuration

To change the avatar appearance:

1. Update `TAVUS_REPLICA_ID` in `.env`
2. Restart the application
3. Optionally rebuild the persona if prompt alignment is needed:

   ```bash
   uv run setup_demo.py --update-persona
   ```

---

## Phase 3 — Custom Avatar Training (Paid)

This phase replaces the stock avatar with a personalized digital model trained on your own video data.

All training is performed through the Tavus platform.

---

### Step 1 — Access Replica Creation

Go to:
[https://platform.tavus.io](https://platform.tavus.io)

Navigate to:

* Replicas → Create Replica
  or directly:
  [https://platform.tavus.io/dev/replicas/create](https://platform.tavus.io/dev/replicas/create)

---

### Step 2 — Provide Training Video

You must submit a short recording (~60 seconds total).

Recommended approach:

* Record using a high-quality camera or smartphone
* Or use browser-based recording tool provided by the platform

Recording structure:

* ~30 seconds speaking naturally (face visible, minimal movement)
* ~30 seconds still pose (neutral expression, steady gaze)

Requirements:

* 1080p or higher
* MP4 format
* Stable lighting and plain background
* Face clearly visible and centered

A consent statement must be read at the beginning as provided by the platform.

---

### Step 3 — Training Process

After submission:

* The system processes and trains your digital replica
* This typically takes several hours
* Once complete, a replica ID is generated

---

### Step 4 — Connect Replica to Application

Update your environment:

```
TAVUS_REPLICA_ID=your_replica_id
```

Then refresh the persona:

```bash
uv run setup_demo.py --update-persona
```

Restart the frontend:

```bash
cd avatar
npm run dev
```

Your application will now generate responses using your custom avatar.

---

## Project Structure

* `README.md` — Main documentation (this file)

* `knowledge/ME.md` — Personal profile used for avatar identity

* `setup_demo.py` — Core script for creating persona and sessions

* `.env.example` — Environment configuration template

* `pyproject.toml` — Python environment definition (uv-managed)

* `avatar/` — Full frontend application

  * `backend/` — Secure API proxy layer

* `TAVUS.md` — Technical integration reference

* `CLAUDE.md` — Project design and architectural notes

