# infrastructure
aws configure --profile=dev_adminuser
aws configure --profile=demo_adminuser

aws configure list --profile dev_adminuser

export AWS_PROFILE=dev_adminuser

aws configure list

aws cloudformation create-stack --stack-name mynewvpc --template-body file://csye6225-infra.yaml --parameters ParameterKey=VpcCidrBlock,ParameterValue="12.0.0.0/16" ParameterKey=subnet01,ParameterValue="12.0.0.0/24" ParameterKey=subnet02,ParameterValue="12.0.1.0/24" ParameterKey=subnet03,ParameterValue="12.0.2.0/24" 


region change :

aws cloudformation create-stack --stack-name myvpc --template-body file://csye6225-infra.yaml --parameters ParameterKey=VpcCidrBlock,ParameterValue="13.0.0.0/16" ParameterKey=subnet01,ParameterValue="13.0.0.0/24" ParameterKey=subnet02,ParameterValue="13.0.1.0/24" ParameterKey=subnet03,ParameterValue="13.0.2.0/24" --profile dev_adminuser  --region us-west-2

Deleting a stack:

aws cloudformation delete-stack --stack-name myvpc --profile demo_adminuser  --region us-east-1

