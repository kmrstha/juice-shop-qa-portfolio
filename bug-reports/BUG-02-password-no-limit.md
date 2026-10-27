\##BUG-02: \[Registration] Repeat Password field accepts more than 40 characters.



Environment: OWASP Juice Shop v20.3.0 (local), Chrome, Windows 11

Requirement: REQ-REG-07

Severity: Low

Status: Draft



\##Steps:

1\. Open the Account menu > Login > "Not yet a customer?"

2\. Enter a new, unregistered email

3\. In password, type 41 characters (for example 1234567890 four times, plus 1) and count what remains

4\. In repeat password, type the same 41 characters and count what remains.



\##Expected: 

Repeat password stops at 40 characters, as the password field does.



\##Actual: 

Password keeps 40 characters. Repeat password keeps all 41 and "Passwords do not match" is shown.



\##Evidence:

!\[No limit in repeat password](../evidence/BUG-02-password-no-limit.png)

