# convert2rhel-satellite-ansible
Use Ansible Tower to convert CentOS systems to RHEL and register to Satellite and Insights (cloud.redhat.com)

Thanks to Andrew Schoenfeld and Tony Harris!

<B> OS Reports:</B>

This checks an inventory for family of OS and creates a report review in a conatiner running on designed container host.

<B>Example:</B>
![image](https://user-images.githubusercontent.com/17077661/120725650-4ca03c80-c48b-11eb-89b1-aca039e95f8b.png)


Steps for OS report:

<B>1 - install podman on target container host. </B>
 
     This can be done via playbook --- install-podman.yml (Makes sure container-tools installed and opens firewalld port defined to access report
     
  <B>VARS TO BE DEFINED: </B>
     <pre class="line-number language-yaml"><code>
     container_host: podmanserver    # Host in inventory that is RHEL8 and where you want a container spun up for HTML report
     target_port: 8081               # Port to xlate to container port 8080 
     </code></pre>
 2 - generate report against target inventory  create-os-report.yml
     (Var to be defined :
      container_host: towerhost  # host in inventory for temp container report
      home_dir: /var/lib/tmp/ # directory user can access for container
      target_port: 8081) # target mapping port for firewalld to container exposure

 For Convert2rhel process include reg to Satellite plus insights (cloud.redhat.com)
 
