# rhsm-repo
A lightweight container to host a basic RHSM repo for CentOS/Oracle Linux 6 hosts. Only used when the RHSM package is not already installed.

## Prerequisites
- Ansible Controller version >= 3.8.0 installed
- Host inventories have been created and are accessible from Ansible Controller

## Playbook Overview
```
launch_rhsm_repo.yml        # Create a service account on the remote host
cleanup_rhsm_repo.yml       # Finish executing the convert2rhel script
```

## Variable Definitions
```
Variable             Required     Suggested Value     Description
--------             --------     ---------------     -----------
repo_user            Yes                              Username of account to run the repo container as
target_port          Yes                              Port to expose, e.g. 8080
```

An example of a proper configuration is provided below:
```
repo_user: svcansible
target_port: 8080
```

## Other Design Considerations
When creating Job Templates from the provided playbooks, it is recommended to increase the verbosity to at least `1 (Verbose)`.

Note that this container can be run in any k8s environment, exposed via an ingress. The internal port is `8080`. See the below template for deployment in OpenShift:

```
apiVersion: v1
kind: Template
labels:
  template: rhsm-repo-template
message: Deployed the Convert2RHEL RHSM Repo template!
metadata:
  name: rhsm-repo-template
objects:
  - kind: DeploymentConfig
    apiVersion: v1
    metadata:
      labels:
        application: ${APPLICATION_NAME}
        api-instance: rhsm-repo
      name: ${APPLICATION_NAME}-1
    spec:
      replicas: 1
      selector:
        api-instance: rhsm-repo
      strategy:
        resources: {}
      template:
        metadata:
          labels:
            application: ${APPLICATION_NAME}
            api-instance: rhsm-repo
        spec:
          containers:
          - args:
            - nginx
            - -g
            - daemon off;
            image: quay.io/samander/convert2rhel-rhsm:latest
            name: ${APPLICATION_NAME}-1
            ports:
            - containerPort: 8080
            resources:
              limits:
                cpu: 30m
                memory: 256Mi
              requests:
                cpu: 10m
                memory: 64Mi
          restartPolicy: Always
      test: false
  - kind: Service
    apiVersion: v1
    metadata:
      labels:
        application: ${APPLICATION_NAME}
        api-instance: rhsm-repo
      name: rhsm-repo-service
    spec:
      ports:
      - name: "8080"
        port: 8080
        targetPort: 8080
      selector:
        api-instance: rhsm-repo
  - kind: Route
    apiVersion: v1
    metadata:
      name: rhsm-repo-route
      labels:
        api-instance: rhsm-repo
        application: ${APPLICATION_NAME}
    spec:
      host: convert2rhel-rhsm-repo.${OCP_URL}
      to:
        kind: Service
        name: rhsm-repo-service
        weight: 100
      port:
        targetPort: '8080'
      tls:
        termination: edge
      wildcardPolicy: None
parameters:
  - name: APPLICATION_NAME
    displayName: Application name
    description: Name of the application being deployed
    value: convert2rhel-rhsm-repo
    required: true
  - name: OCP_URL
    displayName: OpenShift Base URL
    description: Base URL being used for OpenShift applications, e.g. *.apps.openshift.com
    required: true
```