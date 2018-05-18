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
    # ldapsearch -ZZ -x -D "cn=administrator,cn=users,dc=mydomain,dc=com" -w sysadmin0! -h myhost.mydomain.com -b "cn=users,dc=mydomain,dc=com" "(objectclass=*)" "sAMAccountName=username"

## Client Setting

Configure DNS for Samba PDC.

### /etc/resolv.conf

    search localdomain mydomain.com
    nameserver 192.168.0.31 # your samba pdc ip addrss

## User/Group Management

    # samba-tool user create okamototk
    # samba-tool group add mygroup
    # samba-tool group addmembers mygroup okamototk

# samba-tool group addmembers mygroup okamototk

## Trouble Shooting

### Upper DNS doesn't work

Check dns forwarder configuration in /etc/samba/smb.conf

    [global]
            dns forwarder = 8.8.8.8

