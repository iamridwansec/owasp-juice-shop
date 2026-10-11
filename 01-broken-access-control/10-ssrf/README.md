# SSRF: Request a Hidden Resource on the Server Through the Server

**Objective:** Request a hidden resource on the server through the server itself (Server-Side Request Forgery), by infecting the server with "juicy malware" and using a secret URL found inside it to trigger a self-targeted SSRF attack.

## Background

The Juice Shop profile page (`/profile`) lets a user link a Gravatar image by entering an image URL. Instead of the browser fetching that image directly, the **server** fetches it on the user's behalf. This means any URL submitted through that field is requested by the server itself — including internal/local URLs the browser could never reach directly on the server's behalf. This is the core SSRF vulnerability.

A separate part of this challenge involves a "juicy malware" executable containing a hidden secret URL, discoverable via decompiling the program or tunneling its traffic through a proxy. That URL, when triggered through the vulnerable Gravatar field, marks the challenge as solved.

## Steps Taken

### 1. Identified the vulnerable field

Went to `http://localhost:3000/profile` and located the **Image URL** field used to link a Gravatar image.
<img width="966" height="955" alt="Screenshot 2026-10-11 230204" src="https://github.com/user-attachments/assets/e36ce9b5-bd0e-48da-bbb0-329c30fea53f" />


### 2. Confirmed the SSRF behavior

Opened DevTools (`F12`) → **Network** tab, entered a test URL (e.g. `https://placecats.com/100/100`) into the Image URL field, and clicked **Link Image**.
<img width="966" height="955" alt="image" src="https://github.com/user-attachments/assets/ea7986b1-bf9f-42b8-bd5a-c14439050e2f" />


Observed:
- A `POST` request to `http://localhost:3000/profile/image/url` with `imageUrl` set to the entered URL — confirming the server itself receives and processes the submitted URL.
<img width="966" height="955" alt="image" src="https://github.com/user-attachments/assets/eac2a41e-9769-4573-8a4c-2d229caa908f" />


- In this version of Juice Shop, the browser also separately loaded the test image directly (a `GET` request to `placecats.com` to render it on the page) — this is just the page displaying the image to the user, and does not rule out the server also fetching the URL itself server-side.
<img width="966" height="955" alt="image" src="https://github.com/user-attachments/assets/594caef0-7105-45a8-9834-7e5944e1f327" />

### 3. Obtained the secret URL from the "juicy malware"

Per the challenge's guidance, the secret URL can be found by decompiling the malware executable or by running it while tunneling its traffic through a proxy to observe the outbound call. The identified URL was:

```
http://localhost:3000/solve/challenges/server-side?key=tRy_H4rd3r_n0thIng_iS_Imp0ssibl3
```
<img width="966" height="955" alt="image" src="https://github.com/user-attachments/assets/0070bcda-ac0b-4b50-a15c-8b210076c129" />


Visiting this URL directly in the browser has no effect — it must be triggered via the vulnerable Gravatar Link field so that the **server** requests it, satisfying the SSRF condition.

### 4. Triggered the SSRF

Pasted the secret URL into the Image URL field on the profile page:

```
http://localhost:3000/solve/challenges/server-side?key=tRy_H4rd3r_n0thIng_iS_Imp0ssibl3
```

Clicked **Link Image**, causing the server to request that URL on its own behalf. The Network tab confirmed a `GET` request to `localhost:3000/solve/challenges/server-side?key=...` with status `200`, proving the server itself made the call.
<img width="966" height="955" alt="image" src="https://github.com/user-attachments/assets/8c393898-4936-4541-81d9-89b0fdbf9165" />

## Result

Challenge solved — confirmed by the in-app success notification and the Score Board.
<img width="966" height="955" alt="Screenshot 2026-10-11 231620" src="https://github.com/user-attachments/assets/3ed5886a-dfe8-4c30-9dfd-0886c9130ed3" />


## Root Cause

The server fetches arbitrary, user-supplied URLs server-side with no validation or restriction on the target (such as blocking requests to `localhost` or internal addresses), allowing an attacker to make the server issue requests to internal-only resources it would not otherwise expose to a remote client.

## Status

Solved.
