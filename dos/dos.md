# Denial-of-Service (DoS)

This example demonstrates DoS vulnerabilities and how they can be exploited.

## Steps to reproduce

1. Install all dependencies

   `$ npm install`

2. Ignore if you have already done this once. Insert test data in the MongoDB database. Make sure the mongod is up and running by typing the `mongosh` command in the termainal. If mongod process is up then you will see that the connection was successful. Command to insert test data:

   `$ npx ts-node insert-test-users.ts`

This will create a database in MongoDB called **infodisclosure**. Verify its presence by connecting with mongosh and running the command `show dbs;`.

2. Start the **insecure.ts** server

   `$ npx ts-node insecure.ts`

3. In the browser, pretend to be a hacker and type a malicious request

   ```
       http://localhost:3000/userinfo?id[$ne]=
   ```

4. Do you see the server crashing?

Yes, the server crashes in insecure.ts due to its vulnerabilities.

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts** that can lead to a DoS attack.

The insecure.ts application is vulnerable to Denial-of-Service (DoS) attacks due to multiple shortcomings in its implementation. The most significant vulnerability is the absence of input validation or sanitization, particularly for the id query parameter. This leaves the application open to NoSQL injection attacks, where an attacker can craft a malicious query that overwhelms the database by forcing it to process unintended or resource-intensive operations. Additionally, there is no mechanism in place to limit the rate of incoming requests, allowing an attacker to send a large number of requests in a short time frame and exhaust server resources such as memory, CPU, or network bandwidth. Furthermore, improper error handling exacerbates these issues, as unhandled exceptions from invalid or malicious inputs can cause the server to crash, disrupting its availability.

2. Briefly explain how a malicious attacker can exploit them.

An attacker can exploit these vulnerabilities in several ways. By leveraging the NoSQL injection vulnerability, the attacker can manipulate the id query parameter to execute a query such as { \_id: { $ne: "" } }. This query bypasses the intended logic and retrieves all user records, significantly increasing the load on the database and potentially exposing sensitive data. Similarly, malformed input or overly complex queries can trigger unhandled exceptions, causing the server to crash. An attacker can further compound the problem by sending a flood of such malicious requests to the server in a short period, resulting in a Denial-of-Service (DoS) attack. This overwhelms the server, rendering it unresponsive to legitimate users.

3. Briefly explain the defensive techniques used in **secure.ts** to prevent the DoS vulnerability?

The secure.ts implementation introduces several defensive techniques to mitigate these vulnerabilities. First, the inclusion of a rate-limiting middleware restricts the number of requests a client can send within a specific time window. For instance, only one request is allowed every five seconds, which significantly reduces the impact of request floods or brute-force attacks. Second, proper error handling has been implemented using a try-catch block around database queries. This ensures that any exceptions caused by malformed inputs are caught and logged, preventing the server from crashing. While the secure.ts example does not fully sanitize inputs, it demonstrates the importance of validating and sanitizing user-provided data to prevent NoSQL injection. These measures collectively ensure that the server is better equipped to handle malicious inputs and maintains its availability under attack scenarios.
