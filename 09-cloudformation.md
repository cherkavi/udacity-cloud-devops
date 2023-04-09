# cloudformation
## links
* [cloud formation templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
  
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
aws cloudformation create-stack --stack-name $CLOUDFORMATION_STACK --region us-east-1 \
--template-body file://files/cloudformation-vpc-igw-subnet-ec2.yaml \
--parameters ParameterKey=VpcName,ParameterValue=cf-apache-server \
--parameters ParameterKey=Ec2KeyPairName,ParameterValue=cherkavi

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

### ec2 security group another way of writing full output access
```yaml
      SecurityGroupEgress:
      - IpProtocol: tcp
        FromPort: 0
        ToPort: 65535
        CidrIp: 0.0.0.0/0
```
