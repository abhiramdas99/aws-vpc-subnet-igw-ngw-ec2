# aws-vpc-subnet-igw-ngw-ec2
Notes for design a solution to create a secure vpc with two subnet private and public with intergnet gatway and nat gateway

Design the following solution  in Aws Cloud Formation 
-----------------------------------------------
1) create vpc with CIDR - 192.168.0.0/16 and name of vpc is - MyVPC01
2) create two subnet as per follows detail within the vpc ie - MyVPC01  
      2.1) name : mySubnetPublic ,  CIDR - 192.168.100.0/24 
	  2.2) name : mySubnetPrivate , CIDR - 192.168.200.0/24
	  
3) 	Lets configure public subnet-
	For public subnet (mySubnetPublic) we need internt connectivity access the webserver outside the VPC(Internet).
	hence, we require following three thing 
       3.1) Require a Internet Gateway(IGW) to be placed in our VPC 
			3.1.1) Name : My-IGW
			
	   3.2) Create Route table  
			3.2.1) Name : My-RouteTable-Any-To-IGW
			3.2.2) Select the VPC : MyVPC01
			3.2.3) add the route -  destination: 0.0.0.0/0(any trafic)  -> Target : IGW(My-IGW)
			3.2.4) Associate to subnet : mySubnetPublic
			
		3.3) Enable the auto-assigning of Public IP feature in public subnet (mySubnetPublic). So that the resouce will
			 will get the public ip  within this subnet
			 
4)	Lets configure private subnet- 
	For private subnet we need the internet only for update the sysytem. And  have planed that, we will put  DB server here
	To achieve this setup we require following thing 
		4.1) we require NAT gateway(NGW). And place in that subnet , where internet is comming
			4.1.1) Name : My-NGW
			4.1.2) Subnet : mySubnetPublic
			4.1.3) connectivity type : public 
			4.1.4) Elastic IP allocation ID : Need to allocate it 
			
		4.2) Create Route Table 
			4.2.1) Name : My-RouteTable-Any-To-NGW
			4.2.2) Select the VPC : MyVPC01
			4.2.3) add the route -  destination: 0.0.0.0/0(any trafic)  -> Target : NGW(My-NGW)			
			4.2.4) Associate to subnet : mySubnetPrivate	

5)	Now add computing machine(ec2) to both subnet 
		5.1) Add one ec2 to subnet : mySubnetPublic 
			5.1.1) Name : MyWebServer
			5.1.2) AMI  : Amazon Linux - Free tier
			5.1.3) Instance type : t2.micro	
			5.1.4) create a keypair
			5.1.5) Network setting select - vpc :MyVPC01 and in subnet : mySubnetPublic
			5.1.6) In security group allow ssh 
			5.1.7) Select magnetic storage for store data
			
		5.2) Add one ec2 to subnet : mySubnetPrivate 
			5.1.1) Name : MyDBServer
			5.1.2) AMI  : Amazon Linux - Free tier
			5.1.3) Instance type : t2.micro	
			5.1.4) create a keypair
			5.1.5) Network setting select - vpc :MyVPC01 and in subnet : mySubnetPrivate
			5.1.6) In security group allow ssh 
			5.1.7) Select magnetic storage for store data
