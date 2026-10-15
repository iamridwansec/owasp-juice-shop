# Premium Paywall Challenge

**Challenge Goal:** Unlock the Premium Challenge by decrypting a hidden path and visiting it.

---

## Overview

The Score Board contains an encrypted HTML comment.  
This comment is an **AES-256-CBC** ciphertext.  
Once decrypted, it reveals a secret URL that unlocks the premium content.

---

## Step-by-Step Solution

### Step 1: Find the HTML comment on the Score Board

1. Go to:
   ```
   http://localhost:3000/#/score-board
   ```
   <img width="966" height="1015" alt="Screenshot 2026-10-15 235430" src="https://github.com/user-attachments/assets/e27395c9-9545-4555-9245-3551dd2ca2cc" />

2. Search for “premium” in the search box to find the **Premium Paywall** card. <img width="966" height="1015" alt="Screenshot 2026-10-15 235452" src="https://github.com/user-attachments/assets/c14eb5ca-4a2f-4097-98ff-d5e334f978b4" />

3. Right-click directly on that card (or near it) and choose **Inspect**  
   (do **not** use “View Page Source”, because Score Board content is rendered dynamically). 
4. In the Inspector, use the Inspector’s own search box and search for:
   ```
   IvLuRfBJYlmStf9XfL6ckJFngyd9LfV1JaaN
   ```
   
5. That jumps you to the HTML comment containing the encrypted string:

   ```html
   <!--
   IvLuRfBJYlmStf9XfL6ckJFngyd9...
   nkWJUF+0xUF07CeCeqYfxq+OJVVa...
   UbE7e95C+6e+7GtdpqJ8mqm4WcPvU...
   -->
   ```

This matches the encrypted string from the official guide.

---

### Step 2: Retrieve the encryption key

The key file is publicly accessible (no directory protection).

1. Open this URL directly in the browser:
   ```
   http://localhost:3000/encryptionkeys/premium.key
   ```
2. The file downloads automatically (e.g. `premium.key` or `premium(1).key`).
<img width="966" height="1015" alt="Screenshot 2026-10-15 235758" src="https://github.com/user-attachments/assets/67127592-d620-4d8e-9c3f-e703b0bc74d4" />

3. Read the file contents in the terminal:
   ```bash
   cat ~/Downloads/premium*.key
   ```

**Output:**
```
1337133713371337.EA99A61D92D2955B1E9285B55BF2AD42
```

- **IV**  = `1337133713371337`
- **Key** = `EA99A61D92D2955B1E9285B55BF2AD42`

---
<img width="523" height="94" alt="Screenshot 2026-10-15 235937" src="https://github.com/user-attachments/assets/7b4c6515-0202-4655-a67c-e1425326567b" />


### Step 3: Decrypt the ciphertext

Run this exact command:

```bash
echo "IvLuRfBJYlmStf9XfL6ckJFngyd9LfV1JaaN/KRTPQPidTuJ7FR+D/nkWJUF+0xUF07CeCeqYfxq+OJVVa0gNbqgYkUNvn//UbE7e95C+6e+7GtdpqJ8mqm4WcPvUGIUxmGLTTAC2+G9UuFCD1DUjg==" | openssl enc -d -aes-256-cbc -K EA99A61D92D2955B1E9285B55BF2AD42 -iv 1337133713371337 -a -A
```

**Decrypted plaintext:**
```
/this/page/is/hidden/behind/an/incredibly/high/paywall/that/could/only/be/unlocked/by/sending/1btc/to/us
```

---

<img width="949" height="151" alt="Screenshot 2026-10-16 000757" src="https://github.com/user-attachments/assets/97622748-8d93-4f75-be27-be445940b415" />

### Step 4: Visit the secret page

Open the full URL in your browser:

```
http://localhost:3000/this/page/is/hidden/behind/an/incredibly/high/paywall/that/could/only/be/unlocked/by/sending/1btc/to/us
```

You will see the premium VR wallpaper (Juice Shop logo with ants and 3D effect).  
<img width="966" height="955" alt="Screenshot 2026-10-16 000841" src="https://github.com/user-attachments/assets/74f9d9e4-1ac6-4652-8c79-c54248ce2117" />

This solves the **Premium Paywall** challenge.

---

<img width="966" height="955" alt="Screenshot 2026-10-16 001015" src="https://github.com/user-attachments/assets/73008845-c4fe-4c56-a9ef-723e52c3482c" />

## Quick Reference

| Item              | Value |
|-------------------|-------|
| Ciphertext        | `IvLuRfBJYlmStf9XfL6ckJFngyd9LfV1JaaN/KRTPQPidTuJ7FR+D/nkWJUF+0xUF07CeCeqYfxq+OJVVa0gNbqgYkUNvn//UbE7e95C+6e+7GtdpqJ8mqm4WcPvUGIUxmGLTTAC2+G9UuFCD1DUjg==` |
| AES Mode          | AES-256-CBC |
| Key               | `EA99A61D92D2955B1E9285B55BF2AD42` |
| IV                | `1337133713371337` |
| Decrypted path   | `/this/page/is/hidden/behind/an/incredibly/high/paywall/that/could/only/be/unlocked/by/sending/1btc/to/us` |

---

## Notes & Tips

- The key file can be accessed directly — no brute-force is strictly required (although Forced Browse also works).
- The IV appears as `1337` in some write-ups but must be used as the 16-byte value `1337133713371337`.
- Always include the `-a -A` flags with `openssl` so base64 input is handled correctly.
- Visiting the decrypted URL is sufficient to mark the challenge as solved.

---
[README-Premium-Paywall.md](https://github.com/user-attachments/files/33518457/README-Premium-Paywall.md)
