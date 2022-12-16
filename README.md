# Create an IPv4-enabled VPC and instances using the AWS CLI

We are mainly creating our most infra and its management through terraform.But today we are going to see how to create a VPC via aws-cli command. So that we can understand which one is more better.

On this repository, we are going to cover on how to create a VPC, subnets and its asscoiated EC2 instances via aws-cli command line. In this section we will get to know more about the detailed configuration steps required to create a custom VPC and an EC2 instance creation inside it.


### Features of AWS CLI

- One of the main benefits is the ability to save substantial time. The savings comes in the form of easier installs, support of all services from one tool.
- Easier to install.
- Supports all Amazon Web Services.

## You will create the following AWS resources in this below section:

 - A VPC

- Three subnets

- An internet gateway

- A route table

- EC2 instances

## Steps required for the creation of VPC

### Step 1

  > Configure AWS CLI using a IAM user who having programmatic access

```sh
 $ aws configure
AWS Access Key ID [None]: XXXXXXXXXXXXX
AWS Secret Access Key [None]: XXXXXXXXXXXXXXX
Default region name [None]: ap-south-1
Default output format [None]: json
```
### Step 2

>Creating VPC.

Create a VPC with a 172.16.0.0/16 CIDR block using the following command, and it returns the ID of the new VPC.

```sh
 $ aws ec2 create-vpc --cidr-block 172.16.0.0/16 --query Vpc.VpcId --output text
vpc-010d9bf0b371ea043
```
add a name tag to the new VPC.
```sh
$ aws ec2 create-tags --resources vpc-010d9bf0b371ea043 --tags Key=Name,Value=my-vpc-aws
```
### Step 3
> Create subnets and add name tags to each subnet.

a. Create a subnet in your VPC with a  172.16.0.0/18 CIDR block.
```
$ aws ec2 create-subnet --vpc-id  vpc-010d9bf0b371ea043 --cidr-block 172.16.0.0/18 --availability-zone ap-south-1a

    "Subnet": {
        "MapPublicIpOnLaunch": false, 
        "AvailabilityZoneId": "aps1-az1", 
        "AvailableIpAddressCount": 16379, 
        "DefaultForAz": false, 
        "SubnetArn": "arn:aws:ec2:ap-south-1:539081425481:subnet/subnet-0e68fd039baf676e5", 
        "Ipv6CidrBlockAssociationSet": [], 
        "VpcId": "vpc-010d9bf0b371ea043", 
        "State": "available", 
        "AvailabilityZone": "ap-south-1a", 
        "SubnetId": "subnet-0e68fd039baf676e5", 
        "OwnerId": "539081425481", 
        "CidrBlock": "172.16.0.0/18", 
        "AssignIpv6AddressOnCreation": false
    }
}
```
added a tag to the created subnet as subnet 1
```
$  aws ec2 create-tags --resources subnet-0e68fd039baf676e5 --tags Key=Name,Value=Subnet1
```

>  b.  Create the second subnet in your VPC with a 172.16.64.0/18 CIDR block
```
 $ aws ec2 create-subnet --vpc-id vpc-010d9bf0b371ea043 --cidr-block 172.16.64.0/18 --availability-zone ap-south-1b
{
    "Subnet": {
        "MapPublicIpOnLaunch": false, 
        "AvailabilityZoneId": "aps1-az3", 
        "AvailableIpAddressCount": 16379, 
        "DefaultForAz": false, 
        "SubnetArn": "arn:aws:ec2:ap-south-1:539081425481:subnet/subnet-094098bc7fc7351a6", 
        "Ipv6CidrBlockAssociationSet": [], 
        "VpcId": "vpc-010d9bf0b371ea043", 
        "State": "available", 
        "AvailabilityZone": "ap-south-1b", 
        "SubnetId": "subnet-094098bc7fc7351a6", 
        "OwnerId": "539081425481", 
        "CidrBlock": "172.16.64.0/18", 
        "AssignIpv6AddressOnCreation": false
    }
}
```
added a tag to subnet as subnet 2;

