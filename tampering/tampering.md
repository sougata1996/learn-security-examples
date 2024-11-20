# Tampering

This example demonstrates tampering through script injection.

## Steps to reproduce

1. Install all dependencies

   `npm install`

2. Start the **insecure.ts** server

   `npx ts-node insecure.ts`

3. In the browser, type a potentially malicious script in the name field of the form

   ```
       <script> document.body.innerHTML = "<a href='https://google.com'> Gotcha </a>"</script>
   ```

4. Do you see the potentially malicious hyperlink being injected into the form?

Yes, when I submit the malicious script.

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**

The insecure.ts implementation is vulnerable to script injection attacks, particularly Cross-Site Scripting (XSS). When a user submits data through the form, the application directly stores and reflects the input without sanitizing it. This allows attackers to inject malicious scripts into the application. Since the server does not validate or escape special HTML characters in the input, these scripts are executed when the data is rendered in the browser, leading to tampering and unauthorized actions within the application.

2. Briefly explain how a malicious attacker can exploit them.

An attacker can exploit this vulnerability by submitting malicious scripts, such as:

<script> document.body.innerHTML = "<a href='https://google.com'>Gotcha</a>"</script>

When this input is processed, the server reflects the unsanitized content back to the client. The injected script is executed in the user's browser, allowing the attacker to manipulate the page content, steal session cookies, or redirect users to malicious websites. This compromises the application's integrity and potentially exposes sensitive user data.

3. Briefly explain why **secure.ts** does not have the same vulnerabilties?

The secure.ts implementation mitigates script injection vulnerabilities by sanitizing user inputs before processing them. It includes a utility function, escapeHTML, which escapes special HTML characters (e.g., <, >, ", ') in the submitted data. By converting potentially dangerous characters into their HTML entity equivalents, any injected scripts are rendered as plain text instead of executable code. This ensures that user inputs cannot be used to execute malicious scripts in the browser, effectively preventing XSS and tampering attacks.
