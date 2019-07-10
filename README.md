# AwsCodebuild-deployandcodepipeline

Aws Continuous Delivery Tools
CodeDeploy, CodeBuild and Codepipeline

Deploying apps - with codedeploy
Continuous integration - with codebuild 
Automating software releases - with Aws codepipeline

Benefits of CodeDeploy:-
   Automate your deployments
   Fully managed serverless service
   Free with AWS
   $0.02 per on-premises instance update
   Centralize control
   Versatile
   
Components of CodeDeploy:-
Application - The Core Component
  A unique name
  Compute platform (EC2/On-premises,AWS Lambda, Amazon ECS)
Deployment Group - A target for your deployment
  EC2/on-premises (Autoscaling groups,EC2 instance tags, On-premises hosts)
  Lambda (Lambda function name)
  ECS (An ECS Cluster+ An ECS service + A load balancer + Target Group1 + Target Group2)
Deployment  - Roll out an app revision.
  Deployment group - Targets for the deployment
  Deployment type  - "In Place" or "Blue-Green"
  Deployment Configuration - such as "on At a Time"
  Revision    - An application version   
    ( Ec2/on-premises -Github commit id or zip file in S3 Bucket
      Lambda          -AppSpec file, Function name, Function alias, Version info
      ECS             -AppSpec file, Task Definition version, Container name,container port )

The AppSpec File:- 

 AppSpec File: appspec.yml
 appspec file should be at the root of the revision 
 appspec file should at the root of the github repository
 S3 Revision's Zip file  should be present at the root directory of zip file

  version: 0.0
  os: linux
  files:
    - source: /src
      destination: /var/www/html
  hooks:
    BeforeInstall:
    - location: scripts/before_install.sh
      timeout: 300
      runas: root
      
The CodeDeploy-agent:-
   EC2 or on-premises instance 

example - install Codedeploy -Red Hat

#!/bin/bash -xe
yum install -y ruby

cd /opt
curl -O https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install  

chmod +x ./install
./install auto

------------------

demo

Create an IAM role with s3 access
Install the codedeploy-agent
Ensure the service is running
Check the CodeDeploy log

When we are storeing codedeploy revision in s3 bucket ,
we need to give EC2 Instance Permission to access that bucket
By creating IAM role and attaching to ec2 instance.

Created a role codedeploye in IAM and attached to EC2 instance. 
Codedeploy agent script is added in EC2 Instance.

systemctl status codedeploy-agent
journalctl -u codedeploy-agent
less /opt/codedeploy-agent/deployment-root/deployment-logs/codedeploy-agent-deployments.log

---
Code Deploy Service(managed by aws and server less)
We need to create IAM Service role and specify that role when you create deployemt group

EC2 Instance
which are targed by deployment group be a tags or auto scaleing group membership
regards to deployment to work , we need to install codedeploye agent on the EC2 instance, 
which are targed by deployment group. The EC2 instance also need IAM Role attached to them with proper s3 permissions .
If you are downloading revisions from S3 bucket.

Github or S3 (Revisions)
Revisions can be stored in github or s3 . 

ELB

CodeDeploy work as orchestartion to remove or add EC2 instance to ELB or target group.
As process of deployment process.
This orchestation can help any user impact during the deployment, making deployment safer.

----

CodeDeploy service role
Create an application
set up a deployment group
run a deployment
CodeDeploy console

classic elb 
auto scaleing group with 2 ec2 instance that has codedeploy agent on that.