```
 $ aws ec2 create-tags --resources subnet-094098bc7fc7351a6 --tags Key=Name,Value=Subnet2
```
>c. Create the third subnet in your VPC with a 172.16.128.0/18 CIDR block.  We are using this as a private subnet 
```
$ aws ec2 create-subnet --vpc-id vpc-010d9bf0b371ea043 --cidr-block 172.16.128.0/18 --availability-zone ap-south-1b
{
    "Subnet": {
        "MapPublicIpOnLaunch": false, 
        "AvailabilityZoneId": "aps1-az3", 
        "AvailableIpAddressCount": 16379, 
        "DefaultForAz": false, 
        "SubnetArn": "arn:aws:ec2:ap-south-1:539081425481:subnet/subnet-0ec91188932504c0c", 
        "Ipv6CidrBlockAssociationSet": [], 
        "VpcId": "vpc-010d9bf0b371ea043", 
        "State": "available", 
        "AvailabilityZone": "ap-south-1b", 
        "SubnetId": "subnet-0ec91188932504c0c", 
        "OwnerId": "539081425481", 
        "CidrBlock": "172.16.128.0/18", 
        "AssignIpv6AddressOnCreation": false
    }
}
```
add tag  to this subnet as subet3
```
$ aws ec2 create-tags --resources subnet-0ec91188932504c0c --tags Key=Name,Value=Subnet3
```
> d.  Enable public IP for the first two subnets. 
```
$ aws ec2 modify-subnet-attribute --subnet-id subnet-0e68fd039baf676e5 --map-public-ip-on-launch
$ aws ec2 modify-subnet-attribute --subnet-id subnet-094098bc7fc7351a6 --map-public-ip-on-launch
```
Able see the complete information of the VPC and subnets using the below command. 
```
$aws ec2 describe-subnets  --filters "Name=vpc-id,Values=vpc-010d9bf0b371ea043"
{
    "Subnets": [
        {
            "MapPublicIpOnLaunch": false, 
            "AvailabilityZoneId": "aps1-az3", 
            "Tags": [
                {
                    "Value": "Subnet3", 
                    "Key": "Name"
                }
            ], 
            "AvailableIpAddressCount": 16379, 
            "DefaultForAz": false, 
            "SubnetArn": "arn:aws:ec2:ap-south-1:539081425481:subnet/subnet-0ec91188932504c0c", 
            "Ipv6CidrBlockAssociationSet": [], 
            "VpcId": "vpc-010d9bf0b371ea043", 
            "MapCustomerOwnedIpOnLaunch": false, 
            "AvailabilityZone": "ap-south-1b", 
            "SubnetId": "subnet-0ec91188932504c0c", 
            "OwnerId": "539081425481", 
            "CidrBlock": "172.16.128.0/18", 
            "State": "available", 
            "AssignIpv6AddressOnCreation": false
        }, 
        {
            "MapPublicIpOnLaunch": true, 
            "AvailabilityZoneId": "aps1-az3", 
            "Tags": [
                {
                    "Value": "Subnet2", 
                    "Key": "Name"
                }
            ], 
            "AvailableIpAddressCount": 16379, 
            "DefaultForAz": false, 
            "SubnetArn": "arn:aws:ec2:ap-south-1:539081425481:subnet/subnet-094098bc7fc7351a6", 
            "Ipv6CidrBlockAssociationSet": [], 
            "VpcId": "vpc-010d9bf0b371ea043", 
            "MapCustomerOwnedIpOnLaunch": false, 
            "AvailabilityZone": "ap-south-1b", 
            "SubnetId": "subnet-094098bc7fc7351a6", 
            "OwnerId": "539081425481", 
            "CidrBlock": "172.16.64.0/18", 
            "State": "available", 
            "AssignIpv6AddressOnCreation": false
        }, 
        {
            "MapPublicIpOnLaunch": true, 
            "AvailabilityZoneId": "aps1-az1", 
            "Tags": [
                {
                    "Value": "Subnet1", 
                    "Key": "Name"
                }
            ], 
            "AvailableIpAddressCount": 16379, 
            "DefaultForAz": false, 
            "SubnetArn": "arn:aws:ec2:ap-south-1:539081425481:subnet/subnet-0e68fd039baf676e5", 
            "Ipv6CidrBlockAssociationSet": [], 
            "VpcId": "vpc-010d9bf0b371ea043", 
            "MapCustomerOwnedIpOnLaunch": false, 
            "AvailabilityZone": "ap-south-1a", 
            "SubnetId": "subnet-0e68fd039baf676e5", 
            "OwnerId": "539081425481", 
            "CidrBlock": "172.16.0.0/18", 
            "State": "available", 
            "AssignIpv6AddressOnCreation": false
        }
    ]
}

```
### Step 4

