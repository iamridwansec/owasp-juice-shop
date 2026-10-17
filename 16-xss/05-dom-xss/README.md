# OWASP Juice Shop Walkthrough: "Perform a DOM XSS Attack"

This documentation provides an engineering breakdown and step-by-step resolution for exploiting un-sanitized search bar variables to execute DOM-based Cross-Site Scripting scripts.

---

##  Challenge Context & Objective
The objective is to compromise the web user interface page structure by locating a parameter that reflects input variables insecurely. The analyst leverages an unsafe client-side sink mapping rule to pass an inline HTML iframe string that executes arbitrary JavaScript alert commands inside the browser runtime window.

---

##  Step-by-Step Procedure

### Step 1: Locating the Vulnerable Parameter Sink
1. Open the primary interface page of the OWASP Juice Shop platform.
2. Target the main product text input box parameter component labeled **Search...** inside the header bar menu row layout.

### Step 2: Inputting the Validation Exploit Payload
1. Click into the active product lookup field element.
2. Type or paste the designated test script string payload directly into the search variable prompt area:
   ```html
   <iframe src="javascript:alert('xss')">
   ```

### Step 3: Triggering Client-Side Code Execution
1. Press **Enter** to commit the string statement to the front-end layout variables.
2. The browser automatically parses the raw string parameters inside its execution layout context, rendering a live JavaScript confirmation dialogue block reading **"xss"** to clear the challenge flag.

---

##  Core Security Concepts Covered

### 1. Client-Side Document Object Model Sinks
DOM-based Cross-Site Scripting occurs when an application modifies the client-side document layout inside the victim's browser context by processing data from an untrusted source directly into unsafe programmatic targets (called "sinks") without sanitization or defensive translation encoding.

### 2. Differentiating DOM XSS from Server-Side Vectors
Unlike standard Stored or Reflected XSS exploits where the attack strings travel across data routes directly to the backend database structures, DOM XSS payload handling can happen purely on the client side. The target server might never see or log the specific script parameters, meaning traditional server-side input validation controls cannot mitigate or log this specific category of system vulnerabilities.
