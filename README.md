# infrastructure

aws cloudformation create-stack --stack-name mynewvpc --template-body file://csye6225-infra.yaml --parameters ParameterKey=VpcCidrBlock,ParameterValue="12.0.0.0/16" ParameterKey=subnet01,ParameterValue="12.0.0.0/24" ParameterKey=subnet02,ParameterValue="12.0.1.0/24" ParameterKey=subnet03,ParameterValue="12.0.2.0/24" 