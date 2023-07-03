Ansible Tower Installation
System configuration

Disable Selinux

Ban Selinux internal nuclear safety mechanism

setenforce 0 sed -ri 's/^(SELINUX=).*/\1disabled/g' /etc/selinux/config

Check Selinux status

sestatus -v

Disable Firewalld

systemctl disable firewalld --now

Check the firewalld firewall status

firewall-cmd 
systemctl status firewalld
Install Ansible Tower

This article demonstrates that installation using setup-bundle ( can be understood as a one-key offline installation package ), official download source https://releases.ansible.com/ansible-tower
Download Package and Extract
mkdir -p /tmp/tower
cd /tmp/tower
curl -sqL https://releases.ansible.com/ansible-tower/setup-bundle/ansible-tower-setup-bundle-3.8.3-1.tar.gz | tar zxvf -

Edit 'inventory' File

Edit ansible-tower-setup-bundle-3.8.3-1/inventory Guidance list, fill in the user password part, refer to the following configuration. Below is just a reference for a single instance tower server.

cd ansible-tower-setup-bundle-3.8.3-1
############################
[tower]
localhost ansible_connection=local

[automationhub]

[database]

[all:vars]
admin_password='password'

pg_host=''
pg_port=''

pg_database='awx'
pg_username='awx'
pg_password='awx'
pg_sslmode='prefer'  # set to 'verify-full' for client-side enforced SSL

# Automation Hub Configuration
#

automationhub_admin_password='awx'

automationhub_pg_host=''
automationhub_pg_port=''

automationhub_pg_database='automationhub'
automationhub_pg_username='automationhub'
automationhub_pg_password='awx'
automationhub_pg_sslmode='prefer'

############################
Start installation

ansible tower is installed using ansible playbook, and after installation, all services are automatically pulled up and the configuration is turned on.

./setup.sh

Visit Ansible Tower

access https://192.168.1.16/#/login Log in (admin/password)

If, this is your first time login, you need to subscribe to license after login. Use your redhat login ID and password to obtain the trial licence.

Once the trial licence is obtained  follow below.
Ansible Tower License

Test and print log output in 
/var/log/supervisor/awx-callback-receiver.log

Backup licensing.py

cp /var/lib/awx/venv/awx/lib/python3.6/site-packages/awx/main/utils/licensing.py{,.bak} ./

Edit licensing.py

vim /var/lib/awx/venv/awx/lib/python3.6/site-packages/awx/main/utils/licensing.py

At the end of the documentvalidate Method, replace (Python PEP8 grammar rule) with the following
###########################
