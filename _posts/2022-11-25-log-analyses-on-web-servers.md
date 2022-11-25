## Log Analysis on Web Servers

While logging on web servers, requests made with the POST method may not be logged by default. In this case, the situation can be compensated by using modules such as mod_forensic or mod_security.

The data sent with the POST method is not included in the logs. Network traffic needs to be examined to access data.

![Image](/img/log1.png)

A screenshot of the log record of a request sent with the POST method is given above. As can be seen, it is seen in the relevant request to which address the request was sent. Looking at the sent package itself, there is also information such as the data sent by the client to the server and the referring page. A sample POST request has been added below for clarity.

![Image](/img/log2.png)

## Apache Web Servers

In this section, we will see how a web application hosted on an Apache server was exposed to an SQL Injection attack by examining the log records.

#### Log Records

In the Apache web server, log records are kept in **access.log** and **error.log** files under **/var/log/apache2** folder. In the "access.log", a record of requests made to the server is kept.

![Image](/img/log3.png)

In the screenshot above, from the IP address “192.168.2.232” to the “/index.php” page, in the “GET” method in the “[18/Aug/2017:15:02:05 +0000]” time zone, “Mozilla/5.0 (X11; Linux x86_64) ) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/60.0.3112.78 Safari/537.36” User-Agent request was sent.

The "error.log" file contains errors that the server encounters while processing requests.

The following screenshot shows that the client trying to navigate to the "/test" directory failed.

![Image](/img/log4.png)

#### How did the SQL Injection attack happen?

To examine the logs of the server exposed to the SQL Injection attack, go to the /var/log/apache2 folder.

`cat access.log`

When you look at the log file with the above command, it is clear that many SQL injection attempts are made by the same IP address. If the injection attempts were not made noticeably, the most used parameters in sql attacks would be searched in the logs. These are union, select, from, or, They are parameters such as version. These parameters can be searched in log files with the grep command.

`cat access.log | grep -E "%27|--|union|select|from|or|@|version|char|varchar|exec`

Below is a screenshot of the SQL injection records in the "access.log" file.

![Image](/img/log5.png)

In order to view the successful attempts, the responses that return 200 codes from the server must be filtered.

`cat access.log | grep 200`

Filtering is performed with the grep command. After the filtering, output shows the logs that has HTTP Response 200 status code were returned to the SQL query where the username and password were drawn.

![Image](/img/log6.png)

Let's apply the "URL Decode" operation to the URL address above.
Decoded URL: /cat.php?id=1 UNION SELECT 1,concat(login,':',password),3,4 FROM users;
When the encoded URL is decoded, you can see that there is clearly a SQL Injection attack.
To check if the attacker is logged into the "admin" panel, we can look at the access logs via command below:
`cat access.log | grep 192.168.2.232 | grep admin/index.php`
The screenshot of the records of the attacker who made a request to the admin panel is given below:

![Image](/img/log7.png)

In the screenshot, it is seen that the attacker sends a request to the admin panel with the POST method. When the sent POST request is examined with Wireshark, it is certain that the attacker has logged into the panel.

The details of the POST request sent to the admin panel are given in the screenshot below:

![Image](/img/log8.png)

## Nginx Server

In this section, log records of "directory traversal vulnerability" attempts, which are an attack on the application running on the Nginx server, will be examined. In this attack, different files are accessed by changing the directory with dot-dot-slash ( ../ ).

#### Log Records

On Nginx servers, log files are kept in the /var/log/nginx directory, as in Apache servers, in access.log and error.log files.

#### How Did the Attack Occur?

It would be more convenient for us to filter the characters “../” or “..\” for the detection of the attack.

Command: `cat access.log | grep ../`

![Image](/img/log9.png)

As can be seen in the output above, the attacker tried to read the passwd file. After the third attempt, he transferred the page to himself with the wget command.

When the transferred page is examined, it is seen that the information in the "passwd" file has been disclosed. The screenshot of the page copied by the attacker is given below:

![Image](/img/log10.png)

## IIS Web Server

Log records are kept in the C:\inetpub\logs\LogFiles\W3SVC1 folder on the IIS web server.

A sample screenshot of the log records on IIS web servers is given below:

![Image](/img/log11.png)

## Hands On Practise

Q1 -) In what year was the request made to the "/letsdefend.html" path of the Nginx web server?

S1 -) `sudo cat /var/log/nginx/access.log.1 | grep letsdefend.html`

![Image](/img/log12.png)

2022

Q2 -) What is the IP address trying to read the /etc/passwd file on the Nginx web server?

S2 -) `sudo cat /var/log/nginx/access.log.1 | grep etc/passwd`

![Image](/img/log13.png)

Q3 -) What is the IP address that attempted SQL injection attack on Apache2 web server?

S3 -) `sudo cat /var/log/apache2/access.log | grep select`

![Image](/img/log14.png)
