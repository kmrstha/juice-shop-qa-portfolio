BUG-01: [Registration] Password advice is not enforced

Environment: OWASP Juice Shop v20.3.0 (local), Chrome, Windows 11
Requirement: REQ-REG-12
Severity: Medium
Status: Draft

Steps:
1. Open the Account menu > Login > "Not yet a customer?"
2. Toggle "Show password advice" on and note the five rules listed
3. Enter a new, unregistered email
4. Enter "12345" in Password and Repeat password
5. Choose a security question and enter an answer
6. Click Register

Expected: 
Passwords that do not meet the displayed advice are rejected or the advice shown matches the rules the system actually enforce. "12345" has no lowercase letter, no uppercase letter or special character and is shorter than 8 characters.

Actual: 
Registration succeeds with "12345". "abcdefgh" (8 lowercase letters, no digit) was also accepted. "abcd" (4 characters) was rejected, so only the 5-character minimum is enforced.

Note: The password field says 5-40 characters, while the advice says at least 8. The intended rule is unclear. The defect is that the screen shows rules the system does not enforce.

Evidence:
![Advice list](../evidence/BUG-01-advice-list.png) 
![Registration succeeded with 12345](../evidence/BUG-01-registration-success.png)