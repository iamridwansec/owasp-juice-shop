# OWASP Juice Shop – Premium Paywall Challenge

**Challenge Goal:** Unlock the Premium Challenge by decrypting a hidden path and visiting it.

**Date started:** 15 October 2026

---

## Overview

The Score Board contains an encrypted HTML comment.  
This comment is an **AES-256-CBC** ciphertext.  
Once decrypted, it reveals a secret URL that unlocks the premium content.

---

## Step-by-Step Solution

### 1. Locate the encrypted comment

1. Open the Score Board: `http://localhost:3000/#/score-board`
2. Find the **Premium Paywall** challenge row.
3. Right-click → Inspect (or view page source).
4. You will find this HTML comment:

```html
<!--IvLuRfBJYlmStf9XfL6ckJFngyd9LfV1JaaN/KRTPQPidTuJ7FR+D/nkWJUF+0xUF07CeCeqYfxq+OJVVa0gNbqgYkUNvn//UbE7e95C+6e+7GtdpqJ8mqm4WcPvUGIUxmGLTTAC2+G9UuFCD1DUjg==-->
```

### 2. Find the encryption key (Forced Directory Browsing)

The key is stored in a hidden directory.

**Method A – Using ZAP**
- Launch OWASP ZAP (`zaproxy`)
- Use **Tools → Forced Browse → Forced Browse Directory**
- Target: `http://localhost:3000`
- Wordlist: `directory-list-2.3-big.txt` or `directory-list-lowercase-2.3-big.txt`

**Method B – Using terminal (faster)**
```bash
dirb http://localhost:3000 /usr/share/wordlists/dirb/big.txt
# or
gobuster dir -u http://localhost:3000 -w /usr/share/wordlists/dirb/big.txt -t 50
```

Both methods reveal:

```
http://localhost:3000/encryptionkeys
```

Then open:

```
http://localhost:3000/encryptionkeys/premium.key
```

**Content of `premium.key`:**
```
1337133713371337.EA99A61D92D2955B1E9285B55BF2AD42
```

- **IV**  = `1337133713371337`
- **Key** = `EA99A61D92D2955B1E9285B55BF2AD42`

### 3. Decrypt the ciphertext

Run this command in the terminal:

```bash
echo "IvLuRfBJYlmStf9XfL6ckJFngyd9LfV1JaaN/KRTPQPidTuJ7FR+D/nkWJUF+0xUF07CeCeqYfxq+OJVVa0gNbqgYkUNvn//UbE7e95C+6e+7GtdpqJ8mqm4WcPvUGIUxmGLTTAC2+G9UuFCD1DUjg==" | openssl enc -d -aes-256-cbc -K EA99A61D92D2955B1E9285B55BF2AD42 -iv 1337133713371337 -a -A
```

**Expected plaintext:**
```
/this/page/is/hidden/behind/an/incredibly/high/paywall/that/could/only/be/unlocked/by/sending/1btc/to/us
```

### 4. Visit the secret page

Open the decrypted path in your browser:

```
http://localhost:3000/this/page/is/hidden/behind/an/incredibly/high/paywall/that/could/only/be/unlocked/by/sending/1btc/to/us
```

This unlocks the **Premium Paywall** challenge and shows the premium VR wallpaper.

---

## Quick Reference

| Item              | Value                                                                 |
|-------------------|-----------------------------------------------------------------------|
| Ciphertext        | `IvLuRfBJYlmStf9XfL6ckJFngyd9LfV1JaaN/KRTPQPidTuJ7FR+D/nkWJUF+0xUF07CeCeqYfxq+OJVVa0gNbqgYkUNvn//UbE7e95C+6e+7GtdpqJ8mqm4WcPvUGIUxmGLTTAC2+G9UuFCD1DUjg==` |
| AES Mode          | AES-256-CBC                                                           |
| Key               | `EA99A61D92D2955B1E9285B55BF2AD42`                                    |
| IV                | `1337133713371337`                                                    |
| Decrypted path   | `/this/page/is/hidden/behind/an/incredibly/high/paywall/that/could/only/be/unlocked/by/sending/1btc/to/us` |

---

## Notes & Tips

- The IV in the key file is written as `1337` but must be used as the 16-byte value `1337133713371337`.
- Always use the `-a -A` flags with openssl so it correctly handles the base64 input.
- Completing the visit to the secret URL is enough to solve the challenge.

---

**Status:** Key and IV recovered. Decryption and final visit remaining.