> Create an Internet Gateway.

Create an Internet Gateway using the following command, which returns the new Internet Gateway ID.
```
$ aws ec2 create-internet-gateway --query InternetGateway.InternetGatewayId --output text
igw-0ac89377825166b56
```

>Attach the internet gateway to our VPC to allow the instances(and NAT) to communicate with the outside world.
```
$ aws ec2 attach-internet-gateway --vpc-id vpc-010d9bf0b371ea043 --internet-gateway-id igw-0ac89377825166b56
```

### Step 5 

> Create a NAT Gateway.

Create a NAT Gateway after allocating an elastic IP using the allocate-address command
```
$ aws ec2 allocate-address
{
    "Domain": "vpc", 
    "PublicIpv4Pool": "amazon", 
    "PublicIp": "35.154.85.250", 
    "AllocationId": "eipalloc-0702d05cb58b2236c", 
    "NetworkBorderGroup": "ap-south-1"
}

```
```
$ aws ec2 create-nat-gateway --subnet-id subnet-094098bc7fc7351a6 --allocation-id eipalloc-0702d05cb58b2236c
{
    "NatGateway": {
        "NatGatewayAddresses": [
            {
                "AllocationId": "eipalloc-0702d05cb58b2236c"
            }
        ], 
        "VpcId": "vpc-010d9bf0b371ea043", 
        "State": "pending", 
        "NatGatewayId": "nat-062b1fd889db02547", 
        "SubnetId": "subnet-094098bc7fc7351a6", 
        "CreateTime": "2022-12-15T18:17:44.000Z"
    }, 
    "ClientToken": "06943761-5b53-4f3d-8429-71684b66e76e"
}

```

### Step 6 

> Update route tables

A route table will be created by default.  We can find the default route table information of the VPC using the below command


```
$ aws ec2 describe-route-tables --filters "Name=vpc-id,Values=vpc-010d9bf0b371ea043"
{
    "RouteTables": [
        {
            "Associations": [
                {
                    "AssociationState": {
                        "State": "associated"
                    }, 
                    "RouteTableAssociationId": "rtbassoc-0833288fe24acd161", 
                    "Main": true, 
                    "RouteTableId": "rtb-04fdc94bea461ffbc"
                }
            ], 
            "RouteTableId": "rtb-04fdc94bea461ffbc", 
            "VpcId": "vpc-010d9bf0b371ea043", 
            "PropagatingVgws": [], 
            "Tags": [], 
            "Routes": [
                {
                    "GatewayId": "local", 
                    "DestinationCidrBlock": "172.16.0.0/16", 
                    "State": "active", 
                    "Origin": "CreateRouteTable"
                }
            ], 
            "OwnerId": "539081425481"
        }
    ]
}
```
a.  Create a custom route table(for private subnet) using the below command and associate the private subnet with it.
```
$ aws ec2 create-route-table --vpc-id vpc-010d9bf0b371ea043 --query RouteTable.RouteTableId --output text
rtb-010f362b64626b42f
```
```
$ aws ec2 associate-route-table  --subnet-id subnet-0ec91188932504c0c --route-table-id rtb-010f362b64626b42f
{
    "AssociationState": {
        "State": "associated"
    }, 
    "AssociationId": "rtbassoc-02b216b7b3dc4c48a"
}
```
b. Add route table entries.

