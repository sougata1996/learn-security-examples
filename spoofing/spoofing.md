# Spoofing

This example demonstrates spoofind through two ways -- Stealing cookies programmatically and cross site request forgery (CSRF).

## Steps to reproduce the vulnerability

1. Install dependencies

   `$ npx install`

2. Start the **insecure.ts** server

   `npx ts-node insecure.ts`

3. Start the malicious server **mal.ts**

   `npx ts-node mal.ts`

4. Open **http://localhost:8000** in a browser, type a name and Submit.

5. Open the **Application** tab in the Browser's inspect pane. Find the **Cookies** under **Storage**. You should see a **connect.sid** cookie being set.

6. Open the HTML file **mal-steal-cookie.html** file in the same browser (different tab). Open inspect and view the console.

7. Click the link in the HTML file. Do you see the cookie being stolen in the console?

Yes, when I click the link in the mal-steal-cookie.html file, the script in the file accesses the session cookie (connect.sid) using document.cookie and logs it to the browser's console. This demonstrates how attackers can steal cookies if they are not securely marked with httpOnly: true.

8. Open the HTML file **mal-csrf.html** file in the same browser (different tab). What do you see if the user has not logged out of **insecure.ts**? What do you see if the user has logged out?

If the user has not logged out of insecure.ts: The CSRF attack succeeds, and the malicious form submits a POST request to the /sensitive endpoint. Since the user's session cookie is still active, the server processes the request as if it were initiated by the legitimate user, potentially performing unauthorized actions.
If the user has logged out of insecure.ts: The CSRF attack fails because the session is no longer valid, and the server does not process the request. The absence of a valid session cookie ensures the malicious request cannot be authenticated.

## For you to answer

1. Briefly explain the spoofing vulnerability in **insecure.ts**.

The insecure.ts implementation is vulnerable to spoofing because cookies are set with httpOnly: false, allowing attackers to steal session cookies programmatically through JavaScript. Additionally, the lack of CSRF protection enables attackers to craft malicious requests that exploit a logged-in user's session to perform unauthorized actions, such as accessing sensitive endpoints.

2. Briefly explain different ways in which vulnerability can be exploited.

Attackers can exploit vulnerabilities in insecure.ts through cookie stealing, CSRF attacks, and session fixation. By using scripts like mal-steal-cookie.html, attackers can programmatically access and exfiltrate cookies since they are not marked as httpOnly, allowing session hijacking. Similarly, CSRF attacks are possible through malicious forms like mal-csrf.html, which exploit a logged-in user's active session to send unauthorized requests to sensitive endpoints. Weak cookie handling further enables session fixation, allowing attackers to reuse or manipulate session IDs to impersonate legitimate users, compromising the application's security.

3. Briefly explain why **secure.ts** does not have the spoofing vulnerability in **insecure.ts**.

The secure.ts implementation mitigates spoofing by marking cookies as httpOnly: true to prevent theft via JavaScript and sameSite: true to block cross-origin requests, effectively preventing CSRF attacks. These secure configurations ensure that session data is inaccessible to malicious actors and spoofing vulnerabilities are eliminated.
