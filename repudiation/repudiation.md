# Repudiation

The example demonstrates a vulnerability that can lead to repudiation by malicious users attempting to access the services provided by a server.

## Steps to reproduce

1. Install all dependencies

   `$ npm install`

2. Run the server **insecure.ts**.

3. Pretend to be a malicous user and interact with the services by sending requests from the browser.

4. Do you think your actions can be repudiated?

Yes, my actions can be repudiated because insecure.ts lacks logging and authentication.
As a result:

1. There is no record tying your requests to your identity or IP address.
2. Anyone could deny sending malicious content or accessing the messages since there is no mechanism to track or verify user activity.

3. Briefly explain the vulnerability.

The vulnerability in insecure.ts lies in its lack of proper logging and authentication mechanisms, making it susceptible to repudiation attacks. Repudiation occurs when a user denies performing an action, and the system lacks the means to prove otherwise. Since insecure.ts does not authenticate users or log their actions, malicious users can interact with the server, manipulate services (e.g., sending or retrieving messages), and later deny their actions. Without authentication and comprehensive logs, the server cannot trace actions back to the responsible user, making it impossible to establish accountability.

2. Briefly explain why the vulnerability is addressed in **secure.ts**.

In secure.ts, the vulnerability is addressed through proper logging and an authentication mechanism. The following measures ensure that actions can be attributed to specific users:

1. Request Logging: Each request is logged with critical details, such as the timestamp, method, endpoint, and IP address, into a persistent log file (server.log). This creates an audit trail that can be used to trace back user actions.
2. Authentication: Actions like retrieving messages are restricted to authenticated users. This ensures that only authorized users can access certain functionalities, preventing anonymous misuse of the service.
3. Error Handling: The application logs errors and includes them in the audit trail. This provides further accountability by documenting any unexpected behaviors or attempts to exploit vulnerabilities.

These features collectively mitigate repudiation by establishing a reliable mechanism to record and verify user actions.

3. Which design pattern is used in the secure version to address the vulnerability? Briefly explain how it works?

The secure.ts implementation addresses the repudiation vulnerability using the Interceptor Design Pattern, which employs middleware as an intermediary between the client and the server to intercept and process requests or responses. The middleware monitors every incoming request before it reaches the route handlers, logging critical details such as the timestamp, HTTP method, endpoint, and IP address, thereby creating a traceable record of all user actions. In addition to logging, the middleware enforces authentication by verifying user credentials, such as tokens or session data, ensuring that only authorized users can access protected resources. If an action violates expected behavior, such as unauthorized access attempts or malformed requests, the middleware handles the response or logs the issue for further investigation. By providing continuous monitoring, logging, and validation of user actions, this pattern effectively mitigates the risk of repudiation and ensures accountability for all server interactions.
