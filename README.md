# Pull mode
Running ansible in pull mode

Having many Ansible changes to many hosts makes running ansible risky. For this situations pull mode mechanism is preferable, which automatically pull changes.
Command "ansible-pull" starts pulling code from github repo.

## Requeriments
Becouse ansible an git it's gonna work remotely, there should be installed on hosts,
- git installed on EC2 ***ansible 'IP' --private-key ~/.ssh/EffectiveDevOpsAWS.pem --become -m yum -a 'name=git enablerepo=epel state=installed'***
- ansible installed on EC2 ***ansible 'IP' --private-key ~/.ssh/EffectiveDevOpsAWS.pem --become -m yum -a 'name=ansible enablerepo=epel state=installed'***  

## Steps to execute ansible un pull mode
- Install git & ansible in host computers
- Create git proyect for SCM of infra
- Configure git proyect for localhost execution ***localhost*** file
- Configure a crontab in host for download changes from git ***ansible '54.160.87.251' --private-key ~/.ssh/EffectiveDevOpsAWS.pem -m cron -a 'name=ansible-pull minute="*/10" job="/usr/bin/ansible-pull -U https://github.com/jarmandomtz/ansible-book-repo helloworld.yml -i localhost --sleep 60"'***
