# Imaginary Challenge: Solve Challenge #999

**Objective:** Solve challenge #999.

**Difficulty:** ★★★★★★★ (7 stars) — Cryptographic Issues

## Background

Juice Shop uses a `continueCode` cookie to let a user resume their challenge progress. This code is generated using the **hashids** library (confirmed as a dependency via the exposed `package.json.bak` backup file), which encodes a numeric challenge ID into a short, reversible string.

Crucially, Juice Shop uses the **exact example configuration from the hashids.org demo page** itself:
- Salt: `this is my salt`
- Character range: `abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890`
- Minimum length: `60` (the demo itself uses 8)

Since the salt and character set are publicly known defaults rather than a real secret, anyone can generate a valid continue code for *any* arbitrary challenge ID — including one that doesn't correspond to a real challenge, like **#999** — and submit it directly to the server.

## Steps Taken

### 1. Inspected cookies for a continueCode

Opened DevTools (`F12`) → **Storage** tab → **Cookies** → `http://localhost:3000`, and located a cookie named `continueCode`, confirming this is how Juice Shop encodes and tracks challenge progress.

<img width="483" height="176" alt="Screenshot 2026-10-17 233158" src="https://github.com/user-attachments/assets/7c66f5e7-7a0e-427d-9ab0-5c1d8b29a922" />

### 2. Identified the hashids library and its configuration

Found `hashids` listed as a dependency in the exposed `package.json.bak`, then confirmed its usage pattern matched the public demo on [hashids.org](http://hashids.org/) and its linked CodePen demo (`http://codepen.io/ivanakimov/pen/bNmExm`).

### 3. Encoded the value 999 using the known salt/charset/length

Using the demo's own JavaScript:

```javascript
var hashids = new Hashids("this is my salt", 60, "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ1234567890");
var id = hashids.encode(999);
```

Result:
```
69OxrZ8aJEgxONZyWoz1Dw4BvXmRGkM6Ae9M7k2rK63YpqQLPjnlb5V5LvDj
```

### 4. Submitted the forged continue code

Sent a `PUT` request directly to the continue-code application endpoint using the forged hash:

```bash
curl -s -o /dev/null -w "%{http_code}\n" -X PUT "http://localhost:3000/rest/continue-code/apply/69OxrZ8aJEgxONZyWoz1Dw4BvXmRGkM6Ae9M7k2rK63YpqQLPjnlb5V5LvDj"
```

Result:
```
200
```

## Result

Challenge solved — confirmed on the Score Board under **Cryptographic Issues → Imaginary Challenge**.

<img width="483" height="477" alt="Screenshot 2026-10-17 233414" src="https://github.com/user-attachments/assets/f71408da-93b3-48d6-8620-19232cd14f38" />

## Root Cause

The continue-code generation uses a publicly known, unmodified example salt and character set straight from the hashids library's own public demo, rather than a secret unique to the application. This makes it trivial to forge a valid-looking continue code for any numeric value, including non-existent "challenge #999", without any real cryptographic protection.

## Status

Solved.
