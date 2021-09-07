## Satellite Bootstrap Roles
There are two roles included in this directory: `convert2rhel-satellite-prep`, which connects to Red Hat Satellite and prepares Satellite for first time use, including the creation of Activation Keys; `create-rhsm-credential-type` creates a new Credential Type used in fetching Subscription Manifests from Red Hat Subscription Manager (RHSM).

## Notes
Launching the `satellite_bootstrap.yml` playbook requires a RHSM Credential, and a Satellite Credential to run. Variable definitions can be found below.

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
```

Injector Configuration:
```
extra_vars:
  satellite_password: '{{ password }}'
  satellite_server_url: '{{ url }}'
  satellite_username: '{{ username }}'
  validate_certs: '{{ validate_certs }}'
```

## Variable Definitions
```
Variable                          Required     Suggested Value                   Description
--------                          --------     ---------------                   -----------
convert2rhel_versions             Yes          ['7', '8']                        Release versions of Convert2RHEL to use
fetch_rhsm_manifest               Yes          true                              Pull a Satellite manifest directly from RHSM. Required RHSM credential to be present
rhsm_manifest_uuid                No                                             UUID of a manifest created in RHSM
satellite_create_location         Yes          false                             Create a location in Satellite if it doesn't exist
satellite_lifecycle_environment   Yes          Library                           Lifecycle Environment to use for Convert2RHEL
satellite_location                Yes                                            Location to use
satellite_organization            Yes                                            Organization to use
satellite_subscription_name       Yes          Red Hat Enterprise Linux Server   Product name as referenced in the subscription manifest                          
satellite_manifest_path           No                                             Path used to upload a manifest if not fetching from RHSM (not recommended)
skip_content_sync                 No           false                             Skip refreshing repositories if they have already been synced
```

An example of a proper configuration is provided below:
```
convert2rhel_versions:
  - '7'
fetch_rhsm_manifest: true
rhsm_manifest_uuid: '123-456-789'
satellite_create_location: true
satellite_create_organization: true
satellite_lifecycle_environment: Library
satellite_location: San Francisco
satellite_organization: Convert2RHEL
satellite_subscription_name: Red Hat Enterprise Linux Server
skip_content_sync: false
```

Another valid configuration can be found below:
```
convert2rhel_versions:
  - '6'
  - '7'
  - '8'
fetch_rhsm_manifest: false
satellite_manifest_path: 'manifest.zip'
satellite_create_location: false
satellite_create_organization: false
satellite_lifecycle_environment: Library
satellite_location: San Francisco
satellite_organization: Convert2RHEL
satellite_subscription_name: Red Hat Enterprise Linux Server
skip_content_sync: true
```