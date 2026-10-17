# OWASP Juice Shop Walkthrough: "DOM XSS Rich Media Injection"

This documentation provides an engineering breakdown and step-by-step resolution for leveraging un-sanitized search parameters to inject complex, interactive media container structures.

---

##  Challenge Context & Objective
The objective is to expand upon verified DOM Cross-Site Scripting configurations by replacing baseline notification alert frames with an advanced interactive asset payload. The analyst injects an automatic-play media streaming iframe into the application's reflected search parameter block to force external code execution.

---

##  Step-by-Step Procedure

### Step 1: Identifying the Vulnerable Input Vector
1. Open the primary OWASP Juice Shop application storefront window.
2. Focus your terminal cursor on the **Search...** product bar component found in the top navigation row layout.

### Step 2: Injecting the Rich Media Objective Payload
1. Ensure your audio rendering output hardware layers are unmuted.
2. Input the complete external audio stream vector string directly inside the search input box parameter:
   ```html
   <iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://soundcloud.com"></iframe>
   ```
3. Press **Enter** to pass the string payload into the DOM runtime parser layers.

### Step 3: Resolving Browser Security Controls
1. If the browser blocks the external site frame transmission, access a secondary browser tab route targeting `about:config`.
2. Locate the constraint policy assignment string:
   ```text
   security.fileuri.strict_origin_policy
   ```
3. Modify the attribute assignment state cleanly from **true** to **false**.
4. Return to your active interface tab and execute a full reload sequence (`F5`) to render the running stream module and clear the flag.

---

##  Core Security Concepts Covered

### 1. Document Object Model Cross-Site Scripting (DOM XSS)
DOM-based script injection occurs entirely within client-side browser operations. Unlike reflected or stored variants where payloads traverse backend server layers, DOM XSS executes when local client scripts handle data from an untrusted source insecurely, feeding it straight into execution sinks without defensive parsing.

### 2. Standard Clickjacking and Framing Defenses (X-Frame-Options)
Modern data platforms protect visual properties from cross-domain abuse using the `X-Frame-Options: SAMEORIGIN` header or specific Content Security Policy (CSP) `frame-ancestors` restrictions. This configuration blocks external, unauthorized frames from nesting their layouts, preventing malicious sites from wrapping legitimate endpoints inside hidden layers to hijack user actions.
