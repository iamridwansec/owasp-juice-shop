# Five-Star Feedback: Get Rid of All 5-Star Customer Feedback

**Objective:** Delete all five-star customer feedback entries from the store.

## Steps Taken

### 1. Logged in

Attempted to log in with a normal user account first, but this did not provide access to the Administration section. Logged in instead with the default admin account:

- **Email:** `admin@juice-sh.op`
- **Password:** `admin123`

<img width="483" height="477" alt="Screenshot 2026-10-03 010802" src="https://github.com/user-attachments/assets/b03b028f-90d2-4b75-9164-e422962f36e8" />
### 2. Accessed the Administration section

Having already solved the **Admin Section** challenge, navigated to:
```
http://localhost:3000/#/administration
```

<img width="483" height="477" alt="Screenshot 2026-10-03 005205" src="https://github.com/user-attachments/assets/cfed101f-654b-4342-a3d6-9cb2b23f5649" />

### 3. Located the Customer Feedback table

Scrolled down past the Registered Users table to find the Customer Feedback table, listing each feedback entry with its star rating and a delete (trashcan) icon.

<img width="483" height="477" alt="Screenshot 2026-10-03 005236" src="https://github.com/user-attachments/assets/b9263a8a-44fd-4ae3-a877-09302c6b6630" />

### 4. Deleted all 5-star entries

Identified the single entry with a 5-star rating and deleted it using the trashcan icon.

## Result

Challenge solved — confirmed on the Score Board.
<img width="483" height="477" alt="Screenshot 2026-10-03 010218" src="https://github.com/user-attachments/assets/1b6c2837-059c-4414-9814-588a87af724f" />

<img width="483" height="477" alt="Screenshot 2026-10-03 005614" src="https://github.com/user-attachments/assets/9e72a494-99b4-4e86-b590-ab8765d3ef8e" />

## Status

Solved.

---

# Inform the Shop About an Inappropriate Crypto Algorithm or Library

**Objective:** Submit feedback mentioning one of Juice Shop's inappropriate cryptographic choices.

## Background

Juice Shop uses several weak or misused cryptographic approaches internally:

- `z85` (Zero-MQ Base85 implementation) used for coupon codes.
- The `hashid` library used in a way that enables forging valid hashes.
- Passwords in the `Users` table hashed with **unsalted MD5**.
- Users registering via Google receive a default password generated with Base64 encoding.

## Steps Taken

### 1. Went to the Contact page

```
http://localhost:3000/#/contact
```

### 2. Submitted feedback mentioning a relevant keyword

Filled in the feedback form, including one of the flagged keywords (`z85`, `base85`, `base64`, `md5`, or `hashid`) in the comment, e.g.:

> "This shop seems to still be using md5 for password hashing, which is an outdated and insecure choice."

### 3. Solved the CAPTCHA

The form required solving a simple math CAPTCHA (e.g. `8*4+3 = 35`) before submission.

### 4. Submitted the form

Yes sir <img width="483" height="477" alt="Screenshot 2026-10-03 010040" src="https://github.com/user-attachments/assets/cc86bfe7-390d-45a0-907c-ff5dafcb8c9a" />

## Result

Challenge solved — confirmed on the Score Board.

## Root Cause / Notes

This challenge doesn't require actually exploiting these weaknesses — just naming one of them in feedback. The related exploitation challenges (forging an 80%+ discount coupon via `z85`, solving challenge #999 via `hashid`) are separate, more advanced challenges that build on this same set of weak crypto choices.

## Status

Solved.