Add route table entry for IGW in the default route table to allow the instances to communicate with the internet.
```
$ aws ec2 create-route --route-table-id rtb-04fdc94bea461ffbc --destination-cidr-block 0.0.0.0/0 --gateway-id igw-0ac89377825166b56
{
    "Return": true
}
```
Add route table entry in the custom route table to enable instances in the private subnet to communicate with the NAT gateway.
```
$ aws ec2 create-route --route-table-id rtb-010f362b64626b42f --destination-cidr-block 0.0.0.0/0 --nat-gateway-id nat-062b1fd889db02547
{
    "Return": true
}

```
To confirm that the route has been created and is active, we can describe the route table using the following describe-route-tables command.
```
$ aws ec2 describe-route-tables --filters "Name=vpc-id,Values=vpc-010d9bf0b371ea043" 
{
    "RouteTables": [
        {
            "Associations": [
                {
                    "SubnetId": "subnet-0ec91188932504c0c", 
                    "AssociationState": {
                        "State": "associated"
                    }, 
                    "RouteTableAssociationId": "rtbassoc-02b216b7b3dc4c48a", 
                    "Main": false, 
                    "RouteTableId": "rtb-010f362b64626b42f"
                }
            ], 
            "RouteTableId": "rtb-010f362b64626b42f", 
            "VpcId": "vpc-010d9bf0b371ea043", 
            "PropagatingVgws": [], 
            "Tags": [], 
            "Routes": [
                {
                    "GatewayId": "local", 
                    "DestinationCidrBlock": "172.16.0.0/16", 
                    "State": "active", 
                    "Origin": "CreateRouteTable"
                }, 
                {
                    "Origin": "CreateRoute", 
                    "DestinationCidrBlock": "0.0.0.0/0", 
                    "NatGatewayId": "nat-062b1fd889db02547", 
                    "State": "active"
                }
            ], 
            "OwnerId": "539081425481"
        }, 
        {
            "Associations": [
                {
                    "AssociationState": {
                        "State": "associated"
                    }, 
                    "RouteTableAssociationId": "rtbassoc-0833288fe24acd161", 
                    "Main": true, 
                    "RouteTableId": "rtb-04fdc94bea461ffbc"
                }
            ], 
            "RouteTableId": "rtb-04fdc94bea461ffbc", 
            "VpcId": "vpc-010d9bf0b371ea043", 
            "PropagatingVgws": [], 
            "Tags": [], 
            "Routes": [
                {
                    "GatewayId": "local", 
                    "DestinationCidrBlock": "172.16.0.0/16", 
                    "State": "active", 
                    "Origin": "CreateRouteTable"
                }, 
                {
                    "GatewayId": "igw-0ac89377825166b56", 
                    "DestinationCidrBlock": "0.0.0.0/0", 
                    "State": "active", 
                    "Origin": "CreateRoute"
                }
            ], 
            "OwnerId": "539081425481"
        }
    ]
}

```

### Step 7 

> Create security groups

a. Security group for bastion server

```
$  aws ec2 create-security-group --group-name ProjectA-bastion --description "Allow SSH access" --vpc-id vpc-010d9bf0b371ea043
{
    "GroupId": "sg-043d4115dd859f0ad"
}
```
b. Security group for the frontend web server

```
$ aws ec2 create-security-group --group-name ProjectA-webserver --description "Allow SSH access from bastion and http access from all" --vpc-id vpc-010d9bf0b371ea043
{
    "GroupId": "sg-0974866decc081b44"
}
```

c. Security group for the backend server

```
$ aws ec2 create-security-group --group-name ProjectA-backendserver --description "Allow SSH access from bastion"  --vpc-id vpc-010d9bf0b371ea043
{
    "GroupId": "sg-0c10150f23a244fb1"
}
```
Add rules to the security groups.

1. Add a rule to allow SSH access to the bastion server.
```
$ aws ec2 authorize-security-group-ingress --group-id sg-043d4115dd859f0ad --protocol tcp --port 22 --cidr 0.0.0.0/0
```
2. Add a rule to allow SSH access to the frontend server from the bastion server.
```
$ aws ec2 authorize-security-group-ingress --group-id sg-0974866decc081b44 --protocol tcp --port 22 --source-group sg-043d4115dd859f0ad
```
3. Add a rule to allow HTTP access to the frontend server from the outside world(All IPv4).
```
$ aws ec2 authorize-security-group-ingress --group-id sg-0974866decc081b44 --protocol tcp --port 80 --cidr 0.0.0.0/0
```
4. Add a rule to allow SSH access to the backend server from the bastion server.
```
$ aws ec2 authorize-security-group-ingress --group-id sg-0c10150f23a244fb1 --protocol tcp --port 22 --source-group sg-043d4115dd859f0ad 
```
5. Add a rule to enable the frontend server to access mysql on the backend server.

