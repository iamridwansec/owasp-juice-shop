# Forged Feedback: Post some feedback in another user’s name

**Difficulty:** ⭐ (1 star)  
**Category:** Broken Access Control

---

## Objective

Submit customer feedback that is attributed to a different user than the one currently logged in (or while logged out). The application should accept a forged `UserId` without verifying it against the authenticated session.

---

## Solution Steps

### Method 1: Using the Browser (UI)

1. **Find a valid User ID**
   - Go to the Administration page:  
     `http://localhost:3000/#/administration`
   - Click on any user (for example User #2).
   - Note the User ID (e.g. `2` for `jim@juice-sh.op`).
  <img width="483" height="477" alt="Screenshot 2026-10-03 123107" src="https://github.com/user-attachments/assets/5c6dee3b-79ac-49ba-a9f4-ee747f3cfddf" />


2. **Open the Contact Us form**
   - Navigate to:  
     `http://localhost:3000/#/contact`
<img width="483" height="477" alt="Screenshot 2026-10-03 121613" src="https://github.com/user-attachments/assets/d2958f46-a597-4743-a592-4cd6a7563e46" />


3. **Expose the hidden field**
   - Open browser Developer Tools (press `F12`).
  <img width="483" height="477" alt="Screenshot 2026-10-03 121907" src="https://github.com/user-attachments/assets/2f42e920-30ba-4187-a64d-bb92f00db145" />

   - Inspect the Contact form.
   - Locate the hidden input field:

     ```html
     <input id="userId" type="text" hidden ...>
     ```

   - Remove the `hidden` attribute so the field becomes visible on the page.
<img width="483" height="477" alt="Screenshot 2026-10-03 122008" src="https://github.com/user-attachments/assets/81a68993-35a7-48c7-8f61-75166bd8eb25" />


4. **Submit forged feedback**
   - Enter a different user’s ID (e.g. `2`) into the now-visible `userId` field.
   - Write any comment, for example:

     ```
     This is forged feedback posted in another user's name!
     ```
<img width="483" height="477" alt="Screenshot 2026-10-03 123409" src="https://github.com/user-attachments/assets/2eb2e01b-59fd-4225-8ae0-b01289858618" />

   - Choose a rating and click **Submit**.
<img width="483" height="477" alt="Screenshot 2026-10-03 123422" src="https://github.com/user-attachments/assets/45ef2bb5-7217-44d0-804d-359a76924edc" />

---

### Method 2: Direct API Call (Optional)

This method is optional and not recommended for beginners. You can solve the challenge without using the web interface by sending a POST request directly to the API.

**Endpoint:**  
`POST http://localhost:3000/api/Feedbacks`

**Example using curl:**

```bash
curl -X POST http://localhost:3000/api/Feedbacks \
  -H "Content-Type: application/json" \
  -d '{"UserId":2,"comment":"This is forged feedback posted in another user'\''s name!","rating":1}'
```

**JSON payload:**

```json
{
  "UserId": 2,
  "comment": "This is forged feedback posted in another user's name!",
  "rating": 1
}
```

You can also use tools such as Burp Suite, OWASP ZAP, Postman, or the browser’s Network tab to send the same request.

---

## Why This Works

The backend trusts the `UserId` value that comes from the client and does not check whether it matches the currently authenticated user. This is a classic **Broken Access Control** vulnerability (sometimes related to mass assignment).

---

## How to Verify the Challenge is Solved

1. Open the Score Board:  
   `http://localhost:3000/#/score-board`
2. Search for **“Forged Feedback”**.
3. The challenge should now appear as solved.<img width="483" height="477" alt="Screenshot 2026-10-03 123613" src="https://github.com/user-attachments/assets/911bd685-d2f1-4eec-af4f-383e4651f7ad" />

4. Optionally, check the Customer Feedback section to confirm the comment is attributed to the forged user.

---

## Tips

- You do **not** need to be logged in when using the API method.
- Any valid user ID other than your own will work (usually sequential numbers starting from 1).
- The hidden `userId` field is intentionally left in the DOM as a hint by the Juice Shop developers.

---

*This guide explains how to solve the Forged Feedback challenge in OWASP Juice Shop.*
