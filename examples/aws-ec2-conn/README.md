# AWS Ansible connection
Repo for Ansible file of Book examples

Ansible can use inventory static or dynamic. Download a script for Ansible official site for Dynamic query to EC2 AWS instances

Relevant information from Ansible,
- Ansible modules: https://docs.ansible.com/ansible/latest/collections/index_module.html
- Ansible module documentation in cli: ***ansible-doc [MODULE_NAME]***
- Ansible examples: https://github.com/ansible/ansible-examples
- Book examples: https://github.com/yogeshraheja/Automation-with-Ansible-By-Yogesh-Raheja


Prepate Ansible env

```
% curl -Lo ec2.py http://bit.ly/2v4SwE5
% chmod +x ec2.py
% touch ec2.ini
% pip3 install boto
```

***ec2.ini*** file content

```
[ec2]
regions = all
regions_exclude = us-gov-west-1,cn-north-1
destination_variable = public_dns_name
vpc_destination_variable = ip_address
route53 = False
cache_path = ~/.ansible/tmp
cache_max_age = 300
rds = False
```

***ansible.cfg*** file content

```
[defaults]
inventory = ./ec2.py
remote_user = ec2-user
become = True
become_method = sudo
become_user = root
nocows = 1
deprecation_warnings = False
```

***$HOME/.ssh/config*** file content

```
IdentityFile ~/.ssh/EffectiveDevOpsAWS.pem
User ec2-user
StrictHostKeyChecking no
PasswordAuthentication no
ForwardAgent yes
```

Execute script

```
whereis python3
sudo ln -s /usr/bin/python3.8 /usr/bin/python
% python ec2.py                                                       #Inventory by python script
% ansible --private-key ~/.ssh/EffectiveDevOpsAWS.pem ec2 -m ping     #Execute ping module on ec2 instances
                                                                      #becouse ansible.cfg "intentory" param
% ansible ec2 -m ping                                                 #Using ~/.ssh/config file
```

Result:

```
100.25.160.170 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

Running arbitrary commands in sub-segment of inventory, using default authentication (~/.ssh/config)

```
% ansible '100.25.160.*' -a 'df -h'
100.25.160.170 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        484M   56K  484M   1% /dev
tmpfs           494M     0  494M   0% /dev/shm
/dev/xvda1      7.8G  1.1G  6.6G  15% /
```

Example commands using behind ~./ssh/config
- Using specific key or default one in same command: ***ansible --private-key ~/.ssh/ansible-aws.pem ec2 -m ping***
- Filtering Inventory and execute specific command: ***ansible --private-key ~/.ssh/ansible-aws.pem  '3.137.*.*' -a 'df -h'***
  Testing command shows same result ***ssh -i ~/.ssh/ansible-aws.pem ec2-user@3.137.212.127***, ***df -h***
