# SSRF: Request a Hidden Resource on the Server Through the Server

**Objective:** Request a hidden resource on the server through the server itself (Server-Side Request Forgery), by infecting the server with "juicy malware" and using a secret URL found inside it to trigger a self-targeted SSRF attack.

## Background

The Juice Shop profile page (`/profile`) lets a user link a Gravatar image by entering an image URL. Instead of the browser fetching that image directly, the **server** fetches it on the user's behalf. This means any URL submitted through that field is requested by the server itself — including internal/local URLs the browser could never reach directly on the server's behalf. This is the core SSRF vulnerability.

A separate part of this challenge involves a "juicy malware" executable containing a hidden secret URL, discoverable via decompiling the program or tunneling its traffic through a proxy. That URL, when triggered through the vulnerable Gravatar field, marks the challenge as solved.

## Steps Taken

### 1. Identified the vulnerable field

Went to `http://localhost:3000/profile` and located the **Image URL** field used to link a Gravatar image.

### 2. Confirmed the SSRF behavior

Opened DevTools (`F12`) → **Network** tab, entered a test URL (e.g. `https://placecats.com/100/100`) into the Image URL field, and clicked **Link Image**.

Observed:
- A request to `http://localhost:3000/profile/image/url` with `imageUrl` set to the entered URL.
- No direct browser request to the external URL itself — confirming the image was fetched server-side, not by the browser.

### 3. Obtained the secret URL from the "juicy malware"

Per the challenge's guidance, the secret URL can be found by decompiling the malware executable or by running it while tunneling its traffic through a proxy to observe the outbound call. The identified URL was:

```
http://localhost:3000/solve/challenges/server-side?key=tRy_H4rd3r_n0thIng_iS_Imp0ssibl3
```

Visiting this URL directly in the browser has no effect — it must be triggered via the vulnerable Gravatar Link field so that the **server** requests it, satisfying the SSRF condition.

### 4. Triggered the SSRF

Pasted the secret URL into the Image URL field on the profile page:

```
http://localhost:3000/solve/challenges/server-side?key=tRy_H4rd3r_n0thIng_iS_Imp0ssibl3
```

Clicked **Link Image**, causing the server to request that URL on its own behalf.

## Result

Challenge solved — confirmed by the in-app success notification and the Score Board.

## Root Cause

The server fetches arbitrary, user-supplied URLs server-side with no validation or restriction on the target (such as blocking requests to `localhost` or internal addresses), allowing an attacker to make the server issue requests to internal-only resources it would not otherwise expose to a remote client.

## Status

Solved.
