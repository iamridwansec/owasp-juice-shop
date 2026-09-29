# OWASP Juice Shop — Web Security Lab

> A hands-on web application security research and documentation project focused on identifying, understanding, and documenting vulnerabilities in the OWASP Juice Shop.

![OWASP Juice Shop](https://raw.githubusercontent.com/juice-shop/juice-shop/master/frontend/src/assets/public/images/JuiceShop_Logo.png)

##  About This Project

This repository contains our hands-on security testing and documentation work against **OWASP Juice Shop**, an intentionally vulnerable web application designed for learning and practicing modern web application security.

The goal of this project is not simply to complete challenges.

We are using the Juice Shop as a practical environment to understand **how vulnerabilities work, how they can be discovered, how they can be exploited in an authorized lab environment, and how they can be mitigated.**

The project covers a broad range of web security concepts, including:

* Broken Access Control
* Authentication vulnerabilities
* Injection
* Cross-Site Scripting (XSS)
* XML External Entity (XXE)
* Cryptographic Issues
* Improper Input Validation
* Insecure Deserialization
* Sensitive Data Exposure
* Security Misconfiguration
* Vulnerable Components
* Unvalidated Redirects
* And more

---

##  Project Objectives

Through this project, we aim to:

* Develop practical web application security skills.
* Understand common vulnerability classes through hands-on testing.
* Learn how vulnerabilities appear in real application workflows.
* Practice reconnaissance and attack-surface analysis.
* Understand HTTP requests and responses during exploitation.
* Document findings clearly and reproducibly.
* Connect practical findings with security concepts such as **OWASP Top 10** and secure development practices.
* Build a structured security portfolio demonstrating practical learning.

---

##  Methodology

Our approach follows a practical security-testing workflow:

```text
Understand
    ↓
Reconnaissance
    ↓
Identify Attack Surface
    ↓
Analyze Application Behavior
    ↓
Test Input / Functionality
    ↓
Validate Vulnerability
    ↓
Document Evidence
    ↓
Understand Mitigation
```

Where appropriate, we examine:

* Frontend behavior
* Backend APIs
* HTTP requests and responses
* Authentication and authorization
* Input validation
* Client-side JavaScript
* Server-side behavior
* Application logic
* Security controls and their bypasses

All testing is performed against an intentionally vulnerable application in an authorized learning environment.

---

##  Repository Structure

The repository is organized into **17 vulnerability categories**.

Each category contains individual challenge directories, and each challenge has its own `README.md` for documenting the investigation.

```text
.
├── 01-broken-access-control/
├── 02-broken-anti-automation/
├── 03-broken-authentication/
├── 04-cryptographic-issues/
├── 05-improper-input-validation/
├── 06-injection/
├── 07-insecure-deserialization/
├── 08-miscellaneous/
├── 09-observability-failures/
├── 10-score-board/
├── 11-security-misconfiguration/
├── 12-security-through-obscurity/
├── 13-sensitive-data-exposure/
├── 14-unvalidated-redirects/
├── 15-vulnerable-components/
├── 16-xss/
└── 17-xxe/
```

### Challenge Documentation

Individual challenges follow a consistent structure:

```text
01-broken-access-control/
└── 01-ai-debugging/
    └── README.md
```

As the project develops, challenge documentation may include:

* Challenge objective
* Vulnerability explanation
* Reconnaissance
* Attack surface
* Requests and responses
* Exploitation process
* Payloads
* Screenshots/evidence
* Why the vulnerability works
* Security impact
* Mitigation
* Lessons learned

---

##  Vulnerability Categories

| #  | Category                    |
| -- | --------------------------- |
| 01 | Broken Access Control       |
| 02 | Broken Anti-Automation      |
| 03 | Broken Authentication       |
| 04 | Cryptographic Issues        |
| 05 | Improper Input Validation   |
| 06 | Injection                   |
| 07 | Insecure Deserialization    |
| 08 | Miscellaneous               |
| 09 | Observability Failures      |
| 10 | Score Board                 |
| 11 | Security Misconfiguration   |
| 12 | Security Through Obscurity  |
| 13 | Sensitive Data Exposure     |
| 14 | Unvalidated Redirects       |
| 15 | Vulnerable Components       |
| 16 | Cross-Site Scripting (XSS)  |
| 17 | XML External Entities (XXE) |

The repository currently contains the folder structure for **117 individual Juice Shop challenges**.

---

##  Tools & Technologies

The project may make use of tools commonly used during web application security testing, including:

* **Burp Suite** — HTTP interception and request manipulation
* **Browser Developer Tools** — client-side and network analysis
* **cURL** — direct interaction with APIs and HTTP endpoints
* **OWASP Juice Shop** — intentionally vulnerable target application
* **Kali Linux** — security testing environment
* **CyberChef** — encoding, decoding and data transformation
* **Command-line utilities** — analysis and testing
* **JavaScript / HTTP / REST APIs** — understanding application behavior

The exact tools used may vary depending on the challenge.

---

##  Learning Areas

This project helps us connect practical exploitation with broader web security concepts such as:

### Web Application Security

* HTTP methods
* Cookies and sessions
* Authentication
* Authorization
* REST APIs
* Client-side JavaScript
* Server-side processing

### OWASP Concepts

* Injection
* Broken Access Control
* Identification and Authentication Failures
* Security Misconfiguration
* Cryptographic Failures
* Cross-Site Scripting
* XXE
* Vulnerable and Outdated Components

### Offensive Security

* Reconnaissance
* Attack-surface discovery
* Input manipulation
* Authentication testing
* Authorization testing
* Payload development
* Security-control bypasses
* Vulnerability validation

### Defensive Security

Understanding how each vulnerability can be prevented is an important part of the project.

We therefore aim to document not only **how an attack works**, but also **why the application was vulnerable and what developers can do to prevent similar issues.**

---

##  Collaborators

This is a collaborative learning project.

### Ridwan Popoola

**GitHub:** [@iamridwansec](https://github.com/iamridwansec)

Focused on cybersecurity learning, web application security, penetration testing, reconnaissance, and practical security research.

### Victor Oriabure

**GitHub:** [@piccolo-creator](https://github.com/piccolo-creator)

Collaborator contributing to the development, testing, and documentation of the project.

### Wisdom Duduyemi

**GitHub:** [@Wisdom2008-star](https://github.com/Wisdom2008-star)

Collaborator contributing to the development, testing, and documentation of the project.

---

##  How We Work

We are approaching this project as a collaborative security laboratory.

Rather than simply copying solutions, our goal is to understand the reasoning behind each vulnerability.

For each challenge, we aim to answer:

> **What is vulnerable?**

> **Why is it vulnerable?**

> **How can the vulnerability be demonstrated?**

> **What is the security impact?**

> **How could it be prevented?**

This makes the repository useful not only as a record of completed challenges, but also as a reference for future learning.

---

##  Disclaimer

OWASP Juice Shop is intentionally vulnerable and exists for security education and training.

The techniques documented in this repository are intended for use **only against systems that you own or have explicit permission to test**.

Do not apply these techniques against real-world applications, accounts, infrastructure, or systems without authorization.

---

##  Project Status

**Repository structure:**  Complete

**17 vulnerability categories:**  Organized

**117 challenge directories:**  Organized

**Challenge documentation:**  In progress

The next phase of the project is to investigate and document the individual challenges.

---

##  Project Philosophy

> **Don't just memorize the payload. Understand the vulnerability.**

The purpose of this project is to move beyond simply knowing *what command works*.

We want to understand the application behavior that makes the attack possible, the security control that failed, and the defensive principle that could prevent it.

That understanding is what turns a solved challenge into a transferable security skill.

---

##  Acknowledgements

This project uses **OWASP Juice Shop**, an intentionally insecure web application created for security training, awareness demonstrations, and CTF-style learning.

Special thanks to the OWASP Juice Shop project and its contributors for providing an excellent environment for practicing modern web application security.

---

**Built for learning. Built through practice. Built together. 🔐**
