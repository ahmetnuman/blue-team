## Vulnerabilities in Programming Language

## PHP

The mail() function of PHP and the "Phpmailer" library that uses it is used by millions of people. Remote code execution vulnerability occurs due to the use of the "mail()" function without fully checking the extra parameter sending feature. The code for the relevant vulnerability is “CVE-2016-10033”.

#### CVE-2016-10033 Vulnerability Attack Sample

As an example, an application with a security vulnerability is installed.

![Image](/img/vulnlanguage1.png)

Then the exploit prepared for this vulnerability is run. The relevant exploit can be downloaded from the link given below:

- https://github.com/opsxcq/exploit-CVE-2016-10033

The screenshot of the exploit process performed at the target address is given below:

![Image](/img/vulnlanguage2.png)

After the exploit is performed, the system is waiting for the remote command to be executed.

Below is a screenshot of the vulnerability being exploited and running a remote command.

![Image](/img/vulnlanguage3.png)

#### Log Records

When the network traffic of the vulnerability is examined, it is noticed that GET requests are sent to the “backdoor.php” path.

![Image](/img/vulnlanguage4.png)

When the sent commands are decoded with base64, the commands executed by the attacker are exposed. To access detailed information about base64, you can click here.

![Image](/img/vulnlanguage5.png)

#### Protection Methods

- The vulnerability affects versions 5.2.18 and below. The vulnerability can be avoided by updating.

## Java

Under this title, the "Session Injection" vulnerability that occurs in the Java Play Framework will be discussed. The vulnerability is due to session encode.

#### Attack Sample

The target application tells the screen whether the user has admin rights. Here, the attacker's goal will be to gain admin rights.

![Image](/img/vulnlanguages6.png)


First, registration is made under the user name in a normal way and the submitted requests are examined using Burp Suite software.

![Image](/img/vulnlanguage7.png)

Here, assuming the admin user exists, null bytes are used to switch to the admin user.

A new record is created and the "%00%00admin%3a1%00" parameter is added after the user name and the injection is completed. The new session to be created here is misinterpreted by the server with the "admin:1" parameter and access to admin rights. By using empty characters, the "admin:1" parameter is set to the previous parameter. separated from the username.

The user interface of the registration screen is given below:

![Image](/img/vulnlanguage8.png)

The parameter 00%00admin%3a1%00 is added to the end of the username on the sent request.

![Image](/img/vulnlanguage9.png)

The screenshot showing access to admin rights on the application after the request is made is given below:

![Image](/img/vulnlanguage10.png)

#### Log Records

When the log records are examined, it is seen that the "test" user seems to have registered normally, but when the cookie value is examined, an injection attack occurs.

The screenshot of the POST request sent to the Register page is given below:

![Image](/img/vulnlanguage11.png)

#### Protection Methods

- This vulnerability has been fixed with the update. The vulnerability can be mitigated by updating.



























