## Configuring Cloudbreak for LDAP/AD authentication    

By default Cloudbreak uses an internal system as the user store for authentication (enabled by using [CloudFoundry UAA](https://github.com/cloudfoundry/uaa )). If you would like to configure LDAP or Active Directory (AD) external authentication, you need to:  

1. Collect the [following information](#ldapad-information) about your LDAP/AD setup    
2. [Configure Cloudbreak](#configuring-cloudbreak-for-ldapad) to work with that LDAP/AD setup


### LDAP/AD information 

The following table details the properties and values that you need to know about your LDAP/AD environment on order to use the LDAP/AD with Cloudbreak: 

| Parameter | Description | Example |
|---|---|---|
| **base** |
| url | The LDAP url with port | `ldap://10.0.3.128:389/` | 
| userDn | Enter the root Distinguished Name to search in the directory for users. | `cn=Administrator,ou=srv,dc=hortonworks,dc=local` |
| password | Enter your root Distinguished Name password. |  `MyPassword1234!`|
| searchBase | Enter your LDAP user search base. This defines the location in the directory from which the LDAP search begins. | `ou=Users,dc=hortonworks,dc=local` |
| searchFilter | Enter the attribute for which to conduct a search on the user base. | `mail={0}` |
| **groups** |
| searchBase | Enter your LDAP group search base. This defines the location in the directory from which the LDAP search begins. | `ou=Groups,dc=hortonworks,dc=local` |
| groupSearchFilter| Enter the attribute for which to conduct a search on the group base. | `member={0}` |

### Configuring Cloudbreak for LDAP/AD

There are two parts to configuring Cloudbreak for LDAP/AD:

* Configuring LDAP/AD user authentication for Cloudbreak  
* Configuring LDAP/AD group authorization for Cloudbreak  

#### Configure user authentication

Configure LDAP/AD user authentication for Cloudbreak by using these steps. 

**Steps** 

1. On the Cloudbreak host, browse to `/var/lib/cloudbreak-deployment`.  
2. Create a new file called `uaa-changes.yml`. 

    > The name of this file can be customized by setting the following in Profile: `export UAA_SETTINGS_FILE=myldap.yml`
    
3. In the yml file enter the following using your [LDAP/AD information](#ldapad-information). Next, save the file and restart Cloudbreak.  

<pre>spring_profiles: postgresql,ldap

ldap:
  profile:
    file: ldap/ldap-search-and-bind.xml
  base:
    url: ldap://10.0.3.138:389
    userDn: cn=Administrator,ou=srv,dc=hortonworks,dc=local
    password: ’mypassword’
    searchBase: ou=Users,dc=hortonworks,dc=local
    searchFilter: mail={0}
  groups:
    file: ldap/ldap-groups-map-to-scopes.xml
    searchBase: ou=Groups,dc=hortonworks,dc=local
    searchSubtree: false
    maxSearchDepth: 1
    groupSearchFilter: member={0}
    autoAdd: true
</pre>



#### Configure group authorization 

Once user authentication is configured, you need to configure which group(s) can access Cloudbreak. Users (once authenticated) will be granted permission to access Cloudbreak and use the capabilities of Cloudbreak based on their group member. The following describes how to create (i.e. execute-and-map) a group authorization and how to remove (i.e. delete-mapping) an authorization. 

To create a group authorization, execute the following (for example: to add “Analysts” group):
 
<pre>cbd util execute-ldap-mapping cn=Analysts,ou=Groups,dc=hortonworks,dc=local</pre>

To remove a group authorization, execute the following (for example: to remove “Analysts” group):

<pre>cbd util delete-ldap-mapping cn=Analysts,ou=Groups,dc=hortonworks,dc=local</pre>
