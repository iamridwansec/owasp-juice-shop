# Broken Access Control: Access the Administration Section

**Objective:** Access the administration section of the store (`/#/administration`).

## Steps Tried

### 1. Direct URL as a normal user

Navigated to `/#/administration` while logged in as a normal registered user.

**Result:** `403 - You are not allowed to access this page!`. The frontend correctly blocks non-admin roles from this route.

### 2. Inspected the JWT token

Opened DevTools → **Storage** → **Local Storage** → `http://localhost:3000`, and found the `token` key. Decoded the middle (payload) segment of the JWT:

```bash
echo '<payload-segment>' | tr '_-' '/+' | base64 -d
```

The decoded payload confirmed the logged-in user's role:

```json
{"role":"customer", ...}
```

This also revealed the user's password as an MD5 hash stored inside the token payload in `localStorage` — a secondary finding worth noting: sensitive account data is exposed client-side, even if hashed.

### 3. Searched the rendered page for a hidden admin link

Used DevTools **Inspector**'s own search (not the browser's page search) on the account dropdown menu, searching for "admin". No matching elements were found — there is no hidden admin link in the menu for this version.

### 4. Checked the Console for errors

No specific permission error was logged beyond a font warning; the 403 is handled entirely by the frontend route guard.

### 5. Logged in with the default admin account

Logged out, then logged back in with Juice Shop's well-known default admin credentials:

- **Email:** `admin@juice-sh.op`
- **Password:** `admin123`

This succeeded, because the admin account's default password had never been changed.

### 6. Accessed the administration page

With the admin session active, navigated to `/#/administration` again. The page loaded successfully.

## Result

Three challenges were solved by this one action:

- **Admin Section** — accessed the administration page.
- **Login Admin** — logged in with the admin account.
- **Password Strength** — the admin account was still using its default, unchanged password.

## Root Cause

The administration page is correctly protected against being reached directly. The real issue is that the admin account was left with its default credentials — this is a weak/default-credentials issue rather than a bypass of the access control logic itself.

## Status

✅ Solved.
