## PROJECT-1---LAMP-STACK-IMPLEMENTATION

# CREATING AN AWS ACCOUNT AND SPINNING A LINUX SERVER
* Create an AWS Account
* Create an IAM user with programmatic access
* Create a key pair certificate and download it
* Spin up an ec2 instance
* run chmod 400 on The key pair downloaded
* Connect to your ec2 instance over the ssh network.

![image](https://github.com/user-attachments/assets/730cc677-0a55-4bfa-895a-cc2f46d7aa97)
 
THE ACTUAL WORK
-----------------------------------------------------------------------------------------
-----------------------------------------------------------------------------------------
# INSTALLING APACHE AND UPDATING THE FIREWALL

* Always run this command when starting a new server
  ```
    sudo apt update -y
  
  ```

![image](https://github.com/user-attachments/assets/c95d37ea-623b-44f2-bae3-955bdfdc5296)

* Install apache on your server

  ```
    sudo apt install apache2 -y
  
  ```
  
![image](https://github.com/user-attachments/assets/c9e005e7-1a36-4eb8-a249-3de32e452eeb)

* check if your apache server is running without any errors

  ```
    sudo systemctl status apache2

  ```

![image](https://github.com/user-attachments/assets/c4d77f39-950a-4192-8daa-139f56c6bc61)

* Try visiting your site using http://<ip address>:80

![image](https://github.com/user-attachments/assets/35e093a0-3889-44a7-a083-0453258f86e1)

* Site cannot be reached - this is because I have not opened port 80 in my security group (firewall)

# STEPS TO OPEN A NEW PORT IN THE SECURITY GROUPS

* Navigate to the ec2 instance in the aws console and click the security tab

<img width="998" alt="image" src="https://github.com/user-attachments/assets/d6ca0628-834d-4a92-8ae2-dd79546434d3">

* Click on the security group link associated to the ec2 instance

<img width="755" alt="image" src="https://github.com/user-attachments/assets/abe10677-d8cd-4da5-93b3-30ed476c7aa1">

* Edit the inbound rule of the security group

<img width="1120" alt="image" src="https://github.com/user-attachments/assets/d646f5ab-91b9-4f07-a85d-c2ece1ee6f0a">

* Add a rule of type http, port 80 and source anywhere and save

<img width="1395" alt="image" src="https://github.com/user-attachments/assets/b3d4ef70-1a0e-4536-82ce-efe338743046">

* Refresh the errored page to reveal the apache2 landing page

<img width="937" alt="image" src="https://github.com/user-attachments/assets/398f9708-92ea-42be-b4d7-3e12bc91fe2a">

# INSTALLING MYSQL

```
  sudo apt install mysql-server -y

```
![image](https://github.com/user-attachments/assets/fd54a096-4809-4fbc-93f5-261ab4e4e3fd)

* Sudo mysql

![image](https://github.com/user-attachments/assets/ab143f38-0eb1-4a2a-942b-97f9d637a2dc)


* Set the password for the root user

```
  ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
  FLUSH PRIVILEGES;
  
```
![image](https://github.com/user-attachments/assets/948de4ef-6cc6-42f7-acf7-3436b0c6190a)

* Now try to login with the newly created credentials

![image](https://github.com/user-attachments/assets/3ce04b79-92ac-47e8-91f8-1ccb50e34a64)

# INSTALLING PHP

* run sudo apt install php libapache2-mod-php php-mysql

```
  sudo apt install php libapache2-mod-php php-mysql

```


- php - display dynamic content

- hp-mysql - Allow php to communicate with mysql server

- libapache2-mod-php - will enable apache to handle php files


![image](https://github.com/user-attachments/assets/6f830dd3-e3cb-4b8b-8c8b-45d859698ef7)

* When complete, check the php version

```
  php -v

```

![image](https://github.com/user-attachments/assets/ebac62b9-663e-46b8-833d-86d6a82e5cca)


# CREATING VIRTUAL HOSTS FOR WEBSITES


* run the following commands

  ```
    sudo mkdir /var/www/projectlamp
    sudo chown -R $USER:$USER /var/www/projectlamp #assign the ownership of the directory to the current user of the system.
    sudo vi /etc/apache2/sites-available/projectlamp.conf

  ```

![image](https://github.com/user-attachments/assets/b7f78bc2-870e-4164-9f3a-97349d88d805)

* Save the file and quit

* run the following commands

```
  
  sudo a2ensite projectlamp #to enable the virtualhost created
  sudo a2dissite 000-default #to disable the default site
  sudo apache2ctl configtest #check syntax error in config files
  sudo systemctl reload apache2 #to reload the apache server

```
![image](https://github.com/user-attachments/assets/c5f618da-6ca5-4613-9c9d-ae2106b11843)

* But the /var/www/projectlamp is empty so we create an html file to display

```

  sudo vi /var/www/projectlamp/index.html

```

* Paste the content

```
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <title>Title of the document</title>
      </head>
      <body>
    
        <h1>Hello and Welcome! It works!!</h1>
    
      </body>
    </html>

```

# ENABLE PHP ON THE WEBSITE

* run sudo vi /etc/apache2/mods-enabled/dir.conf

* paste the content

```
 <IfModule mod_dir.c>
    #Change this:
    #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
    #To this:
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule> 

```
Original Configuration:

```
  DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm

```

Explanation: 

This directive tells Apache to look for files in the listed order when a user requests a directory. If the directory contains an index.html, Apache serves that. If not, it looks for index.cgi, and so on.

Modified Configuration:

```
  DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm

```

Explanation: 

By moving index.php to the beginning, you're telling Apache to prioritize index.php over other index files. This means that if a directory contains both an index.php and an index.html, Apache will serve the index.php file first.

Reason for Change:

On many web servers (especially those running PHP applications), PHP files such as index.php often serve as the main entry point for web applications. By moving index.php to the front of the DirectoryIndex list, you're ensuring that PHP-driven content is prioritized.

* Restart Apache

```
  sudo systemctl restart apache2

```


* Create the file /var/www/projectlamp/index.php

```
  sudo vi /var/www/projectlamp/index.php

```

* Paste the content

```
  <?php
  phpinfo();

```

The phpinfo() function in PHP outputs detailed information about the current state of PHP, including:
- Installed PHP version
- PHP configuration settings
- Server environment details
- Loaded PHP modules and extensions
- Environment variables

Purpose:
The phpinfo() function is often used for troubleshooting, testing the PHP installation, or viewing the configuration of a PHP server.

* Save changes and close

Visit the site again
Congratulation I now have php configured on my server


