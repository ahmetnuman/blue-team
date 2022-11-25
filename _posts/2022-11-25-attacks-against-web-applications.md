## Attacks Against Web Applications

## Injection

Injection is manipulation by adding various parameters to the query sent to the server.
SQL Injection is sending the desired query to the database by manipulating SQL queries.
This article is described as assuming a basic knowledge of SQL injection attacks. For detailed information about SQL injection, you can look at our lesson.

- Detecting SQL Injection Attacks

Here, we will briefly touch on which steps a person should try to detect a SQL injection attack. Our main purpose in the detection stages is to receive error messages. Database information used in the error messages we receive, etc. We will get the details. For this;

- The most basic payload used is the ' (single quote) character.
- The number of columns can be determined. For this, the payload given below can be used.

`**?id=6 ORDER BY 6--**`

- All data in a column must have the same data type. For this, the column type can be determined using the payload below.

`**?id=6 UNION SELECT 1,null,null--**`

- The following payloads can be used to detect logic-based SOL injection vulnerability.

```
- **test.php?id=6**
- **test.php?id=7-1**
- **test.php?id=6 OR 1=1**
- **test.php?id=6 OR 11-5=6**
```

- The following payloads can be used to detect time-based SOL injection vulnerability.

```
- **SLEEP(25)--**
- **SELECT BENCHMARK(1000000,MD5('A'));**
- **userID=1 OR SLEEP(25)=0 LIMIT 1-- userID=1) OR SLEEP(25)=0 LIMIT 1-- userID=1' OR SLEEP(25)=0 LIMIT 1-- userID=1') OR SLEEP(25)=0**
- **LIMIT 1-- userID=1)) OR SLEEP(25)=0 LIMIT 1-- userID=SELECT SLEEP(25)--**
```

## Example of SQL Injection Attack

When the ID number is entered, the name and surname information of the user of the entered number is returned. 
Details of the information of the user whose ID is 2 are given in the screenshot below:

![Image](/img/sqli1.png)

However, if a manipulated query is sent that the application does not expect, the unsecured web application will be exposed to an injection attack.

For example, the database version is drawn with the query `**' or 0=0 union select null, version() #**` below. While the previous query is completed with quotation marks in this query, the result is always correct with the expression “or 0=0”.” union select null , version()”, version information is also retrieved. Lastly, the "#" sign ensures that the rest of the query remains as a comment line.

- The following image shows the response returned by the query run.

![Image](/img/sqli2.png)

#### Log Records

It has been determined that the most used characters and words in SQL Injection attacks are ' , -- , union , select , from , or , @ , version , char , varchar , exec . 
When the relevant characters are encoded in the URL, a Linux command as follows is generated for attack detection.

- `**cat access.log | grep -E “%27|--|union|select|from|or|@|version|char|varchar|exec**`

![Image](/img/sqli3.png)

It appears that the query ' or 0=0 union select null, version() # is sent when the record URL in the acces.log file is decoded.

![Image](/img/sqli4.png)

#### Protection Methods

- Using prepared query statements
- Checking the sent data
- Filtering the sent data
- Restricting user privileges

measures that can be taken.

## Broken Authentication and Session Management

It is a security weakness caused by not providing full session security.

#### Attack Sample

The purpose of this attack will be to change the cookie information and switch to the desired user account without any authentication mechanism.

![Image](/img/sqli5.png)

Login to the system as "user1".

![Image](/img/sqli6.png)

Then the cookie value is checked. The screenshot of the cookie value of the user "user1" is given below:

![Image](/img/sqli7.png)

As you can see, the cookie value is the same as the username. When the cookie value is changed to admin, if there is an admin user in the system, it will be switched to the admin user.

The screenshot of changing the cookie data is given below:

![Image](/img/sqli8.png)

The screenshot showing the switch to the admin user is given below:

![Image](/img/sqli9.png)

As a result, without using any password, only the cookie value was changed and the admin user was switched.

#### Log Records

When the log records are examined, it is seen that there is no remarkable situation. The screenshot showing that the user "user1" is logged in the analyzed log records is given below:

![Image](/img/sqli10.png)

