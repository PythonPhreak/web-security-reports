🛡️ Vulnerability Report
Exploiting server-side parameter pollution in a query string
Port Swigger lab
Server-side parameter pollution leading to administrator account takeover via password reset endpoint

Summary
The application is vulnerable to server-side parameter pollution (SSPP) in the password reset functionality. 
User-controlled input is improperly concatenated into a backend API request, allowing injection of additional parameters and manipulation of internal fields such as email and reset_token. This ultimately allows an attacker to obtain a valid password reset token for the administrator account and take over it.

Attack Surface
The vulnerability exists in the password reset functionality, specifically in the /forgot-password endpoint.
Key attack surface characteristics:
User input (username) is embedded into a server-side API request
The backend constructs query strings without proper sanitization
Special characters (&, #) are not properly handled
Internal API parameters (e.g. field, reset_token) are exposed through parsing ambiguity
Application behavior changes based on unexpected query structure manipulation

This allows attackers to interfere with backend request construction and extract sensitive internal values.

Steps to Reproduce
Navigate to the forgot password functionality
Intercept the request using Burp Suite
Identify that the username parameter is used to construct a backend request
Modify the parameter to inject additional backend fields (SSPP technique)
Observe application responses indicating valid internal field names such as:
email
reset_token
Set the field parameter to reset_token
Receive a valid password reset token for the administrator user
Use the token to reset the administrator password
Log in as administrator and delete user carlos
Proof of Concept
Injected Request Example
username=administrator%26field=reset_token%23
Response
Password reset token: 123456789
Impact

An attacker can:
Extract valid password reset tokens
Take over any user account, including administrator accounts
Gain full administrative access to the application
Perform destructive actions such as deleting users

This results in full account takeover and complete compromise of the application’s administrative functionality.

Root Cause
The vulnerability is caused by improper handling of user input in server-side request construction:

Lack of sanitization of special characters (&, #)
Unsafe concatenation of user input into backend API queries
Exposure of internal parameters due to ambiguous parsing logic
No strict validation of allowed backend fields
Notes / Learning Outcome

During exploitation, the email field was identified early through experimentation. The correct exploitation path required further refinement of the field parameter, demonstrating the importance of understanding backend request construction and parameter parsing behavior.
