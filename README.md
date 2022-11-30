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

-------

Creating Instance from yaml: 
aws --profile=dev_adminuser cloudformation create-stack --stack-name myVpc --parameters ParameterKey=amiId,ParameterValue=ami-05d4539a461662834 --template-body file://csye6225-infra.yaml --region us-east-1

assignmnet -5
aws cloudformation create-stack --parameters ParameterKey=amiId,ParameterValue=ami-03da85911a66124a8 --template-body file://csye6225-infra.yaml --region us-east-1 --capabilities CAPABILITY_NAMED_IAM --stack-name myVpcnewer


aws cloudformation create-stack --parameters ParameterKey=amiId,ParameterValue=ami-046224a9f93f3cba3 ParameterKey=EnvironmentName,ParameterValue=demo ParameterKey=SshKeyPairName,ParameterValue=aws-demo --template-body file://csye6225-infra.yaml --region us-east-1 --capabilities CAPABILITY_NAMED_IAM --stack-name vpcdemolambda


EBSKeyPolicyForInstance:
    Type: AWS::IAM::Policy
    Properties:
      PolicyName: EC2-EBSKey-Policy
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Action:
              - kms:RetireGrant
              - kms:CreateGrant
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
              - kms:Encrypt
              - kms:DescribeKey
              - kms:Decrypt
              - kms:*
            Resource:
              - !GetAtt EBSKey.Arn
      Roles:
        - !Ref WebappS3IamRole

  RDSKeyPolicyForInstance:
    Type: AWS::IAM::Policy
    Properties:
      PolicyName: EC2-RDSKey-Policy
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Action:
              - kms:RetireGrant
              - kms:CreateGrant
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
              - kms:Encrypt
              - kms:DescribeKey
              - kms:Decrypt
              - kms:*
            Resource:
              - !GetAtt RDSKey.Arn
      Roles:
        - !Ref WebappS3IamRole

  EBSKeyAlias:
    Type: AWS::KMS::Alias
    Properties:
      AliasName: alias/key_for_EBS_encrytpion
      TargetKeyId: !Ref EBSKey
  
  EBSKey:
    Type: AWS::KMS::Key
    Properties:
      EnableKeyRotation: true
      KeyPolicy:
        Version: 2012-10-17
        Id: ebs-kms-key
        Statement:
          - Sid: Allow Administration of the key
            Effect: Allow
            Principal:
              AWS: arn:aws:iam::620068443483:user/*
            Action:
              - kms:Create*
              - kms:Describe*
              - kms:Enable*
              - kms:List*
              - kms:Put*
              - kms:Update*
              - kms:Revoke*
              - kms:Disable*
              - kms:Get*
              - kms:Delete*
              - kms:ScheduleKeyDeletion
              - kms:CancelKeyDeletion
              - kms:Encrypt
              - kms:Decrypt
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
              - kms:DescribeKey
            Resource: '*'
          - Sid: Allow root Administration of the key
            Effect: Allow
            Principal:
              AWS: arn:aws:iam::620068443483:root
            Action:
              - kms:Create*
              - kms:Describe*
              - kms:Enable*
              - kms:List*
              - kms:Put*
              - kms:Update*
              - kms:Revoke*
              - kms:Disable*
              - kms:Get*
              - kms:Delete*
              - kms:ScheduleKeyDeletion
              - kms:CancelKeyDeletion
              - kms:Encrypt
              - kms:Decrypt
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
              - kms:DescribeKey
            Resource: '*'
          - Sid: Allow use of the key
            Effect: Allow
            Principal:
              AWS: arn:aws:iam::909665281672:role/aws-service-role/autoscaling.amazonaws.com/AWSServiceRoleForAutoScaling
            Action:
              - kms:Encrypt
              - kms:Decrypt
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
              - kms:DescribeKey
              - kms:Create*
              - kms:Describe*
              - kms:Enable*
              - kms:List*
              - kms:Put*
              - kms:Update*
              - kms:Revoke*
              - kms:Disable*
              - kms:Get*
              - kms:Delete*
              - kms:ScheduleKeyDeletion
              - kms:CancelKeyDeletion
            Resource: '*'
          - Sid: Allow persistent resources
            Effect: Allow
            Principal:
              AWS: arn:aws:iam::620068443483:role/aws-service-role/autoscaling.amazonaws.com/AWSServiceRoleForAutoScaling
            Action:
              - kms:CreateGrant
              - kms:ListGrants
              - kms:RevokeGrant
            Resource: '*'
            Condition:
              Bool:
                'kms:GrantIsForAWSResource': true

  RDSKeyAlias:
    Type: AWS::KMS::Alias
    Properties:
      AliasName: alias/key_for_RDS_encrytpion
      TargetKeyId: !Ref RDSKey
  
  RDSKey:
    Type: AWS::KMS::Key
    Properties:
      EnableKeyRotation: true
      KeyPolicy:
        Version: 2012-10-17
        Id: ebs-kms-key
        Statement:
          - Sid: Allow Administration of the key
            Effect: Allow
            Principal:
              AWS: arn:aws:iam::620068443483:user/*
            Action:
              - kms:Create*
              - kms:Describe*
              - kms:Enable*
              - kms:List*
              - kms:Put*
              - kms:Update*
              - kms:Revoke*
              - kms:Disable*
              - kms:Get*
              - kms:Delete*
              - kms:ScheduleKeyDeletion
              - kms:CancelKeyDeletion
              - kms:Encrypt
              - kms:Decrypt
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
              - kms:DescribeKey
            Resource: '*'
          - Sid: Allow root Administration of the key
            Effect: Allow
            Principal:
              AWS: arn:aws:iam::620068443483:root
            Action:
              - kms:Create*
              - kms:Describe*
              - kms:Enable*
              - kms:List*
              - kms:Put*
              - kms:Update*
              - kms:Revoke*
              - kms:Disable*
              - kms:Get*
              - kms:Delete*
              - kms:ScheduleKeyDeletion
              - kms:CancelKeyDeletion
              - kms:Encrypt
              - kms:Decrypt
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
              - kms:DescribeKey
            Resource: '*'
          - Sid: Allow use of the key
            Effect: Allow
            Principal:
              AWS: arn:aws:iam::620068443483:role/aws-service-role/rds.amazonaws.com/AWSServiceRoleForRDS
            Action:
              - kms:Encrypt
              - kms:Decrypt
              - kms:ReEncrypt*
              - kms:GenerateDataKey*
              - kms:DescribeKey
              - kms:Create*
              - kms:Describe*
              - kms:Enable*
              - kms:List*
              - kms:Put*
              - kms:Update*
              - kms:Revoke*
              - kms:Disable*
              - kms:Get*
              - kms:Delete*
              - kms:ScheduleKeyDeletion
              - kms:CancelKeyDeletion
            Resource: '*'
          - Sid: Allow persistent resources
            Effect: Allow
            Principal:
              AWS: arn:aws:iam::620068443483:role/aws-service-role/rds.amazonaws.com/AWSServiceRoleForRDS
            Action:
              - kms:CreateGrant
              - kms:ListGrants
              - kms:RevokeGrant
            Resource: '*'
            Condition:
              Bool:
                'kms:GrantIsForAWSResource': true


------

LoadBalancerSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
        GroupDescription: Security group for load balancer
        GroupName: load balancer
        VpcId: !Ref myVPC
        SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0
        - IpProtocol: tcp
          FromPort: 443
          ToPort: 443
          CidrIp: 0.0.0.0/0
        Tags:
        - Key: Name
          Value: !Ref LoadBalancerSG

  AutoScalingGroup:
    Type: AWS::AutoScaling::AutoScalingGroup
    Properties:
      MinSize: '1'
      MaxSize: '5'
      DesiredCapacity: '1'
      LaunchTemplate:
        LaunchTemplateId: !Ref LaunchTemplate
        Version: !GetAtt LaunchTemplate.LatestVersionNumber
      VPCZoneIdentifier:
      - !Ref PublicSubnet1
      - !Ref PublicSubnet2
      - !Ref PublicSubnet3
      TargetGroupARNs:
      - !Ref TargetGroup
      Cooldown: 60
      Tags:
        - Key: Name
          Value: CSYE6225-CloudApplication-ASG
          PropagateAtLaunch: "true"
