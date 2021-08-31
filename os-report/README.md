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
 
PLAYBOOK: install-podman.yml 
     
## VARS TO BE DEFINED: 
<pre class="line-number language-yaml"><code>container_host: podmanserver    # Host in inventory that is RHEL8 and where you want a container spun up for HTML report
target_port: 8081               # Port to xlate to container port 8080 
</code></pre>

## Generate report against target inventory 

PLAYBOOK: create-os-report.yml
 
## VARS TO BE DEFINED: 

<pre class="line-number language-yaml"><code>container_host: podmanserver    # Host in inventory that is RHEL8 and where you want a container spun up for HTML report
home_dir: /var/lib/tmp/         # directory user can access for container
target_port: 8081               # Port to xlate to container port 8080
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

##ADDITIONAL VARIABLES THAT NEED TO BE DEFINED IN TEMPLATE WHEN SENDING SLACK ALERT: 
![image](https://user-images.githubusercontent.com/17077661/120726995-4790bc80-c48e-11eb-818b-d9b60e02d188.png)


<pre class="line-number language-yaml"><code>channel: '#Toweralerts'        # Channel that you want to send the Slack Alert to
slackuser: Ansible Contoller   # Username that shows up as posted message in Slack
</code></pre>

![image](https://user-images.githubusercontent.com/17077661/120726695-8bcf8d00-c48d-11eb-916c-581f23f40382.png)



 
