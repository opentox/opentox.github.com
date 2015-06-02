---
layout: post
title: "Bugzilla Authentication for specific LDAP group combined with Apache LDAP Auth"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Bugzilla has a plugin to support LDAP user authentication, but a support for group restricted access is not implemented. 
An easy workaround to solve this problem the Bugzilla LDAP authentication setup can be combined with Apache2 ldap_authz module based login.

* Apache2 authentication gives login credentials to Bugzilla. 
 

        <Location "/">
            AuthName "Bugzilla login required. "
            AuthType Basic
            AuthBasicProvider ldap
            AuthLDAPURL "ldap://myserver.org:389/ou=people,dc=myldapdomain,dc=org?uid,mail,cn?sub"
            AuthzLDAPAuthoritative on
            AuthLDAPBindDN "cn=proxyagent,dc=myldapdomain,dc=org"
            AuthLDAPBindPassword "myverysecretpassword"
            Require ldap-group cn=MyGroup,ou=groups,dc=myldapdomain,dc=org
            AuthLDAPRemoteUserAttribute uid
        </Location>

NOTE: Do set user access rights after the first login of a user.
