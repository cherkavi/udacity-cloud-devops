# cloudformation
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