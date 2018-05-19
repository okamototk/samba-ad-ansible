# Ansible Playbook for Install Samba as PDC

This ansible will create Active Directory PDC with samba.
You don't need Windows Server now!

## Install Samba

Edit hosts.inventory and run playbook:

    # ansible-playbook site.yml -i hosts.inventory

Administrator password is written in group_vars/samba-pdc.yml

## Check working

### Active Directory

    # kinit administrator@MYDOMAIN.COM
    Password for administrator@MYDOMAIN.COM: <ENTER PASSWORD described in group_vars/samba-adc>
    Warning: Your password will expire in 41 days on Thu 28 Jun 2018 08:37:03 PM EDT

### LDAP

    # cp /var/lib/samba/private/tls/ca.pem /etc/pki/ca-trust/source/anchors/
    # update-ca-trust
    # yum install -y openldap-clients
    # ldapsearch -ZZ -x -D "cn=administrator,cn=users,dc=mydomain,dc=com" -w sysadmin0! -h pdc.mydomain.com -b "cn=users,dc=mydomain,dc=com" "(objectclass=*)" "sAMAccountName=username"

## Client Setting

Configure DNS for Samba PDC.

### /etc/resolv.conf

    search localdomain mydomain.com
    nameserver 192.168.0.31 # your samba pdc ip addrss

## User/Group Management

    # samba-tool user create okamototk
    # samba-tool group add mygroup
    # samba-tool group addmembers mygroup okamototk

## Add client to domain

    # net ads join -S pdc.mydomain.com -U administrator
    Enter administrator's password:
    Using short domain name -- MYDOMAIN
    Joined 'CLIENT' to dns domain 'mydomain.com'



## Trouble Shooting

### Upper DNS doesn't work

Check dns forwarder configuration in /etc/samba/smb.conf

    [global]
            dns forwarder = 8.8.8.8

### Fail DNS update on client

    # net ads join -v -U administrator
    Enter administrator's password:
    Using short domain name -- MYDOMAIN
    Joined 'MYCLIENT' to dns domain 'mydomain.com'
    No DNS domain configured for myclient. Unable to perform DNS Update.
    DNS update failed: NT_STATUS_INVALID_PARAMETER

Please check client configuration. Check following site:

* https://lists.samba.org/archive/samba/2014-June/182437.html