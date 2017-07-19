# ADOP/C OpenVPN Access Server Service

A CloudFormation template to launch a single OpenVPN Access Server service to compliment the hardened ADOP/C.

The template and OpenVPN service provide the following features:

* Optionally assigns an Elastic IPv4 address.
* Enables by default routing of all of the connected users Internet traffic and DNS queries through the VPN.
* Exposes OpenVPN through TCP port 443 and UDP port 1194.
* LDAP backed authentication provider.
* Google Multi-Factor Authentication (MFA) is enabled and enforced by default.
* Self-signed SSL certificates for the OpenVPN connect and management interface.

# Using this template

## Prerequisites

* An ADOP/C or LDAP directory to connect to.
* AWS Elastic IP address allocated.

## Instructions

1. To use the CloudFormation template you will need to accept the AWS license agreement for the OpenVPN Access Server AMI. See the `OpenVPN License Costs` section in this readme.
2. Navigate to the AWS CloudFormation service in your preferred AWS region.
3. Create Stack.
4. Choose template and upload the `service-openvpn-access-server-security-group.yaml` template.
5. Complete the required parameters. See `CloudFormation` section of the readme for more descriptive parameter definitions.
6. On successful stack creation, a security group has been created for your OpenVPN Access Server.
7. Create stack again and choose the `service-openvpn-access-server.yaml` template.
8. Complete the required parameters. See `CloudFormation` section of the readme for more descriptive parameter definitions.  It is recommended to give the initial Open VPN user a complex password as this is a local system configuration fallback user for the VPN. Please also ensure MFA is enabled for this user.
9. On successful stack creation, OpenVPN access server has been setup and configured!
10. The web console uses self-signed SSL certificates. It is recommended to use trusted certificates. [LetsEncrypt](https://letsencrypt.org/) is a service that has an Open CA that you can use to sign certificates free of cost. Please follow the official Open VPN [guide](https://docs.openvpn.net/how-to-tutorialsguides/administration/installing-and-managing-ssl-web-certificates-in-openvpn-access-server/) for managing SSL web certificates.
11. Smoke test -> Confirm you can login to the following links:

* OpenVPN Connect Client. Authenticate using a user in LDAP - https://OpenVPN-EIP-or-FQDN/.
* OpenVPN Management console. Authenticate using OpenVPN Admin username and password (you have to be connected to the VPN to administrate. See Activating MFA and connecting to OpenVPN section in this readme) - https://OpenVPN-Private-IP:943/admin

12. Next, try connecting to the VPN. See "Activating MFA and Connecting to OpenVPN" section in this readme. Note, all users are managed by the configured LDAP.

## Activating MFA and connecting to OpenVPN

1. Navigate to https://OpenVPN-EIP-or-FQDN/
2. Enter an LDAP user username and password in the respective fields.
3. Select "Login" from the drop-down menu and click "go" to authenticate.
4. Scan the QR code using the Google Authenticator app on your Android/Windows or iOS mobile device.
5. Activate Google MFA.
6. Login again using the "connect" option. This time you will be prompted for an MFA token.
7. Install [OpenVPN community GUI client](https://swupdate.openvpn.org/community/releases/openvpn-install-2.4.1-I601.exe)
8. Open OpenVPN GUI client as Administrator.
9. Import your OpenVPN config file (By right clicking OpenVPN GUI and choosing "Import file...").
10. Connect using your username, password and MFA token when prompted.

Note: If using a system other that Windows 10 you can use the OpenVPN Connect Client instead of OpenVPN GUI Client.
1. Download and install the OpenVPN Connect client by clicking "click here to continue".
2. Open the OpenVPN connect client and follow the on-screen instructions to connect to the VPN.

Note: All users prior to logging in to the VPN are required to activate MFA by following the above instructions.

## OpenVPN License Costs

This template uses the OpenVPN Access Service Marketplace AMI which comes prepackaged with an OpenVPN license.

In this template you have the ability to select one of the following license types:

* Bring your own license (BYOL)
* 10 connected devices
* 25 connected devices
* 50 connected devices
* 100 connected devices
* 250 connected devices

#### You could save your project ~$700 (for a 10 users license) a year if you choose the BYOL option, procure (through the correct channels) and [install](https://openvpn.net/index.php/access-server/docs/admin-guides/admin-guides/218-how-to-activate-your-license-in-openvpn-access-server.html) your own OpenVPN license. See [here](https://openvpn.net/index.php/access-server/pricing.html) for BYOL OpenVPN license costs. If the BYOL license is not chosen then the OpenVPN license costs will be billed through your AWS account.

Once you have decided on a license navigate [here](https://aws.amazon.com/marketplace/search/results?x=0&y=0&searchTerms=OpenVPN+Access+Server&page=1&ref_=nav_search_box).
1. Click the license type you require.
2. Select the region you will be launching the CloudFormation template in.
3. Click "continue". Confirm you are happy with the associated license costs.
4. Accept the license agreement.

# CloudFormation

The service uses the AWS CloudFormation service to provision the resource required for the Open VPN Access Server.

## Deployment Instructions

### Template - OpenVPN Security Group

#### Inputs

| Parameter | Description |
|-----------|-------------|
| VpcId | The VPC IP with ADOP/C provisioned (created in layer1) |
| LDAPSG | Security Group your LDAP instance is running in e.g. ADOP/C SecurityGroup (REQUIRED) |
| ProxySG | SecurityGroup for your outer Proxy instance. Set this to an arbitrarily random security group if you do not have an outer proxy setup yet |
| VpnIpWhiteListCidr | The Cidr range of IPs to whitelist. By default the VPN is open to the world. |

#### Outputs

| Output | Description |
|--------|-------------|
| OpenVPNSecurityGroup | ID of the OpenVPN Security Group |

### Template - OpenVPN Access Server

#### Inputs

| Parameter | Description |
|-----------|-------------|
| LdapBindDn | The LDAP server BindDn (by default for ADOP/C, this is cn=admin,dc=ldap,dc=example,dc=com) |
| LdapServerURL | The LDAP server or proxy endpoint e.g. host:389 |
| LdapSSLEnabled | Connect to LDAP via SSL. Select 'never' if SSL is not enabled else 'always'. |
| LdapUsersBaseDn | Users base distinguished name. |
| LdapBindDnPassword | LDAP server Admin bind DN password (The value of LDAP_PWD in your platform.secrets.sh file) |
| LdapLoginAttribute | The LDAP login attribute from the LDAP directory schema. |
| LdapVpnGroupMembership | The LDAP VPN group membership query (e.g. memberOf=cn=vpn,ou=groups,dc=ldap,dc=example,dc=com) Leave this parameter empty if you do not want to restrict access to the Open VPN from a particular group. |
| ElasticIP | The IPv4 address of the Elastic IP (EIP) to assign to the OpenVPN EC2 Instance. If an EIP is not provided a public IP is allocated. |
| InstanceType | The OpenVPN EC2 instance type. It is recommended an instance type with support for enhanced networking is selected given the advantage of network throughput and low network latency. See https://aws.amazon.com/ec2/instance-types/ for more information. |
| OpenVpnServerFQDN | The fully-qualified domain name (FQDN) for the OpenVPN to use (e.g. vpn.example.com). Use the ElasticIP IPv4 address if you do not have a domain name. Leave this parameter blank if you have opted for an auto-assigned IPv4 address.|
| OpenVPNKeyPair | The OpenVPN Key pair name. |
| OpenVpnLicenseType | The OpenVPN License type. See OpenVPN Access Server AMI in the market place for AWS charges. Options: Bring your own license (BYOL), 2/10/25/50/100/250 connected devices respectively. |
| VpcId | The VPC IP with ADOP/C provisioned. |
| SubnetId | The subnet id of the ADOP/C public subnet.
| OpenVPNSecurityGroup | Security Group for OpenVPN (found via outputs from the previous OpenVPN Security Group CloudFormation script) |
| OpenVpnAdminUserUsername | The OpenVPN service admin user username. |
| OpenVpnAdminUserPassword | The OpenVPN service admin user password. Please use a complex password as this is a local system user that can administer the VPN. |
| OpenVpnSplitTunnel | True if the OpenVPN is a split tunnel, else false. A VPN is considered to be a split tunnel if it only routes private traffic over the tunnel and not the traffic destined for the default gateway. |

#### Outputs

| Output | Description |
|--------|-------------|
| OpenVpnManagementConsole | The OpenVPN Management Console URL |
| OpenVpnLoginUrl | OpenVpn User Login URL |


# Administrating OpenVPN

See [OpenVPN Access Server Command-line tools](https://docs.openvpn.net/docs/access-server/openvpn-access-server-command-line-tools.html) for more information on using the CLI to administer OpenVPN.

# What this template does not do?

* OpenVPN service is not highly available.
* Backups - User MFA profiles are not backed up.


# User feedback

## Documentation
Documentation will be captured within this README.md.

## Issues
If you have any problems with or questions about this, please raise an issue.

## Contribute
You are invited to contribute new features, fixes, or updates, large or small; we are always thrilled to receive pull requests, and do our best to process them as fast as we can.

We expect you to have forked this repository in Github and modified to meet your project requirements as needed.
