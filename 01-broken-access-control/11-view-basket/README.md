# View Baskets: View Another User's Shopping Basket

**Objective:** View another user's shopping basket.

## Background

Juice Shop stores the current user's basket ID (`bid`) in the browser's Session Storage. The Angular front-end reads this value to decide which basket to display, without re-validating that the basket actually belongs to the logged-in user. Changing this stored value lets the basket view load a different user's basket.

## Steps Taken

### 1. Logged in

Logged in as a normal user.

### 2. Added products to the basket

Put a few products into the shopping basket to have some reference content to compare against.

### 3. Inspected Session Storage

Opened DevTools (`F12`) → **Storage** tab → **Session Storage** → `http://localhost:3000`, and located a numeric key called `bid`.

<img width="483" height="477" alt="Screenshot 2026-10-10 012432" src="https://github.com/user-attachments/assets/b6c98a39-3fcd-4295-858b-8721c82ba90b" />

<img width="483" height="477" alt="Screenshot 2026-10-10 012452" src="https://github.com/user-attachments/assets/26e687a9-7833-4ac7-b113-e83c59b5b5d4" />

### 4. Changed the bid value

Edited the `bid` value directly in Session Storage, adding or subtracting `1` from the original number to point at a different (likely another user's) basket ID.

<img width="483" height="477" alt="Screenshot 2026-10-10 012648" src="https://github.com/user-attachments/assets/b0568fe0-0fb4-4ac6-a9a4-98a60fac8bfa" />

### 5. Visited the basket page

Navigated to:
```
http://localhost:3000/#/basket
```

<img width="483" height="477" alt="Screenshot 2026-10-10 012707" src="https://github.com/user-attachments/assets/af7b98ef-527e-4afc-be99-d6fcdad32172" />

**Note:** If the challenge did not register as solved immediately, a full page reload (`F5`) was needed to make the Angular client pick up the changed `bid` value from Session Storage.

## Result

Challenge solved — confirmed by the in-app success notification and the Score Board. The basket view displayed contents belonging to a different basket ID than the logged-in user's own.


## Root Cause

The client trusts the `bid` value stored in Session Storage to determine which basket to display, without the server re-verifying that the requested basket actually belongs to the authenticated user's session.

## Status

Solved.
