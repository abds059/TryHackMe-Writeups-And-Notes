# CSRF Introduction

## Task 1 - Introduction

Modern web applications rely heavily on authenticated sessions to perform actions on behalf of the user. When you login, the browser storesa session cookie that identifies us in future requests. While this makes web applications convenient to use, it also creates opportunities for attackers to abuse this trust. One such attack is `Cross-Site Request Forgery (CSRF)`.

Instead of stealing credentials, the attacker tricks the victim into performing actions on the website where the user is already authenticated. Because the browser automatically includes cookies with the requests so the web application may treat a malicious request as legitimate.

### Learning Objectives

- Understand the basic concept of CSRF attacks
- Identify application features that are vulnerable to CSRF
- Exploit CSRF using simple HTML and weak tokens 
- Understand common practices used to exploit CSRF

### Scenario

Throughout this room, we will interact with a simple employee portal called `StaffHub`. This internal application allows employees to update account settings, including their email address and profile preferences.

Your objective as a pentester is to analyse how these actions are performed and determine whether they can be triggered without the user's knowledge. If the application does not properly verify the origin of requests, attackers may be able to trick authenticated users into unknowingly modifying their own account settings.

---

## Task 2 - What is CSRF

CSRF is a web vulnerability where an attacker tricks the user browser into sending a request to a website where the user is already authenticated. Since, the browser includes session cookies with every requests, the web application assumes that the request has been made by the user.

So, instead of stealing credentials the attacker abuse the relationship between the browser and the web application. If the request perform some sensitive actions like changing email address or updating account settings the attacker may be able to modify the victim's account without their knowledge.

For example, imagine a user is logged in to an internal portal while browsing the internet. If the user visits a malicious webpage, that page can silently trigger a request to the portal. If the application does not verify the origin of the request, it may process it as if the user initiated it.

### How CSRF Attack Works 

![CSRF Flow](../Images/Web%20Application%20Vulnerabilities%20I/CSRF%20Introduction/CSRF%20Flow.png)

A typical CSRF attack usually follows three simple steps: 

- The victim logs in to a legitimate web application, and their browser stores a session cookie.
- The attacker tricks the victim into visiting a malicious webpage containing a crafted request.
- The victim’s browser automatically sends the request to the target application along with the stored session cookie.

Because the request contains the valid session cookie, the server treats it as a legitimate user action.

### Why This Is Dangerous

CSRF attacks can be used to perform many actions depending on the functionality of the target application, such as:

- Changing a user’s email address
- Updating account settings
- Performing financial transactions
- Modifying security preferences
- Even though the victim never intended to perform the action, the web application processes it as a legitimate request.

### Answer the questions below

- What relationship between the browser and the web application does a CSRF attack abuse?

    `Trust`

- What does the browser automatically include with requests after login?

    `Cookies`

---

## Task 3 - Why CSRF Works

The vulnerability exist not because of any broken behaviour but due to a web application trusting requests too much without validation.

When a user logs in to a website, the server usually creates a session and sends a session cookie to the browser. This cookie acts like an identity card. Every time the browser sends a request to that website, the cookie is automatically included, so the server knows which user is making the request.

The key detail here is that the browser automatically sends cookies with every request to the same domain. It does not matter whether the request came from a legitimate page on the site or from a malicious webpage somewhere else on the internet.

For example, if a user is logged in to staffhub.thm and visits another website, that site can trigger a request to `staffhub.thm`. When the browser sends the request, it automatically includes the session cookie. From the server’s perspective, the request appears to come from the authenticated user.

This is the exact behaviour attackers exploit during a CSRF attack. If the application does not verify the origin of the request, it may process the request without realising it was triggered by a malicious page. 

### Key Conditions For CSRF

For a CSRF attack to work, three main conditions usually have to exist:

- User must be authenticated on the target application
- Application must perform a state-changing action like updating settings or modifying account data 
- The application must not verify whether the request came form trusted source

If these conditions exist, an attacker may be able to perform actions on behalf of the victim without ever knowing their credentials.

### Answer the questions below

- What type of action is usually required for a CSRF attack to succeed?

    `State-Changing`

---

## Task 4 - Finding CSRF Vulnerabilities

Befor exploiting the pentester must first identify if the vulnerability exist in the first place or not. Not every feature in a web application is a good target. CSRF attacks usually focus on actions that change data or modify account settings.

Whenever a user performs an action on a website, the browser sends an HTTP request to the server. Some requests simply retrieve information, while others modify the application’s state. These state-changing requests are the primary targets for CSRF attacks. 

As a pentester, you should carefully inspect application functionality and ask a simple question:

Can this action be triggered without verifying that the request actually came from the user?

If the answer is yes, the feature might be vulnerable to CSRF.

### Common Features Vulnerable to CSRF

Certain actions frequently appear in CSRF vulnerabilities because they modify important user data. While testing an application, pay special attention to requests that perform operations such as:

![Features for CSRF](../Images/Web%20Application%20Vulnerabilities%20I/CSRF%20Introduction/Features%20for%20CSRF.png)

If these requests do not include any protection mechanism, such as CSRF tokens, they may be susceptible to forged requests.

### GET vs POST - A Common Misconception

Many developers assume that using the POST method automatically protects against CSRF attacks. This is not true.

Both GET and POST requests can be abused if the application does not verify that the request originated from a trusted page. In fact, many CSRF attacks rely on simple GET requests that can be triggered through links or images.

Because of this, pentesters should never rely solely on the request method when testing for CSRF vulnerabilities.

### Answer the questions below

- What HTTP request method do many developers incorrectly believe prevents CSRF?

    `POST`

- What mechanism is commonly used to protect applications from CSRF attacks?

    `CSRF tokens`

---

