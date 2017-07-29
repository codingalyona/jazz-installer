# jazz-installer
Jazz Serverless Development Framework: Developer Preview Installer

## Jazz Serverless Development Framework

# Overview


# Prerequisites
* Create AWS account with necessary permissions.The AWS account should have privillage to create the following
  AWS Resources in us-east-1 region.
* Use your GitHub account to clone the repo
* Use RHEL 7 instance as your installer box.
* SSH to RHEL 7 instance and follow steps below,
 
 1. curl -L    https://raw.githubusercontent.com/tmobile/jazz-installer/master/installscripts/terraforminstaller/rhel7Installer.sh?token=AcuYLenmwbouJf97yJnDDi8dfVBjnR4Sks5ZhjtrwA%3D%3D -o rhel7Installer.sh
 1. chmod +x rhel7Installer.sh
 2. ./rhel7Installer.sh
 
This will prompt to enter AWS credentials (Access key, Secret key, Region and output format)

    AWS Access Key ID [None]:
    AWS Secret Access Key [None]:
    Default region name [None]:
    Default output format [None]: 
		
Example : 

    AWS Access Key ID = AKIAJ24MZSJQ7SYWXUNA
    AWS Secret Access Key = 2CZO1VgW4XdX/bg+tzHEc0E9NZY1J3omY6Uw/N+c
    Default region name= us-east-1
    Default output format=json
		
cd ./jazz-installer/installscripts/wizard

## Scenario 1 - Building full stack (Network, Instances and the rest of the stack).
 1. ./run.py
 
 Follow the prompts:
 
     Please provide the tag Name to Prefix your Stack(Eg:- jazz10 ): jazz123
     Do you need full stack including network(Y/N): Y
     
## Scenario 2 - Building stack in an existing Network (provide Network information to create instances and the rest of the stack).
1. ./run.py

Follow the prompts:

     Please provide the tag Name to Prefix your Stack(Eg:- jazz10 ): jazz123
     Do you need full stack including network(Y/N): n
     Do you have existing Jenkins and Bitbucket Server(Y/N): n
     We will create Jenkins and Bitbucket Servers using the Network Stack you provided
     Please have vpc,subnet and cidr blocks handy
     Please provide VPC id: vpc-e1b9b784
     Please provide subnet id: subnet-c5caafee
     Please provide CIDR BLOCK: 172.31.0.0/16
     
## Scenario 3 - Building stack using existing jenkins/BitBucket instances(provide existing bitbucket/jenkins information to create the rest of the stack).

Note: 

* Please create the following adminid/password on Jenkins Server before you proceed: jenkinsadmin/jenkinsadmin
* Please create the following adminid/password on Bitbucket Server before you proceed: jenkins1/jenkinsadmin
* For your convenience we have provided scripts to create Jenkins/Bitbucket servers to test the scenario using existing network.
Please follow the section,
Create Jenkins/Bitbucket servers for Scenario 3

1. ./run.py

Follow the prompts:

     Please provide the tag Name to Prefix your Stack(Eg:- jazz10 ): jazz700
     Do you need full stack including network(Y/N): n
     Do you have existing Jenkins and Bitbucket Server(Y/N): y
     Please create the following adminid/password on Jenkins Server before you proceed: jenkinsadmin/jenkinsadmin
     Please create the following adminid/password on Bitbucket Server before you proceed: jenkins1/jenkinsadmin
     Press the <ENTER> key to continue...
     Please provide Jenkins Server ELB URL: jazz121-jenkinselb-249111302.us-east-1.elb.amazonaws.com
     Please provide Jenkins Server PublicIp: 54.88.1.198
     Please provide Bitbuckket  Server ELB URL: jazz121-bitbucketelb-1289508647.us-east-1.elb.amazonaws.com
     Please provide bitbucket Server PublicIp: 54.90.228.149
     
## Create Jenkins/Bitbucket servers for Scenario 3

      cd ./jazz-installer/installscripts/terraform-unix-existinginstances-jazz/
    
* Change the text **"replace here"** with proper values in envprefix.tf

      variable "envPrefix" { type = "string" default = "replace here" }
      variable "tagsOwner" { type = "string" default = "replace here" }
      
* Change the VPC, Subnet and CIDR with proper values in variables.tf

      variable "vpc" {
      type = "string"
      default = "**vpc-e1b9b784**"   // us-east-1
      }
      variable "subnet" {
      type = "string"
      default = "**subnet-c5caafee**"         // us-east-1
      }
      variable "cidrblocks" {
      type = "string"
      default = "**172.31.0.0/16**"
      }
      
* Execute the following in ./jazz-installer/installscripts/terraform-unix-existinginstances-jazz/       
      
      nohup ./scripts/create.sh &
     
* Once the script is complete, please pick the values from **./jazz-installer/installscripts/terraform-unix-existinginstances-jazz/settings.txt**

    
# AWS Resources 
    AWS AMIs
    AWS::ApiGateway
    AWS::CloudFront
    AWS::Cognito
    AWS::DynamoDB
    AWS::IAM::Policy
    AWS::EC2::Instance
    AWS::ElasticLoadBalancing::LoadBalancer
    AWS::Lambda
    AWS::S3::Bucket
    AWS::Elasticsearch::Domain

