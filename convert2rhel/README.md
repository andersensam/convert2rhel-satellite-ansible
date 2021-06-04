## How to use this document
This README is provided to give guidelines on setting up a workflow in Ansible Tower to convert CentOS/Oracle Linux to Red Hat Enterprise Linux. This document will not cover setup of Ansible Tower, nor how to setup credentials, etc. Variable definitions and playbook-by-playbook explanations are given.

## Prerequisites
- Ansible Tower version >= 3.8.0 installed
- Host inventories have been created and are accessible from Ansible Tower (validate by using `test-access.yml`)
- Red Hat Satellite is installed and accessible from Ansible Tower
- At least one Content View and Lifecycle Environment exist, with an Activation Key generated for use on remote hosts
- If using Slack for notifications, a Slack Credential exists within the Tower Vault

