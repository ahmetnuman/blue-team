## Discovering the Web Shell

#### Finding the Shell Installed on the Server

Shell is a script that transfers the rights to the installer on the server it is installed on. c99,r57 is one of the most popular shells known. In this section, we will talk about PHP shells.

If we take a look at the shell example, which allows running a simple command, it is seen that it runs the system() function outside of ordinary commands.

Below is a screenshot of a simple PHP shell.

![Image](/img/shell1.png)

In this case, if the files that call the system() function in the server are extracted, the malicious file can be found more easily among hundreds of files.

With the command below, all files that call the system function under the /var/www directory are scanned.

Command : `grep -Rn "system *(" /var/www`

When a different PHP shell is examined, it is seen that the shell_exec() function is preferred instead of the system function.

![Image](/img/shell2.png)

As mentioned before, shell_exec() and eval() functions also stand out when paying attention to the different functions used.

In other words, the shell_exec and eval functions should also be scanned while searching for the shell.

`grep -RPn "(system|shell_exec|eval) *\(" /var/www`

![Image](/img/shell3.png)

When PHP shells are examined, it is observed that they generally contain parameters such as passthru, shell_exec, system, phpinfo, base64_decode, edoced_46esab, chmod, mkdir, fopen, fclose, readfile, php_uname, and eval. For this, it is possible to find the shells in the server in a scan performed with the command given below.

`grep -RPn "(passthru|shell_exec|system|phpinfo|base64_decode|chmod|mkdir|fopen|fclose|readfile|php_uname|eval) *\(" /var/www`

## Shell Hide Methods

In order not to be caught in the firewall by the system administrator or while uploading files, attackers apply some shell hiding methods. In this section, we will discuss these methods.

#### Remote Summoning

In this method, the shell is not actually hosted on the target server. The codes belonging to the shell from another address are pulled and run on the target server.

![Image](/img/shell4.png)

#### Encrypted Code

By encrypting the code of the shell, the firewall, if any, is tried to be bypassed it.

![Image](/img/shell5.png)

#### Hiding Picture

Here, the malicious code is placed in the exif information of any image, and the code is run by reading the exif information of the image with PHP.

First, the desired codes are placed in the exif information of the image with the help of exiftool. The screenshot of the image whose "exif" information has been changed is given below:

![Image](/img/shell6.png)

![Image](/img/shell7.png)


Then PHP code is written to read the exif information from the image.

![Image](/img/shell8.png)

Firewalls can be bypassed with such hiding methods. When it comes to finding the shell, it is not very difficult by searching the files on the server one by one with the help of grep. But apart from the functions mentioned earlier, it will be necessary to scan the exif_read_data() and preg_replace() functions.

`grep -RPn "(passthru|shell_exec|system|phpinfo|base64_decode|chmod|mkdir|fopen|fclose|readfile|php_uname|eval|preg_replace|exif_read_data) *\(" /var/www`

![Image](/img/shell9.png)

As can be seen above, the encrypted shell, the hidden shell in the picture, and the remotely called shells have all been located.

## Hands-On Practise

Q1 -) There is a PHP shell on the server. What is the filename of this shell? 

S1 -) `sudo grep -RPn "(passthru|shell_exec|system|phpinfo|base64_decode|chmod|mkdir|fopen|fclose|readfile|php_uname|eval) *\(" /var/www`

![Image](/img/shell10.png)

run.php

Q2 -) Is there a webshell hidden in the image on the server?

S2 -) `grep -RPn "(passthru|shell_exec|system|phpinfo|base64_decode|chmod|mkdir|fopen|fclose|readfile|php_uname|eval|preg_replace|exif_read_data) *\(" /var/www`

![Image](/img/shell11.png)










