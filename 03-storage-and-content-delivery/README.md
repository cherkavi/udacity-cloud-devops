## s3 bucket
### list of buckets
```sh
aws s3api list-buckets
aws s3api list-buckets --query "Buckets[].Name"
```

### create public bucket
```sh
AWS_BUCKET_NAME="udacity-cherkavi-001"
aws s3 mb s3://$AWS_BUCKET_NAME
aws s3api create-bucket --bucket $AWS_BUCKET_NAME --acl public-read-write
```

### remove bucket
```sh
aws s3 rb s3://$AWS_BUCKET_NAME
```

### copy file to s3
```sh
FILE_NAME=README.md
aws s3 cp $FILE_NAME s3://$AWS_BUCKET_NAME
aws s3 rm s3://$AWS_BUCKET_NAME/$FILE_NAME

aws s3 rm s3://$AWS_BUCKET_NAME --recursive --include "*"
```

## dynamo db
### create item 
```
TABLE_NAME=Music
aws dynamodb put-item \
--table-name $TABLE_NAME \
--item '{"Artist_Id":{"S":"002"}, "Artist":{"S":"Black"} }' \
--region=$AWS_REGION \
--return-consumed-capacity TOTAL 
```
### select all records
```sh
aws dynamodb scan --table-name $TABLE_NAME

aws dynamodb scan --table-name $TABLE_NAME \
--filter-expression "Artist_Id = :id" \
--expression-attribute-values '{":id":{"S":"001"}}'
```
### delete item 
```sh
aws dynamodb delete-item --table-name $TABLE_NAME --key '{"Artist_Id":{"S":"001"}}'
```

## rds
### list of instances
```sh
# rds info
aws rds describe-db-snapshots
aws rds describe-db-clusters

aws rds describe-db-instances 
aws rds describe-db-instances --db-instance-identifier $DB_NAME

# delete db instance 
# aws rds delete-db-instance --db-instance-identifier database-1
```

### connect to rds
```sh
DB_NAME="database-1"

DB_LOGIN=admin
DB_PASSWORD=adminadmin
DB_HOST=`aws rds describe-db-instances --db-instance-identifier $DB_NAME --query 'DBInstances[*].Endpoint.Address' | jq .[]`
DB_PORT=`aws rds describe-db-instances --db-instance-identifier $DB_NAME --query 'DBInstances[*].Endpoint.Port' | jq .[]`


echo  $DB_LOGIN
echo  $DB_PASSWORD
echo  $DB_URL
echo  $DB_PORT

mycli --user $DB_LOGIN --password $DB_PASSWORD --host $DB_HOST --port $DB_PORT --database $DB_NAME --execute 'show tables'

https://knowledge.udacity.com/questions/967808
```

### rds select/query
```sh
# query db
# https://docs.aws.amazon.com/cli/latest/reference/rds-data/execute-sql.html

DB_INSTANCE_ARN="arn:aws:rds:us-east-1:948919258198:db:database-1"
DB_LOGIN=admin
DB_PASSWORD=adminadmin
DB_SECRET_NAME=$DB_NAME-secret
DB_HOST=`aws rds describe-db-instances --db-instance-identifier $DB_NAME --query 'DBInstances[*].Endpoint'  | jq .[].Address`

aws secretsmanager create-secret \
    --name $DB_SECRET_NAME \
    --secret-string "{\"engine\":\"mysql\",\"username\":\"$DB_LOGIN\",\"password\":\"$DB_PASSWORD\",\"dbname\":\"$DB_NAME\",\"port\": \"3306\",\"host\": $DB_HOST}"

# not working ???
aws rds execute-sql ----db-cluster-or-instance-arn 
ws rds-data execute-sql --aws-secret-store-arn $DB_INSTANCE_ARN --db-cluster-or-instance-arn  $DB_SECRET_ARN --sql-statements 'show tables'
```

## cloud front distribution 
```sh
# create bucket
AWS_BUCKET_NAME=cherkavi-static-content
aws s3api create-bucket --bucket $AWS_BUCKET_NAME

# upload simple file 
FILE_NAME=index.html
aws s3 cp $FILE_NAME s3://$AWS_BUCKET_NAME

# remove all files
aws s3 ls s3://$AWS_BUCKET_NAME
aws s3 rm s3://$AWS_BUCKET_NAME  --recursive

aws s3 rb s3://$AWS_BUCKET_NAME
```