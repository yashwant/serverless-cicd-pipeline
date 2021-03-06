# Serverless framework CI/CD with CodePipeline and CodeBuild

## Requirements

* Copy buildspec.yml file at root of serverless project

* Make sure that underscore is not used in logical name of any resource in serverless template file

* Install serverless-sam plugin in you serverless node app[serverless-sam](https://www.npmjs.com/package/serverless-sam)

* AWS CLI already configured with Administrator access 
    - Alternatively, you can use a [Cloudformation Service Role with Admin access](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-iam-servicerole.html)

* [Github Personal Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) with full permissions on **admin:repo_hook and repo**

## Pipeline creation

Go to CloudFormation console. Select create new stack and upload template file. Enter following parameter values: GithubRepo, GithubToken, GithubUser, AppName, UATTopic, ProdTopic, UATApprovalEmail, ProdApprovalEmail. 

GithubRepo - GitHub repository name
GithubToken - GitHub Token
GithubUser - GitHub user name
AppName - Application/Stack Name
UATTopic - User Acceptance Testing Approval[Gamma Stage] email notification SNS topic name
UATApprovalEmail - User Acceptance Testing Stage[Gamma Stage] Approver email id
ProdTopic - Production Approval[Prod Stage] email notification SNS topic name
ProdApprovalEmail - Production Stage[Prod Stage] Approver email id

Make sure you check ==> "I acknowledge that AWS CloudFormation might create IAM resources." option"

## Release through the newly built Pipeline

Although CodePipeline will orchestrate this 3-environment CI/CD pipeline we need to learn how to integrate our toolchain to fit the following sections:

> **Source code**

All you need to do here is to initialize a local `git repository` for your existing service if you haven't done already and connect to the `git repository` that you retrieved in the previous section.

```bash
git init
```

Next, add a new Git Origin to connect your local repository to the remote repository: 

[Git Instructions for HTTPS access](https://help.github.com/articles/adding-a-remote/)
[Git Instructions for SSH access](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-ssh-unixes.html) 
[Git Instructions for HTTPS access](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-https-unixes.html) 

> **Build steps**

This Pipeline expects `buildspec.yaml` to be at the root of this `git repository` and **CodeBuild** expects will read and execute all sections during the Build stage.

Open up `buildspec.yaml` using your favourite editor and customize it to your needs - Comments have been added to guide you what's needed

> **Triggering a deployment through the Pipeline**

The Pipeline will be listening for new git commits pushed to the `master` branch (unless you changed), therefore all we need to do now is to commit to master and watch our pipeline run through:

```bash
git add . 
git commit -m "Kicking the tires of my first CI/CD pipeline"
git push origin master
```
