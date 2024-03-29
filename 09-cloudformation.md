# cloudformation
## links
* [cloud formation templates examples](https://github.com/orgs/aws-samples/repositories?q=cloudformation)
  * [WordPress architecture](https://github.com/aws-samples/aws-refarch-wordpress)
  * [private cloud to on-prem network](https://github.com/udacity/nd9991-c2-Infrastructure-as-Code-v1-Exercises_Solution/tree/master/lesson-2-Infrastructure%20as%20Code)
* [cloud formation resources description](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)

## create stack from yaml
```sh
URL_TO_STACK=https://s3.amazonaws.com/aws-refarch/wordpress/latest/templates/aws-refarch-wordpress-master-newvpc.yaml
STACK_NAME=WordPress

x-www-browser https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=$STACK_NAME&templateURL=$URL_TO_STACK
```  

## create vpc
```sh
cat files/cloudformation-vpc.yaml
```
```sh
CLOUDFORMATION_STACK=udacity-1

aws cloudformation create-stack --stack-name $CLOUDFORMATION_STACK --region us-east-1 --template-body file://files/cloudformation-vpc.yaml
aws cloudformation describe-stacks --stack-name $CLOUDFORMATION_STACK
# aws cloudformation delete-stack  --stack-name $CLOUDFORMATION_STACK
```

## create VPC IGW PublicSubnet EC2 with apache server
![cloudformation-vpc-igw-subnet-ec2](https://user-images.githubusercontent.com/8113355/230791715-158046f5-8d08-465e-9389-18d3d1c46a0b.png)  
```sh
CLOUDFORMATION_STACK=udacity-apache-server
# delete stack 
aws cloudformation delete-stack --stack-name $CLOUDFORMATION_STACK --region us-east-1

# create stack
aws cloudformation create-stack --stack-name $CLOUDFORMATION_STACK --region us-east-1 --debug \
--template-body file://files/cloudformation-vpc-igw-subnet-ec2.yaml \
--parameters ParameterKey=VpcName,ParameterValue=cf-apache-server  ParameterKey=Ec2KeyPairName,ParameterValue=cherkavi

# update stack 
aws cloudformation update-stack 
```

### parameters can be described as json file
```sh
 --parameters file://my-parameters.json
```
my-parameters.json
```json
[
	{
		"ParameterKey": "VpcName",
		"ParameterValue": "cf-apache-server"
	}, 
	{
		"ParameterKey": "Ec2KeyPairName",
		"ParameterValue": "cherkavi"
	}
]
```

### overwrite parameters ( for instance default values )
```sh
aws cloudformation deploy --parameter-overrides \
  Subnets="$SUBNET1,SUBNET2"
  VpcId=$VPC_ID \
  InstanceType=$EC2_INSTANCE_TYPE
```

### ec2 security group another way of writing full out access
```yaml
      SecurityGroupEgress:
      - IpProtocol: tcp
        FromPort: 0
        ToPort: 65535
        CidrIp: 0.0.0.0/0
```

## High load application
* [online diagram of solution](https://online.visual-paradigm.com/w/xqyroxcb/diagrams/#diagram:workspace=xqyroxcb&proj=2&id=33)  
* ![final-vision](https://user-images.githubusercontent.com/8113355/235377012-3f575fb0-d732-43f5-858d-c69b2bf259f4.png)  
* [hometask submission](https://review.udacity.com/#!/reviews/4009545)

### create vpc,subnet,igw,nat
```sh
PROJECT_PREFIX=udacity-ht-03
CLOUDFORMATION_STACK=udacity-network-01
CLOUDFORMATION_TEMPLATE=file://files/cloudformation-vpc-igw-nat.yaml
aws cloudformation create-stack \
--stack-name $CLOUDFORMATION_STACK --region $AWS_DEFAULT_REGION  \
--template-body $CLOUDFORMATION_TEMPLATE \
--parameters \
  ParameterKey=VpcName,ParameterValue=$PROJECT_PREFIX \
  ParameterKey=VpcNetworkMask,ParameterValue='10.0.0.0/16' \
  ParameterKey=SubnetPublicNetworkMask1,ParameterValue=10.0.0.0/24 \
  ParameterKey=SubnetPublicNetworkMask2,ParameterValue=10.0.1.0/24 \
  ParameterKey=SubnetPrivateNetworkMask1,ParameterValue=10.0.2.0/24 \
  ParameterKey=SubnetPrivateNetworkMask2,ParameterValue=10.0.3.0/24 \
  ParameterKey=Ec2KeyPairName,ParameterValue=cherkavi
```

![VPC + Subnet + RouteTable + Route + IGW/NAT](https://github.com/cherkavi/udacity-cloud-devops/assets/8113355/7d1d99ea-c426-4988-9e6a-66d84dd59d75)

### create iam role, security group, launch config, target group
```sh
PROJECT_PREFIX=udacity-ht-03
CLOUDFORMATION_STACK=udacity-permissions-settings-02
CLOUDFORMATION_TEMPLATE=file://files/cloudformation-role-launchconfig.yaml

cloudformation-delete
aws cloudformation create-stack \
--stack-name $CLOUDFORMATION_STACK --region $AWS_DEFAULT_REGION \
--parameters \
  ParameterKey=VpcName,ParameterValue=$PROJECT_PREFIX \
  ParameterKey=VpcNetworkMask,ParameterValue='10.0.0.0/16' \
  ParameterKey=Ec2InstanceType,ParameterValue='t2.micro' \
--template-body $CLOUDFORMATION_TEMPLATE --capabilities CAPABILITY_NAMED_IAM
```

### [bastion host](https://cloudacademy.com/blog/aws-bastion-host-nat-instances-vpc-peering-security/)

### create loadbalancer
![LoadBalancer dependencies](https://user-images.githubusercontent.com/8113355/235375706-57803dc5-07f9-482c-b858-9cade7e6c1d0.png)
> next step after: file://files/cloudformation-role-launchconfig.yaml
```sh
PROJECT_PREFIX=udacity-ht-03
CLOUDFORMATION_STACK=udacity-loadbalancer-03
CLOUDFORMATION_TEMPLATE=file://files/cloudformation-load-balancer.yaml

cloudformation-delete
aws cloudformation create-stack \
--stack-name $CLOUDFORMATION_STACK --region $AWS_DEFAULT_REGION \
--template-body $CLOUDFORMATION_TEMPLATE \
--capabilities "CAPABILITY_IAM" "CAPABILITY_NAMED_IAM" \
--parameters \
  ParameterKey=VpcName,ParameterValue=$PROJECT_PREFIX  
```

## Errors
### Template format error: unsupported structure.
solution:
> check format of path to file, for example:
> --template-body file://file-in-current-folder.yaml


### if set not existing type: ParameterKey=Ec2InstanceType,ParameterValue='t1.tiny', error will be:
>  "User: arn:aws:sts::...:assumed-role/...=... is not authorized to perform: autoscaling:CreateLaunchConfiguration on resource: arn:aws:autoscaling:...