# Limitations
We are creating the stack on us-east-1 region. Because us-east-2 has permission issue with s3 Bucket and Cognito resource is not available in us-west-1 region.

# Stack Creation Flow
Terraform will create the stack with the following AWS resources,

    AWS::Cognito
    Cognito_user_pool
    AWS::EC2::SecurityGroup
    Aws_security_group.bitbucketelb
    Aws_security_group.jenkins
    Aws_security_group.bitbucket
    Aws_security_group.jenkinselb
    AWS::S3::Bucket
    Aws_s3_bucket.cloudfrontlogs
    AWS::DynamoDB
    Aws_dynamodb_table.dynamodb-table-stg
    Aws_dynamodb_table.dynamodb-table-dev
    Aws_dynamodb_table.dynamodb-table-prod
    AWS::ApiGateway
    Aws_api_gateway_rest_api.jazz-dev
    Aws_api_gateway_rest_api.jazz-prod
    Aws_api_gateway_rest_api.jazz-stag
    AWS::Lambda
    Aws_iam_role.lambda_role
    AWS::S3::Bucket
    Aws_s3_bucket.oab-apis-deployment-dev
    Aws_s3_bucket.oab-apis-deployment-stg
    Aws_s3_bucket.oab-apis-deployment-prod
    Aws_s3_bucket.jazz-web
    AWS::CloudFront
    Aws_cloudfront_origin_access_identity.origin_access_identity
    Aws_cloudfront_distribution.jazz
    AWS::IAM::Policy
    Aws_iam_role_policy_attachment.apigatewayinvokefullAccess
    Aws_iam_role_policy_attachment.lambdafullaccess
    Aws_iam_role_policy_attachment.cloudwatchlogaccess
    AWS::ElasticLoadBalancing::LoadBalancer
    Aws_elb.bitbucketelb
    Aws_elb.jenkinselb
    AWS::Elasticsearch::Domain
    Aws_elasticsearch_domain
    Aws_elasticsearch_domain_policy.elasticsearchdomain_policy
    AWS::EC2::Instance
    Aws_instance.bitbucketserver
    Aws_instance.jenkinsserver
    AWS::ElasticLoadBalancing::LoadBalancer
    Aws_elb_attachment.jenkins
    Aws_elb_attachment.bitbucket

* The following software should be installed on the existing bitbucket server
 
         chefdk - 1.4.3
         git 2.9.4
         Java version: 1.8.0_131
         atlassian-bitbucket-5.0.2
* Please install the following addons as well

        Bitbucket CLI Connector
        bitbucket-webhooks
        Bitbucket Web Post Hooks Plugin

* On the existing bitbucket server please create  admin user with

        userid : jenkins1
        password jenkinsadmin. 
* On the Jenkins Server please install the following 

        chefdk - 1.4.3
        UnZip 6.00
        git 2.9.4
        curl 7.29.0
        apache-maven-3.5.0
        Java version: 1.8.0_131
        npm 5.0.3
        node v8.1.3
        aws cli
        aws api gateway importer
        install serverless using npm install
        opensource jenkins -2.68
* Please ensure the following plugins exist in the Jenkins Server. If not please install the missing ones

        bouncycastle-api
        pipeline-input-step
        cloudbees-folder
        branch-api
        structs
        docker-commons
        junit
        antisamy-markup-formatter
        pam-auth
        windows-slaves
        pipeline-build-step
        display-url-api
        docker-workflow
        mailer
        ldap
        token-macro
        external-monitor-job
        icon-shim
        matrix-auth
        pipeline-model-api
        script-security
        matrix-project
        build-timeout
        credentials
        ssh-credentials
        workflow-step-api
        plain-credentials
        git-client
        credentials-binding
        timestamper
        jackson2-api
        scm-api
        workflow-api
        workflow-support
        github-api
        durable-task
        workflow-durable-task-step
        git-server
        resource-disposer
        ws-cleanup
        ant
        git
        gradle
        workflow-cps
        pipeline-milestone-step
        workflow-cps-global-lib
        jquery-detached
        ace-editor
        workflow-scm-step
        pipeline-stage-step
        github
        workflow-job
        pipeline-stage-view
        pipeline-graph-analysis
        pipeline-rest-api
        handlebars
        momentjs
        pipeline-model-extensions
        workflow-multibranch
        authentication-tokens
        pipeline-stage-tags-metadata
        pipeline-model-declarative-agent
        workflow-basic-steps
        pipeline-model-definition
        workflow-aggregator
        github-branch-source
        pipeline-github-lib
        mapdb-api
        subversion
        ssh-slaves
        email-ext
        config-file-provider
        pipeline-npm
        mercurial
        cloudbees-bitbucket-branch-source
        bitbucket
        envinject
        build-user-vars-plugin
        javadoc
        jquery
        extended-choice-parameter
        aws-java-sdk
        aws-credentials
        cloudbees-credentials

* On the Jenkins Server please create an admin user

        Userid: jenkinsadmin
        Password: jenkinsadmin


