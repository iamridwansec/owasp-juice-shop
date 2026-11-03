[README-password-strength.md](https://github.com/user-attachments/files/34308284/README-password-strength.md)
# Password Strength

**Difficulty:** ⭐ (1/6)

**Category:** Broken Authentication

**Objective:** Log in with the administrator's user credentials without previously changing them or applying SQL Injection.

## Steps

1. Navigated to `http://localhost:3000/#/login`.<br>     <img width="483" height="477" alt="Screenshot 2026-11-03 084308" src="https://github.com/user-attachments/assets/a0cd8ecd-ccd5-44f1-ac89-e3662f5d0da1" />

2. Entered the default administrator credentials:
   - Email: `admin@juice-sh.op`
   - Password: `admin123`
3. Submitted the login form — authenticated successfully as admin with no injection or brute-force required.
4. Confirmed the solve on the Score Board (`/#/score-board`).<img width="159" height="128" alt="Screenshot 2026-11-03 084526" src="https://github.com/user-attachments/assets/152ff632-40a5-4fd3-8ce0-4852ebfbf69e" />


## Observation

The application ships with a default administrator account whose credentials were never rotated. `admin123` is trivially guessable, appears in common rainbow tables, and would fall to a basic dictionary/brute-force attack in seconds. This highlights the risk of shipping or deploying with default credentials left unchanged in production.

## Remediation

- Force a password change on first login for any seeded/default account.
- Enforce a minimum password complexity policy (length, character classes) at account creation and reset.
- Never ship default accounts with static, well-known passwords into a production build.
