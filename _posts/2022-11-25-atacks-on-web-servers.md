## Attacks on Web Servers

Attacks on web servers are usually caused by the vulnerability of the web server itself, the vulnerabilities of the application server, or the web application that has not taken adequate security measures.

## Application Servers

In this section, we will examine the security vulnerabilities arising from application servers such as tomcat, jboss, glassfish.

#### Tomcat Application Server

The vulnerability is caused by the server using mod_jk and the application server allowing the URL addresses sent by the client to be decoded. The purpose here is to use the directory traversal vulnerability by encoding the ".." parameter twice.

“..” → “%2e” →“%252e”

The page cannot be accessed when the "/manager/html" path of the target address is accessed.
However, when trying to go to the “/examples/jps/%252e%252e/%252e%252e/manager/html” path , the login panel is encountered.

![Image](/img/appsrv1.png)

After trying the default user name and password, access to the admin panel is provided. Before the prepared webshell is loaded, “/examples/jsp/%252e%252e/%252e%252e/” is added to the beginning of the action part of the deploy button. After the process, the webshell is loaded.

The screenshot of the action part of the Deploy button is given below:

![Image](/img/appsrv2.png)

Then, the desired commands can be run by going to the "/examples/jsp/%252e%252e/%252e%252e/test" path.
The screenshot of the path with Webshell is given below:

![Image](/img/appsrv3.png)

#### Log Records

Requests made to the login panel are listed with the command below. When the requests were examined, it was seen that the address was reached with the URL encode.

Command: `cat access.log | grep manager/html | grep 200`

The relevant screenshot is given below:

![Image](/img/appsrv4.png)

When filtering the logs with the IP address of the attacker reaching the panel; The requests sent by the attacker were examined. The POST request sent is noteworthy.
With the following command, all 200 http response code requests of the target are listed.
Command: cat `access.log | grep 192.168.68.1 | grep 200`

![Image](/img/appsrv5.png)

Using the Wireshark filter below, the relevant request is found and examined.
Wireshark filter: ip.src == 192.168.68.1 && http.request.method == POST

![Image](/img/appsrv6.png)

As seen in the screenshot above, the attacker installed the test.war file on the system.

#### Protection Methods

The vulnerability can be avoided by updating “mod_jk”.

## Glassfish 

This section will discuss CVE-2011-0807 and the remote code execution vulnerability.

The panel is accessed using various methods such as authentication bypass, default user name, and password, and remote access is provided by uploading a malicious file to the system. The target system has Sun GlassFish Enterprise Server 2.1 and Java System Application Server 9.1. In this case, the target system is exploited using the "exploit/multi/http/glassfish_deployer" exploit in the Metasploit Framework.

First of all, the target is scanned with nmap and it is tried to determine the open ports on the system and what the ports are used for.

![Image](/img/glass1.png)

As seen in the image above, GlassFish 2.1 is running on the target system. Exploits created for GlassFish are searched with the help of msfconsole.

![Image](/img/glass2.png)

The exploit is selected with the “use” command and necessary adjustments are made. The settings for the exploit are given in the screenshot below:

![Image](/img/glass3.png)

The exploit is run by issuing the "run" command.

![Image](/img/glass4.png)

After the exploit is run, our meterpreter session is prepared and we can run commands on the target.
Below is a screenshot showing that the Meterpreter session is active and the command is executable on the target.

![Image](/img/glass5.png)

#### Log Records

With the "netstat -an" command, the addresses that the system communicates with are displayed and it is seen that it is communicating with an unrecognized address on port 4444.

![Image](/img/glass6.png)

When the network traffic is examined, it is seen that the attacker sent three different GET requests. Relevant requests are given in the screenshot below:

![Image](/img/glass7.png)

After GET requests, lots of TCP traffic occurs over 4444 ports. However, since Metasploit encrypts the data with AES, we cannot learn which commands the attacker executed over the network traffic.

#### Protection Methods

- Not leaving the username and password as default.
- Installing updates

## Jboss

In this section, the remote code execution vulnerability running in Jboss AS versions 3,4,5, and 6 will be examined. The target system is running Jboss 6 on Ubuntu 14.04. Exploit ID number 36575 in exploit-db;

- https://www.exploit-db.com/exploits/36575/

The vulnerability will be exploited.
After the exploit is downloaded, the target address and port number are specified and the attack is started with command below.

`python 36575.py http://192.168.2.105:8080`

![Image](/img/jboss1.png)

Then the exploit is performed and the shell appears on the screen. "Whoami" and "uname -a" commands are run on this screen.

![Image](/img/jboss2.png)

## Log Records

When the HTTP requests to the server are examined, parameters such as id, whoami, uname -a draw attention.

![Image](/img/jboss3.png)

When the details of the relevant packets are examined, the response returned by the server and the address of the request is seen.

![Image](/img/jboss4.png)

As seen in the records, requests are made to "/jbossass/jbossass.jps" path.
The file where the request is made is searched on the server with the command below:

`find /opt/jboss-6.0.0.Final/ -type f -name "jbossass.jsp"`

![Image](/img/jboss5.png)

Below is a screenshot of the source code of the "jbossass.jsp" file. When the related file is examined, as seen that it is a webshell.

![Image](/img/jboss6.png)

#### Protection Methods

- Upgrade to JBoss EAP 7
- Running software with an unauthorized user

## Hands On Practise

Q1 -) The attacker with the IP address “91.93.236.194” made various XSS attempts on the Apache2 server. On what September day happened this attack?

S1 -) `sudo cat /var/log/apache2/access.log | grep 91.93.236.194 | grep script`

![Image](/img/hackedwebserverlab1.png)

27

Q2 -) What is the name of the attack that the IP address “156.146.59.9” tried on the Apache2 web server?

`sudo cat /var/log/apache2/access.log | grep 156.146.59.9`

![Image](/img/hackedwebserverlab2.png)

xss

Q3 -) What is the User Agent information of the POST request sent to the Apache2 web server on “27/Sep/2022 10:56:39“?

`sudo cat /var/log/apache2/access.log | grep 27/Sep | grep POST`

![Image](/img/hackedwebserverlab3.png)

PostmanRuntime/7.29.2
















