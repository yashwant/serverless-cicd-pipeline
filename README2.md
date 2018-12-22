# Serverless framework CI/CD with CodePipeline and CodeBuild


## Requirements

* Copy buildspec.yml file at root of serverless project

* Make sure that underscore is not used in logical name of any resource in serverless template file

* Install serverless-sam plugin in you serverless node app[serverless-sam](https://www.npmjs.com/package/serverless-sam)

* AWS CLI already configured with Administrator access 
    - Alternatively, you can use a [Cloudformation Service Role with Admin access](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-iam-servicerole.html)

* [Github Personal Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) with full permissions on **admin:repo_hook and repo**

## Pipeline creation

This Pipeline is configured to look up for GitHub information stored on [EC2 System Manager Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-paramstore.html) such as Branch, Repo, Username and OAuth Token.

Replace the placeholders with values corresponding to your GitHub Repo and Token:

```bash
aws ssm put-parameter \
    --name "/service/serverless-pipeline/github/repo" \
    --description "Github Repository name for Cloudformation Stack cicd pipeline" \
    --type "String" \
    --value "GITHUB_REPO_NAME"

aws ssm put-parameter \
    --name "/service/serverless-pipeline/github/token" \
    --description "Github Token for Cloudformation Stack cicd pipeline" \
    --type "String" \
    --value "TOKEN"

aws ssm put-parameter \
    --name "/service/serverless-pipeline/github/user" \
    --description "Github Username for Cloudformation Stack cicd pipeline" \
    --type "String" \
    --value "GITHUB_USER"

aws ssm put-parameter \
    --name "/service/serverless-pipeline/appname" \
    --description "Stack Name for Cloudformation Stack cicd pipeline" \
    --type "String" \
    --value "GITHUB_USER"

```


Run the following AWS CLI command to create your first pipeline for your Serverless App:

```bash
aws cloudformation create-stack \
    --stack-name <your stack name goes here> \
    --template-body file://pipeline.yaml \
    --capabilities CAPABILITY_NAMED_IAM
```

This may take a couple of minutes to complete, therefore give it a minute or two and then run the following command to retrieve the Git repository:

```bash
aws cloudformation describe-stacks \
    --stack-name <your stack name goes here> \
    --query 'Stacks[].Outputs'
```


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
