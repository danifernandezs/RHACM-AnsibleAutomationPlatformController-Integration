# Ansible Tower instance

## Provision Lab Environment
- Navigate to the OPENTLC lab portal and login using your OPENTLC credentials.
- Navigate to Services → Catalogs → All Services → OPENTLC Automation.

## Connect to the control node

````bash
[laptop]$ ssh <OPENTLC-USER-ID>@control.${GUID}.${SUBDOMAIN}
````
````bash
[user@control ~]$ sudo su
````

## Install Red Hat Ansible Tower cluster

All releases

https://releases.ansible.com/ansible-tower/setup/

````bash
mkdir /home/tower
ansible localhost -m unarchive -a "src=https://releases.ansible.com/ansible-tower/setup/ansible-tower-setup-3.8.4-1.tar.gz dest=/home/tower remote_src=yes"
````
````bash
cd /home/tower
cd ansible-tower-setup-3.8.4-1

cat > inventory <<EOF
[tower]
localhost ansible_connection=local

[automationhub]

[database]

[all:vars]
admin_password='redhat'

pg_host=''
pg_port=''

pg_database='awx'
pg_username='awx'
pg_password='redhat'
pg_sslmode='prefer'  # set to 'verify-full' for client-side enforced SSL

EOF
````
````bash
./setup.sh
````

## Ansible Tower Subscription
You will need a valid subscription to use the AAP stack
