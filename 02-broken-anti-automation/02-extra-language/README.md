# Extra Language: Retrieve the Language File That Never Made It Into Production

**Objective:** Retrieve the language file that never made it into production.

## Background

Juice Shop loads translation files from `/i18n/<locale>.json`, where each locale uses the official underscore notation (e.g. `de_DE`, `zh_CN`, `nl_NL`). One supported language was never fully exposed in the production UI: **Klingon**, represented by the non-standard three-letter language code `tlh` with the dummy country code `AA`.

## Steps Taken

### 1. Observed how translation files are loaded

Monitored the Network tab while switching languages in the app, confirming requests to paths like:

```
http://localhost:3000/i18n/en.json
http://localhost:3000/i18n/de_DE.json
http://localhost:3000/i18n/nl_NL.json
http://localhost:3000/i18n/zh_CN.json
```

This confirmed the underscore-based locale naming pattern used for all language files.

### 2. Ruled out brute-forcing standard locale codes

Brute-forcing standard two-letter locale/country combinations (`aa_AA` through `zz_ZZ`) would not reveal the hidden file, since the actual code uses a non-standard three-letter language code.

### 3. Investigated how translations are managed

Looked into how Juice Shop's translations are contributed and found they are managed via the project's Crowdin page (`https://crowdin.com/project/owasp-juice-shop`), which lists all supported languages — including **Klingon**. Hovering over the Klingon flag revealed the exact locale code: `tlh_AA`.

### 4. Requested the hidden language file

```
http://localhost:3000/i18n/tlh_AA.json
```

## Result

Challenge solved — confirmed by the in-app success notification and the Score Board.

## Root Cause

A language file for a locale that was never linked or selectable in the production UI was still deployed and publicly accessible at a predictable path, since the server does not restrict which `i18n/<locale>.json` files can be requested.

## Status

Solved.
