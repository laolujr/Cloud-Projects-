# Three-tier web application-
This document would work you through the steps I took in creating a three-tier web application using the AWS Management console.
I leveraged on various cloud services to ensure my application was highly available,scalable and fault tolerrant.This application was deployed using a mix of click ops and automation or scripting.
To start off the deployment of my three-tier web application I designed the web-application architecture to give me a clear picture of what i wanted to create and to avoid mistakes.
Snapshots of my web application architecture is attached to this project 
This project has three main components 

`Public application layer`

`Private application layer`

`Database layer`

![Three-tier web app architecture] (https://github.com/laolujr/Cloud-Projects-/assets/29700247/09c2389d-46dc-40f3-ad95-e1b5f92dc990)

Login to an `AWS account` using an `Iam user` not a  `rootuser` with admin privileges and ensure your region is set to your desired region it is best practise to use a region in which you are situated to avoid latency `us-east-1` `N. Virginia`

The first service we would need is the `VPC`,the `VPC` we create would be the host of every other service we would be using in the creating and deploying our aplication.Below is my refrence architecture for my `VPC` in this project.
