# Ansible Roles
Ansible Examples using roles

Roles are used for segment ansible scripts in terms of reutilization. In our example nodejs installation is reusable but not WebApp commands, so we create a role for each one.

## Preparing env
Create "nodejs" role
- Create roles directory
- Create "nodejs" role, using ansible-galaxy initilize a role (dir & files| structure): ***ansible-galaxy init nodejs***
- Edit task/main.yml file, add nodejs install instructions

Create "helloworld" role
- Create helloworld role, using ansible-galaxy
- Copy helloworld.js & helloworld.cfg to files dir
- Edit task/main.yml file, add copy instructions & start service
- Edit handlers/main.yml file, add re-start instruction
- Edit meta/main.yml file, Add nodejs dependency (dependencies section)
- Create playbook file: ./helloworld.yml

Execute playbook for list hosts

```
% ansible-playbook helloworld.yml --private-key ~/.ssh/ansible-aws.pem -e target=ec2 --list-hosts
```

Execute playbook for predict changes

```
% ansible-playbook helloworld.yml --private-key ~/.ssh/ansible-aws.pem -e target=18.191.195.170 --check
```
