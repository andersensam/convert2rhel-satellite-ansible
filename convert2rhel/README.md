## How to use this document
This README is provided to give guidelines on setting up a workflow in Ansible Controller to convert CentOS/Oracle Linux to Red Hat Enterprise Linux. This document will not cover setup of Ansible Controller, nor how to setup credentials, etc. Variable definitions and playbook-by-playbook explanations are given.

## Prerequisites
- Ansible Controller version >= 3.8.0 installed
- Host inventories have been created and are accessible from Ansible Controller (validate by using `test_access.yml`)
- Public/private key-pair for provisioning the service account. This should be added as a credential in Ansible Controller
- Red Hat Satellite is installed and accessible from Ansible Controller
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
update_all_packages.yml     # Update all packages and reboot the host
```

## Suggested Setup
The recommended way to utilize the Convert2RHEL playbooks is to create a Workflow Template. The benefit of Workflow Template is that all survey variables (defined below) persist across playbooks, preventing manual entry between steps. Additionally, the Workflow can have logical breaks to check if the user wants to proceed. The recommended sequence is as follows:

1. Check root access - password (`test_access.yml`): this playbook should leverage existing credentials, whether SSH key or password.
2. Create Ansible Service Account (`create_user.yml`)
3. Check root access - SSH key (`test_access.yml`): note that we are using the same playbook from step 1; however, we use a different credential: the SSH created for the service account (see prerequisites)
4. Update all packages (`update_all_packages.yml`): update all installed packages and reboot the host. Required for successful conversions.
5. Setup Convert2RHEL (`setup_convert2rhel.yml`)
6. Satellite Client Setup (`satellite_host_config.yml`)
7. Approval to finish conversion: this is another approval. Clicking approve leads to step 8a, deny leads to 8b.
8. (a): Finish Conversion (`finish_conversion.yml`), (b): Rollback Convert2RHEL (`undo_setup.yml`): this playbook will rollback any changes made to the system in previous steps (remove the service account, remove the Convert2RHEL YUM repo, etc.)

## Variable Definitions
```
Variable                      Required     Suggested Value             Description
--------                      --------     ---------------             -----------
use_svcansible                Yes          'True'                      Create a service account if true. Other supported value(s): 'False'
svcansible_username           No           svcansible                  Username of service account to create
svcansible_home               No           /var/lib/                   Prefix for service account home directory. Using /var/lib creates the directory /var/lib/$(svcansible_username)
svcansible_sudoer             No           'True'                      Add the service account to /etc/sudoers.d directory, allowing passwordless sudo
svcansible_public_key         No                                       The public SSH key to install to the remote host. Follows the format of ~/.ssh/authorized_keys
satellite_server_url          Yes                                      Satellite server URL, make sure to include "https://"
satellite_organization        Yes                                      Satellite organization the converted host should be placed into
activation_key                Yes                                      Activation Key from Satellite
register_to_insights          Yes          'True'                      Register the host to Red Hat Insights. Other supported value(s): 'False'
convert2rhel_source           Yes          remote                      Source for Convert2RHEL package. Local fetches from Satellite. Other supported value(s): local
convert2rhel_activation_key   No                                       Activation Key used to install Convert2RHEL from Satellite
rhel6_rhsm_preinstalled       Yes          'True'                      Use the RHSM package already installed on EL6 hosts, otherwise fetch from rhel6_rhsm_repo. Other supported value(s): 'False'
rhel6_rhsm_repo               No                                       URL of the RHSM repo to use if rhel6_rhsm_preinstalled == 'False'
```

An example of a proper configuration is provided below:
```
activation_key: '123-456-789'
convert2rhel_activation_key: '987-654-321'
convert2rhel_source: local
register_to_insights: 'True'
rhel6_rhsm_preinstalled: 'False'
rhel6_rhsm_repo: 'http://podman-host:9091/repo'
satellite_organization: Convert2RHEL
satellite_server_url: 'https://satellite-services.lab.redhat.com'
svcansible_home: /var/lib/
svcansible_public_key: ssh-rsa <ssh-key-here> sam@samander-mac
svcansible_sudoer: 'True'
svcansible_username: svcansible
use_svcansible: 'True'
```

Another valid configuration can be found below:
```
activation_key: '123-456-789'
convert2rhel_source: remote
register_to_insights: 'True'
rhel6_rhsm_preinstalled: 'True'
satellite_organization: Convert2RHEL
satellite_server_url: 'https://satellite-services.lab.redhat.com'
use_svcansible: 'False'
```

## Other Design Considerations
When creating Job Templates from the provided playbooks, it is recommended to increase the verbosity to at least `1 (Verbose)`.

Additionally, while the above variables can be defined at the Job Template level, creating them as Surveys at the Workflow Template level is recommended.
