Hosting a WordPress blog on Amazon Linux 2

The following procedures will help you install, configure, and secure a WordPress blog on your Amazon Linux instance. This tutorial is a good introduction to using Amazon EC2 in that you have full control over a web server that hosts your WordPress blog, which is not typical with a traditional hosting service.


Prerequisites
Install a LAMP web server on Amazon Linux 2 for Amazon Linux 2. 


The following procedures help you install an Apache web server with PHP and MySQL support on your Amazon Linux instance (sometimes called a LAMP web server or LAMP stack). You can use this server to host a static website or deploy a dynamic PHP application that reads and writes information to a database.

Tasks

Step 1: Prepare the LAMP server
Step 2: Test your Lamp server
Step 3: Secure the database server

Step 1: Prepare the LAMP server
Prerequisites

This tutorial assumes that you have already launched a new instance using the Amazon Linux AMI, with a public DNS name that is reachable from the internet. For more information, see Step 1: Launch an instance. You must also have configured your security group to allow SSH (port 22), HTTP (port 80), and HTTPS (port 443) connections. For more information about these prerequisites, see Authorize inbound traffic for your Linux instances.

To install and start the LAMP web server with the Amazon Linux AMI

Connect to your instance.

To ensure that all of your software packages are up to date, perform a quick software update on your instance. This process may take a few minutes, but it is important to make sure that you have the latest security updates and bug fixes.

The -y option installs the updates without asking for confirmation. If you would like to examine the updates before installing, you can omit this option.
sudo yum update -y

Now that your instance is current, you can install the Apache web server, MySQL, and PHP software packages.
Use the yum install command to install multiple software packages and all related dependencies at the same time.
sudo yum install -y httpd24 php72 mysql57-server php72-mysqlnd

Start the Apache web server.
sudo service httpd start

Use the chkconfig command to configure the Apache web server to start at each system boot.
sudo chkconfig httpd on

The chkconfig command does not provide any confirmation message when you successfully use it to enable a service.
You can verify that httpd is on by running the following command:
chkconfig --list httpd

Add a security rule to allow inbound HTTP (port 80) connections to your instance if you have not already done so. By default, a launch-wizard-N security group was set up for your instance during initialization. This group contains a single rule to allow SSH connections.

Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.

Choose Instances and select your instance.

On the Security tab, view the inbound rules. You should see the following rule:
port :22

Test your web server. In a web browser, type the public DNS address (or the public IP address) of your instance. You can get the public DNS address for your instance using the Amazon EC2 console. If there is no content in /var/www/html, you should see the Apache test page. When you add content to the document root, your content appears at the public DNS address of your instance instead of this test page.

Verify that the security group for the instance contains a rule to allow HTTP traffic on port 80. For more information, see Add rules to a security group.

If you are not using Amazon Linux, you may also need to configure the firewall on your instance to allow thee connections. For more information about how to configure the firewall, see the documentation for your specific distribution.


![Screenshot 2021-10-04 at 12 10 18 AM](https://user-images.githubusercontent.com/91830664/135773066-fa904395-cbcf-4c03-a19b-5fa189397454.png)


To set file permissions
Add your user (in this case, ec2-user) to the apache group.

sudo usermod -a -G apache ec2-user

Test your Lamp server
If your server is installed and running, and your file permissions are set correctly, your ec2-user account should be able to create a PHP file in the /var/www/html directory that is available from the internet.

To test your LAMP web server

Create a PHP file in the Apache document root.

echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php


In a web browser, type the URL of the file that you just created. This URL is the public DNS address of your instance followed by a forward slash and the file name. For example:

http://my.public.dns.amazonaws.com/phpinfo.php

Secure the database server
The default installation of the MySQL server has several features that are great for testing and development, but they should be disabled or removed for production servers. The mysql_secure_installation command walks you through the process of setting a root password and removing the insecure features from your installation. Even if you are not planning on using the MySQL server, we recommend performing this procedure.

To secure the database server

Start the MySQL server.
sudo service mysqld start

Run mysql_secure_installation.

sudo mysql_secure_installation

When prompted, type a password for the root account.

Type the current root password. By default, the root account does not have a password set. Press Enter.

Type Y to set a password, and type a secure password twice. For more information about creating a secure password, see https://identitysafe.norton.com/password-generator/. Make sure to store this password in a safe place.

Setting a root password for MySQL is only the most basic measure for securing your database. When you build or install a database-driven application, you typically create a database service user for that application and avoid using the root account for anything but database administration.

Type Y to remove the anonymous user accounts.

Type Y to disable the remote root login.

Type Y to remove the test database.

Type Y to reload the privilege tables and save your changes.

Install WordPress
Connect to your instance, and download the WordPress installation package.

To download and unzip the WordPress installation package

Download the latest WordPress installation package with the wget command. The following command should always download the latest release.

wget https://wordpress.org/latest.tar.gz

Unzip and unarchive the installation package. The installation folder is unzipped to a folder called wordpress.

tar -xzf latest.tar.gz

To create a database user and database for your WordPress installation

Your WordPress installation needs to store information, such as blog posts and user comments, in a database. This procedure helps you create your blog's database and a user that is authorized to read and save information to it.

Start the database server.

Amazon Linux 2

sudo systemctl start mariadb

Amazon Linux AMI
sudo service mysqld start

Log in to the database server as the root user. Enter your database root password when prompted; this may be different than your root system password, or it might even be empty if you have not secured your database server.

If you have not secured your database server yet, it is important that you do so. For more information, see To secure the MariaDB server (Amazon Linux 2) or To secure the database server (Amazon Linux AMI).

mysql -u root -p

![Screenshot 2021-10-04 at 12 10 18 AM](https://user-images.githubusercontent.com/91830664/135773360-5f971b46-1cf4-401e-b030-1c5af8c5cc34.png)



