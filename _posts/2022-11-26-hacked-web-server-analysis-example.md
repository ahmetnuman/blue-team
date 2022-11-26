## Hacked Web Server Analysis Example

In this section, we will be examining a fully compromised web server running Wordpress by post-attack analysis.
First, understanding whether the attacker has access to the admin panel can be discovered by entering this command below:

`cat access.log | grep POST | grep wp-login`

![Image](/img/analy1.png)

As can be seen in the output in the screenshot above, many POST requests have been sent to the admin panel login page over the same IP address. Network traffic is examined to check the data sent with the request. For this, in Wireshark;

Wireshark is the world's foremost and widely-used network protocol analyzer.

Query: ip.src == 192.168.2.232 && ip.dst == 192.168.2.31 && http.request.method == POST

POST requests to the web server through the attacker are listed using the filter.

![Image](/img/analy2.png)

When the requests were examined, it was determined that a brute force attack was carried out on the "wp-login" page.

![Image](/img/analy3.png)

![Image](/img/analy4.png)

As a result of the attack, the correct username and password were found as admin: admin

When the “error.log” file is examined, it is seen that the fscockopen() function is intended to be activated. Thus, it can be predicted what the attacker did in the admin panel. The cat error.log command reads the error.log page.

![Image](/img/analy5.png)

The attacker wanted to go to an inaccessible page and get a 404 Not Found. Thus, instead of the classic 404 Not Found error page, it is thought that the code he has placed will work. We can look at the change by examining the network traffic or logging into the admin panel.

Looking at the changes, it was determined that the content of the 404 Not Found error page was changed by the attacker.

![Image](/img/analy6.png)

The piece of code used by the attacker for the error page is shown in the screenshot below:

![Image](/img/analy7.png)

When the code used by the attacker for the 404 error code page is examined, it is seen that he opened access to his own address on port 1234.

The attacker will have access to the server with www-data user rights with this door opened for himself. In this case, the commands run by the "www-data" user on the server are examined.

Below is a screenshot of the commands the attacker ran on the server.

![Image](/img/analy8.png)

The attacker, who read the "wp-config.php" file, switched to the root user. When looking at the wp-config.php file, the database password is the same as the root user's password.

A screenshot of the contents of the wp-config.php file is given below:

![Image](/img/analy9.png)

With the above password, the attacker gained root authority and took over the entire serve

#### Conclusion


As discussed in the tutorial, attackers can attack the web server in various ways to take over. In order to detect attacks, log analysis and good control of network traffic are required. For this, it is necessary to know and understand the attack vectors for effective analysis.

Sometimes a security vulnerability can be caused by the server or programming language you are using. Therefore, it is necessary to keep the components on the server constantly updated.











