# OWASP Top 10 (2025) : IAAA Failures

## Task 1 - Introduction

In this room 3 of the OWASP top 10 categories are listed. In this room failures related to identity, authentication, authorisation and accountability are explained. The following three categories are covered here:

- A01: Broken Access Control
- A07: Authentication Failures
- A09: Logging and Alerting Failures

---

## Task 2 - What is IAAA ?

IAAA is a security principle that is used to protect systems and data. It stands for Identity, Authentication, Authorisation and Accountability. Each item is incomplete without each other so implementation of all is necessary. 

The four items are:

- **Identity** - the unique account (e.g., user ID/email) that represents a person or service.
- **Authentication** - proving that identity (passwords, OTP, passkeys).
- **Authorisation** - what that identity is allowed to do.
- **Accountability** - recording and alerting on who did what, when, and from where.

The three categories of OWASP Top 10:2025 discussed in this room relates to failures in how IAAA was implemented. Weaknesses here can be incredibly detrimental, as it can allow threat actors to either access the data of other users or gain more privileges than they are suppose to have.

### Answer the questions below

- What does IAAA stands for

    `Identity, Authentication, Authorisation, Accountability`

---

## Task 3 - AO1: Broken Access Control

Broken Access Control occurs when the server doesnto properly validates each request and properly enforce who can access what (authorisation). A common vulnerability that arises due to this is IDOR (Insecure Direct Object Reference) that allows users to access information about other users without requiring their credentials.

IDOR can lead to horizontal priviledge escalation as well as in worst case to vertical privilege exacalation. 

### Answer the questions below

- If you don't get access to more roles but can view the data of another users, what type of privilege escalation is this?

    `Horizontal`

- What is the note you found when viewing the user's account who had more than $ 1 million?

    ![flag 1](../Images/OWASP%20Top%2010%20(2025)/IAAA%20Failures/flag%201.png)

---

## Task 4 - A07: Authentication Failures

Authentication Failures occur when the application fails to verify user identity correctly. Common issues include:

- User enumeration
- Weak guessable passwords (no rate limiting / account lockouts applied)
- Logic flaws in login/registration flows
- Insecure session or cookie handling

If any of these are present, an attacker can often log in as someone else or bind a session to the wrong account.

We can imply the same strategy by registering a user named `aDmiN` in a=the attached site and the login as that user to view the original admin account.

### Answer the questions below

- What is the flag on the admin user's dashboard?

    ![flag 2](../Images/OWASP%20Top%2010%20(2025)/IAAA%20Failures/flag%202.png)

---

## Task 05 - A09: Logging and Alerting Failures

Logging and Alerting failures occur when the application fails to provide alerts for security related events resulting in defenders not able to detect or investigate alerts. 

Good logging amplifies accountability principle (who did what, when and from where). In practice failures related to this category look like:

- Missing authentication events
- Vague error logs
- No alerting on brute force attempts or privilege escalation
- Short or temporary storage of alerts
- Logs stored in a directory where they can be accessed and modified 

### Answer the questions below

- It looks like an attacker tried to perform a brute-force attack, what is the IP of the attacker?

    `203.0.113.45`

- Looks like they were able to gain access to an account! What is the username associated with that account?

    `admin`

- What action did the attacker try to do with the account? List the endpoint the accessed.

    `/supersecretadminstuff`

---

## Task 6 - Conclusion

In this room we worked through the essentials of Identity, Authentication, Authorisation, and Accountability in web applications and how it can cause several of the categories of vulnerabilities discussed in OWASP Top 10:2025. The big ideas to keep:

- **A01 Broken Access Control:** Enforce server-side checks on every request.

- **A07 Authentication Failures:** Enforce unique indexes on the canonical form, rate-limit/lock out brute force, and rotate sessions on password/privilege changes.

- **A09 Logging & Alerting Failures:** Log the full auth lifecycle (fail/success, password/2FA/role changes, admin actions), centralise logs off-host with retention, and alert on anomalies (e.g., brute-force bursts, privilege elevation).

---
