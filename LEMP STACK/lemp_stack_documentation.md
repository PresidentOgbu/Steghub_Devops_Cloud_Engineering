## WEB STACK IMPLEMENTATION (LEMP STACK) IN AWS

### Introduction:

__The LEMP stack is a popular open-source web development platform that consists of four main components: Linux, Nginx, MySQL, and PHP (or sometimes Perl or Python). This documentation outlines the setup, configuration, and usage of the LEMP stack.__


## Step 0: Prerequisites

__1.__ EC2 Instance of t3.micro type and Ubuntu 24.04 LTS (HVM) was launched in the us-north-1 region using the AWS console.

![Launch Instance](.\images\create_ec2.png)
![Launch Instance](.\images\ec2_details.png)
__2.__ Created SSH key pair named __lemp-key__ to access the instance on port 22

__3.__ The security group was configured with the following inbound rules:

- Allow traffic on port 80 (HTTP) with source from anywhere on the internet.
- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.
- Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.

![Security Rules](./images/security_rules.png)

__4.__ The default VPC and Subnet was used for the networking configuration.

![Default Network](./images/default_network.png)

__5.__ The private ssh key that got downloaded was located, permission was changed for the private key file and then used to connect to the instance by running
```
chmod 400 lemp-key.pem
```
```
ssh -i "lemp-key.pem" ubuntu@16.170.246.55
```
Where __username=ubuntu__ and __public ip address=16.170.246.55__

![Connect to instance](./images/ssh_access.png)


## Step 1 - Install nginx web server

__1.__ __Update and upgrade the server’s package index__

```
sudo apt update
sudo apt upgrade -y
```
![Update Packages](./images/update_ec2.png)

__2.__ __Install nginx__

```
sudo apt install nginx -y
```
![Install Nginx](./images/install_nginx.png)

__3.__ __Verify that nginx is active and running__

```
sudo systemctl status nginx
```
If it's green and running, then nginx is correctly installed
![Nginx Status](./images/nginx_status.png)

__4.__ __Access nginx locally on the Ubuntu shell__

```
curl http://127.0.0.1:80
```
![Local URL](./images/curl_access.png)

__5.__ __Test with the public IP address if the Nginx server can respond to request from the internet using the url on a browser.__

```
http://16.170.246.55
```
![Nginx Default Page](./images/nginx_default.png)

This shows that the web server is correctly installed and it is accessible throuhg the firewall.

__6.__ __Another way to retrieve the public ip address other than check the aws console__

```
curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```

![Public IP with curl](./images/curl_authorized.png)


## Step 2 - Install MySQL

__1.__ __Install a relational database (RDB)__

MySQL was installed in this project. It is a popular relational database management system used within PHP environments.
```
sudo apt install mysql-server
```
![Install MySQL](./images/install_mysql.png)

__2.__ __Log in to mysql console__
```
sudo mysql
```
This connects to the MySQL server as the administrative database user __root__ infered by the use of __sudo__ when running the command.

![MySQL console](./images/mysql_shell.png)


Exit the MySQL shell
```
exit
```

__4.__ __Run an Interactive script to secure MySQL__

The security script comes pre-installed with mysql. This script removes some insecure settings and lock down access to the database system.
```
sudo mysql_secure_installation
```
![Change root password](./images/secure_mysql.png)

__5.__ __After changing root user password, log in to MySQL console.__

A command prompt for password was noticed after running the command below.
```
sudo mysql -p
```
![MySQL login with password](./images/mysql_console.png)

Exit MySQL shell
```
exit
```

## Step 3 - Install PHP

__1.__ __Install php__

Install php-fpm (PHP fastCGI process manager) and tell nginx to pass PHP requests to this software for processing. Also, install php-mysql, a php module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

The following were installed:
- php-fpm (PHP fastCGI process manager)
- php-mysql

```
sudo apt install php-fpm php-mysql -y
```
![Install PHP](./images/install_php.png)


## Step 4 - Configure nginx to use PHP processor

__1.__ __Create a root web directory for your_domain__

```
sudo mkdir /var/www/projectLEMP
```
![Web root dir](./images/mkdir_root_dir.png)

__2.__ __Assign the directory ownership with $USER which will reference the current system user__

```
sudo chown -R $USER:$USER /var/www/projectLEMP
```
![Change owner](./images/change_owner.png)

__3.__ __Create a new configuration file in Nginx’s “sites-available” directory__.

```
sudo nano /etc/nginx/sites-available/projectLEMP
```
Paste in the following bare-bones configuration:

```
server {
  listen 80;
  server_name projectLEMP www.projectLEMP;
  root /var/www/projectLEMP;

  index index.html index.htm index.php;

  location / {
    try_files $uri $uri/ =404;
  }

  location ~ \.php$ {
    include snippets/fastcgi-php.conf;
    fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
  }

  location ~ /\.ht {
    deny all;
  }
}
```

![Nginx config](./images/nginx_config.png)

### Here’s what each directives and location blocks does:

- __listen__ - Defines what port nginx listens on. In this case it will listen on port 80, the default port for HTTP.

- __root__ - Defines the document root where the files served by this website are stored.

- __index__ - Defines in which order Nginx will prioritize the index files for this website. It is a common practice to list index.html files with a higher precedence than index.php files to allow for quickly setting up a maintenance landing page for PHP applications. You can adjust these settings to better suit your application needs.

- __server_name__ - Defines which domain name and/or IP addresses the server block should respond for. Point this directive to your domain name or public IP address.

- __location /__ - The first location block includes the try_files directive, which checks for the existence of files or directories matching a URI request. If Nginx cannot find the appropriate result, it will return a 404 error.

