# OSREPORT 

This will run a report against Linux OS and provide you what flavor and version you are running

EXAMPLE:

![image](https://user-images.githubusercontent.com/17077661/121974681-c7434480-cd34-11eb-8168-f2b44e9ab633.png)



# SETUP 

Make sure you have your inventory setup this will check for facts against live running machines with connectivity

EXAMPLE:

![image](https://user-images.githubusercontent.com/17077661/122114586-4ccc0080-cdd8-11eb-8168-d7536b10cccf.png)

Notice in the credentials it has 3 creds
1 - HOME machines   # this is your standard creds to access target report machines for gathering facts
2 - Slack Token     # Optional if you want the template to send a link to the report when done
3 - Report-ssh keys # This was the custome creds to created earlier to allow for changing of creds to the Container host to update the report

## Extra Vars
<pre class="line-number language-yaml">---
channel: '#Toweralerts'          # Optional if sending Slack
container_host: 172.16.1.10      # Should be the same target host as the inital setup
home_dir: /var/lib/tmp           # Should match the inital setup
report_user: ansibleacct         # Should match the intial setup 
target_port: 8081                # Should match the intial setup
slackuser: Ansible Controller    # Optional whatever name you want the message to show up as
</code></pre>
