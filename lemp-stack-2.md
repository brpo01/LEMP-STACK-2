# **WEB STACK IMPLEMENTATION (LEMP STACK) IN AWS**

This Project covers the implementation of developing a LEMP Stack app from start to finish and deploying to **AWS** (Amazon Web Services). The LEMP Stack is a Technology Stack. A Technology stack comprises of framework/tools that are used in building a software application. The LEMP Acronym stands for Linux, Nginx, MySQL, PHP.

- Linux - It is an operating system that is free and open source, and was built from UNIX. We would be creating our AWS EC2 instance on a virtual Server.

- Nginx - Nginx is a very popular high perfomance web server software that is used for hosting applications

- MySQL - It is a Database management system that is used for storing data in form of tables(rows, columns). It is a relational database and data must be stored in a structured format.

- PHP - PHP is a modern server side languauge that is used for building web applications.

Let's get started with implementing the LEMP Stack and deploying to AWS.

## **Create an EC2 instance on your AWS Dashboard**

You have to create a Linux virtual server on AWS. There are several features and services that AWS offer but for this project, I'll be using the ec2 instance. If you're using windows, you have to download a CLI (Command Line Interface) such as Git, Putty, Termius, MobaXterm that is Linux-enabled so you can run basic linux commands. I watched this [video](https://www.youtube.com/watch?v=xxKuB9kJoYM&list=PLtPuNR8I4TvkwU7Zu0l0G_uwtSUXLckvh&index=7) to create a Linux virtual server, also i need to add that the Linux distro we're using is Ubuntu.

When we're done creating the server, we first have to add permissions to our private key using this command.

![1](https://user-images.githubusercontent.com/47898882/125632371-a7e02c58-c8e3-4593-b40a-65b94be6f6e6.JPG)

If that works out successfully, you can then connect to the server using the ssh command.

![2](https://user-images.githubusercontent.com/47898882/125632377-684f1abe-6ea6-4072-9416-f15b2ee1654c.JPG)

Now that our Virtual Server is up and running we can start building our application.

## **Install Nginx Web Server**

To install the nginx web server, we have to use the _apt_ package manager and thankfully it comes installed with linux.

Use this commands below to update your apt package manager and install nginx

```
$ sudo apt upgrade
$ sudo apt install nginx
```

When that is done installing you can check the status of nginx to verify if it is running as a service using:

```
$ sudo systemctl status nginx
```

When that is run, you need to get an output like this:

![3](https://user-images.githubusercontent.com/47898882/125633048-8b8512b5-e172-4bfd-93ed-2f2ebc1708b8.JPG)

In order to be able to test our nginx server on the browser, we have to allow inbound connections into port 80. Port 80 handles http connections and allows you to connect to the internet over a browser. To achieve this, we have to go into our AWS console and add that configuration

![4](https://user-images.githubusercontent.com/47898882/125615842-422b1b42-fb47-41f8-891b-496470a750d4.JPG)

After that has been configured, we can then test on our browser using our public IP address or DNS name:

`http://<Public-IP-Address>:80 or http://DNS-name:80`

You can check for your IP address on your aws console or better still type out this command on your terminal:

```
$ curl -s http://169.254.169.254/latest/meta-data/public-ipv4
```

If it displays the Nginx Default Page. You're good to go!

## **Install MySQL**

Install SQL using the _apt_ command:

```
$ sudo apt install mysql-server
```

When that is done installing, you have run a security script to remove default settings and secure the server

```
$ sudo mysql_secure_installation
```

When you input this command it'll give you a prompt to create a password and after that is done you can test that mysql works fine using `sudo mysql`

![4](https://user-images.githubusercontent.com/47898882/125633796-65273b8b-2862-4119-889d-eb2928761210.JPG)

## **Install PHP**

You have to install the php package on your server using the _apt_ command. There are also some other php packages you'll need to install such as the `php-mysql` package which allows php to communicate with MySQL and `php-fpm` package that is a program that acts as an intermediary between the php interpreter and the nginx web server. This occurs because by default nginx doesn't directly embed the php interperter to process requests but need it needs `php-fpm` package to do so.

```
$ sudo apt install php-mysql php-fpm
```

If that installs correctly you can check the php version you installed using `php -v`

At this junction, we have installed all the tools in LEMP Stack and they're all fully functional.

## **Configure Nginx to use the PHP Processor**

In order to test our setup with a php script, we have to check if nginx can process requests from php with the help of the 'php-fpm' package

Create a folder in your in the /var/www/html directory using `mkdir`. This directory is the default that has been configured for serving our files.

```
$ sudo mkdir lempserver
```

Assign ownership to the user using `chown` command.

```
$ sudo chown $user:$user lempserver
```

When that is done we have to create our own configuration script in the nginx configuration folder

```
$ sudo vi /etc/nginx/site-avialable/lempserver.conf
```

_vi_ also known as vim is a text editor in linux for writing code. The configuration for the virtual host will be written as below. You can use _i_ to insert, :_wq_ to write(save) into the file and quit.

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
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```

We can activate our configuration by using _ln_ command to link our configuration to /etc/nginx/sites-enabled directory.

```
$ sudo ln -s /etc/nginx/sites-available/lempserver  /etc/nginx/sites-enabled/
```

We also have to disable(unlink) the default configuration using the command below:

```
$ sudo unlink /etc/nginx/sites-enabled/default
```

**Note** - If your configuration in lempserver.conf is not accurate, it'll throw an error when you're testing it on your browser. Make sure the DocumentRoot is correct most especially and is linking to the `/var/www/lempserver` folder we created. You can also test the accuracy of your configuration using `sudo nginx -t`

![5](https://user-images.githubusercontent.com/47898882/125636061-3b83afca-bac0-407c-9faa-7b477510bb80.JPG)

If your config works perfectly go back into your `/var/www/lempserver` folder and create a file called index.html using the _touch_ command.

```
$ cd /var/www/lempserver
$ touch index.html
```

Then type out the following code below:

```
$ sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlemp/index.html
```

What this piece of code will do is redirect the output of that command into the index.html. You can confirm using `cat index.html` to check the file. You can now test this on your browser using:

`http://<Public-IP-Address>:80 or http://DNS-name:80`.
You should see something like this on your browser:

![6](https://user-images.githubusercontent.com/47898882/125636983-e95ecd43-7c04-48a8-921c-85fda87e54b8.JPG)

We can also test that nginx can process php requests by writing a simple php script. In that same /var/www/lempserver directory, create another file called index.php and use _vi_ to enter into the file. When that is done, the following script should be typed below:

```
<?php
phpinfo();
```

After all of this is done, we can test in our browser using
`http://<Public-IP-Address>:80/index.php or http://DNS-name:80/index.php`.

You should see an output like this:
![9](https://user-images.githubusercontent.com/47898882/125628082-c3a68cae-5086-4da6-ba60-69d315bedfd0.JPG)

## **Retriving Data from MySQL Database with PHP**

In this section, we will create a test database, so we can use php to query to database and retrieve data over the nginx server. The default authentication method used by sql is `caching_sha2_authentication` but the `php-mysql` library we installed a while back does not support this method. So before we can create a new user and enable php and mysql to connect, we have to use `mysql_native_password` authentication method.

What we'll do next is login into sql and create a database using the commands below:

```
$ sudo mysql
```

```
mysql> CREATE DATABASE `lemp-database`;
```

We'll then create a user and give them full privileges to that database using the commands below:

```
mysql> CREATE USER 'lemp-user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';

mysql> GRANT ALL ON lemp-database.* TO 'lemp-user'@'%';
```

In order for the user we just created be able to login into their SQL account we must exit the shell using the `exit` command, and then:

```
$ mysql -u lemp-user -p
```

The `-u` flag specifies the user while the `-p` flag specifies the password. If you enter the command correctly, you'll be prompted to input your password, when that is done you'll be logged in.

To test that the database was indeed created and given to the user, you can use the `SHOW DATABASES`
command

![7](https://user-images.githubusercontent.com/47898882/125681449-668d4327-e03c-41fb-9877-3f0e80c06184.JPG)

You should find the database you created called lemp-database and assigined full privileges to user (lemp-user) as shown in the image above.

The next step is to create a table in your database using the command below:

```
mysql> CREATE TABLE lemp-database.todo_list(item_id INT AUTO_INCREMENT,
content VARCHAR(255),
PRIMARY KEY(item_id))
```

You can then insert data into your database using the command below:

```
mysql> INSERT INTO lemp-database.todo-list (content) VALUES ("My first important item");
```

You can add a couple more to see how it works. To confirm the data has been stored in the database. Use the command below `SELECT * FROM lemp-database.todo_list`, you should get the output below:

![8](https://user-images.githubusercontent.com/47898882/125685054-f6dccefc-1801-401b-88d7-e86340d2b9fc.JPG)

After this has been done, we can then create a script in /var/www/lempserver to query the database, retrieve the data and send the response(output) to the browser(client)

```
$ sudo vi /var/www/lempserver/todo_list.php
```

As i said before, vi also known as vim is a text editor in linux for writing code. You can use i to insert, :wq to write(save) into the file and quit. So input the configuration below into the file

```
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
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
```

When that is done, we can test on the browser using:

`http://<Public-IP-Address>/todo-list.php or http://DNS-name:80/todo-list.php`. You should get this output on your browser.

![9](https://user-images.githubusercontent.com/47898882/125686611-9e583110-6498-4cc3-bc1f-afae15f12369.JPG)

So we have successfully built a LEMP stack application and deployed to our AWS ec2 instance.

**This is complete Web Stack Implemenatation for the LEMP Stack**
