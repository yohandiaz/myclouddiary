---
layout: post
title:  "EC2 Instance Bootstrapping"
date:   2023-02-17 04:45:00 +0000
categories: AWS EC2 WEBAPP
---

# EC2 Instance Bootstrapping

## Introduction:

Welcome again to MyCloudDiary, in today's article, we will be discussing EC2 instance bootstrapping, an essential process for configuring and customizing your Amazon Web Services (AWS) EC2 instances.

When setting up an EC2 instance, there are often several tasks that need to be performed, such as installing software, setting up security measures, and configuring network settings. The process of performing these tasks automatically when an instance is launched is known as bootstrapping.

In this article, we will cover the basics of EC2 instance bootstrapping, including the benefits of automating this process, the various methods available for performing it, and some best practices to follow when implementing it. We will also provide some practical examples of how to bootstrap EC2 instances, demonstrating how to streamline your instance setup and management, and save time and resources in the process.

Whether you're an experienced AWS user or just getting started with cloud computing, understanding the fundamentals of EC2 instance bootstrapping is an essential skill. So, let's dive in and explore this topic in more detail.

## Technologies description:

**Amazon Elastic Compute Cloud (EC2):**  is a web service that provides resizable compute capacity in the cloud. With EC2, users can launch and manage virtual server instances, which allows them to run applications and services without having to invest in and maintain their own physical hardware. EC2 instances can be scaled up or down as needed, and users only pay for the compute resources they actually use.

## Pre-requisites:

I will be using the OS Ubuntu 22.04 LTS for this project but it can be followed in nearly the most common Operating Systems distributions such as Windows, Mac OS, and the different distros of Linux.

This guide can be followed using the free tier of AWS, however you will need an AWS account.

## Guide:

In this guide we will automate the installation of an apache2 server, a MySql Server as well as a initial configuration for the apache2 website adding the metadata information of the instance.

For this we will use a custom script that will run just after launching the instance.

### Launching EC2 Instance

Login into the AWS Console and go to the EC2 service page.

 - Click on Launch instances
 - Select Ubuntu, then for the image select Ubuntu Server 22.04 LTS.
 - Select t2.micro in the instance type as it is free tier elegible.
 - In this time we will not need any key pair so select "Proceed without a key pair"
 - Then click on Advanced details, scroll down to User Data and there we will add our custom script to run, find this script below.
 
```sh
#!/bin/bash
sudo apt-get update -y
sudo apt-get install apache2 unzip -y
sudo systemctl enable apache2
echo '<html><h1>Bootstrap Demo</h1><h3>Availability Zone: ' > /var/www/html/index.html
curl http://169.254.169.254/latest/meta-data/placement/availability-zone >> /var/www/html/index.html
echo '</h3> <h3>Instance Id: ' >> /var/www/html/index.html
curl http://169.254.169.254/latest/meta-data/instance-id >> /var/www/html/index.html
echo '</h3> <h3>Public IP: ' >> /var/www/html/index.html
curl http://169.254.169.254/latest/meta-data/public-ipv4 >> /var/www/html/index.html
echo '</h3> <h3>Local IP: ' >> /var/www/html/index.html
curl http://169.254.169.254/latest/meta-data/local-ipv4 >> /var/www/html/index.html
echo '</h3></html> ' >> /var/www/html/index.html
sudo apt-get install mysql-server -y
sudo systemctl enable mysql
```

Then click Launch instance

After the instance is running, select it and connect to the instance using EC2 Connect.

Once you are into the instance run the following commands to check that the script has run succesfully.

```sh
sudo systemctl status apache2

sudo systemctl status mysql
```

Both of these commands should show that the services are installed and running.

Now to check that the metadata information has been correctly collected and the web server is running copy the public ipv4 address of the instance and go to the browser. There you should see information of the instance in the site.

Thanks for following this guide, keep engaged to know about more projects!
