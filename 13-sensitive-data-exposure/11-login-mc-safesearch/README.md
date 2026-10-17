# OWASP Juice Shop Walkthrough: "Log in with MC SafeSearch's original user credentials"

This documentation provides an engineering breakdown and step-by-step resolution for the account takeover of MC SafeSearch using Open Source Intelligence (OSINT) data harvesting methods.

---

##  Challenge Context & Objective
The objective is to compromise the account belonging to the user `mc.safesearch@juice-sh.op` without exploiting software vulnerabilities. The analyst must examine an external media asset associated with the user, decipher the password generation rules embedded in the media text, and construct the correct plaintext credential string to log into the application portal.

---

##  Step-by-Step Procedure

### Step 1: Target Identification and Intelligence Gathering
1. Isolate the target account profile email address parameter from public interface data:
   ```text
   mc.safesearch@juice-sh.op
   ```
2. Conduct open-source research on the user entity "MC SafeSearch" to uncover a promotional track titled "Protect Ya' Passwordz".

### Step 2: Analyzing the Password Complexity Logic
1. Analyze the song lyrics to uncover operational disclosures regarding the user's password structure.
2. The text reveals two primary structural constraints:
   * The baseline password string is the name of his dog: `Mr. Noodles`
   * The variation rule requires mutating all vowel characters into numerical zeros.

### Step 3: Password Construction Matrix
1. Initialize the target string array: `Mr. Noodles`
2. Replace all instances of vowels (`o`, `o`, `e`) with the integer `0`:
   ```text
   Mr. N00dles
   ```

### Step 4: Authentication Execution
1. Navigate to the login view at `http://localhost:3000/#/login`.
2. Input the target credential combinations into the application prompt:
   * **Email:** `mc.safesearch@juice-sh.op`
   * **Password:** `Mr. N00dles`
3. Click login to execute the transaction, authenticate the session, and solve the flag condition.

---

##  Core Security Concepts Covered

### 1. Weaknesses in Human-Generated Password Complexity
Users frequently create passwords by taking memorable, static core words and applying predictable modification transformations (such as substituting "o" with "0" or "e" with "3"). Because these transformation rules follow highly predictable patterns, modern brute-force dictionaries and wordlist mutation rules easily calculate and compromise these strings.

### 2. Information Leakage via Creative Content (OSINT Vulnerability)
Operational Security (OPSEC) failures occur when organizational employees or public figures disclose anecdotes regarding their security setups in casual conversations, social media entries, or creative content. Attackers systematically monitor public media assets to isolate these clues and build highly targeted custom dictionaries against high-value infrastructure interfaces.
