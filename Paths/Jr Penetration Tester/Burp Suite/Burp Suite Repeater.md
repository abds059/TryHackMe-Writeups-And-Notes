# Burp Suite : Repeater

## Task 2 -  What Is Repeater ?

The `Repeater` module in Burp Suite allows us to modify and resend requests to a target. It allows us to take requests captured in the Burp Proxy and manipulate them, sending them repeatedly or manually create from scractch like curl requests.

This ability of Repeater of sending multiple modified requests makes it valuable for manula exploration and testing of endpoints. It provides a user-friendly graphical interface for crafting requests payloads and also a rendering engine for graphical representation.

The Repeater interface consists of six sections as labeled in the image and described below:

![Repeater Interface sections](../Images/Burp%20Suite/Burp%20Suite%20Repeater/Repeater%20features.png)

**1. Requests Lists:** This list consist of requests which are sent from `Proxy` to `Repeater`

**2. Request Controls:** This enables us to send requests, cancel hanging request and navigate through the request history

**3. Request and Response Review:** Occupying the majority of the interface, this section displays the Request and Response views. We can edit the request in the Request view and then forward it, while the corresponding response will be shown in the Response view

**4. Layout Options:** Located at the top-right of the Request/Response view, these options enable us to customize the layout of the Request and Response views. The default setting is a side-by-side (horizontal) layout, but we can also choose a vertical layout or combine them in separate tabs

**5. Inspector:** Allows modifying requests in a more intuitive mannerthan using the raw editor

**6. Target:** This specifies the IP or domain we are sending the request to. When Request are send to Repeater from some other module liek Proxy this section is automatically populated

### Answer the questions below

- Which sections gives us a more intuitive control over our requests?

`Inspector`

---

## Task 3 - Basic Usage

Now since we know what the interface looks like, lets learn to effectively utilize this.

While manual request crafting is an option, it is more common to capture a request in Proxy mode and then forward it to Repeater. 

Once a request has been captured in Proxy mode we can select the captured request and then Right Click and select `Select To Repeater`, or simply press `Ctrl + R`. 

![Intercepting Request](../Images/Burp%20Suite/Burp%20Suite%20Repeater/intecept.png)

![Sending Request To Repeater](../Images/Burp%20Suite/Burp%20Suite%20Repeater/send%20to%20repeater.png)

### Answer the questions below

- Which view will populate when sending a request from the Proxy module to Repeater?

    `Request`

---

## Task 4 - Message Analysis ToolBar

Repeater provides us with various request and response presentation options, ranging from hexadecimal output to a fully rendered page.

To explore these options, we can refer to the section located above the response box, where the following four view buttons are available:

![Presentation Options](../Images/Burp%20Suite/Burp%20Suite%20Repeater/Presentation%20Options.png)

We are presented with the following display choices:

**1. Pretty:** This is the default option, which takes the raw response and applies slight formatting enhancements to improve readability

**2. Raw:** This option displays the unmodified response directly received from the server without any additional formatting

**3. Hex:** By selecting this view, we can examine the response in a byte-level representation, which is particularly useful when dealing with binary files

**4. Render:** The render option allows us to visualize the page as it would appear in a web browser. While not commonly utilised in Repeater, as our focus is usually on the source code, it still offers a valuable feature

For most scenarios, the Pretty option is generally sufficient. However, it is beneficial to be acquainted with the usage of the other three options.

Adjacent to the view buttons, on the right-hand side, we find the Show non-printable characters button (`\n`). This functionality enables the display of characters that may not be visible with the Pretty or Raw options. For example, each line in the response typically ends with the characters `\r\n`, representing a carriage return followed by a new line. These characters play an important role in the interpretation of HTTP headers.

While not mandatory for most tasks, this option can prove advantageous in certain situations.

### Answer the questions below

- Which option allows us to visualize the page as it would appear in a web browser?

    `Render`

---

## Task 5 - Inspector

`Inspector` is a supplementary feature in the `Repeater` module. It is also used to visually obtain organized breakdown of requests and response as well as for experimenting to set how changes made using the higher level inspector affect equivalent raw versions.

Inspector can be utilized both in the Proxy and Repeater module. In both instances, it is situated on the far-right side of the window, presenting a list of components within the request and response:

![Inspector options](../Images/Burp%20Suite/Burp%20Suite%20Repeater/Inpector.png)

Among these components, the sections pertaining to the request can typically be modified, enabling the addition, editing, and removal of items. For instance, in the Request Attributes section, we can alter elements related to the location, method, and protocol of the request. This includes modifying the desired resource to retrieve, changing the HTTP method from GET to another variant, or switching the protocol from HTTP/1 to HTTP/2:

![Request Attributes](../Images/Burp%20Suite/Burp%20Suite%20Repeater/Request%20Attributes.png)

Other sections available for viewing and/or editing include:

**1. Request Query Parameter:** This captures query parameters like in `https://admin.tryhackme.com/?redirect=false` the query parameter redirect is set to false:  `redirect=false` 

**2. Request Body Parameter:** Similar to query parameter, but specifically for `POST` requests. Any data sent as part of `POST` request will be displayed in this section, allowing us to modify the parameters before sending

**3. Request Cookies:** This section contains modifiable list of cookies sent with evry request

**3. Request Headers:** Enables us to view, access and modify headers sent with the request. Editing them is valuable to see how server responds to unexpected headers

**4. Response Headers:** Displays the headers returned with the response. They are non-modifiable, as we dont have control over headers returned by the header. This sections becomes visble only when a response is received in reply to a request.

