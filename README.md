# Pull mode
Running ansible in pull mode

Having many Ansible changes to many hosts makes running ansible risky. For this situations pull mode mechanism is preferable, which automatically pull changes being the host responsible for request their update each certain time
Command "ansible-pull" starts pulling code from github repo.

## Requeriments
Becouse ansible an git it's gonna work remotely, there should be installed on hosts,
- git & ansible installed on EC2 f
- git repo containing Ansible playbook with in this case 3 roles: nodejs, helloworld, crontab
  - nodejs grouping needs for install nodejs
  - helloworld grouping needs for install apache and app files
  - crontab grouping needs for install cron, copying files and load cront command

## Steps to execute ansible on pull mode
- Install git & ansible in host computers using AWS, [code repo](https://github.com/jarmandomtz/aws-book.git)  [Terraform file](aws-book/chapter04/tr-ansible-aws-pull/tr-ansible-webapp-pull.tf)
- Create [git proyect](https://github.com/jarmandomtz/ansible-book-repo.git) for Ansible SCM of infra
  - Localhost execution specification: localhost in crontab command & helloworld.yml in Ansible project, using Template in crontab role of playbook & YAML Terraform file
- Execute Ansible since Terraform first time, configuring crontab daemon. This execution load ansible-pull command which will execute update of host in a timed loop

Configuration validation
- Connect to host IP to validate
- Check existent crontab commands

```
ssh -i ~/.ssh/EffectiveDevOpsAWS.pem ec2-user@PUBLIC_IP
[ec2-user@PUBLIC_IP ~]$ crontab -l
# Cron job to git clone/pull a repo and then run locally
*/2 * * * *  /usr/bin/ansible-pull -U https://github.com/jarmandomtz/ansible-book-repo.git helloworld.yml -i localhost >>/var/log/ansible-pull.log 2>&1
```

## Steps to add code-deploy for WebServer
- Add aws code deploy dependency to library path
- Create codedeploy role, enable aws_codedeploy library
- Create new playbook for prepare nodejs server **nodeserver.js**
- Create Troposphere template for WebServer **nodeserver-cf-template.py** and add new policy giving S3 permissions
- Create CloudFormation template
- Launch CloudFormation template
```
% mkdir library
% curl -L https://raw.githubusercontent.com/yogeshraheja/Effective-DevOps-with-AWS/master/Chapter05/ansible/library/aws_codedeploy > ./library/aws_codedeploy
% cd roles
% ansible-galaxy init codedeploy
% nano codedeploy/tasks/main.yml                  # enable use of aws_codedeploy
% cd ..
% nano nodeserver.js                              # Playbook for prepare WebServer, install roles: nodejs, codedeploy
...
% python nodeserver-cf-template.py > nodeserver-cf.template
% aws cloudformation create-stack \
      --capabilities CAPABILITY_IAM \
      --stack-name helloworld-staging \
      --template-body file://nodeserver-cf.template \
      --parameters ParameterKey=KeyPair,ParameterValue=EffectiveDevOpsAWS
```

- Create IAM service role for CodeDeploy

```
% aws iam create-role \
    --role-name CodeDeployServiceRole \
    --assume-role-policy-document file://misc/CodeDeploy-Trust.json
```

- Attach role policy to provide the proper permisions to the service role

```
% aws iam attach-role-policy \
    --role-name CodeDeployServiceRole \
    --policy-arn arn:aws:iam::aws:policy/service-role/AWSCodeDeployRole

```

This give next policies to the role
- Amazon EC2 Auto Scaling
- Amazon CloudWatch
- Amazon EC2
- Elastic Load Balancing
- Amazon SNS
- Amazon Resource Group Tagging API

- Go to CodeDeploy and create an application
Developer Tools -> CodeDeploy -> Applications -> Create application
