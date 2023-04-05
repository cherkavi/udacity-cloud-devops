# static web site
## create s3 bucket
```sh
# create bucket 
AWS_BUCKET_NAME="my-bucket-name" 
aws s3 mb s3://$AWS_BUCKET_NAME
# put public-read-write
aws s3api put-bucket-acl --bucket $AWS_BUCKET_NAME --acl public-read-write
# list buckets
aws s3api list-buckets
```

## upload files
```sh
wget https://video.udacity-data.com/topher/2020/May/5ecea462_udacity-starter-website/udacity-starter-website.zip

unzip -d udacity-starter-website udacity-starter-website.zip

aws s3 sync ./udacity-starter-website s3://$AWS_BUCKET_NAME/

rm -rf udacity-starter-website udacity-starter-website.zip

aws s3 ls --recursive s3://$AWS_BUCKET_NAME
```

## set policy, secure bucket
```sh
echo '{
    "Version": "2012-10-17",
    "Id": "policy-bucket-public",
    "Statement": [
        {
            "Sid": "statement-for-bucket",
            "Effect": "Allow",
            "Principal": "*", 
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::'$AWS_BUCKET_NAME'/*"
        }
    ]
}' >> bucket-policy.json

aws s3api put-bucket-policy --bucket $AWS_BUCKET_NAME --policy file://bucket-policy.json
rm bucket-policy.json

aws s3api get-bucket-policy --bucket $AWS_BUCKET_NAME --output json

# check public access to bucket 
x-www-browser https://$AWS_BUCKET_NAME.s3.amazonaws.com/index.html
```

## set static website hosting
```sh
aws s3 website s3://$AWS_BUCKET_NAME/ --index-document index.html

BUCKET_HOST=$AWS_BUCKET_NAME.s3-website-`aws configure get region`.amazonaws.com
BUCKET_URL=http://$BUCKET_HOST

# check access as website 
curl -X GET $BUCKET_URL
www-browser $BUCKET_URL
x-www-browser $BUCKET_URL
```

## distribute via cloud front
```sh
DISTRIBUTION_ID=$BUCKET_HOST'-cli-3'
DOMAIN_NAME=$BUCKET_HOST
echo '{
    "CallerReference": "cli-example",
    "Aliases": {
        "Quantity": 0
    },
    "DefaultRootObject": "index.html",
    "Origins": {
        "Quantity": 1,
        "Items": [
            {
                "Id": "'$DISTRIBUTION_ID'",
                "DomainName": "'$DOMAIN_NAME'",
                "OriginPath": "",
                "CustomHeaders": {
                    "Quantity": 0
                },
                "CustomOriginConfig": {
                    "HTTPPort": 80,
                    "HTTPSPort": 443,
                    "OriginProtocolPolicy": "http-only",
                    "OriginSslProtocols": {
                        "Quantity": 1,
                        "Items": [
                            "TLSv1.2"
                        ]
                    },
                    "OriginReadTimeout": 30,
                    "OriginKeepaliveTimeout": 5
                },
                "ConnectionAttempts": 3,
                "ConnectionTimeout": 10,
                "OriginShield": {
                    "Enabled": false
                },
                "OriginAccessControlId": ""
            }
        ]
    },
    "OriginGroups": {
        "Quantity": 0
    },
    "DefaultCacheBehavior": {
        "TargetOriginId": "'$DISTRIBUTION_ID'",
        "ForwardedValues": {
            "QueryString": false,
            "Cookies": {
                "Forward": "none"
            },
            "Headers": {
                "Quantity": 0
            },
            "QueryStringCacheKeys": {
                "Quantity": 0
            }
        },        
        "TrustedSigners": {
            "Enabled": false,
            "Quantity": 0
        },
        "TrustedKeyGroups": {
            "Enabled": false,
            "Quantity": 0
        },
        "ViewerProtocolPolicy": "redirect-to-https",
        "MinTTL": 0,
        "AllowedMethods": {
            "Quantity": 2,
            "Items": [
                "HEAD",
                "GET"
            ],
            "CachedMethods": {
                "Quantity": 2,
                "Items": [
                    "HEAD",
                    "GET"
                ]
            }
        },
        "SmoothStreaming": false,
        "Compress": true,
        "LambdaFunctionAssociations": {
            "Quantity": 0
        },
        "FunctionAssociations": {
            "Quantity": 0
        },
        "FieldLevelEncryptionId": ""
    },
    "CacheBehaviors": {
        "Quantity": 0
    },
    "CustomErrorResponses": {
        "Quantity": 0
    },
    "Comment": "",
    "PriceClass": "PriceClass_All",
    "Enabled": true,
    "ViewerCertificate": {
        "CloudFrontDefaultCertificate": true,
        "SSLSupportMethod": "vip",
        "MinimumProtocolVersion": "TLSv1",
        "CertificateSource": "cloudfront"
    },
    "Restrictions": {
        "GeoRestriction": {
            "RestrictionType": "none",
            "Quantity": 0
        }
    },
    "WebACLId": "",
    "HttpVersion": "http2",
    "IsIPV6Enabled": true,
    "Staging": false
}' > distribution-config.json
# vim distribution-config.json
aws cloudfront create-distribution --distribution-config file://distribution-config.json

aws cloudfront list-distributions | grep DomainName

# aws cloudfront list-distributions | grep '"Id":'
# aws cloudfront delete-distribution --id E6Q0X5NZY... --if-match E2ADZ1SMWE7...
```

## remove resources
```sh
### cloudfront delete
DISTRIBUTION_ID=`aws cloudfront list-distributions | jq -r ".DistributionList.Items[].Id"`
echo $DISTRIBUTION_ID | clipboard
aws cloudfront get-distribution --id $DISTRIBUTION_ID > $DISTRIBUTION_ID.cloud_front
DISTRIBUTION_ETAG=`jq -r .ETag $DISTRIBUTION_ID.cloud_front`
## disable distribution
# fx $DISTRIBUTION_ID.cloud_front
jq '.Distribution.DistributionConfig.Enabled = false' $DISTRIBUTION_ID.cloud_front |  jq '.Distribution.DistributionConfig' > $DISTRIBUTION_ID.cloud_front_updated 
aws cloudfront update-distribution --id $DISTRIBUTION_ID --if-match $DISTRIBUTION_ETAG --distribution-config file://$DISTRIBUTION_ID.cloud_front_updated 
## remove distribution
aws cloudfront get-distribution --id $DISTRIBUTION_ID > $DISTRIBUTION_ID.cloud_front
DISTRIBUTION_ETAG=`jq -r .ETag $DISTRIBUTION_ID.cloud_front`
aws cloudfront delete-distribution --id $DISTRIBUTION_ID --if-match $DISTRIBUTION_ETAG

### remove s3
aws s3 ls
AWS_BUCKET_NAME='cherkavi-bucket-for-static-web'
aws s3 rm s3://$AWS_BUCKET_NAME --recursive --include "*"
aws s3api delete-bucket --bucket $AWS_BUCKET_NAME
```