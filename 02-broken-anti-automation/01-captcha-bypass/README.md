# CAPTCHA Bypass: Submit 10 or More Customer Feedbacks Within 20 Seconds

**Objective:** Submit 10 or more customer feedbacks within 20 seconds.

## Background

The Contact Us feedback form retrieves a CAPTCHA from `GET /rest/captcha/`, returning a response like:

```json
{"captchaId":18,"captcha":"5*8*8","answer":"320"}
```

The form submission (`POST /api/Feedbacks`) sends back the solved `captcha` and matching `captchaId`:

```json
{"comment":"Hello","rating":1,"captcha":"320","captchaId":18}
```

Critically, the server does not enforce that the `captchaId`/`captcha` pair used must be the *most recently issued* one — any previously issued, correctly solved pair is still accepted. This means a CAPTCHA can effectively be "pinned" and reused, or alternatively, a script can simply fetch and solve a fresh CAPTCHA automatically for each submission. Since solving CAPTCHAs manually one at a time cannot realistically hit 10 submissions in 20 seconds, this needs to be automated.

## Steps Taken

### 1. Confirmed the CAPTCHA/feedback flow

Opened DevTools (`F12`) → **Network** tab, visited `http://localhost:3000/#/contact`, and observed the `GET /rest/captcha/` request and its response shape, followed by the `POST /api/Feedbacks` request when submitting a feedback manually.

### 2. Confirmed CAPTCHA pinning was possible

Submitted one feedback normally, then requested a new CAPTCHA, but submitted a second feedback using the **previous** `captchaId`/`answer` pair instead of the newly issued one. The server accepted it, confirming old CAPTCHA pairs remain valid.

### 3. Automated the submissions with a browser console script

Rather than relying only on pinning, used a script that fetches a **fresh CAPTCHA for every submission** (robust even if pinning were fixed), run directly in the browser Console on the Juice Shop page:

```javascript
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
```

### 4. Ran the script

Pasted the script into the Console and pressed Enter, firing 15 feedback submissions in rapid succession, well within the 20-second window and comfortably over the required 10.

## Result

Challenge solved — confirmed by the in-app success notification and the Score Board.

## Root Cause

The CAPTCHA mechanism exists purely as a client-facing deterrent — the server accepts any previously issued, correctly solved `captchaId`/`captcha` pair rather than strictly requiring the most recently issued one. Combined with no rate limiting on the feedback endpoint itself, this allows automated, high-volume submissions that a human-facing CAPTCHA is meant to prevent.

## Notes

Alternative approaches mentioned in the official solution guide:
- A tool like **Selenium WebDriver** could read the CAPTCHA question from the page's HTML, solve it with JavaScript, and type the answer into the form field like a real user, avoiding the API entirely.
- A load-testing tool such as **RaceTheWeb** could fire repeated `POST` requests using a single pinned `captchaId`/`captcha` pair.

## Status

Solved.
