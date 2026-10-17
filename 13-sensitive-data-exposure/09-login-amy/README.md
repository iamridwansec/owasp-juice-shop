# OWASP Juice Shop Walkthrough: "Log in with Amy's original user credentials"

This documentation provides an engineering breakdown and step-by-step resolution for executing a tailored credential-guessing attack based on predictable string padding logic and personal profile mapping.

---

##  Challenge Context & Objective
The objective is to achieve an unauthenticated takeover of the account assigned to `amy@juice-sh.op`. By researching a precise math metric string linked to password evaluation tools, the analyst discovers a flawed formatting template used by the victim and reconstructs the plaintext entry string.

---

##  Step-by-Step Procedure

### Step 1: Open-Source Intelligence Gathering
1. Isolate the target account email profile metadata via internal asset leaks:
   ```text
   amy@juice-sh.op
   ```
2. Search external documentation indexes for the exact phrase: `93.83 billion trillion trillion centuries`. This exposes a specific password baseline calculator hosted at `https://grc.com`.

### Step 2: Isolating the Padding Methodology
1. Inspect the example structure showcased within the calculation metrics layout.
2. The pattern details a short baseline root followed by trailing periods to pad string limits: `D0g.....................`.
3. Identify the personal root handle linked to the user's spouse: `Kif`, translated into basic automated filter leetspeak as `K1f`.

### Step 3: Compiling the Plaintext Password Array
1. Take the baseline character root: `K1f`.
2. Append the identical period sequence array length extracted from the platform example documentation:
   ```text
   K1f.....................
   ```

### Step 4: Authentication Execution
1. Access the web store authentication terminal interface at `http://localhost:3000/#/login`.
2. Submit the compiled credentials into the login window prompt:
   * **Email:** `amy@juice-sh.op`
   * **Password:** `K1f.....................`
3. Process the submission to authorize the session cookie state and trigger the flag.

---

##  Core Security Concepts Covered

### 1. The Fallacy of Password Padding Systems
Many users apply artificial padding to satisfy high character-length criteria. While this increases the theoretical mathematical combinations against brute-force tools, the layout remains highly vulnerable to focused target wordlist configurations because the padding pattern itself has zero entropy.

### 2. Targeted Profiling
When users create authentication keys based on family data, their credential ecosystem becomes brittle against targeted dictionary generation. Threat actors exploit these baseline rules to bypass long character controls cleanly.