```
$ aws ec2 authorize-security-group-ingress --group-id sg-0c10150f23a244fb1 --protocol tcp --port 3306 --source-group sg-0974866decc081b44
```
### Step 8
> Create a key pair
Create a key pair and save it to a file.  Also, update the permission of the file.
```
$ aws ec2 create-key-pair --key-name ProjectAAngelKey.pem --query 'KeyMaterial' --output text > ProjectAAngelKey.pem
```
```
chmod 400 ProjectAAngelKey.pem
```
### Step 9
> Enabled public DNS hostname for the VPC and verified it using describe-vpc-attribute command. 
```
$ aws ec2 modify-vpc-attribute --vpc-id vpc-010d9bf0b371ea043 --enable-dns-hostnames "{\"Value\":true}"
```
```
$ aws ec2 describe-vpc-attribute --vpc-id vpc-010d9bf0b371ea043 --attribute enableDnsHostnames
{
    "VpcId": "vpc-010d9bf0b371ea043", 
    "EnableDnsHostnames": {
        "Value": true
    }
}
```
### Step 10
> Launch instances and add tag(s) to the instances to identify them quickly. 

a. Launch the bastion server with the required security group, keypair, subnet, and AMI (Here, I've used Amazon Linux AMI)
```
$ aws ec2 run-instances --image-id ami-074dc0a6f6c764218 --count 1 --instance-type t2.micro --key-name ProjectAAngelKey.pem --security-group-ids sg-043d4115dd859f0ad --subnet-id subnet-094098bc7fc7351a6
{
    "Instances": [
        {
            "Monitoring": {
                "State": "disabled"
            }, 
            "PublicDnsName": "", 
            "StateReason": {
                "Message": "pending", 
                "Code": "pending"
            }, 
            "State": {
                "Code": 0, 
                "Name": "pending"
            }, 
            "EbsOptimized": false, 
            "LaunchTime": "2022-12-15T19:06:34.000Z", 
            "PrivateIpAddress": "172.16.84.110", 
            "ProductCodes": [], 
            "VpcId": "vpc-010d9bf0b371ea043", 
            "CpuOptions": {
                "CoreCount": 1, 
                "ThreadsPerCore": 1
            }, 
            "StateTransitionReason": "", 
            "InstanceId": "i-0806ef36062bbda11", 
            "EnaSupport": true, 
            "ImageId": "ami-074dc0a6f6c764218", 
            "PrivateDnsName": "ip-172-16-84-110.ap-south-1.compute.internal", 
            "KeyName": "ProjectAAngelKey.pem", 
            "SecurityGroups": [
                {
                    "GroupName": "ProjectA-bastion", 
                    "GroupId": "sg-043d4115dd859f0ad"
                }
            ], 
            "ClientToken": "c90faa6e-d4f4-4c99-a77d-84a7824e0ffc", 
            "SubnetId": "subnet-094098bc7fc7351a6", 
            "InstanceType": "t2.micro", 
            "CapacityReservationSpecification": {
                "CapacityReservationPreference": "open"
            }, 
            "NetworkInterfaces": [
                {
                    "Status": "in-use", 
                    "MacAddress": "0a:d9:90:ff:62:b0", 
                    "SourceDestCheck": true, 
                    "VpcId": "vpc-010d9bf0b371ea043", 
                    "Description": "", 
                    "NetworkInterfaceId": "eni-07b2b90b290283cf6", 
                    "PrivateIpAddresses": [
                        {
                            "PrivateDnsName": "ip-172-16-84-110.ap-south-1.compute.internal", 
                            "Primary": true, 
                            "PrivateIpAddress": "172.16.84.110"
                        }
                    ], 
                    "PrivateDnsName": "ip-172-16-84-110.ap-south-1.compute.internal", 
                    "InterfaceType": "interface", 
                    "Attachment": {
                        "Status": "attaching", 
                        "DeviceIndex": 0, 
                        "DeleteOnTermination": true, 
                        "AttachmentId": "eni-attach-0743d74d985c79925", 
                        "AttachTime": "2022-12-15T19:06:34.000Z"
                    }, 
                    "Groups": [
                        {
                            "GroupName": "ProjectA-bastion", 
                            "GroupId": "sg-043d4115dd859f0ad"
                        }
                    ], 
                    "Ipv6Addresses": [], 
                    "OwnerId": "539081425481", 
                    "SubnetId": "subnet-094098bc7fc7351a6", 
                    "PrivateIpAddress": "172.16.84.110"
                }
            ], 
            "SourceDestCheck": true, 
            "Placement": {
                "Tenancy": "default", 
                "GroupName": "", 
                "AvailabilityZone": "ap-south-1b"
            }, 
            "Hypervisor": "xen", 
            "BlockDeviceMappings": [], 
            "Architecture": "x86_64", 
            "RootDeviceType": "ebs", 
            "RootDeviceName": "/dev/xvda", 
            "VirtualizationType": "hvm", 
            "MetadataOptions": {
                "State": "pending", 
                "HttpEndpoint": "enabled", 
                "HttpTokens": "optional", 
                "HttpPutResponseHopLimit": 1
            }, 
            "AmiLaunchIndex": 0
        }
    ], 
    "ReservationId": "r-0c6612e491aec1c13", 
    "Groups": [], 
    "OwnerId": "539081425481"
}
```
create a tag to bastion EC2 server
```
$ aws ec2 create-tags --resources i-0806ef36062bbda11 --tags Key=Name,Value=bastion
```
 b. Launch the frontend server with the required security group, keypair, subnet, and AMI
```
$ aws ec2 run-instances --image-id ami-074dc0a6f6c764218 --count 1 --instance-type t2.micro --key-name ProjectAAngelKey.pem --security-group-ids sg-0974866decc081b44 --subnet-id subnet-0e68fd039baf676e5
{
    "Instances": [
        {
            "Monitoring": {
                "State": "disabled"
            }, 
            "PublicDnsName": "", 
            "StateReason": {
                "Message": "pending", 
                "Code": "pending"
            }, 
            "State": {
                "Code": 0, 
                "Name": "pending"
            }, 
            "EbsOptimized": false, 
            "LaunchTime": "2022-12-15T19:17:16.000Z", 
            "PrivateIpAddress": "172.16.34.151", 
            "ProductCodes": [], 
            "VpcId": "vpc-010d9bf0b371ea043", 
            "CpuOptions": {
                "CoreCount": 1, 
                "ThreadsPerCore": 1
            }, 
            "StateTransitionReason": "", 
            "InstanceId": "i-0ce009ae902b07ea2", 
            "EnaSupport": true, 
            "ImageId": "ami-074dc0a6f6c764218", 
            "PrivateDnsName": "ip-172-16-34-151.ap-south-1.compute.internal", 
            "KeyName": "ProjectAAngelKey.pem", 
            "SecurityGroups": [
                {
                    "GroupName": "ProjectA-webserver", 
                    "GroupId": "sg-0974866decc081b44"
                }
            ], 
            "ClientToken": "1365ef03-aeb7-4a42-ad26-86961f592f5b", 
            "SubnetId": "subnet-0e68fd039baf676e5", 
            "InstanceType": "t2.micro", 
            "CapacityReservationSpecification": {
                "CapacityReservationPreference": "open"
            }, 
            "NetworkInterfaces": [
                {
                    "Status": "in-use", 
                    "MacAddress": "02:6f:90:de:7d:d4", 
                    "SourceDestCheck": true, 
                    "VpcId": "vpc-010d9bf0b371ea043", 
                    "Description": "", 
                    "NetworkInterfaceId": "eni-04080fbccd99fdc4b", 
                    "PrivateIpAddresses": [
                        {
                            "PrivateDnsName": "ip-172-16-34-151.ap-south-1.compute.internal", 
                            "Primary": true, 
                            "PrivateIpAddress": "172.16.34.151"
                        }
                    ], 
                    "PrivateDnsName": "ip-172-16-34-151.ap-south-1.compute.internal", 
                    "InterfaceType": "interface", 
                    "Attachment": {
                        "Status": "attaching", 
                        "DeviceIndex": 0, 
                        "DeleteOnTermination": true, 
                        "AttachmentId": "eni-attach-0c294142596176f8c", 
                        "AttachTime": "2022-12-15T19:17:16.000Z"
                    }, 
                    "Groups": [
                        {
                            "GroupName": "ProjectA-webserver", 
                            "GroupId": "sg-0974866decc081b44"
                        }
                    ], 
                    "Ipv6Addresses": [], 
                    "OwnerId": "539081425481", 
                    "SubnetId": "subnet-0e68fd039baf676e5", 
                    "PrivateIpAddress": "172.16.34.151"
                }
            ], 
            "SourceDestCheck": true, 
            "Placement": {
                "Tenancy": "default", 
                "GroupName": "", 
                "AvailabilityZone": "ap-south-1a"
            }, 
            "Hypervisor": "xen", 
            "BlockDeviceMappings": [], 
            "Architecture": "x86_64", 
            "RootDeviceType": "ebs", 
            "RootDeviceName": "/dev/xvda", 
            "VirtualizationType": "hvm", 
            "MetadataOptions": {
                "State": "pending", 
                "HttpEndpoint": "enabled", 
                "HttpTokens": "optional", 
                "HttpPutResponseHopLimit": 1
            }, 
            "AmiLaunchIndex": 0
        }
    ], 
    "ReservationId": "r-0a1b1a9a1ec18224e", 
    "Groups": [], 
    "OwnerId": "539081425481"
}
```
add a tag to frontend server
```
aws ec2 create-tags --resources i-0ce009ae902b07ea2 --tags Key=Name,Value=frontend-webserver
```
c.  Launch the backend database server with the required security group, keypair, subnet, and AMI.

```
$ aws ec2 run-instances --image-id ami-074dc0a6f6c764218 --count 1 --instance-type t2.micro --key-name ProjectAAngelKey.pem --security-group-ids sg-0c10150f23a244fb1 --subnet-id subnet-0ec91188932504c0c
{
    "Instances": [
        {
            "Monitoring": {
                "State": "disabled"
            }, 
            "PublicDnsName": "", 
            "StateReason": {
                "Message": "pending", 
                "Code": "pending"
            }, 
            "State": {
                "Code": 0, 
                "Name": "pending"
            }, 
            "EbsOptimized": false, 
            "LaunchTime": "2022-12-15T19:21:51.000Z", 
            "PrivateIpAddress": "172.16.164.44", 
            "ProductCodes": [], 
            "VpcId": "vpc-010d9bf0b371ea043", 
            "CpuOptions": {
                "CoreCount": 1, 
                "ThreadsPerCore": 1
            }, 
            "StateTransitionReason": "", 
            "InstanceId": "i-0c000e1a9630d2f7c", 
            "EnaSupport": true, 
            "ImageId": "ami-074dc0a6f6c764218", 
            "PrivateDnsName": "ip-172-16-164-44.ap-south-1.compute.internal", 
            "KeyName": "ProjectAAngelKey.pem", 
            "SecurityGroups": [
                {
                    "GroupName": "ProjectA-backendserver", 
                    "GroupId": "sg-0c10150f23a244fb1"
                }
            ], 
            "ClientToken": "decdcaa4-f617-48f7-ab96-fdaaaabdcb24", 
            "SubnetId": "subnet-0ec91188932504c0c", 
            "InstanceType": "t2.micro", 
            "CapacityReservationSpecification": {
                "CapacityReservationPreference": "open"
            }, 
            "NetworkInterfaces": [
                {
                    "Status": "in-use", 
                    "MacAddress": "0a:e5:b0:70:94:18", 
                    "SourceDestCheck": true, 
                    "VpcId": "vpc-010d9bf0b371ea043", 
                    "Description": "", 
                    "NetworkInterfaceId": "eni-051d3621e0f168169", 
                    "PrivateIpAddresses": [
                        {
                            "PrivateDnsName": "ip-172-16-164-44.ap-south-1.compute.internal", 
                            "Primary": true, 
                            "PrivateIpAddress": "172.16.164.44"
                        }
                    ], 
                    "PrivateDnsName": "ip-172-16-164-44.ap-south-1.compute.internal", 
                    "InterfaceType": "interface", 
                    "Attachment": {
                        "Status": "attaching", 
                        "DeviceIndex": 0, 
                        "DeleteOnTermination": true, 
                        "AttachmentId": "eni-attach-04ee0da03b723ddf9", 
                        "AttachTime": "2022-12-15T19:21:51.000Z"
                    }, 
                    "Groups": [
                        {
                            "GroupName": "ProjectA-backendserver", 
                            "GroupId": "sg-0c10150f23a244fb1"
                        }
                    ], 
                    "Ipv6Addresses": [], 
                    "OwnerId": "539081425481", 
                    "SubnetId": "subnet-0ec91188932504c0c", 
                    "PrivateIpAddress": "172.16.164.44"
                }
            ], 
            "SourceDestCheck": true, 
            "Placement": {
                "Tenancy": "default", 
                "GroupName": "", 
                "AvailabilityZone": "ap-south-1b"
            }, 
            "Hypervisor": "xen", 
            "BlockDeviceMappings": [], 
            "Architecture": "x86_64", 
            "RootDeviceType": "ebs", 
            "RootDeviceName": "/dev/xvda", 
            "VirtualizationType": "hvm", 
            "MetadataOptions": {
                "State": "pending", 
                "HttpEndpoint": "enabled", 
                "HttpTokens": "optional", 
                "HttpPutResponseHopLimit": 1
            }, 
            "AmiLaunchIndex": 0
        }
    ], 
    "ReservationId": "r-024bc9269912cdd54", 
    "Groups": [], 
    "OwnerId": "539081425481"
}
```
add tag to backend server
```
$ aws ec2 create-tags --resources i-0c000e1a9630d2f7c --tags Key=Name,Value=backend-db
```

### Step 11

> Creating a Wordpress website.

a.  SSH to the bastion server using its public IP and update the hostname.
```
ssh -i ProjectAAngelKey.pem ec2-user@3.110.117.113
The authenticity of host '3.110.117.113 (3.110.117.113)' can't be established.
ECDSA key fingerprint is SHA256:ZlmdBHdD70fSKMlubQBv1xt8ssjVUU1fr3hDfscarAk.
ECDSA key fingerprint is MD5:6c:bb:0c:ab:94:5a:f5:e3:7f:aa:0b:9c:a3:76:dd:0c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '3.110.117.113' (ECDSA) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
19 package(s) needed for security, out of 38 available
Run "sudo yum update" to apply all updates.
```
```
hostnamectl set-hostname bastion.ap-south-1.compute.internal
```
b. Create/copy the SSH key file ProjectAKey.pem and set the file permission to 400

c. SSH to the frontend server  from bastion server via the private IP of frontend server

```
ssh -i ProjectAAngelKey.pem ec2-user@172.16.34.151
The authenticity of host '172.16.34.151 (172.16.34.151)' can't be established.
ECDSA key fingerprint is SHA256:Ooo74j9gElkI19Z+Jw8J+75qxkBH/7aYgjUTnfVWvEo.
ECDSA key fingerprint is MD5:13:3b:b4:45:87:eb:41:29:15:39:3e:03:4e:63:8e:65.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.16.34.151' (ECDSA) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
19 package(s) needed for security, out of 38 available
Run "sudo yum update" to apply all updates.
```
```
sudo hostnamectl set-hostname frontend.ap-south-1.compute.internal
```
d. SSH to backend server from bastion server and set hostname

```
ssh -i ProjectAAngelKey.pem ec2-user@172.16.164.44
The authenticity of host '172.16.164.44 (172.16.164.44)' can't be established.
ECDSA key fingerprint is SHA256:Zc5QluGBSAKlUu0FzzQ2BvrBOuZK2RBdQGBrHecxEJw.
ECDSA key fingerprint is MD5:e9:d7:3b:64:34:b8:e4:cc:b7:e5:19:45:ff:c7:db:cb.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.16.164.44' (ECDSA) to the list of known hosts.

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
19 package(s) needed for security, out of 38 available
Run "sudo yum update" to apply all updates.
```
```
$ sudo hostnamectl set-hostname backend.ap-south-1.compute.internal
```
e. Installed, started, and enabled MariaDB on backend server

```
sudo yum install mariadb-server -y
sudo systemctl restart mariadb.service
sudo systemctl enable mariadb.service

```
f.  Set database root password.
```
$ mysql_secure_installation
```
g. Create a database and user, and grant the privileges.
```
$ mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 10
Server version: 5.5.68-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> create database wpdb;
Query OK, 1 row affected (0.00 sec
MariaDB [(none)]> create user 'wpdbuser'@'%' identified by 'xxxxxxxxx';
Query OK, 0 rows affected (0.00 sec)
MariaDB [(none)]> grant all privileges on wpdb.* to 'wpdbuser'@'%';
Query OK, 0 rows affected (0.00 sec)
MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

h. SSH to the frontend server using its private IP and do the follwoing;
- Install Apache and PHP.
- Download Wordpress, extract it, and move the contents to the document root.
- Update the database credentials(database name, username, password, and private IP of the backend database server) in the wp-config file. 

```
$ sudo yum install httpd -y
$ sudo amazon-linux-extras install php7.4
$ sudo systemctl restart httpd.service
$ sudo systemctl enable httpd.service
$ wget https://wordpress.org/latest.zip
$ unzip latest.zip
$ ll
$ sudo mv wordpress/* /var/www/html/
$ sudo mv /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
$ sudo vi /var/www/html/wp-config.php
```
i. Access the website using the public IP, public DNS name, or domain name(If the domain is pointing to the server IP)  to complete the Wordpress installation and design the website.


