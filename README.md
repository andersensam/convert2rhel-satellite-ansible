# convert2rhel-satellite-ansible
Use Ansible Tower to convert CentOS systems to RHEL and register to Satellite and Insights (cloud.redhat.com)

Thanks to Andrew Schoenfeld, Brandon Marlow, and Tony Harris!


## Prerequisites
- Ansible Tower version >= 3.8.0 installed
- Host inventories have been created and are accessible from Ansible Tower (validate by using `test-access.yml`)
- Red Hat Satellite is installed and accessible from Ansible Tower
- At least one Content View and Lifecycle Environment exist, with an Activation Key generated for use on remote hosts
- If using Slack for notifications, a Slack Credential exists within the Tower Vault
- Connected to Certified Collection content for redhat.insights 


![image](https://user-images.githubusercontent.com/17077661/120847792-d2be9080-c528-11eb-9a42-c950f77a731a.png)
