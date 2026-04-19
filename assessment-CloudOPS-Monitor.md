# Assessment — CloudOPS Monitor: Credential Disclosure + IDOR + Command Injection

---

## Question 1 — MCQ

**Where were the initial credentials for the CloudOPS Monitor discovered?**

- A) A database dump accessible via directory enumeration
- B) An API response leaking user data
- C) **HTML comments in the application's login page source** ✅
- D) A log file exposed in the web root

> **Answer:** C — Developer comments containing `dev.user` / `DevUser@123` are present in the login page HTML source.

---

## Question 2 — MCQ

**What dual encoding scheme does the application use to generate server reference tokens ("CloudWrap")?**

- A) AES + Base64
- B) XOR + Hex
- C) SHA256 + URL encoding
- D) **ROT13 + Base64** ✅

> **Answer:** D — The hint "CloudWrap™ (B64 + R13)" indicates ROT13 followed by Base64. To forge a reference: apply ROT13, then Base64 encode.

---

## Question 3 — MCQ

**Which re-encoded reference grants access to the hidden admin server SRV-999?**

- A) `RkVJLTEwMQ==`
- B) `RkVJLTEwMg==`
- C) **`RkVJLTk5OQ==`** ✅
- D) `U1JWLTEwMQ==`

> **Answer:** C — `SRV-999` → ROT13 → `FEI-999` → Base64 → `RkVJLTk5OQ==`. This is used in `/tools.php?ref=RkVJLTk5OQ==`.

---

## Question 4 — Fill in the Blank

**What Base64+ROT13 encoded reference value must be supplied in the `ref` parameter to access the hidden admin server SRV-999?**

**Answer:** `RkVJLTk5OQ==`

> `SRV-999` encoded via CloudWrap (ROT13 first → `FEI-999`, then Base64 → `RkVJLTk5OQ==`). Supplying this in `/tools.php?ref=RkVJLTk5OQ==` bypasses ownership checks (IDOR) and loads the hidden admin diagnostic panel.

---

## Question 5 — Fill in the Blank

**What single operator character, used instead of `&&`, bypasses the command injection filter in the diagnostic tool?**

**Answer:** `&`

> The application blacklists `&&` but not the single `&` operator. The payload `127.0.0.1 & whoami` executes `whoami` in the background after the ping command, successfully injecting an OS command through the `shell_exec()` call.

---

*Lab target:* `http://localhost:8091`
