# Ansible Tower instance

## Provision Lab Environment
- Navigate to the OPENTLC lab portal and login using your OPENTLC credentials.
- Navigate to Services → Catalogs → All Services → OPENTLC Automation.

## Connect to the control node

````bash
[laptop]$ ssh <OPENTLC-USER-ID>@control.${GUID}.${SUBDOMAIN}
````
````bash
[user@control ~]$ sudo su - devops
````

## Prepare Lab Nodes
````bash
lab deploy satellite
lab deploy gitlab
lab deploy tower
````

## Install Red Hat Ansible Tower cluster

All releases

https://releases.ansible.com/ansible-tower/setup/

````bash
ansible localhost -m unarchive -a "src=https://releases.ansible.com/ansible-tower/setup/ansible-tower-setup-3.8.4-1.tar.gz dest=/home/devops remote_src=yes"
````
````bash
cd ansible-tower-setup-3.8.4-1

cat > inventory <<EOF
[tower]
tower1.example.com
tower2.example.com
tower3.example.com

[database]
towerdb.example.com

[all:vars]
admin_password='redhat'
ansible_become=true
pg_host='towerdb.example.com'
pg_port='5432'
pg_database='awx'
pg_username='awx'
pg_password='redhat'
pg_sslmode='prefer'
nginx_disable_https=true

EOF
````
````bash
./setup.sh
````
