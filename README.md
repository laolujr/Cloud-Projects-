# Three-tier web application-
This document would work you through the steps I took in creating a three-tier web application using the AWS Management console.
I leveraged on various cloud services to ensure my application was highly available,scalable and fault tolerrant.This application was deployed using a mix of click ops and automation or scripting.
To start off the deployment of my three-tier web application I designed the web-application architecture to give me a clear picture of what i wanted to create and to avoid mistakes.
Snapshots of my web application architecture is attached to this project 
This project has three main components 

`Public application layer`

`Private application layer`

`Database layer`

![Three-tier web app architecture](https://github.com/laolujr/Cloud-Projects-/assets/29700247/09c2389d-46dc-40f3-ad95-e1b5f92dc990)

### Login to AWS as an IAm user with priviledges to build your application 
Login to an `AWS account` using an `Iam user` not a  `Root user` with admin privileges and ensure your region is set to your desired region it is best practise to use a region in which you are situated to avoid latency `us-east-1` `N. Virginia`
For high availability we would be replicating every application in two Availability zones `us-east-1a` and `us-east-1b`

### Create a VPC,Internet Gateway,Subnets and Routetables.

The first service we would need is the `VPC`,the `VPC` we create would be the host of every other service we would be using in the creating and deploying our aplication.Below is my refrence architecture for my `VPC` in this project.
![VPC_Reference_Architecture](https://github.com/laolujr/Cloud-Projects-/assets/29700247/6a257e70-ba42-40a8-bf4b-7d854b3f8c85)

 I named my `VPC`  and called it `dev-vpc` and I used an `IPV4-CIDR` this would be used for communication of the resources within the `VPC`
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

and made sure both subnets were attached to `dev-vpc`

For the third tier which is `Private data Subnet` I created two private data  subnets called `Private data subnet az1 ` and `Private data subnet az2`

I then configured the  private `IPV4-CIDR` for `Private data az1 ` and `Private data subnet az2`  and made sure both subnets were attached to  my cusom VPC `dev-vpc`

I then created 

