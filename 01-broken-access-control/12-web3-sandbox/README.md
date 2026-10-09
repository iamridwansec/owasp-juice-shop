# Web3 Sandbox: Find an Accidentally Deployed Code Sandbox

**Objective:** Find an accidentally deployed code sandbox hidden in the application.

## Background

Juice Shop's front-end JavaScript bundle (`main.js`) contains a route that was not meant to be publicly discoverable — a "Web3 sandbox" page. While not linked anywhere in the visible UI, its route is still present in the compiled Angular routing code and can be found by searching through the bundled source.

## Steps Taken

### 1. Opened the Sources tab in DevTools

Opened DevTools (`F12`) → **Debugger** (Firefox's equivalent of Chrome's "Sources" tab) and located the `main.js` file.

### 2. Pretty-printed the minified code

Used the browser's built-in pretty-print feature (the `{}` formatting button) to make the minified bundle readable.

### 3. Searched for relevant keywords

Used the Debugger's search function to look for the terms `web3` and `sandbox` within the formatted source, iterating through matches until finding a route mapping section referencing a hidden path.

### 4. Navigated to the discovered route

```
http://localhost:3000/#/web3-sandbox
```

## Result

Challenge solved — confirmed by the in-app success notification and the Score Board. Simply visiting the hidden route was enough to trigger the solve.

## Root Cause

A development/testing route was left reachable in the production build. Although not linked anywhere in the visible navigation, the route was still compiled into the client-side JavaScript bundle and accessible to anyone who inspects the application's source code.

## Status

Solved.
