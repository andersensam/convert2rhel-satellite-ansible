## How to use this document
This README is provided to give guidelines on setting up a workflow in Ansible Tower to convert CentOS/Oracle Linux to Red Hat Enterprise Linux. This document will not cover setup of Ansible Tower, nor how to setup credentials, etc. Variable definitions and playbook-by-playbook explanations are given.

## Prerequisites
- Ansible Tower version >= 3.8.0 installed
- Host inventories have been created and are accessible from Ansible Tower (validate by using `test-access.yml`)
- Red Hat Satellite is installed and accessible from Ansible Tower
- At least one Content View and Lifecycle Environment exist, with an Activation Key generated for use on remote hosts
- If using Slack for notifications, a Slack Credential exists within the Tower Vault

## Playbook Overview
```
create-user.yml             # Create a service account on the remote host
finish-conversion.yml       # Finish executing the convert2rhel script
preflight-checks.yml        # Ensure required variables are defined
register-insights.yml       # Register RHEL machine to Red Hat Insights
satellite-host-config.yml   # Prepare host for connection to Satellite
setup-convert2rhel.yml      # Enable the convert2rhel repo and install required packages
slack-alert.yml             # Send an alert via Slack
test-access.yml             # Test connectivity to remote hosts
undo-setup.yml              # Undo changes made by previous steps
```