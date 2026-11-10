# Broken Access Control – OWASP WebGoat Lab

## 1. Overview

**Lab Platform:** OWASP WebGoat  
**Vulnerability Category:** Broken Access Control  
**Domain:** Web Application Security  
**Difficulty:** Based on the completed WebGoat exercises  
**Status:** Completed  
**Tools:** OWASP WebGoat, Burp Suite, Web Browser

### Introduction

Broken Access Control is a web application security vulnerability that occurs when an application fails to properly enforce restrictions on what authenticated users are allowed to access or perform.

Access control determines whether a user has permission to access specific resources, view sensitive information, or execute particular actions.

When access control mechanisms are incorrectly implemented, attackers may gain unauthorized access to other users' information, restricted functionality, or administrative resources.

Broken Access Control is listed as **A01:2021 – Broken Access Control** in the OWASP Top 10 (2021).

## 2. Objectives

The main objectives of this lab were:

- Understand the concept of Broken Access Control.
- Learn how access control mechanisms work in web applications.
- Identify weaknesses in authorization mechanisms.
- Understand horizontal and vertical privilege escalation.
- Learn about Insecure Direct Object References (IDOR).
- Analyze HTTP requests and responses.
- Understand how attackers may bypass authorization restrictions.
- Learn security best practices to prevent Broken Access Control.

## 3. Understanding Access Control

Access control is a security mechanism that determines which users can access specific resources and perform particular operations within an application.

It generally involves three important components:

**1. Authentication**

Authentication verifies the identity of a user.

Example: Logging into an application using a username and password.

**2. Authorization**

Authorization determines what an authenticated user is allowed to access or perform.

Example: A normal user can view their profile but cannot access the administrator dashboard.

**3. Access Control**

Access control enforces the permissions and restrictions defined by the application.

Example: Preventing unauthorized users from accessing sensitive records or restricted functionalities.

### Authentication vs Authorization

| Authentication | Authorization |
|---|---|
| Verifies user identity | Verifies user permissions |
| Answers: Who are you? | Answers: What can you do? |
| Usually happens during login | Happens when accessing resources |
| Example: Username and password | Example: Admin-only dashboard |

## 4. Types of Broken Access Control

### 4.1 Horizontal Privilege Escalation

Horizontal privilege escalation occurs when a user gains unauthorized access to another user's resources at the same privilege level.

**Example:**

Suppose a web application contains these user accounts:

- User A – Account ID: 101
- User B – Account ID: 102

User A should only be able to access their own account information.

However, if User A changes an account identifier in a request and the application returns User B's information without checking ownership, horizontal privilege escalation may occur.

**Security Impact:**
- Unauthorized access to other users' information.
- Exposure of personal data.
- Unauthorized modification of user records.

### 4.2 Vertical Privilege Escalation

Vertical privilege escalation occurs when a user gains access to functionality or resources belonging to a higher privilege level.

**Example:**

A normal user accesses an administrative endpoint that should only be available to administrators.

If the application does not properly verify the user's role and permissions, the user may gain unauthorized administrative access.

**Security Impact:**
- Unauthorized administrative operations.
- Modification or deletion of sensitive information.
- Privilege abuse.
- Potential compromise of the application.

### 4.3 Insecure Direct Object Reference (IDOR)

IDOR is an access control vulnerability that occurs when an application exposes references to internal objects, such as user IDs, account IDs, document IDs, or order IDs, without properly verifying whether the requesting user is authorized to access them.

**Example HTTP Request:**

```http
GET /api/user/profile/101 HTTP/1.1
Host: example.com
Cookie: session=USER_SESSION
```

An attacker may attempt to modify the object identifier:

```http
GET /api/user/profile/102 HTTP/1.1
Host: example.com
Cookie: session=USER_SESSION
```

If the server returns another user's private information without checking ownership, the application is vulnerable to IDOR.

**Security Impact:**
- Unauthorized information disclosure.
- Exposure of sensitive records.
- Unauthorized access to other users' resources.

### 4.4 Missing Function-Level Access Control

This occurs when an application fails to verify whether a user has permission to access a particular function or endpoint.

