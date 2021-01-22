# Pull mode
Running ansible in pull mode

Having many Ansible changes to many hosts makes running ansible risky. For this situations pull mode mechanism is preferable, which automatically pull changes.
Command "ansible-pull" starts pulling code from github repo.

## Requeriments
Becouse ansible an git it's gonna work remotely, there should be installed on hosts,
- git installed on EC2 ***ansible 'IP' --private-key ~/.ssh/EffectiveDevOpsAWS.pem --become -m yum -a 'name=git enablerepo=epel state=installed' ***  
- ansible installed on EC2 ***ansible 'IP' --private-key ~/.ssh/EffectiveDevOpsAWS.pem --become -m yum -a 'name=ansible enablerepo=epel state=installed' ***
