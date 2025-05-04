7 Day DevOps Challenge


Change the permissions of your .pem file:

icacls "nextwork-keypair.pem" /reset
icacls "nextwork-keypair.pem" /grant:r "neela:R"
icacls "nextwork-keypair.pem" /inheritance:r


1)Connect to your EC2 instance:

ssh -i C:\Users\neela\OneDrive\Desktop\Devops\nextwork-keypair.pem ec2-user@ec2-13-201-188-4.ap-south-1.compute.amazonaws.com

<p> using terminal added the line by nano command by using Navigate to the directory containing index.js using the cd command</p>


2)Connect your local repo with your GitHub repo.:

connect your local project folder with your Github repo

git remote add origin https://github.com/Pavan-Kalyan-11/nextwork-web-project.git

3)set up name and email instead of Ec2 instance name and IP

git config --global user.name "Pavan"
git config --global user.email pavankalyanseelam01@gmail.com

To ask Git to remember your credential details for next time!

git config --global credential.helper store


resetting your Git credentials, and starting over again:

git config --global --unset credential.helper
git config --local --unset credential.helper
git remote set-url origin https://Pavan-Kalyan-11@github.com/Pavan-Kalyan-11/nextwork-web-project

This pulls the latest changes from GitHub and rebases them onto your local branch, helping you integrate any new commits.
git pull origin master --rebase



4) Run the Build and Troubleshoot Failures while getting code connection failed error

CLIENT_ERROR: Failed to get access token from arn:aws:codeconnections:ap-south-1:905630096700:connection/e38d0221-847a-4e3c-a132-d9d4a291f7b4: Access denied to connection arn:aws:codeconnections:ap-south-1:905630096700:connection/e38d0221-847a-4e3c-a132-d9d4a291f7b4

Troubleshooted the connection access by searching CloudWatch logs and updated the 
CodeBuildCodeConnectionsSourceCredentialsPolicy-nextwork-devops-cicd-ap-south-1-905630096700   ----- policy by manually changing the connection in the Json.


To automatically trigger a build whenever you push code to your repository, you can integrate AWS CodeBuild with AWS CodePipeline or GitHub Webhooks. Here’s how you can modify your buildspec.yml to include a build trigger:

version: 0.2

phases:
  install:
    runtime-versions:
      java: corretto8
  pre_build:
    commands:
      - echo Initializing environment
      - export CODEARTIFACT_AUTH_TOKEN=`aws codeartifact get-authorization-token --domain nextwork --domain-owner 905630096700 --region ap-south-1 --query authorizationToken --output text`

  build:
    commands:
      - echo Build started on `date`
      - mvn -s settings.xml compile
  post_build:
    commands:
      - echo Build completed on `date`
      - mvn -s settings.xml package

artifacts:
  files:
    - target/nextwork-web-project.war
  discard-paths: no

cache:
  paths:
    - ~/.m2/repository

triggers:
  - type: EVENT
    eventPattern:
      source: ["aws.codecommit"]
      detail-type: ["CodeCommit Repository State Change"]
      resources: ["arn:aws:codecommit:ap-south-1:905630096700:nextwork-web-project"]



