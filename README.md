# convert2rhel-satellite-ansible
Use Ansible Tower to convert CentOS systems to RHEL and register to Satellite

Thanks to Andrew Schoenfeld and Tony Harris!


For OS Reports -  
Steps:
 1 - install podman on target container host.  install-podman.yml
     target_port: 8081) # target mapping port for firewalld to container exposure
 2 - generate report against target inventory  create-os-report.yml
     (Var to be defined :
      container_host: towerhost  # host in inventory for temp container report
      home_dir: /var/lib/tmp/ # directory user can access for container
      target_port: 8081) # target mapping port for firewalld to container exposure

 For Convert2rhel process include reg to Satellite plus insights (cloud.redhat.com)
 
