## How to use this document
This README is provided to give guidelines on setting up a workflow in Ansible Tower to convert CentOS/Oracle Linux to Red Hat Enterprise Linux. This document will not cover setup of Ansible Tower, nor how to setup credentials, etc. Variable definitions and playbook-by-playbook explanations are given.

## Prerequisites
- Ansible Tower version >= 3.8.0 installed
- Host inventories have been created and are accessible from Ansible Tower (validate by using `test_access.yml`)
- Public/private key-pair for provisioning the service account. This should be added as a credential in Ansible Tower
- Red Hat Satellite is installed and accessible from Ansible Tower
- At least one Content View and Lifecycle Environment exist, with an Activation Key generated for use on remote hosts
- If using Slack for notifications, a Slack Credential exists within the Tower Vault

## Playbook Overview
```
create_user.yml             # Create a service account on the remote host
finish_conversion.yml       # Finish executing the convert2rhel script
preflight_checks.yml        # Ensure required variables are defined
register_insights.yml       # Register RHEL machine to Red Hat Insights
satellite_host_config.yml   # Prepare host for connection to Satellite
setup_convert2rhel.yml      # Enable the convert2rhel repo and install required packages
slack_alert.yml             # Send an alert via Slack
test_access.yml             # Test connectivity to remote hosts
undo_setup.yml              # Undo changes made by previous steps
```

## Suggested Setup
The recommended way to utilize the Convert2RHEL playbooks is to create a Workflow Template. The benefit of Workflow Template is that all survey variables (defined below) persist across playbooks, preventing manual entry between steps. Additionally, the Workflow can have logical breaks to check if the user wants to proceed. The recommended sequence is as follows:

1. Check root access - password (`test_access.yml`): this playbook should leverage existing credentials, whether SSH key or password.
2. Approval to create a service account: sanity check before creating a service account. Clicking approve allows the Workflow to proceed.
3. Create Ansible Service Account (`create_user.yml`)
4. Check root access - SSH key (`test_access.yml`): note that we are using the same playbook from step 1; however, we use a different credential: the SSH created for the service account (see prerequisites)
5. Setup Convert2RHEL (`setup_convert2rhel.yml`)
6. Satellite Client Setup (`satellite_host_config.yml`)
7. Approval to finish conversion: this is another approval. Clicking approve leads to step 8a, deny leads to 8b.
8. (a): Finish Conversion (`finish_conversion.yml`), (b): Rollback Convert2RHEL (`undo_setup.yml`): this playbook will rollback any changes made to the system in previous steps (remove the service account, remove the Convert2RHEL YUM repo, etc.)

## Required Variables
```
Variable                Default Value               Description
--------                -------------               -----------
svcansible_username     svcansible                  Username of service account to create
svcansible_home         /var/lib/                   Prefix for service account home directory. Using /var/lib creates the directory /var/lib/$(svcansible_username)
svcansible_sudoer       'true'                      Add the service account to /etc/sudoers.d directory, allowing passwordless sudo
svcansible_public_key                               The public SSH key to install to the remote host. Follows the format of ~/.ssh/authorized_keys
satellite_server_url                                Satellite server URL, make sure to include "https://"
satellite_organization                              Satellite organization the converted host should be placed into
activation_key                                      Activation Key from Satellite
register_to_insights    'true'                      Register the host to Red Hat Insights
```

An example of a proper configuration is provided below:
```
svcansible_username: svcansible
svcansible_home: /var/lib/
svcansible_sudoer: 'true'
svcansible_public_key: ssh-rsa <ssh-key-here> sam@samander-mac
satellite_server_url: 'https://satellite-services.lab.redhat.com'
satellite_organization: AnsibleLab
activation_key: Ansible_RHEL7
register_to_insights: 'true'
```

## Other Design Considerations
When creating Job Templates from the provided playbooks, it is recommended to increase the verbosity to at least `1 (Verbose)`.

Additionally, while the above variables can be defined at the Job Template level, creating them as Surveys at the Workflow Template level is recommended.
