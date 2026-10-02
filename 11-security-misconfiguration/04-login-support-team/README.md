# Login Support Team

## Challenge Information

| Field          | Details                                                                                                                |
| -------------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Challenge**  | Login Support Team                                                                                                     |
| **Category**   | Security Misconfiguration                                                                                              |
| **Difficulty** | 6                                                                                                                    |
| **Objective**  | Log in with the support team's original user credentials without using SQL Injection or another authentication bypass. |
| **Target**     | OWASP Juice Shop — `http://127.0.0.1:42000`                                                                            |

---

## Reconnaissance / Discovery

The challenge description provided several breadcrumbs:

* The support team uses a third-party tool to access the current account password.
* The support account uses a strong password.
* SQL Injection can authenticate as the support team, but that does not solve the challenge.
* The objective is to recover and use the support team's **original credentials**.

Instead of attempting SQL Injection, the investigation focused on finding exposed support-related files and resources.

### 1. Discovering the FTP Directory

The Juice Shop `/ftp` endpoint was checked:

```bash
curl -i http://127.0.0.1:42000/ftp/
```

The server returned `200 OK` and exposed a directory listing.

The listing contained several files, including:

```text
incident-support.kdbx
```

![FTP directory listing](images/01-ftp-directory-listing.png)

### Finding

The file name `incident-support.kdbx` was significant because `.kdbx` is the database format used by **KeePass**, a password-management application.

This matched the challenge clue about the support team using a third-party tool to access credentials.

---

### 2. Downloading and Identifying the Database

The exposed database could be downloaded directly:

```bash
curl -O http://127.0.0.1:42000/ftp/incident-support.kdbx
```

The downloaded file was then identified:

```bash
file incident-support.kdbx
```

The result identified it as:

```text
Keepass password database 2.x KDBX
```

![KeePass database identified](images/02-keepass-database.png)

### Finding

The support team's password-management database was publicly accessible through the application's `/ftp` directory.

The database itself was encrypted and required a master password, so it was not necessary to bypass the database encryption to continue investigating the challenge.

---

## Attack Surface

The investigation identified the following attack surface:

```text
/ftp/
└── incident-support.kdbx
```

The important issue was that a sensitive password-management database was exposed through a publicly accessible application endpoint.

---

## Validation

The application source was searched for support-related authentication information:

```bash
grep -RniE "support.{0,100}password|password.{0,100}support" \
/var/lib/juice-shop/build \
--exclude-dir=node_modules --exclude-dir=test 2>/dev/null | head -50
```

The search revealed the support authentication logic in:

```text
routes/login.js
```

The code contained the original support account credentials.

![Support credential discovered in source code](images/03-support-credential-source.png)

> **Note:** The password has been redacted in the screenshot to prevent publishing a working credential in the repository.

### Finding

The original support credentials were present in application source code.

This provided a direct route to the intended authentication target without using SQL Injection or another authentication bypass.

---

## Exploitation

The discovered support account was used through the normal Juice Shop login functionality.

The account used the application's configured domain:

```text
support@<application-domain>
```

The original password discovered during source-code analysis was entered through the normal login form.

No SQL Injection or authentication bypass was required.

---

## Evidence

After submitting the credentials, Juice Shop confirmed that the challenge had been solved:

![Login Support Team challenge solved](images/04-challenge-solved.png)

The application displayed:

```text
You successfully solved a challenge:
Login Support Team
```

---

## Security Impact

Exposing authentication credentials in application source code can allow unauthorized users to authenticate as privileged or sensitive accounts.

In this case, the issue exposed the original credentials of the support team account.

The publicly accessible KeePass database also represented a separate exposure of sensitive credential-management material.

Potential consequences include:

* Unauthorized access to the support account
* Exposure of support
