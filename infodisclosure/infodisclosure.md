# Tampering

This example demonstrates information disclosure by injecting malicious query objects to a NoSQL database.

## Steps to reproduce

1. Install all dependencies

   `$ npm install`

2. Insert test data in the MongoDB database. Make sure the mongod is up and running by typing the `mongosh` command in the termainal. If mongod process is up then you will see that the connection was successful. Command to insert test data:

   `$ npx ts-node insert-test-users.ts`

This will create a database in MongoDB called **infodisclosure**. Verify its presence by connecting with mongosh and running the command `show dbs;`.

2. Start the **insecure.ts** server

   `$ npx ts-node insecure.ts`

3. In the browser, pretend to be a hacker and type a malicious request

   ```
       http://localhost:3000/userinfo?username[$ne]=
   ```

4. Do you see user information being displayed despite the malicious request not having a valid username in the request?

Yes.

Answer the following:

1. Briefly explain the potential vulnerabilities in **insecure.ts**

The insecure.ts application is vulnerable to NoSQL injection due to the lack of input validation and sanitization. Specifically, it directly uses the user-provided username parameter in the MongoDB query without verifying its format or sanitizing it. This allows attackers to inject malicious query objects, such as username[$ne]=, which alters the intended query behavior and retrieves unintended data from the database. Furthermore, there is no mechanism to restrict what type of data can be passed, enabling attackers to craft complex queries that exploit MongoDB’s flexible query syntax. This opens the door to sensitive information disclosure, as the application exposes user data based on the injected query.

2. Briefly explain how a malicious attacker can exploit them.

A malicious attacker can exploit the lack of input validation by injecting a query such as:
http://localhost:3000/userinfo?username[$ne]=
This query uses MongoDB’s $ne (not equal) operator to retrieve records where the username field does not match an empty string. As a result, the database query unintentionally matches all users, and sensitive user data is exposed in the response. The attacker does not need to know any valid username to exploit this. By modifying the request further, the attacker can craft more complex queries to exfiltrate additional data or even cause the application to crash by overloading the database.

3. Briefly explain the defensive techniques used in **secure.ts** to prevent the information disclosure vulnerability?

The secure.ts implementation employs several defensive techniques to mitigate vulnerabilities, particularly NoSQL injection and information disclosure. Input validation ensures that the username parameter is of type string. If the parameter is missing or invalid, the server promptly returns a 400 Bad Request error, processing only properly formatted input. Additionally, input sanitization is performed by removing non-alphanumeric characters from the username parameter using a regular expression (replace(/[^\w\s]/gi, '')), preventing the use of special characters that could facilitate injection attacks. Furthermore, robust error handling is implemented by wrapping database queries in a try-catch block. This prevents unhandled exceptions from crashing the application and ensures that errors are logged appropriately, while the server responds with a generic 500 Internal Server Error message to avoid revealing sensitive details. By combining input validation, sanitization, and error handling, secure.ts processes only safe queries, effectively preventing NoSQL injection and information disclosure vulnerabilities.
