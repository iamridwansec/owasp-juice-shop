# Web3 Sandbox: Find an Accidentally Deployed Code Sandbox

**Objective:** Find an accidentally deployed code sandbox hidden in the application.

## Background

Juice Shop's front-end JavaScript bundle (`main.js`) contains a route that was not meant to be publicly discoverable — a "Web3 sandbox" page. While not linked anywhere in the visible UI, its route is still present in the compiled Angular routing code and can be found by searching through the bundled source.

## Steps Taken

### 1. Opened the Sources tab in DevTools

Opened DevTools (`F12`) → **Debugger** (Firefox's equivalent of Chrome's "Sources" tab) and located the `main.js` file.

<img width="483" height="477" alt="Screenshot 2026-10-10 002756" src="https://github.com/user-attachments/assets/8b26fb66-da59-4b8d-bb88-2519c2b9dbd9" />

<img width="483" height="477" alt="Screenshot 2026-10-10 002936" src="https://github.com/user-attachments/assets/05b57df3-a607-43ea-9228-fae90a0b81f8" />

### 2. Pretty-printed the minified code

Used the browser's built-in pretty-print feature (the `{}` formatting button) to make the minified bundle readable.

<img width="483" height="477" alt="Screenshot 2026-10-10 003245" src="https://github.com/user-attachments/assets/f6da10c6-edda-43eb-bcde-d983fdfa8c14" />

### 3. Searched for relevant keywords

Used the Debugger's search function to look for the terms `web3` and `sandbox` within the formatted source, iterating through matches until finding a route mapping section referencing a hidden path.

<img width="483" height="477" alt="Screenshot 2026-10-10 003340" src="https://github.com/user-attachments/assets/ae379d61-f792-4cef-9e55-7ab2de47d212" />

### 4. Navigated to the discovered route

```
http://localhost:3000/#/web3-sandbox
```

<img width="483" height="477" alt="Screenshot 2026-10-10 003454" src="https://github.com/user-attachments/assets/ef344a59-14f6-4c9b-b832-6469a70bc351" />

## Result

Challenge solved — confirmed by the in-app success notification and the Score Board. Simply visiting the hidden route was enough to trigger the solve.

<img width="483" height="477" alt="Screenshot 2026-10-10 005507" src="https://github.com/user-attachments/assets/e41381da-2a2f-4bf5-8293-ba3fc8406bac" />

## Root Cause

A development/testing route was left reachable in the production build. Although not linked anywhere in the visible navigation, the route was still compiled into the client-side JavaScript bundle and accessible to anyone who inspects the application's source code.

## Status

Solved.