- __location ~ \.php$__ - This location handles the actual PHP processing by pointing Nginx to the fastcgi-php.conf configuration file and the php7.4-fpm.sock file, which declares what socket is associated with php-fpm.

- __location ~ /\.ht__ - The last location block deals with .htaccess files, which Nginx does not process. By adding the deny all directive, if any .htaccess files happen to find their way into the document root, they will not be served to visitors.

__4.__ __Activate the configuration by linking to the config file from Nginx’s sites-enabled directory__

```
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
![Link config](./images/sys_link.png)
This will tell Nginx to use this configuration when next it is reloaded.

__5.__ __Test the configuration for syntax error__

```
sudo nginx -t
```
![Test syntax](./images/test_syntax.png)

__6.__ __Disable the default Nginx host that currently configured to listen on port 80__

```
sudo unlink /etc/nginx/sites-enabled/default
```
![Disable nginx default](./images/disable_default_nginx.png)

__7.__ __Reload Nginx to apply the changes__
```
sudo systemctl reload nginx
```
![Reload nginx](./images/reload_nginx.png)

__8.__ __The new website is now active but the web root /var/www/projectLEMP is still empty. Create an index.html file in this location so to test the virtual host work as expected.__

```
sudo echo ‘Hello LEMP from hostname’ $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) ‘with public IP’ $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
![Site content](./images/site_content.png)

#### Open the website on a browser using IP address
```
http://16.170.246.55
```
![Site with ip-address](./images/site_IP.png)

#### Open it with public dns name (port is optional)
```
http://<public-DNS-name>:80
```
![Site with dns name](./images/site_dns.png)


This file can be left in place as a temporary landing page for the application until an index.php file is set up to replace it. Once this is done, remove or rename the index.html file from the document root as it will take precedence over index.php file by default.

The LEMP stack is now fully configured.
At this point, the LEMP stack is completely installed and fully operational.


## Step 5 - Test PHP with Nginx

Test the LEMP stack to validate that Nginx can handle the .php files off to the PHP processor.

__1.__ __Create a test PHP file in the document root. Open a new file called info.php within the document root.__

```
sudo nano /var/www/projectLEMP/info.php
```
Past in:
```
<?php
phpinfo();
```
__2.__ __Access the page on the browser and attach /info.php__
```
http://16.170.246.55/info.php
```
![PHP page](./images/php_page.png)

After checking the relevant information about the server through this page, It’s best to remove the file created as it contains sensitive information about the PHP environment and the ubuntu server. It can always be recreated if the information is needed later.
```
sudo rm /var/www/projectLEMP/info.php
```

## Step 6 - Retrieve Data from MySQL database with PHP

### Create a new user with the mysql_native_password authentication method in order to be able to connect to MySQL database from PHP.

Create a database named todo_database and a user named todo_user

__1.__ __First, connect to the MySQL console using the root account.__
```
sudo mysql -p
```
![MySQL console](./images/mysql_console.png)

__2.__ __Create a new database__
```
CREATE DATABASE todo_database;
```
![Create database](./images/create_db.png)

__3.__ __Create a new user and grant the user full privileges on the new database.__
```
CREATE USER 'todo_user'@'%' IDENTIFIED WITH mysql_native_password BY 'Admin123$';

GRANT ALL ON todo_database.* TO 'todo_user'@'%';
```
![Create user](./images/create_user_and_privilege.png)
```
exit
```
__4.__ __Login to MySQL console with the user custom credentials and confirm that you have access to todo_database.__

```
mysql -u todo_user -p

SHOW DATABASES;
```
![Show database](./images/show_db.png)

The -p flag will prompt for password used when creating the example_user

__5.__ __Create a test table named todo_list__.

From MySQL console, run the following:
```
CREATE TABLE todo_database.todo_list (
  item_id INT AUTO_INCREMENT,
  content VARCHAR(255),
  PRIMARY KEY(item_id)
);
```
__6.__ __Insert a few rows of content to the test table__.
```
INSERT INTO todo_database.todo_list (content) VALUES ("My first important item");

INSERT INTO todo_database.todo_list (content) VALUES ("My second important item");

INSERT INTO todo_database.todo_list (content) VALUES ("My third important item");

INSERT INTO todo_database.todo_list (content) VALUES ("and this one more thing");
```
![Insert rows](./images/insert_rows.png)

__7.__ __To confirm that the data was successfully saved to the table run:__
```
SELECT * FROM todo_database.todo_list;
```
![Query table](./images/query_table.png)
```
exit
```

### Create a PHP script that will connect to MySQL and query the content.

__1.__ __Create a new PHP file in the custom web root directory__
```
sudo nano /var/www/projectLEMP/todo_list.php
```
The PHP script connects to MySQL database and queries for the content of the todo_list table, displays the results in a list. If there’s a problem with the database connection, it will throw an exception.

Copy the content below into the todo_list.php script.
```
<?php
$user = "todo_user";
$password = "Admin123$";
$database = "todo_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
?>
```
![PHP Script](./images/php_script.png)

__2.__ __Now access this page on the browser by using the domain name or public IP address followed by /todo_list.php__

```
http://16.170.246.55/todo_list.php
```

__Access this page on the browser by using the domain name followed by /todo_list.php__

![site with dns](./images/php_site_dns.png)


### Conclusion

The LEMP stack provides a robust platform for hosting and serving web applications. By leveraging the power of Linux, Nginx, MySQL (or MariaDB), and PHP, developers can deploy scalable and reliable web solutions.

