DVWA AND ITS INSTALLATION 
DVWA: DVWA stands for Damn Vulnerable Web Application.
It is basically a PHP/SQL web Application. It is a software project that intentionally includes security vulnerabilities for security professionals to test their skills and tools and understand the process of securing web applications.
Steps to download and install DVWA on your windows:
1)	Before Installing DVWA, make sure you download and Install XAMPP Control Panel.
XAMPP Control Panel: It is a management tool(server) that provides the suitable environment for testing MYSQL, PHP, Apache on the computer. It is a free and open-source PHP development environment developed by Apache.

If it is not there in your system, Use the below link to download XAMPP and follow the options prompted accordingly:
https://www.apachefriends.org/de/index.html

2)	Download DVWA using the below link:
 DVWA - Damn Vulnerable Web Application
Select Download that can be found if you open the page and scroll down.
 
3)	Once downloaded, click on the downloaded file in your computer and right click on it and click on extract files and save the files on the desktop by choosing the saving options.
4)	Now, Go to XAMPP that is downloaded previously, and click on explorer, it opens a new tab. Click on htdocs found in the drop-down list of that tab and copy the extracted files that are saved on the desktop and paste it in the htdocs folder.
5)	Now go back to XAMPP tab and click on start action found next to Apache, MYSQL.
6)	Go to the previous tab where you found htdocs. Now click on config. It opens a folder named “config.inc.php.dist”. Click on it, where you will be seeing a notepad having some variables. 
	$DVWA[ "db_user" ] = "dvwa";  to $DVWA[ "db_user" ] = "root";
 $DVWA[ "db_password"] = "p@ssw0rd"; to  $DVWA[ "db_password"] = " ";            
            
 Save and close the notepad after making changes.
7)	Open control Panel and click on show hidden files and folders under file explorer options and uncheck “Hide extensions to known types”. Click on apply and save the changes and close the control panel.
8)	Change the name of “config.inc.php.dist folder to “config.inc.php”. click on Ok and close the tab.
9)	Open Command prompt and type “ipconfig” to get you system IP address. Copy and search with that IP address in the browser.
10)	You will be prompted to DVWA page asking for username and password.
11)	Type admin as username and password as password.
12)	You will be logged in as admin into DVWA page on your IP address.
13)	DVWA installed successfully.



