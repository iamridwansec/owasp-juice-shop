# CSRF: Change a User's Name via Cross-Site Request Forgery

**Objective:** Change the name of a logged-in user by performing a Cross-Site Request Forgery (CSRF) attack from another origin.

## Background

The Juice Shop profile update endpoint (`POST /profile`) accepts a `username` field and relies solely on the session cookie for authentication, with no CSRF token or `SameSite` cookie protection. This means any page from a different origin can submit a form to this endpoint, and if the victim is logged in, the request is carried out using their session.

## Steps Taken

### 1. Logged in as a normal (non-admin) user

Used an existing registered account to act as the "victim" of the attack.

<img width="483" height="477" alt="Screenshot 2026-10-02 235508" src="https://github.com/user-attachments/assets/5b710e50-c88e-40f4-aa59-fa19cc70f62c" />

### 2. Created a malicious self-submitting HTML form

Saved the following as a local HTML file, acting as a page hosted on a different origin than Juice Shop:

```html
<form action="http://localhost:3000/profile" method="POST">
  <input name="username" value="CSRF"/>
  <input type="submit"/>
</form>
<script>document.forms[0].submit();</script>
```

### 3. Opened the file while logged into Juice Shop

Opened the file directly in the browser (`file://` origin — a different origin than `http://localhost:3000`) in a separate tab, while still logged into Juice Shop in another tab. The form auto-submitted via the inline `<script>`.

> **Note:** The official Juice Shop guide suggests using an online HTML editor (`htmledit.squarefree.com`) to host the form on a genuine external origin. If that site is unavailable or doesn't load, the same result can be achieved locally instead:
>
> 1. Open a terminal and run:
>    ```bash
>    nano ~/csrf-attack.html
>    ```
> 2. Paste in the form/script code above, save (`Ctrl+O`, `Enter`), and exit (`Ctrl+X`).
> 3. Open it in the browser at `file:///home/kali/csrf-attack.html`.
>
> A local `file://` page still counts as "another origin" relative to `http://localhost:3000`, so this still demonstrates the same vulnerability.

### 4. Verified the result

Navigated to `http://localhost:3000/profile` and confirmed the username had changed to **"CSRF"**, without the user manually filling in or submitting the profile form themselves.
<img width="483" height="477" alt="Screenshot 2026-10-02 234451" src="https://github.com/user-attachments/assets/31363ef2-c140-46a3-a59a-f3b65e05b1db" />

## Result

Challenge solved — confirmed on the Score Board.

## Root Cause

The profile update endpoint does not use a CSRF token and does not set `SameSite` restrictions on its session cookie, allowing a form on an entirely different origin to perform an authenticated action on behalf of a logged-in user without their knowledge or consent.

## Real-World Risk

In a real attack, a victim would be tricked into visiting an attacker-controlled page (e.g. via a phishing link) while already logged into the target site. The malicious form could be hidden (for example inside a zero-size `<iframe>`) so the victim never notices the request happening in the background.

## Status

Solved.
