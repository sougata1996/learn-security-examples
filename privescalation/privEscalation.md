# Privilege Escalation

The example demonstrates a privilege escalation vulnerability and how to exploit it.

## Steps to reproduce

1. Install all dependencies

   `$ npm install`

2. Start the **insecure.ts** server

   `$ npx ts-node insecure.ts`

3. In the browser, send a GET request

   ```
       http://localhost:3000/send-form
   ```

4. Try different UserIds and see which one gives you authorized access to change the role of that user.

UserId: 1
Role: admin

This gives access {"message":"User role updated successfully"}.

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**

The insecure.ts application is vulnerable to privilege escalation due to weak authentication and authorization mechanisms. The application relies solely on user-provided data (userId) to authenticate and authorize users. Since the userId is sent as part of the request body, it can be easily tampered with, allowing a malicious user to impersonate an admin by providing an admin userId. Furthermore, there is no session management or secure method to identify the currently logged-in user. The role verification is also insufficiently secure because it depends on this tampered userId. This flawed design allows unauthorized users to escalate their privileges by masquerading as an admin.

2. Briefly explain how a malicious attacker can exploit them.

A malicious attacker can exploit these vulnerabilities by sending a crafted request with a userId set to that of an admin (e.g., 1). For example, they could send a POST request to /update-role with the body:
{
"userId": 1,
"newRole": "admin"
}
Since the server does not verify the authenticity of the user making the request, it will treat the attacker as an admin and allow them to update roles. This enables the attacker to gain unauthorized access, escalate their privileges, or modify the roles of other users, potentially compromising the entire system.

3. Briefly explain the defensive techniques used in **secure.ts** to prevent the privilege escalation vulnerability?

The secure.ts implementation effectively mitigates privilege escalation vulnerabilities through robust session management and authentication mechanisms. It utilizes express-session to track logged-in users, securely tying each session to a userId stored on the server, which prevents tampering by clients. Role-based authorization is enforced using session data, ensuring that only authenticated users with the admin role can perform sensitive actions such as updating roles. Additionally, sessions are safeguarded with cookies configured with security features like httpOnly and sameSite: 'strict', protecting against session hijacking and cross-site request forgery (CSRF). By validating user roles based on secure session data rather than client-provided input, and employing these authentication and authorization strategies, secure.ts ensures that only authorized users can perform privileged actions, effectively preventing privilege escalation attacks.
