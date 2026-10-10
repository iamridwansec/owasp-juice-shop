# Forged Review: Post a Product Review as Another User

**Objective:** Post a product review as another user, or edit any user's existing review.

## Steps Taken

### 1. Logged in as a normal (non-admin) user

**Important:** This step must be done while logged in as an account *other than* the one being forged. An initial attempt was made while logged in as the admin account and forging the author as `admin@juice-sh.op` — this did **not** register as solved on the Score Board, since there was no actual identity mismatch (posting as yourself isn't forgery). Logging in as a normal user and forging the author as a different account solved the challenge correctly.

### 2. Wrote a review for a product

Selected a product (Banana Juice) and opened the "Write a review" dialog, entering a comment (e.g. "Great juice!").
<img width="483" height="477" alt="Screenshot 2026-10-09 090014" src="https://github.com/user-attachments/assets/14b6c60b-c2f0-4124-b505-0bab792a752f" />


### 3. Watched the Network tab while submitting

Opened DevTools (`F12`) → **Network**, then clicked **Submit** on the review. A `PUT` request to:
```
http://localhost:3000/rest/products/<id>/reviews
```
appeared in the list, with a request body of:
```json
{"message":"Great juice!","author":"samuel1234@gmail.com"}
```
<img width="483" height="477" alt="Screenshot 2026-10-09 090023" src="https://github.com/user-attachments/assets/5f656c25-8500-42a2-800d-97c8c50e5c54" />

### 4. Edited and resent the request
<img width="483" height="477" alt="Screenshot 2026-10-09 090051" src="https://github.com/user-attachments/assets/3ef81eae-e2dc-45a9-b39d-cb77af797bc0" />

Right-clicked the `PUT` request and used Firefox's built-in **Edit and Resend** panel. Changed the `author` field in the request body to a different user's identity, e.g.:
```json
{"message":"Great juice!","author":"admin@juice-sh.op"}
```
<img width="197" height="76" alt="Screenshot 2026-10-09 090136" src="https://github.com/user-attachments/assets/d8bdaf7a-6a28-4ea8-a5f4-28cf8e24180b" />

<img width="185" height="45" alt="Screenshot 2026-10-09 090244" src="https://github.com/user-attachments/assets/ea487e37-7283-410c-b9cc-892a2d6e8532" />

Clicked **Send**.


### 5. Confirmed the result

The resent request returned **201 Created**. Refreshing the product page showed the review now attributed to the forged author instead of the actual submitting account.

## Result

Challenge solved — confirmed on the Score Board.
<img width="483" height="477" alt="Screenshot 2026-10-09 090418" src="https://github.com/user-attachments/assets/b627f673-8339-442e-b42e-288e256159d6" />


## Root Cause

The review endpoint accepts an `author` field directly from the client-supplied request body instead of deriving it from the authenticated session, allowing any logged-in (or even unauthenticated) client to forge the attributed author of a review.

## Status

Solved.
