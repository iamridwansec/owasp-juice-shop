# Reset Morty's Password: Brute-Force a Security Question Answer

## Overview

This README documents a learning exercise for the **Reset Morty's Password** challenge in OWASP Juice Shop, an intentionally vulnerable web application used for cybersecurity training.

> **Use responsibly:** Perform this exercise only in your own Juice Shop lab or an environment where you have explicit permission to test.

## Objective

Understand how a password-reset workflow works, why security-question answers can be guessable, and why applications should protect reset endpoints against repeated attempts.

## Lab Setup

1. Start your local OWASP Juice Shop instance.
2. Open the application in a browser.
3. Open the Score Board and find **Reset Morty's Password**.
4. If Juice Shop is running locally on its default port, the application is commonly available at `http://localhost:3000`.

## Steps Followed

### 1. Open the password-reset page

Navigate to:

`http://localhost:3000/#/forgot-password`

This address assumes the local Juice Shop instance is running on port `3000`.

<img width="966" height="955" alt="Screenshot 2026-10-14 095545" src="https://github.com/user-attachments/assets/5bba6867-8ba6-4703-8365-bc9a244e91e1" />

### 2. Identify the challenge account

The challenge references Morty Smith. The account used in the exercise is:

`morty@juice-sh.op`

Use this only in the local training application.

### 3. Investigate the security question

The challenge uses a clue related to Morty's dog. The names associated with the clue are **Snuffles** and **Snowball**.

A leetspeak-style version of “Snowball” discussed during the exercise was:

`5N0wb41L`

The accepted answer can depend on the challenge version and its implementation. Treat this as a lab clue rather than a general password-reset technique.

### 4. Submit the reset form

If the lab accepts the security answer:

1. Enter a new password.
2. Enter the same value in the confirmation field.
3. Submit the form.

A simple example password discussed during the exercise was:

`BlueSky@2026`

**Security note:** This example appears in this README and should be treated as public. Do not reuse it for a real account. Choose a unique password for any account that matters.

### 5. Verify the result

Return to the Juice Shop Score Board and check whether **Reset Morty's Password** is marked as completed.
<img width="966" height="955" alt="Screenshot 2026-10-14 100423" src="https://github.com/user-attachments/assets/5b456607-1ef1-42b6-995a-fdecc65a5618" />


The challenge may include additional protections, such as rate limiting. If it does not complete, consult the official Juice Shop Companion Guide for the version installed in your lab.
<img width="966" height="955" alt="Screenshot 2026-10-14 101440" src="https://github.com/user-attachments/assets/13f0fa32-868e-479c-b6eb-537627a54850" />


## Security Lessons

- Security-question answers are often guessable or discoverable and should not be treated as strong authentication factors.
- Password-reset workflows should use secure, unpredictable reset tokens or other robust verification methods.
- Applications should rate-limit reset attempts and monitor suspicious activity.
- Avoid exposing sensitive account details in error messages.
- Passwords should be unique, sufficiently long, and stored using a modern password-hashing algorithm.
- Test only systems for which you have authorization.

## References

- OWASP Juice Shop: https://owasp.org/www-project-juice-shop/
- OWASP Juice Shop Companion Guide: https://help.owasp-juice.shop/part2/

## Completion Checklist

- [ ] Started the local Juice Shop lab.
- [ ] Located the challenge on the Score Board.
- [ ] Opened the password-reset page.
- [ ] Investigated the security-question clue.
- [ ] Submitted the reset form in the authorized lab.
- [ ] Verified the challenge status on the Score Board.
