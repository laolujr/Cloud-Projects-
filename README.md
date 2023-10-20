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
 After creating and naming my `VPC` the next step was to create an `Internet Gateway` which is attched to the `VPC` the `Internet Gateway`  is the path way to which components of the web application communicate with the internet.
After creating the `Internet Gateway`  I attached the `Internet Gateway`  to the `VPC`
Next step was to create `Subnets` in my first tier which is my `Public application layer` I  created two Public subnets and called them 
`Public-Az1` in  `us-east-1a` and `Public-Az2` in `us-east-1b`
I then assigned `IPV4-CIDR` to both `Public-az's` and automated the assigning of `Ip