However, when the details of the requests sent with the network traffic were examined, it was seen that there was an abnormality in the cookie values.
A screenshot of the cookie value of the user entering the system from the 192.168.68.1 IP address is given below:

![Image](/img/sqli11.png)

The screenshot of the cookie value in the second request sent to the system from the same IP address is given below:

![Image](/img/sqli12.png)

As seen in the screenshots, the person connecting to the system from the same IP address has changed the cookie values ​​and switched to the "admin" user.

#### Protection Methods

- Providing strong authentication and session management
- Preventing XSS attacks so that cookie information is not stolen

## Cross-Site Scripting (XSS)

XSS is a type of attack that allows the client to run code on a site with the help of HTML and JavaScript. In addition, attacks such as cookie stealing and page redirection can also be organized.

XSS is usually found in sections where GET and POST methods are used. It is found in fields that take input such as search boxes, galleries, messages, and memberships. The payload used for detection is usually in the form of given below. Of course, this payload is a very classic example. Different event handlers (onclick, onload, onmouseover, etc.) can be used depending on the filtering situation. For detailed information about XSS and for examples, you can visit the website.

- `**<script>alert(1)</script>**`

Detecting xss attacks

#### Attack Sample

In this sample attack, the vulnerability will be exploited by adding JavaScript code to the section where a message should be written for the guestbook. For this, the appropriate JavaScript code is written and sent to the message section first. If the vulnerability is exploited with the code written as an example, "1" will be written on the screen as a pop-up message. The application is tried to be exploited by writing JS code outside of the expected message in the guestbook.

![Image](/img/sqli13.png)

Users who visit the page after the message is sent are affected by the code written. Below is a screenshot showing that the user visiting the page is affected by the JavaScript code.

![Image](/img/sqli14.png)

Looking at the source code of the page, it is seen that the javascript code has been added.

![Image](/img/sqli15.png)

#### Log Records

When the log records are examined, it is seen that data is sent to the page related to the POST method.

![Image](/img/sqli16.png)

Network traffic is inspected to see the details.

![Image](/img/sqli17.png)

As can be seen in the records of the examined traffic, JavaScript code was injected into the attacker message section.

In order to detect XSS attacks performed with the GET method, it is necessary to filter some keywords in the log files. The most used characters and words in XSS attacks are <, >, alert, script, src, cookie, onerror, document. While filtering the search with the grep command, it is necessary to search for the URL encoded version of the '<' and '>' characters. As a result;

- `**cat access.log | grep -E "%3C|%3E|alert|script|src|cookie|onerror|document"**` 

A screenshot of an example XSS attack detection is given below:

![Image](/img/sqli18.png)

#### Protection Methods

- Verifying the input (checking that the data entered is indeed the requested type).
- Using whitelist instead of blacklist.

## Security Misconfiguration

This vulnerability is due to security configurations being left incorrect, weak, or default.

#### Attack Sample

In this sample attack, as a result of not changing the password of the "admin" user automatically created by the system, the attacker can log into the system with the default user name and password.

#### Protection Methods

- Editing the default configurations.
- Keeping software up to date.
- Disable unused services and ports.

## Cross-Site Request Forgery (CSRF)

CSRF stands for cross-site request forgery. It is a type of attack that is carried out by making the user take action against his will by the attackers.

#### Attack Sample

In this sample attack, our goal will be to change the victim's password by sending a request to the target application with a fake web page. There is only a "click" button on the fake web page. The screenshot of the relevant button is given below:

![Image](/img/sqli19.png)

A screenshot of the source code of the fake page is given below:

![Image](/img/sqli20.png)

The following action will be taken on our fake page; If the victim clicks the "click" button, they will send a request to the target application that they want to change their password to 123456. And the password of the user who clicked the button will be changed. The relevant screenshot is given below:

![Image](/img/sqli21.png)

#### Log Records

When the log records are examined after the attack, it is seen that the victim requested a password to the application and the user's password was changed.

![Image](/img/sqli22.png)

#### Protection Methods

- Using the CSRF protection mechanisms offered by the frameworks.
- Using token and session.




















































