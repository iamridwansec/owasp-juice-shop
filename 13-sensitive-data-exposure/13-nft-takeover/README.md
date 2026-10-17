# OWASP Juice Shop Walkthrough: "Take Over the Wallet Containing Our Official Soul Bound Token"

This documentation provides an engineering breakdown and step-by-step resolution for the OWASP Juice Shop challenge involving the deterministic derivation of an Ethereum-compatible wallet's private key from an exposed mnemonic phrase.

---

## Challenge Context & Objective
The goal is to navigate to a hidden endpoint (`/juicy-nft`) within the OWASP Juice Shop environment, authenticate into a specific cryptographic wallet containing a Soul Bound Token (SBT), and trigger the successful flag condition. 

---

##  Step-by-Step Procedure

### Step 1: Finding the Mnemonic Exposure
1. Open the OWASP Juice Shop application and browse to the **About Us** page layout.
2. In the user review/feedback section, an unintended feedback submission highlights an individual's personal digital wallet asset configuration.
3. The raw 12-word standard BIP39 mnemonic sequence leaked within the text reads:
   ```text
   purpose betray marriage blame crunch monitor spin slide donate sport lift clutch
   ```

### Step 2: Key Derivation Infrastructure
To interface directly with raw database submission nodes, a direct cryptographic private key string is mandatory. A Python script can be leveraged natively on Kali Linux to handle this offline and avoid third-party exposure.

#### Python Cryptographic Implementation (`derive.py`)
```python
from eth_account import Account

# Enable deterministic HD Wallet derivation capabilities
Account.enable_unaudited_hdwallet_features()

# Captured phrase parameters
mnemonic = "purpose betray marriage blame crunch monitor spin slide donate sport lift clutch"

# Standard EVM Derivation Path definition (Account index 0)
derivation_path = "m/44'/60'/0'/0/0"
account = Account.from_mnemonic(mnemonic, account_path=derivation_path)

print(f"Address:     {account.address}")
print(f"Private Key: {account.key.hex()}")
```

#### Terminal Execution & Arithmetic Proof
When executing the codebase over local dependencies, the underlying algorithm handles standard PBKDF2 hashing functions linked over an HMAC-SHA512 seed vector to compute deterministic hexadecimal blocks:

```bash
$ python3 derive.py
Address:     0x8343d2eb2B13A2495De435a1b15e85b98115Ce05
Private Key: 5bcc3e9d38baa06e7bfaab80ae5957bbe8ef059e640311d7d6d465e6bc948e3e
```

### Step 3: Payload Construction & Submission
1. Copy the raw hexadecimal string representing the private key.
2. Format the string for EVM standards by adding the `0x` hexadecimal type indicator prefix:
   ```text
   0x5bcc3e9d38baa06e7bfaab80ae5957bbe8ef059e640311d7d6d465e6bc948e3e
   ```
3. Change your web application URL route context manually to `/#/juicy-nft`.
4. Insert the full formatted string into the input field and hit submit to complete the token verification protocol.

---

##  Core Security Concepts Covered

### 1. Hexadecimal Formatting Prefixes (`0x`)
Computer interpreters demand distinctive labels to distinguish numeral domains. The `0x` prefix specifically commands systems to treat the trailing payload as base-16 (hexadecimal) characters, avoiding system parsing failures or numerical classification bugs.

### 2. Information Leaks and Scraping Bot Behaviors
Production security pipelines use continuous code linting and signature identification (via patterns like GitGuardian) because active indexing bots browse the internet looking for raw `0x` chains or specific mnemonic patterns to sweep and exploit assets.

### 3. Deterministic Derivation Path Standards (BIP32/BIP39)
Using structural constraints like `m/44'/60'/0'/0/0` enables multiple blockchain ecosystem networks to compute identical key outputs from unified phrases across different user interfaces. This structure maintains complete user system recovery if specific front-end vendor portals stop functioning.
