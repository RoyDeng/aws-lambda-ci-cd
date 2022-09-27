# aws-lambda-ci-cd

```
aws s3 mb s3://awscodepipeline-demobucket-roy-deng

aws s3api put-bucket-versioning --bucket awscodepipeline-demobucket-roy-deng --versioning-configuration Status=Enabled

aws s3 cp SampleApp_Linux.zip s3://awscodepipeline-demobucket-roy-deng/
```

```
aws iam create-role --role-name roy-deng-iamrole-ec2 --assume-role-policy-document file://ec2-trust-policy.json

aws iam create-instance-profile --instance-profile-name roy-deng-iamrole-ec2-profile

aws iam add-role-to-instance-profile --instance-profile-name roy-deng-iamrole-ec2-profile --role-name roy-deng-iamrole-ec2

aws iam attach-role-policy --policy-arn arn:aws:iam::aws:policy/service-role/AmazonEC2RoleforAWSCodeDeploy --role-name roy-deng-iamrole-ec2

aws iam attach-role-policy --policy-arn arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore --role-name roy-deng-iamrole-ec2

aws ec2 create-key-pair --key-name roy-deng-code-pipeline-key-pair --key-type rsa --key-format pem --query "KeyMaterial" --output text > roy-deng-code-pipeline-key-pair.pem

chmod 400 roy-deng-code-pipeline-key-pair.pem

aws ec2 run-instances --tag-specifications 'ResourceType=instance,Tags=[{Key=Name,Value=roy-deng-code-pipeline}]' --image-id ami-0c2ab3b8efb09f272 --count 2 --instance-type t2.micro --key-name roy-deng-code-pipeline-key-pair --iam-instance-profile Name="roy-deng-iamrole-ec2-profile"
```

## CodeDeploy

```
aws iam create-role --role-name roy-deng-iamrole-code-deploy --assume-role-policy-document file://code-deploy-trust-policy.json

aws iam attach-role-policy --policy-arn arn:aws:iam::aws:policy/service-role/AWSCodeDeployRole --role-name roy-deng-iamrole-code-deploy

aws deploy create-application --application-name roy-deng-code-deploy-application --compute-platform Server

aws deploy create-deployment-group --application-name roy-deng-code-deploy-application --deployment-group-name roy-deng-code-deploy-application-deployment-group --service-role-arn arn:aws:iam::486377825449:role/roy-deng-iamrole-code-deploy --deployment-style deploymentType=IN_PLACE,deploymentOption=WITHOUT_TRAFFIC_CONTROL --ec2-tag-filters Key=Name,Value=roy-deng-code-pipeline,Type=KEY_AND_VALUE
```

```
aws iam create-role --role-name roy-deng-iamrole-code-deploy --assume-role-policy-document file://code-deploy-trust-policy.json


aws codepipeline create-pipeline
```

```
aws iam create-role --role-name roy-deng-iamrole-lambda --assume-role-policy-document file://lambda-trust-policy.json
```

```
aws iam attach-role-policy --role-name roy-deng-iamrole-lambda --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
```

```
cd test_api
zip test-api.zip lambda_function.py
```

```
aws lambda create-function --function-name my-test-function --zip-file fileb://test-api.zip --handler lambda_function.lambda_handler --runtime python3.9 --role arn:aws:iam::486377825449:role/lambda-ex

aws lambda list-functions --max-items 10

aws lambda update-function-code --function-name my-test-function --zip-file fileb://test-api.zip
```