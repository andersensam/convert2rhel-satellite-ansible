# convert2rhel-satellite-ansible
Use Ansible Tower to convert CentOS systems to RHEL and register to Satellite and Insights (cloud.redhat.com)

Thanks to Andrew Schoenfeld and Tony Harris!

<B> OS Reports </B>
This checks an inventory for family of OS and creates a report review in a conatiner running on designed container host.
Example:
![image](https://user-images.githubusercontent.com/17077661/120725650-4ca03c80-c48b-11eb-89b1-aca039e95f8b.png)



Steps:
 1 - install podman on target container host.  install-podman.yml
     target_port: 8081) # target mapping port for firewalld to container exposure
 2 - generate report against target inventory  create-os-report.yml
     (Var to be defined :
      container_host: towerhost  # host in inventory for temp container report
      home_dir: /var/lib/tmp/ # directory user can access for container
      target_port: 8081) # target mapping port for firewalld to container exposure

 For Convert2rhel process include reg to Satellite plus insights (cloud.redhat.com)
 
