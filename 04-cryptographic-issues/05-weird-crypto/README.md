# Weird Crypto: Inform the Shop About an Inappropriate Crypto Algorithm or Library

**Objective:** Inform the shop about an algorithm or library it should definitely not use the way it does.

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

<img width="966" height="955" alt="Screenshot 2026-10-14 213837" src="https://github.com/user-attachments/assets/55b0c4ec-8634-4651-a540-abec6429ba87" />


### 2. Submitted feedback mentioning a relevant keyword

Filled in the feedback form, including one of the flagged keywords (`z85`, `base85`, `base64`, `md5`, or `hashid`) in the comment, e.g.:

> "This shop seems to still be using md5 for password hashing, which is an outdated and insecure choice."

<img width="966" height="955" alt="Screenshot 2026-10-14 213900" src="https://github.com/user-attachments/assets/5f1f4bb0-824e-429a-807c-c394aae37976" />

### 3. Solved the CAPTCHA

The form required solving a simple math CAPTCHA (e.g. `8*4+3 = 35`) before submission.

### 4. Submitted the form

## Result
Challenge solved — confirmed on the Score Board, under **Cryptographic Issues → Weird Crypto**.

<img width="966" height="955" alt="Screenshot 2026-10-14 214253" src="https://github.com/user-attachments/assets/38f76d65-7987-4b92-aa14-05782cd8b847" />


## Root Cause / Notes

This challenge doesn't require actually exploiting these weaknesses — just naming one of them in feedback. The related exploitation challenges (forging an 80%+ discount coupon via `z85`, solving challenge #999 via `hashid`) are separate, more advanced challenges that build on this same set of weak crypto choices.

## Status

Solved.
