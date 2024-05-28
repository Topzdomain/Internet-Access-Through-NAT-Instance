<h2 align="center"> INTERNET ACCESS TO A PRIVATE SUBNET USING A NAT INSTANCE</h2>

When a cloud-based virtual machine used for hosting a company's internal server(s) and/or application(s) is placed in a private subnet, one major challenge that comes up is the ability of the internal server(s) and application(s) to communicate with the internet. This challenge can be conquered through the following ways:

1. Creating a NAT gateway through which all traffic from the private subnet destined for the internet is routed, on the routing table (private rt) configuration and the route table associated with the private subnet.
2. Creating an EC2 instance with NAT software pre-configured into the AMI. This EC2 instance is placed in the public subnet, and all traffic from the private subnet destined for the internet is routed through the EC2 instance on the routing table (private rt) configurations, and the routing table (private rt) associated with the private subnet.
3. A Bastion Host can also be created from which SSH into the Private Subnet can be made to gain access. The security group also needs to allow SSH.


In this project, I'll be granting internet access to a private subnet using a NAT Instance. To achieve this, I created a Bastion Host, a Private Subnet and a NAT Instance.

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/Network%20Architecture.png" height="50%" width="70%"/>
</p>
<h5 align="center"> Network Architecture</h5>

<h2 align="left"> Creating the VPC</h2>

Based on the network architecture, I created a VPC with a CIDR of 10.1.0.0/16.

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/vpc%20creation.png" height="40%" width="60%"/>
</p>

<h2 align="left"> Creating Internet Gateway</h2>

Next, I created the internet gateway which I attached to the vpc.

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/igw.png" height="40%" width="60%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/attaching%20igw%20to%20vpc1.png" height="40%" width="60%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/attaching%20igw%20to%20vpc2.png" height="40%" width="60%"/>
</p>

<h2 align="left"> Creating Route Tables</h2>

Whenever a new VPC is created, a default route table is created for that vpc. I renamed the route table and used it as the public route table. I then proceeded to create a new route table which I used as the private route table.

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/private%20rt.png" height="50%" width="60%"/>
</p>

<h2 align="left"> Subnet Creation</h2>

I created two subnets, a private subnet which would house an ec2 instance (a virtual machine that runs a company's private/internal applications or server that is not accessible to the public) and a public subnet which would house an ec2 instance (a virtual machine that runs the application(s) accessible to the public, and from which the private server can be accessed and queried). The private subnet was created with a CIDR of 10.1.2.0/24 and 10.1.1.0/24 for the public subnet.

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/subnet%20creation1.png" height="40%" width="60%"/>
</p>

<p>
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/public%20subnet.png" height="65%" width="45%"/>
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/private%20subnet.png" height="65%" width="45%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/subnet%20creation2.png" height="40%" width="60%"/>
</p>

<h2 align="left"> Creating a NAT Instance</h2>

Before creating the NAT instance (an EC2 instance with NAT software configured into its AMI), I created an EC2 instance for the private subnet and another for the public subnet. I had also pre-configured the default Security Group that the VPC created to allow traffic from my IP address only (IP Whitelisting) and I also have a pre-downloaded PEM key to enable SSH into the servers. To create the NAT instance, I searched the community AMIs for anyone that has NAT and VPC (amzn-ami-vpc-nat), then selected one of them to use as AMI (Amazon Machine Image). I also ensured the EC2 instance was placed in the public subnet. After creating the EC2 instance, I navigated to Networking, from the Action drop-down menu, selected "Change source/destination check", and ticked stop, then I saved the change.

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/nat%20instance%20creation1.png" height="40%" width="60%"/>
</p>

<p>
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/nat%20instance%20creation2.png" height="40%" width="45%"/>
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/nat%20instance%20creation3.png" height="40%" width="45%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/disabling%20source-destination%20check1.png" height="40%" width="60%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/disabling%20source-destination%20check2.png" height="40%" width="60%"/>
</p>

<h2 align="left"> Editing Route Table and Associating Subnets</h2>

PUBLIC ROUTE TABLE

I edited the route for the public route table to use the internet gate for all traffic from the public subnet destined for the internet and associated the routing table with the public subnet.

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/editing%20public%20rt.png" height="65%" width="80%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/subnet%20association%20for%20public%20rt.png" height="65%" width="80%"/>
</p>

PRIVATE ROUTE TABLE

I edited the route for the private route table to use the EC2 NAT Instance created, for all traffic from the private rt destined for the internet and associated the routing table with the private subnet.

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/editing%20private%20rt.png" height="65%" width="80%"/>
</p>

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/subnet%20association%20for%20private%20rt.png" height="65%" width="80%"/>
</p>

<h2 align="left"> Testing The Architecture For Internet Access</h2>

To test the architecture to see if it works, first I used SSH to log into the Bastion host using the public IP, then used SSH to copy the pem key into the Bastion host and changed the permission to allow read permission for only the owner of the pem key after which I then used SSH to log into the private server using the private IP of the server. At the successful login to the server, I used commands like ping and curl to check for internet connectivity. 

Logging Into the Bastion Host

```commandline
ssh -i "practice.pem" ec2-user@51.20.68.81
```

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/login%20to%20bastion%20host.png" height="65%" width="60%"/>
</p>

Copying The Pem Key To Bastion Host

```commandline
scp -i practice.pem practice.pem ec2-user@51.20.68.81:/home/ec2-user
```

Changing Permission For The PEM Key

```commandline
chmod 400 practice.pem
```

Logging Into the Private Server

```commandline
ssh -i "practice.pem" ubuntu@10.1.2.250
```

<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/login%20to%20private%20server.png" height="60%" width="60%"/>
</p>

Testing For Internet Connectivity

```commandline
ping google.com
```
<p align="center">
<img src="https://github.com/Topzdomain/Internet-Access-Through-NAT-Instance/blob/main/screenshots/internet%20connectivity.png" height="60%" width="75%"/>
</p>
