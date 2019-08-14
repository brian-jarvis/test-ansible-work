# test-ansible-work

The password for the vaulted var is `redhat`

The purpose of the test-vars.yml is to validate multiple methods of using variables both clear and vaulted.  Focus was on variables that are set in group_vars then used in the inventory.  Also vars that are used in the middle of a string value.

**Note:** the inventory defines `openshift_master_identity_providers` and the group_vars `openshift_master_identity_providers_groupvar`.  This is done to demonstrate usage of the vaulted variable in both an inventory setting and a group_vars setting.  If you are using this as an example for OpenShift 3.11 the correct variable name is `openshift_master_identity_providers`.  Copy/Paste can be dangerous, use caution. 

To execute run the following command
~~~ bash
ansible-playbook -i inventory test-vars.yml --ask-vault-pass
~~~

You should see the following output.  I have highlighed the notable values
~~~ bash
PLAY [setup testing of various variables from the inventory and group] *******************************

TASK [server from inventory] *************************************************************************
ok: [localhost] => {
    **"srv_name": "test_server_name"**
}

TASK [ldapuser from group] ***************************************************************************
ok: [localhost] => {
    **"ldapuser": "billy"**
}

TASK [ldappass_open from group] **********************************************************************
ok: [localhost] => {
    **"ldappass_open": "redhat-open"**
}

TASK [ldappass_hidden from group using vault] ********************************************************
ok: [localhost] => {
    **"ldappass_hidden": "redhat"**
}

TASK [openshift_master_htpasswd_users from inventory static value] ***********************************
ok: [localhost] => {
    "openshift_master_htpasswd_users": {
        **"ocpadmin": "$apr1$IVr4ykY5$Cc4Gt3IWRMLxcmxfgrkwz0"**
    }
}

TASK [openshift_master_htpasswd_users_vars from inventory group value] *******************************
ok: [localhost] => {
    "openshift_master_htpasswd_users_vars": {
        **"ocpadmin": "redhat-open"**
    }
}

TASK [openshift_master_identity_providers from inventory group vault value] **************************
ok: [localhost] => {
    "openshift_master_identity_providers": [
        {
            "attributes": {
                "email": [
                    "mail"
                ], 
                "id": [
                    "dn"
                ], 
                "name": [
                    "cn"
                ], 
                "preferredUsername": [
                    "uid"
                ]
            }, 
            "bindDN": "cn=Manager,dc=example,dc=com", 
            **"bindPassword": "redhat"**, 
            "ca": "my-ldap-ca-bundle.crt", 
            "challenge": "true", 
            "insecure": "false", 
            "kind": "LDAPPasswordIdentityProvider", 
            "login": "true", 
            "name": "ldap_provider", 
            "url": "ldap://ldap01vm.example.com/dc=example,dc=com?uid"
        }
    ]
}

TASK [openshift_master_identity_providers_groupvar from group  vault value] **************************
ok: [localhost] => {
    "openshift_master_identity_providers_groupvar": [
        {
            "attributes": {
                "email": [
                    "mail"
                ], 
                "id": [
                    "dn"
                ], 
                "name": [
                    "cn"
                ], 
                "preferredUsername": [
                    "uid"
                ]
            }, 
            "bindDN": "cn=Manager,dc=example,dc=com", 
            **"bindPassword": "redhat"**, 
            "ca": "my-ldap-ca-bundle.crt", 
            "challenge": "true", 
            "insecure": "false", 
            "kind": "LDAPPasswordIdentityProvider", 
            "login": "true", 
            "name": "ldap_provider", 
            "url": "ldap://ldap01vm.example.com/dc=example,dc=com?uid"
        }
    ]
}

~~~