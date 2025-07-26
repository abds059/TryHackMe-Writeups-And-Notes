# HTTP in Detail

## Task 1 - What is HTTP(S)

### What is HTTP (HyperText Transfer Protocol)?

HTTP is a set of rules used for communicating with web servers for transmitting webpage data (like HTML, videos, images etc)

### What is HTTPS (HyperText Transfer Protocol Secure)?

HTTPS is a secure version of HTTP that encrypts data to stop people from seeing data that your receiving or sending. It also gives you assurance that you are talking to the correct web server and not something impersonating it

### Answer the questions below

- What does HTTP stand for?

    **Hyper Text Transfer Protocol**

- What does the S in HTTPS stand for?

    **Secure**

- On the mock webpage on the right there is an issue, once you've found it, click on it. What is the challenge flag?

    **THM{INVALID_HTTP_CERT}**

---

## Task 2 - Requests And Responses

### What is a URL? (Uniform Resource Locator)

A URL is predominantly an instruction on how to access a resource on the internet

#### Scheme: 
Instructs on what protocol to use for accessing the resource such as HTTP, HTTPS, FTP (File Transfer Protocol)

#### User: 
Some services require authentication to log in, you can put a username and password into the URL to log in.

#### Host:
The domain name or IP address of the server you wish to access

#### Port: 
The Port that you are going to connect to, usually 80 for HTTP and 443 for HTTPS, but this can be hosted on any port between 1 - 65535

#### Path: 
The file name or location of the resource you are trying to access

#### Query String: 
Extra bits of information that can be sent to the requested path. For example, /blog?id=1 would tell the blog path that you wish to receive the blog article with the id of 1.

#### Fragment: 
This is a reference to a location on the actual page requested. This is commonly used for pages with long content and can have a certain part of the page directly linked to it, so it is viewable to the user as soon as they access the page 

### Making A Request

It is possible to make request to a web server with only one line **GET / HTTP/1.1**

#### Example Request:

```
GET / HTTP/1.1

Host: tryhackme.com
User-Agent: Mozilla/5.0 Firefox/87.0
Referer: https://tryhackme.com/
```

To breakdown each line of this request:

**Line 1:** This request is sending the GET method. requesting the home page with / and telling the web server we are using **HTTP protocol version 1.1** 

**Line 2:** We tell the web server that we want the website **www.tryhackme.com**

**Line 3:** We tell the web browser we are using Firefox version 87 browser

**Line 4:** We are telling the web server that the web page that referred us to this one is https://tryhackme.com

**Line 5:** HTTP requests always ends with a blank line to inform the web server that the request has ended

#### Example Response:

```
HTTP/1.1 200 OK

Server: nginx/1.15.8
Date: Fri, 09 Apr 2021 13:34:03 GMT
Content-Type: text/html
Content-Length: 98


<html>
<head>
    <title>TryHackMe</title>
</head>
<body>
    Welcome To TryHackMe.com
</body>
</html>
```

**Line 1:** HTTP 1.1 is the version of the HTTP protocol the server is using and then followed by the HTTP Status Code in this case "200 OK" which tells us the request has completed successfully

**Line 2:** This tells us the web server software and version number

**Line 3:** The current date, time and timezone of the web server

**Line 4:** The Content-Type header tells the client what sort of information is going to be sent, such as HTML, images, videos, pdf, XML

**Line 5:** Content-Length tells the client how long the response is, this way we can confirm no data is missing

**Line 6:** HTTP response contains a blank line to confirm the end of the HTTP response

**Line 7 - 14:** The information that has been requested, in this instance the homepage

### Answer the questions below

- What HTTP protocol is being used in the above example?

    **HTTP/1.1**

- What response header tells the browser how much data to expect?

    **Content-Length**

---

## Task 3 - HTTP Methods

HTTP methods are a way for the client to show their intended action when making an HTTP request

### Types Of HTTP Methods

#### GET Request
This is used for getting information from a web server

#### POST Request
This is used for submitting data to the web server and potentially creating new records

#### PUT Request
This is used for submitting data to a web server to update information

#### DELETE Request
This is used for deleting information/records from a web server

### Answer the questions below

- What method would be used to create a new user account?

    **POST**

- What method would be used to update your email address?

    **PUT**

- What method would be used to remove a picture you've uploaded to your account?

    **DELETE**

---

