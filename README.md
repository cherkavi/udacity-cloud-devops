# [udacity-cloud-devops](https://learn.udacity.com/my-programs?tab=Currently%2520Learning)
## [current budget, balance](https://console.aws.amazon.com/billing/home#/)

## end working session
```sh
### remove all elements from AWS
# delete all cloud-formation stacks
for stack in $(aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE --query 'StackSummaries[].StackName' --output text); do
    echo "Deleting stack $stack"
    aws cloudformation delete-stack --stack-name $stack
    aws cloudformation wait stack-delete-complete --stack-name $stack
done
# delete all database instances
aws rds describe-db-instances | jq -r '.DBInstances[].DBInstanceIdentifier' | xargs -I {} aws rds delete-db-instance --db-instance-identifier {}
# terminate vpc
aws ec2 describe-vpcs --query 'Vpcs[].VpcId' --output text | xargs -n 1 aws ec2 delete-vpc --vpc-id

# delete redschift cluster
# ToDo

### ------------------------------------------------------
### check for empty output
aws ec2 describe-vpcs --query 'Vpcs[].VpcId' --output text
aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE --query 'StackSummaries[].StackName'
aws rds describe-db-instances | jq -r '.DBInstances[].DBInstanceIdentifier'
```

## start working session
### bash automatization 
```sh
WORKING_DIR=$HOME_PROJECTS/udacity-cloud-devops
export AWS_PROFILE=cherkavi-udactity
export AWS_REGION=us-east-1
export AWS_DEFAULT_REGION=us-east-1
export AWS_KEY_PAIR_NAME=cherkavi
export AWS_KEY_PAIR=$WORKING_DIR/key-pairs/${AWS_KEY_PAIR_NAME}.pem
pushd $WORKING_DIR

alias udacity-browser='open_url https://learn.udacity.com/my-programs?tab=Currently%2520Learning'

alias cloudformation-list="aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE --query 'StackSummaries[].StackName'"
function cloudformation-delete(){ 
    aws cloudformation delete-stack --stack-name $CLOUDFORMATION_STACK --region $AWS_DEFAULT_REGION 
}
function cloudformation-describe(){ 
    aws cloudformation describe-stacks --stack-name $CLOUDFORMATION_STACK --region $AWS_DEFAULT_REGION
}
function cloudformation-validate(){ 
    aws cloudformation validate-template --template-body $CLOUDFORMATION_TEMPLATE
}
```

### copy credentials from udacity html modal window
1. Launch Cloud Gateway, 
2. select all text in "modal window"
3. copy
4. go to Visual Code -> new tab, paste
5. ctrl-p 
6. > macros
7. udacity credentials
8. check connection:  
```sh
aws ec2 describe-instances
```

user for previous steps ( IAM ) has Policy: AdministratorAccess
macros for previous steps:
```json
    "macros.list": {
        "udacity credentials ": [
            "cursorPageUp",
            
            "cursorHome",
            "cursorLineEndSelect",
            {"command": "type", "args": {"text": "aws configure set aws_access_key_id " }},
            "deleteRight",
            "cursorDown",

            "cursorHome",
            "cursorLineEndSelect",
            {"command": "type", "args": {"text": "aws configure set aws_secret_access_key " }},
            "deleteRight",
            "cursorDown",

            "cursorHome",
            "cursorLineEndSelect",
            {"command": "type", "args": {"text": "aws configure set aws_session_token " }},
            "deleteRight",

            "cursorPageUp",
            "cursorHome",
            
            "editor.action.selectAll"
        ]
    }
```


## preparations
```
mkdir udacity
git clone https://github.com/udacity/nd9991-c2-Infrastructure-as-Code-v1-Exercises_Solution udacity/nd9991-c2-Infrastructure-as-Code-v1-Exercises_Solution

git clone  https://github.com/udacity/nd9991-c2-Infrastructure-as-Code-v1  udacity/nd9991-c2-Infrastructure-as-Code-v1 

git clone https://github.com/udacity/nd9991-c3-hello-world-exercise-solution
```

## Links
* [architecture examples and diagrams](https://aws.amazon.com/architecture/)
* [course materials](https://github.com/udacity/nd9991-c2-Infrastructure-as-Code-v1-Exercises_Solution)
* [questions to mentor](https://knowledge.udacity.com/activity/questions)
* draw tools
  * https://www.lucidcharts.com
  * https://online.visual-paradigm.com/
