# Easter Egg: Find the Hidden Easter Egg

**Objective:** Find the hidden easter egg, then apply cryptanalysis to find the real one hidden within it.

## Background

Juice Shop exposes a misconfigured FTP-style directory (`/ftp`) that lists legacy/backup files, a Broken Access Control issue on its own. One of these files, `eastere.gg`, is protected by a file-extension filter that only allows `.md` and `.pdf` files to be downloaded.

## Steps Taken

### 1. Found the exposed FTP directory

Navigated to:
```
http://localhost:3000/ftp
```
This revealed a directory listing of files not meant to be publicly browsable.

<img width="483" height="477" alt="Screenshot 2026-10-03 003223" src="https://github.com/user-attachments/assets/011954c0-62c2-4628-aacb-bcaa7cf1b5a3" />

### 2. Attempted to download the egg file directly

```
http://localhost:3000/ftp/eastere.gg
```

Result:
```
403 Error: Only .md and .pdf files are allowed!
```

<img width="483" height="477" alt="Screenshot 2026-10-03 001132" src="https://github.com/user-attachments/assets/592b95fe-178d-40e8-91f0-7a32974dec1c" />

### 3. Bypassed the extension filter with a Poison Null Byte

Exploited **CWE-626: Null Byte Interaction Error** by appending a URL-encoded null byte followed by a fake, allowed extension:

```
http://localhost:3000/ftp/eastere.gg%2500.md
```

<img width="353" height="105" alt="Screenshot 2026-10-03 001244" src="https://github.com/user-attachments/assets/b5e22d68-81f4-46a5-9ea3-ddd1064a4940" />

The server's filter only checks the fake `.md` suffix, while the underlying file system still returns the real `eastere.gg` file contents. This alone solved the **Find the hidden easter egg** challenge.

### 4. Read the file contents

The downloaded file contained a congratulatory message revealing it was a decoy, along with this base64-encoded string pointing to a further hidden challenge:

```
L2d1ci9xcmlmL25lci9mYi9zaGFhbC9ndXJsL3V2cS9uYS9ybmZncmUvcnR0L2p2Z3V2YS9ndXIvcm5mZ3JlL3J0dA==
```

<img width="483" height="477" alt="Screenshot 2026-10-03 001402" src="https://github.com/user-attachments/assets/1833b623-093a-4207-9a8b-254434a68a80" />

### 5. Base64-decoded the string

```bash
echo "L2d1ci9xcmlmL25lci9mYi9zaGFhbC9ndXJsL3V2cS9uYS9ybmZncmUvcnR0L2p2Z3V2YS9ndXIvcm5mZ3JlL3J0dA==" | base64 -d
```

Result:
```
/gur/qrif/ner/fb/shaal/gurl/uvq/na/rnfgre/rtt/jvguva/gur/rnfgre/rtt
```
<img width="479" height="78" alt="Screenshot 2026-10-03 003438" src="https://github.com/user-attachments/assets/376c6e90-0266-43a8-b69a-238488937f3a" />


This isn't a usable URL yet — visiting it directly fails. The repeated fragments (`rtt`, `gur`) hint at a simple substitution cipher rather than a dead end.

### 6. ROT13-decoded the result

```bash
echo "/gur/qrif/ner/fb/shaal/gurl/uvq/na/rnfgre/rtt/jvguva/gur/rnfgre/rtt" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

Result:
```
/the/devs/are/so/funny/they/hid/an/easter/egg/within/the/easter/egg
```

<img width="442" height="71" alt="Screenshot 2026-10-03 003648" src="https://github.com/user-attachments/assets/31ec1b4d-be07-485f-a7cd-c493ced612e4" />

### 7. Visited the decoded path

```
http://localhost:3000/the/devs/are/so/funny/they/hid/an/easter/egg/within/the/easter/egg
```

This revealed an interactive 3D scene of **Planet Orangeuze** — the "real" hidden easter egg, solving the **Apply some advanced cryptanalysis to find the real easter egg** challenge.

<img width="483" height="477" alt="Screenshot 2026-10-03 001932" src="https://github.com/user-attachments/assets/3b86794e-5a94-427f-b464-106660d00aec" />

## Result

Both challenges solved — confirmed on the Score Board:
- Find the hidden easter egg
- Apply some advanced cryptanalysis to find the real easter egg

<img width="483" height="477" alt="Screenshot 2026-10-03 003822" src="https://github.com/user-attachments/assets/e674e4fc-99fe-4a6e-bd97-995605a0746d" />

## Root Cause / Notes

- The server's file-extension allowlist check can be bypassed using a null byte, allowing access to any file in the exposed directory regardless of its real extension.
- ROT13 provides no real cryptographic security — it's a well-known weak substitution cipher used here purely as a lighthearted puzzle rather than a genuine security control.

## Status

Solved.
