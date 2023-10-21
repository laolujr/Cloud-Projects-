# Three-tier web application-
This document would work you through the steps I took in creating a three-tier web application using the AWS Management console.
I leveraged on various cloud services to ensure my application was highly available,scalable and fault tolerrant.This application was deployed using a mix of click ops and automation or scripting.
To start off the deployment of my three-tier web application I designed the web-application architecture to give me a clear picture of what i wanted to create and to avoid mistakes.
Snapshots of my web application architecture is attached to this project 
This project has three main components 

`Public application layer`

`Private application layer`

`Database layer`

![Three-tier web app architecture](https://github.com/laolujr/Cloud-Projects-/assets/29700247/3bb803ba-1083-458a-84aa-ab3f38df781e)

### Login to AWS as an IAm user with priviledges to build your application 
Login to an `AWS account` using an `Iam user` not a  `Root user` with admin privileges and ensure your region is set to your desired region it is best practise to use a region in which you are situated to avoid latency `us-east-1` `N. Virginia`
For high availability we would be replicating every application in two Availability zones `us-east-1a` and `us-east-1b`

### Create a VPC,Internet Gateway,Subnets and Routetables.

The first service we would need is the `VPC`,the `VPC` we create would be the host of every other service we would be using in the creating and deploying our aplication.Below is my refrence architecture for my `VPC` in this project.
![VPC_Reference_Architecture](https://github.com/laolujr/Cloud-Projects-/assets/29700247/66947cf2-099a-4f53-a590-4308a2c9e33c)


 I named my `VPC`  and called it `Dev VPC` and I used an `IPV4-CIDR` this would be used for communication of the resources within the `VPC`
 After creating and naming my `dev-vpc` the next step was to create an `Internet Gateway` which is attched to the `dev-vpc` the `Internet Gateway`  is the path way to which components of the web application communicate with the internet.
After creating the `Internet Gateway`  I attached the `Internet Gateway`  to the `dev-vpc`

Next step was to create `Subnets` in my first tier which is my `Public application layer` I  created two Public subnets and called them 
`Public subnet  Az1` in  `us-east-1a` and `Public subnet Az2` in `us-east-1b` this is for highavailability and fault tolerance.

I then assigned `IPV4-CIDR` to both `Public-az's` and automated the assigning of `IP address` to my `Public-Subnets`

For `Public subnet  AZ1` I assigned `IPV4-CIDR` of `10.0.0.0/24` and for `Public subnet AZ2` `10.0.1.0/24`
I then created a `Routetable` which serves as the logic of best routes to the internet between my `Public-Subnets` and attached the `Routetable` to my `VPC`

After attaching my `Public-Routetable` to my  cutom `VPC` I then allowed traffic from anywhere into the `Routetable` by editing routes and allowing trrafic from `0.0.0.0/0` and targeted it towards my custom `Internet Gateway`
Finally I associated my `Public-Subnets` to my  `Public-Routetable` to ensure  communication.

For the second tier which is `Private App Subnet` I created two private  subnets called `Private app az1 ` and `Private App subnet az2`
I then configured the  private `IPV4-CIDR` for `Private app az1 ` and `Private App subnet az2` 

For `Private app subnet AZ1` I assigned `IPV4-CIDR` of `10.0.0.2/24` and for `Private app subnet AZ2` `10.0.3.0/24`
and made sure both subnets were attached to my custom VPC  `Dev VPC`

For the third tier which is `Private data Subnet` I created two private data  subnets called `Private data subnet az1 ` and `Private data subnet az2`

I then configured the  private `IPV4-CIDR` for `Private data az1 ` and `Private data subnet az2`    for `Private data az1 ` I assigned `IPV4-CIDR`  `10.0.0.4/24`  to `Private data az1 ` and `10.0.0.5/24` to `Private data subnet az2` and made sure both subnets were attached to  my cusom VPC `Dev VPC`

Automatically `Private App Subnets` and  `Private data Subnets` are atached to  the main `Routetable` by default. The main `routetable` was created automatically whem I created `dev-vpc` thats the custom VPC for this project. It is important to note that the main `routetable` doesn't communicate over the internet.

### Create NAT Gateways and Private Route Tables.

I created `NAT Gateways` in `Public Subnet AZ1` called  `NAT Gateway AZ1` and `NAT Gateway AZ2`  for `Public Subnet AZ2` 
I then automatically assigned `Elastic IP addresses` to both `NAT Gateway AZ1` and `NAT Gateway AZ2`  and attached the `NAT Gateways` 
to `Public subnet  AZ1` and `Public subnet  AZ2` 
I then associated `NAT Gateway AZ1` and `NAT Gateway AZ2` to `Public-Routetable`

I then created `Private Route Table AZ1` for `Private app subnet az1` and `Private data subnet  az1`  I added routes `0.0.0.0/0`  and targeted `NAT Gateway AZ1` to ebanle traffic  from my `NAT Gateway AZ1` and created an association between them.

I also created `Private Route Table AZ2` for `Private app subnet az2` and `Private data subnet  aZ2`  I added routes `0.0.0.0/0` and targeted `NAT Gateway AZ2` to enable traffic from my  `NAT Gateway AZ2` and associated them.

This way my private subnets can communicate with the internet using via network address translation service of the  `NAT Gateways`  I creted in both  `Public subnet  AZ1` and `Public subnet  AZ2`

### Crerate Security Groups
To determinte the accessibility to resources in my web application `Security Groups` are the default firewall utility in AWS.
To create `Security Groups`  for thid application in the AWS management Console, I navigated to  `VPC` and under `Security Groups` 

I created new `Security Group` called `ALB-SG` and permitted traffic from everywhere `0.0.0.0/0` listening on  `Http port 80` and `Https port 443`

I then created another `Security Group` called `SSH-SG` and permited traffic from my IP addrress listening from `SSH port 22`

I then created another `Security Group` called `Webserver-SG` and permited traffic from my IP addrress listening from `SSH `port `22` `Http port 80` and `Https` port `443` and permitted traffic from  `ALB-SG` and `SSH-SG` 

I then created another `Security Group` called `Database-SG` listening on port `3306` and permiting traffic from only the `Webserver-SG`

Finally I created the `Security Group`  called `EFS-SG` listening from port `2049` and Port `22` permitting traffic from `Webserver-SG`,`EFS-SG` and `SSH-SG`

### Create RDS Instance

The next step I took was to create an RDS Instance for my web application and from my refrence architecture I created a `MySQL RDS Instance`
In the management console, I went to services and selected `RDS` I then created a new `Subnet Group` called `Database Subnet` within the custom `VPC`  the `Dev VPC`
I also specified the availability zone I wanted to host my `RDS` Instance in `us-east-1a` and `us-east-1b`
I then specified the preffered Subnet for the  `RDS` Instance to be  `Private data subnet  aZ1`  and  `Private data subnet  aZ2` 
After that I created a Database using the `MYSQL` engine  and used `MYSQL` version `5.7.36`
I leveraged on a single `Database` Instance and named Instance `Dev-RDS-DB`
I then created a Master Username for `Dev-RDS-DB` and a Master Password for `Dev-RDS-DB`
I then saved this credentials securley as it would be handly later on in the project when making changes to `Dev-RDS-DB` via Terminal `SSH`

I then chose an instance class for `Dev-RDS-DB` and it was the free tier `Db.t2.micro`

I pointed `Dev-RDS-DB` to the custom `VPC` for this project `Dev VPC`

I selected the `Database Subnet` I then selected the `Database-SG` for my firewall settings I then selected the `us-east-1b` Availability zone to host my Master `Database` 
I then created an intial `Database`  name called `Application DB` 
I then created the `Database`  after all the needful configurations.
I saved the configuration `Database endpoint` for my `Database` 

### Create EFS and Mount targets in Database Tier 

The next step in my project was to create an `EFS` so my `Webservers` in both availability zones can share files.
The point of the `EFS` and `Mount Targets` is to enable my `Webservers`  access configuration code and application file from a shared folder `EFS`.

In the AWS management conslole  I went to the `EFS` service and Created a File system.

I called my `EFS` `Dev-EFS` I tagged my `EFS` with the same name  `Dev-EFS` and didn't encrypt my `EFS` due to the project being test project.
It is best practice to encrypt your `EFS`.

I pointed `Dev-EFS` to `Dev VPC` in the File system network settings.

I also targeted `Dev-EFS` to `Private Data Subnet AZ1` in `us-east-1a` and `Private Data Subnet AZ2` in `us-east-1b`

For the firewall settings in my `Dev-EFS` I selected the `EFS-SG` which listens from port `2049` and Port `22` permitting traffic from `Webserver-SG`,`EFS-SG` and `SSH-SG`

I verified all my settings and `Dev-EFS` was created.

I then saved the default script to mount `Dev-EFS` to  `Private Data Subnet AZ1` and  `Private Data Subnet AZ2`

`sudo mount -t nfsxxxxxxx:/ efs`

### Create a Keypair  Mac 

I use a Macbook so I created a `KeyPair` for my OS 

The point of the `KeyPair` is to securley `SSH` into the projects `EC2-Instance`

In the  AWS Management Conslole I went to `EC2` and went to  security to create my `KeyPair`

I created a `KeyPair` and named it `Devkeypair.pem` and Used `RSA` format.

In my computer `Devkeypair.pem` automatically downloaded into the downloads directory i then moved it to my home directory  `Laolujr`

I did this because I would `SSH` from my home directory

I then modified the settings of my `KeyPair`  using rhe `CHMOD 400` command in terminal.

That command smply modifies the fle permission to be read only for owner and no permission for users or groups.

### Launch Setup Server
The purpose of this sever is to launch `WordPress` in my `Public subnet AZ1`
I went to the AWS Management Console and under services I created a server called `Setup Server` in `EC2`
`Setup Server`  ran on `Amazon Linux 2` and had a Keypair `Devkeypair.pem` and firewall settings of `SSH-SG` to Enable Secureshell access to the server where `WordPress`  would be installed the `Webserver-SG` and `ALB-SG` were also selected.

Instance Type was `t2.micro` under free tier and I pointed this `Setup Server`  to `Dev VPC`

I verified all my settings were correct and I launched the `Setup Server`

### SSH into Setup server to install wordpress

I used the command `ssh -i Devkeypair.pem ec2-user@67.145.137.241`
This command allowed me to securley log into the server now i can install resources into the server by rescources i mean coponents of my website.

### Install Website components Wordpress

Now I had securley connected to `Setup Server` 
I ran the following scripts to install website components.

InstallWordPress (1).sh

```



#1. create the html directory and mount the efs 

sudo su
yum update -y
mkdir -p /var/www/html
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-03c9b3354880b36a6.efs.us-east-1.amazonaws.com:/ /var/www/html


#2. install apache 

sudo yum install -y httpd httpd-tools mod_ssl
sudo systemctl enable httpd 
sudo systemctl start httpd


#3. install php 7.4

sudo amazon-linux-extras enable php7.4
sudo yum clean metadata
sudo yum install php php-common php-pear -y
sudo yum install php-{cgi,curl,mbstring,gd,mysqlnd,gettext,json,xml,fpm,intl,zip} -y


#4. install mysql5.7

sudo rpm -Uvh https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
sudo yum install mysql-community-server -y
sudo systemctl enable mysqld
sudo systemctl start mysqld


#5. set permissions

sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
chown apache:apache -R /var/www/html 


#6. download wordpress files
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cp -r wordpress/* /var/www/html/


#7. create the wp-config.php file

cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php


#8. edit the wp-config.php file

nano /var/www/html/wp-config.php


#9. restart the webserver

service httpd restart

```
It is important to note that each of the scripts were run secureley in my `Setup Server` one after the other 

I then defined Parameters in my `database`  like and `DB name`, `DB Useername`  `DB host`and `DB password` restarted the `Setup Server` which is common practice when you make significant instalations and changes.

I copied the `Public IPV4` of my `Setup Server` and my website sucessfully opened.









