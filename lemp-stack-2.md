# **WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS**

This Project covers the implementation of developing a boilerplate LAMP Stack app from start to finish and deploying to **AWS** (Amazon Web Services). The LAMP Stack is a Technology Stack. A Technology stack comprises of framework/tools that are used in building a software application. The LAMP Acronym stands for Linux, Apache, MySQL, PHP.

- Linux - It is an operating system that is free and open source, and was built from UNIX. We would be creating our AWS EC2 instance on a virtual Server.

- Apache - Apache is a very popular web server software that is used for hosting applications

- MySQL - It is a Database management system that is used for storing data in form of tables(rows, columns). It is a relational database and data must be stored in a structured format.

- PHP - PHP is a modern server side languauge that is used for building web applications.

Let's get started with implementing the LAMP Stack and deploying to AWS.

## **Create an EC2 instance  on your AWS Dashboard**
You have to create a Linux virtual server on AWS. There are several features and services that AWS offer but for this project, I'll be using the ec2 instance. If you're using windows, you have to download a CLI (Command Line Interface) such as Git, Putty, Termius, MobaXterm that is Linux-enabled so you can run basic linux commands. I watched this [video](https://www.youtube.com/watch?v=xxKuB9kJoYM&list=PLtPuNR8I4TvkwU7Zu0l0G_uwtSUXLckvh&index=7) to create a Linux virtual server, also i need to add that the Linux distro we're using is Ubuntu.

When we're done creating the server, we first have to add permissions to our private key using this command.
