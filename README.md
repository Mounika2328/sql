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
Introduction: 
SQL Injection is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. This might include data belonging to other users, or any other data that the application is able to access. In some situations, an attacker can escalate an SQL injection attack to compromise the underlying server or perform a denial-of-service attack.

1.Open Xampp control panel and start the MySQL which runs on port 3306 and Apache server which listens on 80,443.
2. Configure the Dvwa so that you can access on the localhost
3. Visit localhost [127.0.0.1] in your browser and we should see the Dvwa login page.
4.Login to the Dvwa with the below credentials[username]
5. set Dvwa on low and select SQL injection vulnerability

6.now type 1 we should see the following response

![Picture1](https://user-images.githubusercontent.com/101981586/159578777-93c22fbd-3b43-44f2-b318-e782c5eae77e.png)

  
7. Below is the configuration of the vulnerability
<?php

if( isset( $_REQUEST[ 'Submit' ] ) ) {
    // Get input
    $id = $_REQUEST[ 'id' ];

    switch ($_DVWA['SQLI_DB']) {
        case MYSQL:
            // Check database
            $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";
            $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

            // Get results
            while( $row = mysqli_fetch_assoc( $result ) ) {
                // Get values
                $first = $row["first_name"];
                $last  = $row["last_name"];

                // Feedback for end user
                echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
            }

            mysqli_close($GLOBALS["___mysqli_ston"]);
            break;
        case SQLITE:
            global $sqlite_db_connection;

            #$sqlite_db_connection = new SQLite3($_DVWA['SQLITE_DB']);
            #$sqlite_db_connection->enableExceptions(true);

            $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";
            #print $query;
            try {
                $results = $sqlite_db_connection->query($query);
            } catch (Exception $e) {
                echo 'Caught exception: ' . $e->getMessage();
                exit();
            }

            if ($results) {
                while ($row = $results->fetchArray()) {
                    // Get values
                    $first = $row["first_name"];
                    $last  = $row["last_name"];

                    // Feedback for end user
                    echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
                }
            } else {
                echo "Error in fetch ".$sqlite_db->lastErrorMsg();
            }
            break;
    } 
}

?>
8. As we can see there is no sanitization of the input, we can try to inject malicious payload and retrieve the data from database
9. Try inject “ ‘ ” after 1 in input to confirm the vulnerability on the web app.
 ![Picture2](https://user-images.githubusercontent.com/101981586/159578779-2d7c9247-925b-4daf-826d-1b55f3be5534.png)
10. We saw the MySQL error that confirms the vulnerability
11. Now try to check the no of tables present on the SQL database. To get the tables insert “ order by 5 --+” after 1’
![Picture3](https://user-images.githubusercontent.com/101981586/159578781-172c03db-f73f-4d4e-9e68-d7be4cbf1136.png)
 
12.From this we can conclude that there is no data on table column 5 on the database and so we can keep on decreasing the value until we get some results. Here “order by 2” gave some results as shown below
![Picture4](https://user-images.githubusercontent.com/101981586/159578782-35c74acf-3571-4fea-916e-fc3aa3cc5be9.png)
 
13. Now we must find the vulnerable columns present on the database by inserting the following query “1' union select 1,2 – “
 ![Picture5](https://user-images.githubusercontent.com/101981586/159578784-fbec19c9-706e-4532-bf9e-7665000868b9.png)
 
14. Here in first name and last name we can see 1 and 2 which states the both columns  vulnerable.
15.Now we will try to get the database name and version from SQL data base by using the following query " 1' union select database (), version () -- "

 ![Picture6](https://user-images.githubusercontent.com/101981586/159578785-2f384463-b6eb-4d2e-9193-6689dc8b3775.png)

16. we can see that database name is “Dvwa” and version is “10.4.22-MariaDB”
17.  Now try to get all tables from the information schema tables 
 ![Picture7](https://user-images.githubusercontent.com/101981586/159578786-5968e4eb-396b-4b8f-bd3d-30d19e7cc50a.png)
18. As we can see all table names in second column
19 Now we will retrieve the all the columns “union select 1, column_name from information_schema.columns from table_name=’users’”
20. After that we can get all the users present on the database.




Patch:
1.Check if the input is numeric like “if (is_numeric( $id ))”
2. Always use Limit to avoid this vulnerability as follows $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id' LIMIT 1;

Below is the patch code for the above configuration
<?php

if( isset( $_REQUEST[ 'Submit' ] ) ) {
	// Get input
	$id = $_REQUEST[ is_numeric('id') ];

	switch ($_DVWA['SQLI_DB']) {
		case MYSQL:
			// Check database
			$query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id' LIMIT 1;";
			$result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

			// Get results
			while( $row = mysqli_fetch_assoc( $result ) ) {
				// Get values
				$first = $row["first_name"];
				$last  = $row["last_name"];

				// Feedback for end user
				$html .= "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
			}

			mysqli_close($GLOBALS["___mysqli_ston"]);
			break;
		case SQLITE:
			global $sqlite_db_connection;

			#$sqlite_db_connection = new SQLite3($_DVWA['SQLITE_DB']);
			#$sqlite_db_connection->enableExceptions(true);

			$query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";
			#print $query;
			try {
				$results = $sqlite_db_connection->query($query);
			} catch (Exception $e) {
				echo 'Caught exception: ' . $e->getMessage();
				exit();
			}

			if ($results) {
				while ($row = $results->fetchArray()) {
					// Get values
					$first = $row["first_name"];
					$last  = $row["last_name"];

					// Feedback for end user
					$html .= "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
				}
			} else {
				echo "Error in fetch ".$sqlite_db->lastErrorMsg();
			}
			break;
	} 
}

?>

References:
https://audreybetsy.medium.com/sql-injection-attack-in-dvwa-f6adc95f239b 



