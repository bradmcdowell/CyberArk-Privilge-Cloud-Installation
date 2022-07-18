# CyberArk Privilge Cloud Installation
CyberArk Privilege Cloud Installation Notes

## 01 - Pre-Requisites Script
Download Pre-Requisites Script from the SFE.

https://cyberark-customers.force.com/s/article/Privilege-Cloud-How-to-run-the-PSMCheck


## 02 - Connector Install

Hardware Requirements
https://docs.cyberark.com/Product-Doc/OnlineHelp/PrivCloud/Latest/en/Content/Privilege%20Cloud/PrivCloud-sys-req-connector.htm?tocpath=Setup%7CSystem%20requirements%7C_____2

Network Requirements
https://docs.cyberark.com/Product-Doc/OnlineHelp/PrivCloud/Latest/en/Content/Privilege%20Cloud/PrivCloud-sys-req-networks.htm?tocpath=Setup%7CSystem%20requirements%7C_____1



## 03 - Secure Tunnel and LDAPs

LDAP Certificate Tool

## 04 - SAML Authentiation


In Azure Active Directory Domain Services console, create a new Enterprise Application (non-gallery application).
Configure Mode as: SAML-based Sign-on
Basic SAML Configuration:
Identifier (Entity ID): 
```
PasswordVault
```
Reply URL: 
```
https://<customer>.privilegecloud.cyberark.com/PasswordVault/api/auth/saml/logon
```
Check 'Show advanced URL settings' > Sign on URL: 
```
https://<customer>.privilegecloud.cyberark.com/PasswordVault/v10/logon/saml
```
Logout URL:
```
https://<customer>.privilegecloud.cyberark.com/PasswordVault/logoff.aspx
```
User Attributes & Claims
•  Unique User Identifier: 
```
samaccountname
```

Provide CyberArk:
In 'SAML Signing Certificate':
The 'App Federation Metadata Url' which will contain the information from above (Recommended)


## 05 - PSM Certificate and HTML5 Gateway

### PSM Server Change Certificate

```
#Copy the tumbprint for the certificate you have the Private Key of (usually the personal certificate of the machine unless the customer followed your instructions to build the CA from scratch)

Get-ChildItem "Cert:\LocalMachine\My"

#Set the path for where the thumbprint will be seen by RDS
$PATH = (Get-WmiObject -class "Win32_TSGeneralSetting" -Namespace root\cimv2\terminalservices)

#Execute the change
Set-WmiInstance -Path $PATH -argument @{SSLCertificateSHA1Hash="<INSERT-Thumbprint-HERE>"}

```



Configure the connection component to use the HTML5 GW

```
AllowSelectHTML5

HTML5 Gateway

CyberArk.TransparentConnection.BooleanUserParameter, CyberArk.PasswordVault.TransparentConnection
```



## 06 - Loadbalancer

## 07 - PSMP Installation

### Create proxymng User and proxymanagers Group

```
useradd proxymng
passwd proxymng
groupadd proxymanagers
usermod -a -G proxymanagers proxymng
```
### Modify SSHD Config File
```
vi /etc/ssh/sshd_config
```

Add the following line to the bottom of /etc/ssh/sshd_config
```
AllowGroups proxymanagers PSMConnectUsers
```
Restart the SSH service
```
service sshd restart
service sshd status
```

### Change Directory to /home/localadmin/PSMP/
```
cd /home/localadmin/PSMP/
```
### Unzip CyberArk Files
```
ls
unzip PrivilegedSessionManagerSSHProxy-RHELinux8-Intel64-Rls-v1X.X.zip
unzip psmpwiz1XX.zip
```
### Set the follwoing files to exexutable
```
ls -ltr
chmod 755 CARKpsmp-1X.xx.xx.xx.rpm
chmod 755 CreateCredFile
chmod 755 psmpwiz1XX.sh
```

### Run the PSMP install script
```
./psmpwiz1XX.sh
```
  



PSM for SSH requirements
https://docs.cyberark.com/Product-Doc/OnlineHelp/PrivCloud/Latest/en/Content/Privilege%20Cloud/PrivCloud-sys-req-PSM-SSH.htm?tocpath=Setup%7CSystem%20requirements%7C_____3

##

## 08 - PSM Native Access RDP

https://docs.cyberark.com/Product-Doc/OnlineHelp/PrivCloud/Latest/en/Content/Privilege%20Cloud/privCloud-connect-using-RDP.htm?tocpath=End%20Users%7CConnect%20to%20a%20target%20device%7C_____3#RDPsettings

```
full address:s:PSMHOSTNAME
alternate shell:s:psm /u localadmin01 /a win1.cybr.com /c PSM-RDP
username:s:john
enablecredsspsupport:i:0
```
If using RDCMan
https://cyberark-customers.force.com/s/article/How-to-setup-Remote-Desktop-Connection-Manager


CPM Troubleshooting

https://cyberark-customers.force.com/s/article/How-does-CPM-manage-Windows-accounts-passwords

##
