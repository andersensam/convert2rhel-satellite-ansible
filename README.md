# convert2rhel-satellite-ansible
Use Ansible Tower to convert CentOS systems to RHEL and register to Satellite and Insights (cloud.redhat.com)

Thanks to Andrew Schoenfeld and Tony Harris!

<B> OS Reports:</B>

This checks an inventory for family of OS and creates a report review in a conatiner running on designed container host.

<B>Example:</B>
![image](https://user-images.githubusercontent.com/17077661/120725650-4ca03c80-c48b-11eb-89b1-aca039e95f8b.png)


Steps for OS report:

<B>1 - Install podman on target container host and open port for HTML access. </B>
 
PLAYBOOK: install-podman.yml 
     
<B>VARS TO BE DEFINED: </B>
<pre class="line-number language-yaml"><code>container_host: podmanserver    # Host in inventory that is RHEL8 and where you want a container spun up for HTML report
target_port: 8081               # Port to xlate to container port 8080 
</code></pre>

<B>2 - Generate report against target inventory</B>

PLAYBOOK: create-os-report.yml
 
<B>VARS TO BE DEFINED: </B>

<pre class="line-number language-yaml"><code>container_host: podmanserver    # Host in inventory that is RHEL8 and where you want a container spun up for HTML report
home_dir: /var/lib/tmp/         # directory user can access for container
target_port: 8081               # Port to xlate to container port 8080
</code></pre>

<B>OPTIONAL STEP FOR SLACK ALERTS: </B>

<B> CREATE CUSTOM CREDS FOR SLACK API CALL: </B>

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

<B> ADDITIONAL VARIABLES THAT NEED TO BE DEFINED IN TEMPLATE </B>

<pre class="line-number language-yaml"><code>channel: '#Toweralerts'        # Channel that you want to send the Slack Alert to
slackuser: Ansible Contoller   # Username that shows up as posted message in Slack
</code></pre>

![image](https://user-images.githubusercontent.com/17077661/120726695-8bcf8d00-c48d-11eb-916c-581f23f40382.png)



 For Convert2rhel process include reg to Satellite plus insights (cloud.redhat.com)
 
