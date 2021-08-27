## Satellite Bootstrap Roles
There are two roles included in this directory: `convert2rhel-satellite-prep`, which connects to Red Hat Satellite and prepares Satellite for first time use, including the creation of Activation Keys; `create-rhsm-credential-type` creates a new Credential Type used in fetching Subscription Manifests from Red Hat Subscription Manager (RHSM).

## Notes
Please note that the `create-rhsm-credential-type` role can <b>ONLY</b> be used on non-containerized versions of Ansible Tower. To create the proper credential type on a deployment running in OpenShift, for example, please follow the instruction below.

## Creating the RHSM Credential Type
1. On the lefthand sidebar in Ansible Tower, click on "Credential Types" under the "Administration" section.

2. Click the green plus on the lefthand side of the window

3. Name the credential whatever you'd like, for this deployment we've chosen `RHSM Credential`

4. Use the following for the Input Configuration:
```
fields:
  - id: rhsm_username
    type: string
    label: Username
  - id: rhsm_password
    type: string
    label: Password
    secret: true
required:
  - rhsm_username
  - rhsm_password
```

5. Use the following for the Injector Configuration:
```
extra_vars:
  rhsm_password: '{{ rhsm_password }}'
  rhsm_username: '{{ rhsm_username }}'
```

6. Click Save

## Creating a custom Red Hat Satellite Credential Type
While not required, it is strongly recommended to create a credential type for use by the Satellite Bootstrap process. Follow the above instructions for creating the `RHSM Credential`, using the following values:

Input Configuration:
```
fields:
  - id: username
    type: string
    label: Username
  - id: password
    type: string
    label: Password
    secret: true
  - id: url
    type: string
    label: Satellite Server URL
  - id: validate_certs
    type: boolean
    label: Validate SSL Certificates
required:
  - username
  - password
  - url
  - validate_certs
```

Injector Configuration:
```
extra_vars:
  satellite_password: '{{ password }}'
  satellite_server_url: '{{ url }}'
  satellite_username: '{{ username }}'
  validate_certs: '{{ validate_certs }}'
```