For example, a normal user may access an administrative API endpoint because the server does not enforce administrator-level authorization.

### 4.5 Forced Browsing

Forced browsing involves directly requesting restricted URLs or application resources instead of accessing them through the normal application interface.

For example:

```text
https://example.com/admin
https://example.com/dashboard
https://example.com/manage-users
```

If a restricted resource is accessible without proper authorization checks, it may indicate Broken Access Control.

### 4.6 Parameter-Based Access Control Weakness

Some applications rely on client-controlled parameters to determine user roles or permissions.

For example:

```http
GET /dashboard?role=user HTTP/1.1
```

If changing a client-controlled parameter allows a user to obtain unauthorized privileges, the application has an access control weakness.

**Important:** The server must never trust client-side role or permission values.

## 5. Lab Environment

The following environment was used for practical learning:

| Component | Description |
|---|---|
| Lab Application | OWASP WebGoat |
| Vulnerability | Broken Access Control |
| Testing Environment | Authorized WebGoat lab |
| Browser | Web Browser |
| Interception Tool | Burp Suite |
| Protocol | HTTP |
| Testing Type | Manual Web Application Security Testing |

## 6. Practical Testing Methodology

The following methodology describes the security testing approach for Broken Access Control.

### Step 1: Access the WebGoat Application

- Start the OWASP WebGoat application.
- Open the application in a web browser.
- Log in to the lab environment.
- Navigate to the Broken Access Control section.
- Review the available lessons and exercise instructions.

### Step 2: Understand the Access Control Mechanism

- Identify the user roles involved in the exercise.
- Understand which resources are restricted.
- Identify the expected permissions for each user.
- Determine which actions should be allowed or denied.

### Step 3: Analyze HTTP Requests Using Burp Suite

- Configure the browser to communicate through Burp Suite.
- Open Burp Suite and navigate to Proxy.
- Access the relevant WebGoat exercise.
- Capture the HTTP requests generated by the application.
- Examine request methods, URLs, headers, cookies, parameters, and response codes.

### Step 4: Examine Authorization Restrictions

During testing, examine whether the application correctly verifies:

- User identity.
- Resource ownership.
- User roles.
- Permission levels.
- Access to restricted endpoints.
- Authorization for sensitive operations.

### Step 5: Test Access Control Restrictions

Within the authorized WebGoat exercises:

- Examine whether users can access resources belonging to other users.
- Check whether restricted functionality is accessible to lower-privileged users.
- Investigate whether modifying object identifiers changes resource access.
- Examine whether direct URL requests bypass application restrictions.
- Verify whether the server properly rejects unauthorized operations.

### Step 6: Analyze Server Responses

Review the server responses to determine whether access was correctly enforced.

Important response indicators include:

| HTTP Status Code | Meaning |
|---|---|
| 200 OK | Request successfully processed |
| 302 Found | Redirect response |
| 401 Unauthorized | Authentication required or failed |
| 403 Forbidden | Access denied |
| 404 Not Found | Requested resource not found or concealed |

A successful HTTP response does not automatically mean an authorization vulnerability exists. The actual response content and the user's permissions must also be examined.

### Step 7: Complete the WebGoat Exercises

- Follow the instructions provided in each Broken Access Control exercise.
- Perform the authorized security tests.
- Analyze the application behavior.
- Understand why the vulnerability occurs.
- Complete the exercises and verify the completion status.

## 7. HTTP Request Analysis

HTTP request analysis is important when testing Broken Access Control because it helps security testers understand how applications identify users and protect resources.

### Example Request

```http
GET /profile?userId=101 HTTP/1.1
Host: example.com
Cookie: session=USER_SESSION
User-Agent: Mozilla/5.0
Accept: text/html
```

### Important Components

| Component | Purpose |
|---|---|
| GET | HTTP request method |
| /profile | Requested application resource |
| userId=101 | Resource identifier |
| Cookie | Contains session information |
| Host | Target application hostname |
| User-Agent | Identifies the client application |

### Security Testing Considerations

A security tester should verify whether:

1. The server validates the authenticated user's identity.
2. The requested resource belongs to the authenticated user.
3. The user has sufficient permissions.
4. Client-controlled parameters are not trusted for authorization.
5. Restricted resources cannot be accessed through direct requests.

