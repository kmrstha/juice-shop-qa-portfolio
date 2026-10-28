BUG-03: [Registration] Password counter shows limit 20 instead of 40

Environment: OWASP Juice Shop v20.3.0 (local), Chrome, Windows 11
Requirement: REQ-REG-18
Severity: Low
Status: Draft



Steps:

1. Open the Account menu > Login > "Not yet a customer?"
2. Click the password field and type 10 characters
3. Continue typing up to 40 characters
4. Read the counter at 10 and at 40 characters
5. Repeat steps 2 to 4 in the Repeat password field

Expected: 
Both fields show the real limit: "10/40" at 10 characters and "40/40" at 40.

Actual: 
Password shows "10/20" and then "40/20". Repeat password shows "10/40" and then "40/40".

Evidence:
![Wrong limit in counter](../evidence/BUG-03-counter-shows-wrong-limit.png)