### Answer the questions below

- Which section in Inspector is specific to POST requests?

    `Body Parameters`

---

## Task 6 - Practical Example

Repeaeter is well suited for task that include sending repeated requests withy minor modification. This is particularly useful for activities like manual testing for SQLi, attempting to bypassing WAFs or adjusting parameters in form submission.

Let's begin with an exceedingly simple example: Utilizing Repeater to modify the headers of a request sent to a target.

Capture a request to `http://<Machine-ip>/` in the Proxy module and send it to Repeater.

Send the request once from Repeater — you should see the HTML source code for the page you requested in the Response view.

Try viewing this in one of the other display options (e.g. Hex).

Using Inspector (or manually, if you prefer), add a header called `FlagAuthorised` and set it to have a value of `True`, as shown below:

![FlagAuthorised](../Images/Burp%20Suite/Burp%20Suite%20Repeater/Setting%20FlagAuthorised%20header.png)

Then click on `Send`.

### Answer the questions below

- What is the flag you receive?

    ![flag1](../Images/Burp%20Suite/Burp%20Suite%20Repeater/flag1.png)

---

## Task 7 - Challenge

In the previous task, we demonstrated the usage of Repeater by adding a header and sending a request. This served as an illustrative example for utilizing Repeater. Now, it's time for a straightforward challenge!

To begin, make sure intercept is disabled in your Proxy module and navigate to `http://<Machine-ip>/products/`. Next, try clicking on some of the `See More` links.

Observe that you are redirected to a numeric endpoint (e.g., /products/3).

The objective is to validate the endpoint, confirming the existence of the number you wish to navigate to and ensuring it is a valid integer. However, consider what might occur if this endpoint is not adequately validated.

Okay so now enable `Intercept` in proxy mode and try clicking on `See More`. This will capture a request in `Proxy` mode. Forward this request to `Repeater` module.

In Repeater module now we will play with the urls numeric value `/products/<value>`. Try increasing it to extreme levels to get the server to throw a `500 Internal Server Error`.

I first increased the value towards the positive extremes but kept on hitting `404 Page Not Found` errors:

![404 Page Not Found](../Images/Burp%20Suite/Burp%20Suite%20Repeater/404%20error.png)

Then after a few attempts I tried, the negative extreme side of values and it gave me the 500 error and the flag in the first go:

![500 error and flag value](../Images/Burp%20Suite/Burp%20Suite%20Repeater/flag2.png)

---

## Task 8 - Extra-mile Challenge

### Challenge Objective

Our objective in this challenge is to identify and exploit a Union SQL Injection vulnerability present in the ID parameter of the /about/ID endpoint. By leveraging this vulnerability, our task is to launch an attack to retrieve the notes about the CEO stored in the database.

So we know that there is an SQL UNION Injection vulnerability present we just need to exploit it. 

So we can first start by sending a request to `http://<Machine-ip>/about/2` and capture the request in `Proxy` mode and then send it to `Repeater`. 

SO lets confirm the SQLi by first slightly modifying the request. So we can add a single apostrophe `'` after the endpoint `/about/2` and send the request:

![500 error](../Images/Burp%20Suite/Burp%20Suite%20Repeater/500%20error%20SQLI.png)

So this gave us a 500 error and a sql query in response.

The message tells us a couple of things that will be invaluable when exploiting this vulnerability:

- The database table we are selecting from is called `people`.
- The query selects five columns from the table: `firstName, lastName, pfpLink, role, and bio`. We can guess where these fit into the page, which will be helpful for when we choose where to place our requests.

As we know the table name and the number of rows, we can use a union query to select the column names for the people table from the columns table in the information_schema default database.

A simple query for this is as follows:

`/about/0 UNION ALL SELECT column_name,null,null,null,null FROM information_schema.columns WHERE table_name="people"`

This creates a union query and selects our target, then four null columns (to avoid the query erroring out). Notice that we also changed the ID that we are selecting from 2 to 0. By setting the ID to an invalid number, we ensure that we don't retrieve anything with the original (legitimate) query; this means that the first row returned from the database will be our desired response from the injected query.

Looking through the returned response, we can see that the first column name (id) has been inserted into the page title:

![id](../Images/Burp%20Suite/Burp%20Suite%20Repeater/id%20col.png)

e have successfully pulled the first column name out of the database, but we now have a problem. The page is only displaying the first matching item — we need to see all of the matching items.

Fortunately, we can use our SQLi to group the results. We can still only retrieve one result at a time, but by using the group_concat() function, we can amalgamate all of the column names into a single output:

`/about/0 UNION ALL SELECT group_concat(column_name),null,null,null,null FROM information_schema.columns WHERE table_name="people"`

This process is shown below:

![columns](../Images/Burp%20Suite/Burp%20Suite%20Repeater/all%20cols.png)

We have successfully identified eight columns in this table: id, firstName, lastName, pfpLink, role, shortRole, bio, and notes.

Considering our task, it seems a safe bet that our target column is notes.

Finally, we are ready to take the flag from this database, we have all of the information that we need:

- The name of the table: people.
- The name of the target column: notes.
- The ID of the CEO is 1; this can be found simply by clicking on Jameson Wolfe's profile on the /about/ page and checking the ID in the URL.
- Let's craft a query to extract this flag:
`0 UNION ALL SELECT notes,null,null,null,null FROM people WHERE id = 1`

![flag3](../Images/Burp%20Suite/Burp%20Suite%20Repeater/flag3.png)

---

