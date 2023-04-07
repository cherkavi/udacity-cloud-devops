## local settings for aws connection

### aws console parameters
```sh
WORKING_DIR=$HOME_PROJECTS/github-mirror/udacity-cloud-devops

AWS_PROFILE=cherkavi-udactity
AWS_REGION=us-east-1
AWS_KEY_PAIR_NAME=cherkavi
AWS_KEY_PAIR=$WORKING_DIR/key-pairs/${AWS_KEY_PAIR_NAME}.pem
```

### aws credentials
```sh
vim ~/.aws/credentials
```
```sh
# aws cli version 2
aws configure set aws_access_key_id ...
aws configure set aws_secret_access_key ...
aws configure set aws_session_token ...

# aws cli version 1
AWS_PROFILE=cherkavi-udactity
aws configure set ${AWS_PROFILE}.aws_access_key_id ...
aws configure set ${AWS_PROFILE}.aws_secret_access_key ...
aws configure set ${AWS_PROFILE}.aws_session_token ...

aws configure list

# in case of having issue like:
# An error occurred (UnauthorizedOperation) when calling the DescribeInstances operation: You are not authorized to perform this operation.
# pls set proper AWS_REGION
```

## ec2 
### ec2 start
```sh
# https://docs.aws.amazon.com/cli/latest/reference/ec2/start-instances.html
# AMI catalog: https://us-east-1.console.aws.amazon.com/ec2/home?region=us-east-1#AMICatalog:
aws ec2 run-instances --image-id ami-0557a15b87f6559cf --count 1 --instance-type t2.micro --key-name ${AWS_KEY_PAIR_NAME} --security-group-ids launch-wizard-1
```

### ec2 status
```sh
# https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html
aws ec2 describe-instances
# ec2 filter running instances 
aws ec2 describe-instances --filter "Name=key-name,Values=cherkavi" "Name=instance-state-name,Values=running" --query "Name=network-interface.addresses.private-ip-address,Values=172.*" --query 'Reservations[*].Instances[*].{InstanceId:InstanceId,KeyName:KeyName,State:State.Name, IP:NetworkInterfaces[0].PrivateIpAddresses[0].Association.PublicIp}'
```

### ec2 connect
```sh
ssh -i $AWS_KEY_PAIR  ubuntu@52.91.202.80
```

### ec2 stop
```sh
aws ec2 terminate-instances --instance-ids i-0443c8f92bfe9fab4
```

### ec2 run on nacked VPC
* create VPC
    * Enable DNS resolution
    * Enable DNS hostnames
* create internet gateway
* internet gateway attach to vpc
* vpc->route tables->edit routes->add route->0.0.0.0/0, internet gateway
* create ec2, network->edit
    * select public subnet
    * auto-assign public IP
    * security group with port 22

## ebs - Elastic Block Storage
### check volume
```sh
sudo file -s /dev/xvdf
```
### create filesystem on the volume
```sh
sudo mkfs -t xfs /dev/xvdf
```

### mount ebs to instance
> To mount an attached EBS volume on every system reboot, add an entry for the device to the /etc/fstab 
```sh
sudo mkdir /external-ebs
sudo mount /dev/xvdf /external-ebs
cd /external-ebs
```
