# AI Debugging: Reveal Behind-the-Scenes Information on the Chatbot (Non-Admin)

**Objective:** Reveal behind-the-scenes information about the Juice Shop chatbot ("Juicy the Smart Assistant") while logged in as a normal, non-admin user.

## Environment

- Host: Windows, running Kali Linux in VirtualBox
- Target: OWASP Juice Shop running in Docker
- Browser: Firefox on Kali
- URL: `http://localhost:3000`

## Steps Taken

### 1. Registered and logged in as a normal user

Registered a new, non-admin account and logged in.

### 2. Opened the chatbot

Opened **AI Chat** from the left menu under *Contact* (`/#/chatbot`), showing "Juicy the Smart Assistant".

### 3. Watched the network traffic

Opened DevTools (`F12`) → **Network** tab, then sent the message `hello` in the chat. Two relevant requests were observed:

| Method | Status | File | Notes |
|---|---|---|---|
| POST | 200 | `chat` | Chat message sent to the server (event stream). |
| GET | 304 | `application-configuration` | App settings sent to the browser. |

### 4. Observed the chatbot's response

Juicy replied to `hello` with an error:

> "Oops! Looks like our AI brain is taking a juice break. The AI endpoint is not configured or reachable. Please talk to the shop administrator to get it back on track!"

### 5. Confirmed the root cause via the Score Board

Checking the **AI Debugging** challenge card on the Score Board (`/#/score-board`) showed it tagged **"Requires LLM API"**.

### 6. Confirmed via official project documentation

OWASP Juice Shop's own v20.0.0 release notes confirm this directly:

> "AI Debugging ⭐⭐ — because even artificial intelligence sometimes needs a human in the loop. ... These challenges require a configured LLM/AI endpoint to function."

This confirms the chatbot error is expected behavior in a Juice Shop instance that has no LLM/AI backend (such as an OpenAI API key) configured — it is not a bug to work around through further prompting or DevTools inspection.

## Result

**Blocked** — this challenge cannot be completed without an administrator configuring a real LLM/AI endpoint for the Juice Shop container (typically via an API key passed as an environment variable at container startup).

## Root Cause / Notes

- This is one of three new AI-themed challenges introduced in Juice Shop v20.0.0 (alongside *Chatbot Prompt Injection* and *Greedy Chatbot Manipulation*), all of which require a working LLM backend.
- To unblock this challenge, whoever manages the Docker deployment would need to supply a valid LLM API key (e.g. OpenAI) when starting the container, per the official Juice Shop documentation.

## Status

Blocked — pending LLM API configuration by an administrator.
