# Ansible Tower Installation
## System configuration

### Disable Selinux

**Stop Selinux internal safety mechanism**

> setenforce 0

> sed -ri 's/^(SELINUX=).*/\1disabled/g' /etc/selinux/config

**Check Selinux status**

> sestatus -v

**Disable Firewalld**

> systemctl disable firewalld --now

**Check the firewalld firewall status**

> firewall-cmd

> systemctl status firewalld

### Install Ansible Tower

This article demonstrates installation using setup-bundle ( can be understood as a one-key offline installation package ), official download source https://releases.ansible.com/ansible-tower

**Download Package and Extract**
> mkdir -p /tmp/tower
> 
> cd /tmp/tower
> 
> curl -sqL https://releases.ansible.com/ansible-tower/setup-bundle/ansible-tower-setup-bundle-3.8.3-1.tar.gz | tar zxvf -

**Edit 'inventory' File**

Edit ansible-tower-setup-bundle-3.8.3-1/inventory Guidance list, fill in the user password part, refer to the following configuration. Below is just a reference for a single instance tower server.

> cd ansible-tower-setup-bundle-3.8.3-1
> 
> vim inventory
```
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

#Automation Hub Configuration
#

automationhub_admin_password='awx'

automationhub_pg_host=''
automationhub_pg_port=''

automationhub_pg_database='automationhub'
automationhub_pg_username='automationhub'
automationhub_pg_password='awx'
automationhub_pg_sslmode='prefer'
```

### Start installation

ansible tower is installed using ansible playbook, and after installation, all services are automatically pulled up and the configuration is turned on.

> ./setup.sh

### Visit Ansible Tower

access https://192.168.1.16/#/login 

***Log in to your respective Ansible Tower UI hostname or IP using (admin/password) if you have followed above setting on inventory, else use respective password that you have configured***

***If, this is your first time login, you need to subscribe to license after login. Use your redhat login ID and password to obtain the trial licence.***

Once the trial licence is obtained  follow below.

## Ansible Tower License

### Test and print log output in 
> /var/log/supervisor/awx-callback-receiver.log

## Backup licensing.py

> cp /var/lib/awx/venv/awx/lib/python3.6/site-packages/awx/main/utils/licensing.py{,.bak} ./

## Edit licensing.py

> vim /var/lib/awx/venv/awx/lib/python3.6/site-packages/awx/main/utils/licensing.py

At the end of the documentvalidate Method, replace with the following
```
def validate(self):
        # Return license attributes with additional validation info.
        attrs = copy.deepcopy(self._attrs)
        #type = attrs.get('license_type', 'none')
        attrs.update(dict(subscription_name='Ansible Tower Enterprise', license_type='enterprise'))

        if (type == 'UNLICENSED' or False):
            attrs.update(dict(valid_key=False, compliant=False))
            return attrs
        attrs['valid_key'] = True

        if Host:
            current_instances = Host.objects.active_count()
        else:
            current_instances = 0
        #available_instances = int(attrs.get('instance_count', None) or 0)
        available_instances = 99999
        attrs['current_instances'] = current_instances
        attrs['available_instances'] = available_instances
        free_instances = (available_instances - current_instances)
        attrs['free_instances'] = max(0, free_instances)

        #license_date = int(attrs.get('license_date', 0) or 0)
        attrs['license_date'] = 2556115199
        license_date = attrs['license_date']
        current_date = int(time.time())
        time_remaining = license_date - current_date
        attrs['time_remaining'] = time_remaining
        if attrs.setdefault('trial', False):
            attrs['grace_period_remaining'] = time_remaining
        else:
            attrs['grace_period_remaining'] = (license_date + 2592000) - current_date
        attrs['compliant'] = bool(time_remaining > 0 and free_instances >= 0)
        attrs['date_warning'] = bool(time_remaining < self.SUBSCRIPTION_TIMEOUT)
        attrs['date_expired'] = bool(time_remaining <= 0)
        return attrs
```
## Restart Ansible Tower Service
> ansible-tower-service restart
***Vefify the status***
> ansible-tower-service status

# More on Ansible Docs
http://docs.ansible.com/ansible-tower/latest/html/quickinstall/index.html
