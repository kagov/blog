---
title: Reverse proxy using Httpd
date: 2017-05-13 17:51:11
tags:
  - httpd
  - aws
---



Httpd stands for HTTP Deamon and sits on top of your webserver. In this post
<!--more-->we are going to see how to configure reverse proxy for your back-end application.

Imagine you are running a flask application on 5000 port and you do not want to expose that port to the clients then you have to install a reverse proxy server to listen to the HTTP calls (usually port 80) and forward them to 5000.

The Apache's Httpd provides a simple way to do that.

### Install Httpd

Installing should not pose any difficulties.

    $ sudo apt-get install httpd  // Ubuntu
    $ yum install httpd //CentOS or Amazon instance

The program is installed inside /etc/httpd. The directory will contain a httpd.conf file and a conf.d directory. It may also contain other directories but we are mostly going to deal with these two only.

###  Configuration

The httpd.conf file is the main file that has all the configurations, feel free to look inside the verbose file. But apart from that the proxy server will also load all the .conf files present inside the conf.d directory. So cd into conf.d and create your own conf file

    $ cd conf.d
    $ vi flask.conf   // you can name your file anything you want

Now we are going to write a directive inside this file which will listen to the HTTP calls and redirect to the the specified port.

{{< codeblock "flask.comf" "apacheconf"  >}}
<VirtualHost *:80>
    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>

    ProxyPass / http://localhost:5000/ retry=0
    ProxyPassReverse / http://localhost:5000/
    ProxyPreserveHost on

    ErrorLog /var/log/httpd/flask-error_log
    Timeout 300
    ProxyTimeout 300

</VirtualHost>
{{< /codeblock  >}}

So whenever there is a request for port 80 then it will be redirected to the 5000 port. The error log folder is specified inside the directory along with the request timeout and proxy timeout. Make that that you restart the service by typing

    $ sudo service restart httpd

### Further reading

If you want to configure this inside your EC2 instance then after doing the above steps make sure that you add a security group inside your AWS console.


![](/images/security_group_1.JPG)

After creating a security group go to your EC2 dashboard select the instance and assign the security group for this instance.