**Note:** The HTTP requests above are illustrative examples, not claims about the exact requests captured during the WebGoat exercises.

## 8. Security Impact

Broken Access Control can have serious consequences for web applications.

**1. Unauthorized Data Access**

Attackers may access confidential information belonging to other users.

**2. Privilege Escalation**

Normal users may gain access to administrative functionality.

**3. Data Modification**

Attackers may modify or delete information without permission.

**4. Sensitive Information Disclosure**

Personal information, account details, and confidential records may become exposed.

**5. Account Compromise**

Weak access control mechanisms may allow attackers to perform unauthorized account-related operations.

**6. Business Impact**

Organizations may experience financial loss, reputational damage, compliance issues, and loss of customer trust.

## 9. Prevention and Mitigation

The following security practices help prevent Broken Access Control.

### 9.1 Implement Server-Side Authorization

Always enforce authorization checks on the server rather than relying on frontend restrictions.

### 9.2 Apply the Principle of Least Privilege

Users should receive only the permissions necessary to perform their assigned tasks.

### 9.3 Validate Resource Ownership

Before returning or modifying a resource, verify that the authenticated user is authorized to access it.

### 9.4 Use Role-Based Access Control (RBAC)

Define roles such as:

- Administrator
- Manager
- Normal User

Assign appropriate permissions to each role and enforce them consistently.

### 9.5 Deny Access by Default

Unless a user has explicitly been granted permission, access should be denied.

### 9.6 Avoid Trusting Client-Side Parameters

Never rely exclusively on parameters, hidden fields, cookies, or frontend JavaScript to determine authorization.

### 9.7 Perform Authorization Checks on Every Request

Every sensitive endpoint and operation should independently enforce the appropriate authorization requirements.

### 9.8 Conduct Regular Security Testing

Perform regular security assessments to identify authorization weaknesses and access control bypasses.

## 10. Learning Outcomes

After completing the Broken Access Control section in OWASP WebGoat, the key learning areas include:

- Understanding the difference between authentication and authorization.
- Understanding horizontal and vertical privilege escalation.
- Learning how IDOR vulnerabilities occur.
- Understanding missing function-level authorization.
- Recognizing the risks of forced browsing.
- Analyzing HTTP requests and responses.
- Understanding the importance of server-side access control.
- Learning security measures to prevent unauthorized access.

## 11. Evidence and Screenshots

Practical screenshots should be included to demonstrate the lab work.

| Screenshot | Description |
|---|---|
| 01 | OWASP WebGoat application |
| 02 | Broken Access Control lesson |
| 03 | Relevant HTTP request in Burp Suite |
| 04 | Access control testing evidence |
| 05 | Completed WebGoat exercise |

Add your actual screenshots to the `screenshots` directory.

You can display them in this report using:

```markdown
## Screenshots

### 1. WebGoat Application
![WebGoat Application](screenshots/01-webgoat-broken-access-control.png)

### 2. Broken Access Control Lesson
![Broken Access Control](screenshots/02-lab-exercise.png)

### 3. HTTP Request Analysis
![HTTP Request](screenshots/03-http-request.png)

### 4. Access Control Testing
![Access Control Testing](screenshots/04-access-control-testing.png)

### 5. Lab Completion
![Lab Completion](screenshots/05-lab-completion.png)
```

## 12. Conclusion

Completing the Broken Access Control section in OWASP WebGoat provided practical learning experience in understanding authorization weaknesses in web applications.

This topic helped develop an understanding of how improper authorization checks can expose sensitive information, allow unauthorized actions, and lead to privilege escalation.

The exercises also reinforced the importance of analyzing HTTP requests, understanding application permissions, and validating access restrictions.

Broken Access Control is an important area of web application security testing, and understanding its different forms is essential for identifying and preventing authorization-related vulnerabilities.

---

**Learning Platform:** OWASP WebGoat  
**Topic:** Broken Access Control  
**Category:** Web Application Security  
**Status:** Completed

**Disclaimer:** All security testing described in this report is intended for authorized educational environments and controlled lab exercises.
