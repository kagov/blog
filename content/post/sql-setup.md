---
title: MYSQL setup in a Linux Machine
date: 2017-06-26 09:17:57
tags:
  - mysql
  - aws
---
Setup a MYSQL server in a few minutes. Quite useful for an EC2 instance
<!--more-->

We all want a persistent storage for our application and this post we are going to find out how to setup your mysql server in Linux. This will be very helpful when you are creating a new EC2 instance and want a local mysql server inside your EC2.

## Lets begin

Fire up your terminal and install mysql server by typing

    $ yum install mysql-server  // for CentOS
    $ sudo apt-get install mysql-server // for Ubuntu
Now if you are running ubuntu then the installation will ask you to setup the root password there itself. Type any password and remember it. But if you are running CentOS then we will see how to setup the root password in a moment.

After the installation is complete we need to start the mysql service. Type

    $ service mysqld start  // for CentOS
    $ service mysql start // for ubuntu

## Setting up a root user for CentOS

Since we we did not provide a root password for mysql in CentOS installation, we are going to provide one now.

    $ mysql

This will open the mysql shell

    > use mysql;
    > update user set password=PASSWORD("your password") where User='root';
    > flush privileges;

The above commands selects the database as mysql and updates the user table with your password. This completes the setup of mysql in a linux machine but hang on, AWS will not allow you to access mysql as a root user. Feel free to try that for yourself, if he is allowing then you can skip the next part but if not bear with me.

## Adding a new user

Now we are going to create a user and grant him permissions to the database that we want that user to access.

    > CREATE USER 'new_user'@'%' IDENTIFIED BY 'new_password';

This will create a new user globally. If you want that the user is created only in your local machine then replace % with localhost.

    > GRANT ALL ON your_db_name.* TO 'new_user'@'%';

The above command will grant the new user permission to access the database that you specify in "db_name". If the databae does not exist then create it by using the create schema command and specify the name of the database. Make sure you refresh the shell by typing.

    > flush privileges;

## Importing an existing database to the new instance

If you already have a database backup then you can import all that data with this simple command.

    > mysql -u username -p your_db_name < your_db_dump.sql;

This will prompt you to enter your password that you setup in the installation step.

## Footnotes

The commands that are prefixed with "$" are supposed to be entered in the bash shell and the commands prefixed with ">" are to be typed in the mysql shell.

If you notice that your changes are not being reflected then run the flush privileges command.
