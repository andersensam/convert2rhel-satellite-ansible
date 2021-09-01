## os-report
This folder contains everything you need to run a report against and inventory for Linux OS Types
<pre class="line-number language-yaml"><code>
additional_ssh_cred.yml        # will create custom creds in Tower (need tower creds etc) easier to copy and paste see below
cleanup_reports.yml            # This will stop the container and remove the home dir for the container
init_container.yml             # This will make sure @container-tools is installed on targeted machine, and enable port for conatiner access
run_os_report.yml              # This will start the container, copy css to container_host, Check for alive hosts, and create html report
slack_custome_cred.yml         # will create custom Slack creds in Tower (need tower creds etc) easier to copy and paste see below


</code></pre>
## OS Reports:

This checks an inventory for family of OS and creates a report review in a conatiner running on designed container host.

<B>Example:</B>
![image](https://user-images.githubusercontent.com/17077661/120725650-4ca03c80-c48b-11eb-89b1-aca039e95f8b.png)


## Steps for OS report:

<B>1 - Install podman on target container host and open port for HTML access. </B>
 
PLAYBOOK: init_container.yml

ESCALATION: Needed 

## VARS TO BE DEFINED: 
<pre class="line-number language-yaml"><code>container_host: podmanserver    # Host in inventory that is RHEL8 and where you want a container spun up for HTML report
target_port: 8081               # Port to xlate to container port 8080 
</code></pre>

![image](https://user-images.githubusercontent.com/17077661/131545799-ef1aa5a0-0f08-427e-b814-06334d22d9e1.png)


## Generate report against target inventory 

PLAYBOOK: run_os_report.yml

ESCALATION: Not Needed

## VARS TO BE DEFINED: 

<pre class="line-number language-yaml"><code># for containerrestart
target_port: 8080               # external port for container
# for Slackalerts (Optional)
channel: '#Toweralerts'         # Channel for slack Alerts    
slackuser: Ansible Controller   # User that sends slack Alerts to channel
# for osreport
container_host: podman-host     # Host in inventory that is RHEL8 and where you want a container spun up for HTML report
report_user: ec2-user           # User that is to spin up the contianer must have custom creds setup if not using same user that has access to the inventory
home_dir: /var/lib/tmp          # Home directory of the container
</code></pre>

![image](https://user-images.githubusercontent.com/17077661/131547021-b14b71c4-e04a-4f6c-bd65-1200547e3725.png)


## ADDITONAL SSH CREDS FOR TEMPLATE: 

### Please note that all servers including report_host should have SSH_keys setup.  Username/Password will only work if its the same across the whole inventory.  SSH keys can have different usernames and sshkeys for the playbooks.
### Recommendation is the create a service account/sshkey consitant across the inventory.

## CREATE CUSTOM CREDS FOR report_user:

INPUT CONFIGURATION:

<pre class="line-number language-yaml"><code>fields:
  - id: username
    type: string
    label: Username
  - id: my_ssh_key
    type: string
    label: Private Key
    secret: true
    multiline: true
</code></pre>

INJECTOR CONFIGURATION:
<pre class="line-number language-yaml"><code>extra_vars:
  my_ssh_key_file: '{{tower.filename}}'
file:
  template: '{{my_ssh_key}}'
</code></pre>




## OPTIONAL STEP FOR SLACK ALERTS: 

## CREATE CUSTOM CREDS FOR SLACK API CALL:

INPUT CONFIGURATION:

<pre class="line-number language-yaml"><code>fields:
  - id: slack_token
    type: string
    label: Slack webhook Token
    secret: true
</code></pre>

INJECTOR CONFIGURATION:
<pre class="line-number language-yaml"><code>extra_vars:
  slack_token: '{{ slack_token }}'
</code></pre>

##ADDITIONAL CREDs SENDING SLACK ALERT AND/OR USING DIFFERENT USER FOR PODMAN-HOST IN run_os_report.yml (Template): 

![image](https://user-images.githubusercontent.com/17077661/131546504-ce7abd54-b1c4-489b-8c63-26bdd06e09ac.png)

## Example Slack Alert
![image](https://user-images.githubusercontent.com/17077661/120726695-8bcf8d00-c48d-11eb-916c-581f23f40382.png)



 
