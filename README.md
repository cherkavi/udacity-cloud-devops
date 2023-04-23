# [udacity-cloud-devops](https://learn.udacity.com/my-programs?tab=Currently%2520Learning)

## end working session
```sh
### remove all elements from AWS
# terminate vpc
aws ec2 describe-vpcs --query 'Vpcs[].VpcId' --output text | xargs -n 1 aws ec2 delete-vpc --vpc-id
# delete all cloud-formation stacks
for stack in $(aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE --query 'StackSummaries[].StackName' --output text); do
    echo "Deleting stack $stack"
    aws cloudformation delete-stack --stack-name $stack
    aws cloudformation wait stack-delete-complete --stack-name $stack
done
# delete all database instances
aws rds describe-db-instances | jq -r '.DBInstances[].DBInstanceIdentifier' | xargs -I {} aws rds delete-db-instance --db-instance-identifier {}

# delete redschift cluster
# ToDo

### ------------------------------------------------------
### check for empty output
aws ec2 describe-vpcs --query 'Vpcs[].VpcId' --output text
aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE --query 'StackSummaries[].StackName'
aws rds describe-db-instances | jq -r '.DBInstances[].DBInstanceIdentifier'
```

## start working session
1. Launch Cloud Gateway, 
2. select all text in "modal window"
3. copy
4. go to Visual Code -> new tab
5. ctrl-p 
6. > macros
7. udacity credentials
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
```

## Links
* [architecture examples and diagrams](https://aws.amazon.com/architecture/)
* [course materials](https://github.com/udacity/nd9991-c2-Infrastructure-as-Code-v1-Exercises_Solution)
* draw tools
  * https://www.lucidcharts.com
  * https://online.visual-paradigm.com/