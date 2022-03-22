DVWA AND ITS INSTALLATION 
 
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

SQL INJECTION
SQL: Structured Query Language, which is most widely used programming language for managing data held in a relational database.
What is SQL INJECTION (SQLi): SQL Injection is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. This might include data belonging to other users, or any other data that the application is able to access. In some situations, an attacker can escalate an SQL injection attack to compromise the underlying server or perform a denial-of-service attack.
What is the impact of a successful SQL injection attack?
The successful attack can gain access to all the internal information within a database.
Email addresses, usernames, and passwords will be accessed.
The impact of compromising confidentiality on that data goes well beyond the system. In some cases, an attacker can obtain a persistent backdoor into an organization's systems, leading to a long-term compromise that can go unnoticed for an extended period.
SQL INJECTION EXAMPLES:
Some common examples of SQL injection vulnerabilities include:
a)	Retrieving hidden data.
b)	Subverting Application Logic.
c)	UNION Attacks.
d)	Examining the Database.

I am going to use Port swigger lab do demonstrate my vulnerability.

a)	Retrieving hidden data: 

Using Burp suite, accessing the lab in port swigger to demonstrate my topic. 
Using the below link in port swigger.

https://insecure-website.com/products?category=Gifts

We are now trying to find the hidden information or unrevealed information.
Points to know before seeing the hidden data:
Every product displayed under any shopping site has two things in common.
a)	Its category
b)	Is it allowed to release or not? 
eg: the products displayed under gifts we see are 3. The category of those 3 products is same i.e., gifts, and comes under release = 1(which means the products are not hidden and can be seen by the viewer as its released) 
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
But they hide some information by using a SQL query saying release = 0 which means the products are available on the site but are hidden from displaying them to viewer.
 So, In order to display the hidden items I am trying to use a query below:
https://insecure-website.com/products?category=Gifts'--
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1

furthermore, if we want to see all the products (including hidden)
I use the below query:
https://insecure-website.com/products?category=Gifts’ or 1=1 –-
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
The modified query will return all items where either the category is Gifts, or 1 is equal to 1. Since 1=1 is always true, the query will return all items.
b) Subverting Application logic:
the attacker can login to the website by submitting the username as admin’ - -
the query will be like below:
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
password can be entered anything. He can get access to the site as administrator as username.
C) Retrieving data from other database tables:
we must know how many columns the table has. (it has 2 columns username and password in the table called users) 
 First, we login to the shopping site of the port swigger and we intercept the lab in burp suite.
Making the changes in the input under category by giving,
a)	 Category = ‘+UNION + SELECT + NULL, NULL --  (This will send an error as there are 2 columns in the table and we are looking for only one column which cannot happen.) 
  Sending it to the repeater and send to response and we will see there is no response received.
b)	Next am trying to give the input value as Category = ‘+UNION + SELECT + NULL -- instead of Tech + gifts. The request is sent to the response and hence we will receive the response (because, we have included 2 null characters which implies 2 columns)
c)	Get all the usernames and passwords from the user’s table using the following command:
     Category = ‘+UNION + SELECT + username, + password + FROM + users --
And send to response. We will have the list of all the users and passwords displayed.
eg: copy administrator’s password and try logging in the site. We will be logged in as administrator.
d)	Examining the database: 
Category = ‘+ UNION + SELECT + @@version, + NULL# and send to response.
Takes time to receive the response)
Response results in MY SQL version say e.g: 8.0.23
Category = ‘+UNION + SELECT + ‘NULL’, ‘NULL’ #
(The contents of the database of the table that contains the number of columns)
How to detect SQL injection vulnerabilities:
Burp Suite's web vulnerability scanner can rapidly and accurately detect the majority of SQL injection issues. SQL injection can be detected manually by running a series of tests against each application's entry point. This often involves:
	Checking for typos or other anomalies after submitting the single quote character '
	Submitting SQL-specific syntax that evaluates to the entry point's base (original) value, as well as a new value, and checking for systematic discrepancies in the application answers.
	Using Boolean conditions like OR 1=1 and OR 1=2 to see if the application responds differently.
	Using payloads that are designed to cause time delays when executed within a SQL query and looking for discrepancies in response times.
How to prevent SQL injection:
Instead of string concatenation within the query, most occurrences of SQL injection can be avoided by utilising parameterized queries (also known as prepared statements).
Because the user input is concatenated straight into the query, the following code is vulnerable to SQL injection:
String query = "SELECT * FROM products WHERE category = '"+ input + "'";
Statement statement = connection.createStatement();
ResultSet resultSet = statement.executeQuery(query);

The above code can be modified and rewritten in a way that prevents the user input from interfering with the query structure:


PreparedStatement statement = connec-tion.prepareStatement("SELECT * FROM products WHERE category = ?");
statement.setString(1, input);
ResultSet resultSet = statement.executeQuery();



