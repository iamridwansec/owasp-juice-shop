[README-reset-jim's-password.md](https://github.com/user-attachments/files/34340878/README-reset-jim.s-password.md)
# Reset Jim's Password

**Difficulty:** ⭐⭐ (2/6)

**Category:** Broken Authentication

**Objective:** Reset Jim's password via the Forgot Password mechanism.

## Steps

1. Visited `http://localhost:3000/#/forgot-password` and entered `jim@juice-sh.op` as the email. <img width="483" height="477" alt="Screenshot 2026-11-03 214015" src="https://github.com/user-attachments/assets/44493f4f-d9cd-452d-be97-b1e01b79515c" />
This revealed Jim's chosen security question: **"Your eldest sibling's middle name?"**
2. Searched the application for breadcrumbs tied to Jim's identity (`UserId 2`):
   - A product review on the **OWASP Juice Shop-CTF Velcro Patch**: *"Looks so much better on my uniform than the boring Starfleet symbol."*
   - A product review on the **Green Smoothie**: *"Fresh out of a replicator."*
   - A **Recycling Request** linked to the saved address *"Room 3F 121, Deck 5, USS Enterprise, 1701"*
3. These clues (Starfleet, replicator, USS Enterprise) identified Jim as **James T. Kirk**.
4. Looked up James T. Kirk on Wikipedia, under the Depiction section, which names his brother: **George Samuel Kirk**.
5. Derived the security answer as the brother's middle name: **Samuel**.
6. Returned to `http://localhost:3000/#/forgot-password`, entered `jim@juice-sh.op` again, and answered the security question with `Samuel`. 
7. Entered a new password and matching repeat password, then clicked **Change**. <img width="483" height="477" alt="Screenshot 2026-11-03 214548" src="https://github.com/user-attachments/assets/e79a9a26-0146-465a-97c5-d67ebb964e8b" />

8. Confirmed the solve on the Score Board (`/#/score-board`). 

<img width="483" height="477" alt="Screenshot 2026-11-03 214600" src="https://github.com/user-attachments/assets/3fd8badb-87c5-4b94-aa72-8e91eb061047" />

<img width="483" height="477" alt="Screenshot 2026-11-03 214726" src="https://github.com/user-attachments/assets/d29eaccc-843c-404b-844b-425eb63f7ec0" />

## Observation

The security question mechanism is only as strong as how obscure the answer is. Here, the answer was publicly discoverable through in-app product reviews and an external Wikipedia page once the user's fictional identity was pieced together. Security questions based on information that can be researched or inferred from public sources (or from content the user themselves posted) are a weak second factor for account recovery.

## Remediation

- Avoid relying on security questions as a sole account-recovery factor; pair with email/SMS verification or time-limited reset tokens.
- Warn users against choosing security answers that are publicly discoverable or guessable from their own public activity.
- Rate-limit and monitor repeated forgot-password attempts tied to a single account.
