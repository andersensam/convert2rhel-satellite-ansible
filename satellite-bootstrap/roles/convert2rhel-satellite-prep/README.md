# convert2rhel-satellite-prep
This role takes care of the minimal setup and config used by Satellite to streamline the convert2rhel for scale.  By default it will create the necessary assets for centOS to RHEL conversions from major versions 6, 7 and 8.


## Prerequisites
- Satellite must be installed
- An organization and location must be created
- A subscription manifest must be downloaded and stored in the files directory of this role.  It can be either Simple Content Access enabled or Classic

## Configurables
All of the required variables are listed out and given as examples in the vars directory.  Optional variables are defined in the defaults directory.  These can be overridden by putting different values in the vars directory.

## Features

### Location Creation
By default, this role requires that you have both your Organization and Lcoation defined, however you can have this role create those defined in the vars directory by setting the `satellite_create_organization` and/or `satellite_create_location` variables to `true`.

### Asset Base Names
The defaults directory sets several sane base names for the Satellite assets that, in turn, are concatenated with appropriate major version numbers to allow for straight-forward and understandable asset names.  If you have organizational needs or standards that dictate chaning these you can place overrides for the variables in the vars directory.

### Certificate Validation
By default, this role will validate that Satellite is using an appropriately signed and trusted certificate.  If the certificate is self-signed (or otherwise untrusted), this behavior can be overridden by updating the `satellite_validate_certs` variabel to `true` in the vars directory.

### Rollback
If removal of the assets created by this role is desired, the variable `rollback` can be set to true.  **This action will remove both the Organization and Location named in the role** If you have other assets in this Organization **DO NOT** use this.


