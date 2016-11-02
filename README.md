# Universal Oracle Client

[![Build Status](https://travis-ci.org/ChristopherDavenport/ansible-role-oracle-client.svg?branch=master)](https://travis-ci.org/ChristopherDavenport/ansible-role-oracle-client)


## Overview

This is the build from the comprehensive oracle-client, you will need to
supply the zip file for the 64 bit client to to to the files directory.
There are plent of roles for the instant client, but this will do a
comprehensive install with all the fundamentals in place.

## Requirements

This requires that you first obtain the oracle client download from oracle and
place it in the files folder for this role. Once placed there you are off to
the races downloading to your hearts content.

You will also want to follow instructions for variables as by default this
installs the client with no databases.

The download page can be found
[here](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html)
although obvious new releases will come out. You are looking for the linuxamd64
client, near the bottom of the page.

## Role Variables

### Oracle Databases

First whether there is a primary database that this server will be
automatically configured for. The entry for this primary database is
the first entry in the list of databases.

```
oracle_client_has_primary_db: True
```

** NO Databases are set in defaults **

You must set this up if you want to install a database. This
is an example of what the entry should look like. It is not the
default.

```
oracle_client_databases:
  - sid: TEST
    host: fakedb.christopherdavenport.io
    port: 2322
    service_name: TEST
```

If you want to engage the listener then these two variables need to
be set. Again this is an example, these are not the defaults.

```
oracle_client_listener_host: fakedb.christopherdavenport.io
oracle_client_listener_port: 2322
```


### Oracle Install

This is simply so that if you are working into an existing ecosystem
that may not follow these install protocols you can still benefit from
the templating and configuration section.

```
oracle_client_install: True
```

The name of the zip file that needs to be present to installed. It
will be unarchived in a temporary location.

```
oracle_client_zip: linuxamd64_12102_client.zip
```

The temporary location that the client installer files and the
response file will be placed.

```
oracle_client_tmp_install_storage: /tmp/oracleclient
```

These are the base install locations that will fill variables
such as ORACLE_BASE and ORACLE_HOME

```
oracle_client_oracle_app_base: /u01/app
oracle_client_oracle_base: "{{ oracle_client_oracle_app_base }}/oracle"
oracle_client_oracle_home: "{{ oracle_client_oracle_base}}/product/12.1.0/client_1"
```

### Oracle Install Configuration


This can force the variable to a specific hostname if this is being
installed on a machine with multiple hostnames. Otherwise the installer will takes for first hostname found.

```
oracle_client_hostname: ""
```

This is where the oracle inventory folder is installed to. This
is the default location of the installer.

```
oracle_client_inventory_location: /u01/app/oraInventory
```

This is a list of all language components you want installed

```
# Map of Code to Language Component to be installed
#
# en   : English                  ja   : Japanese
# fr   : French                   ko   : Korean
# ar   : Arabic                   es   : Latin American Spanish
# bn   : Bengali                  lv   : Latvian
# pt_BR: Brazilian Portuguese     lt   : Lithuanian
# bg   : Bulgarian                ms   : Malay
# fr_CA: Canadian French          es_MX: Mexican Spanish
# ca   : Catalan                  no   : Norwegian
# hr   : Croatian                 pl   : Polish
# cs   : Czech                    pt   : Portuguese
# da   : Danish                   ro   : Romanian
# nl   : Dutch                    ru   : Russian
# ar_EG: Egyptian                 zh_CN: Simplified Chinese
# en_GB: English (Great Britain)  sk   : Slovak
# et   : Estonian                 sl   : Slovenian
# fi   : Finnish                  es_ES: Spanish
# de   : German                   sv   : Swedish
# el   : Greek                    th   : Thai
# iw   : Hebrew                   zh_TW: Traditional Chinese
# hu   : Hungarian                tr   : Turkish
# is   : Icelandic                uk   : Ukrainian
# in   : Indonesian               vi   : Vietnamese
# it   : Italian
#
# all_langs   : All languages

oracle_client_selected_languages:
  - en
```

Oracle Client Install Type - Must Be Exactly One of These
-   InstantClient
-   Administrator
-   Runtime
-   Custom

```
oracle_client_install_type: InstantClient
```

If the install type is set to custom these are the options that will
be installed.

```
# sqlj              --> "Oracle SQLJ"
# rdbms.util        --> "Oracle Database Utilities"
# javavm.client     --> "Oracle Java Client"
# sqlplus           --> "SQL*Plus"
# dbjava.jdbc       --> "Oracle JDBC/THIN Interfaces"
# ldap.client       --> "Oracle Internet Directory Client"
# rdbms.oci         --> "Oracle Call Interface (OCI)"
# precomp           --> "Oracle Programmer"
# xdk               --> "Oracle XML Development Kit"
# network.aso       --> "Oracle Advanced Security"
# oraolap.mgmt      --> "OLAP Analytic Workspace Manager and Worksheet"
# network.client    --> "Oracle Net"
# network.cman      --> "Oracle Connection Manager"
# network.listener  --> "Oracle Net Listener"
# ordim.client      --> "Oracle Multimedia Client Option"
# odbc              --> "Oracle ODBC Driver"
# has.client        --> "Oracle Clusterware High Availability API"
# dbdev             --> "Oracle SQL Developer"
# rdbms.scheduler   --> "Oracle Scheduler Agent"

oracle_client_custom_install_components:
  - sqlj
  - rdbms.util
  - javavm
  - sqlplus
  - dbjava
  - precomp
  - network.client
```

These are the oracle scheduler variables if you need it to be set.

```
oracle_client_scheduler_agent_hostname: ""
oracle_client_scheduler_agent_port: 1500
```

The oracle client version is specific to the installer in use. Since
the client is reverse compatible this is based on the current
installer when writing this. Please submit pull requests or
file an issue if I have missed a new release at some point

```
oracle_client_version: 12.1.0.2.0
```

### Oracle Client Configuration

First are the variables that enable setting the customizations or
not.

```
oracle_client_set_oratab: True
oracle_client_set_tnsnames: True
oracle_client_set_environmental_variables: True
```

If environmental variables are being set and you want to be able to
reference the name of the shell script, or set it to something else.

```
oracle_client_environment_shell_name: oracle-client.sh
```

If you need to set the NLS Lang environment variable.

```
oracle_client_nls_lang: AMERICAN_AMERICA.WE8ISO8859P1
```

### Oracle Users and Groups

These are fairly constants but in case you need to modify them

```
__oracle_client_user: oracle
__oracle_client_user_id: 11000
__oracle_client_group: oinstall
__oracle_client_group_id: 11000
```

## Dependencies

None.

## Example Playbook

This would be an install for an administrator install.

```
- hosts: oracle_clients
  vars:
    oracle_client_databases:
      - sid: TEST
        host: fakedb.christopherdavenport.io
        port: 2322
        service_name: TEST
    oracle_client_listener_host: fakedb.christopherdavenport.io
    oracle_client_listener_port: 2322
    oracle_client_install_type: Administrator
  roles:
    - ChristopherDavenport.universal-oracle-client
```

A custom install then allows you to set a set of components of
your choice.

```
- hosts: oracle_clients
  vars:
    oracle_client_databases:
      - sid: TEST
        host: fakedb.christopherdavenport.io
        port: 2322
        service_name: TEST
    oracle_client_listener_host: fakedb.christopherdavenport.io
    oracle_client_listener_port: 2322
    oracle_client_install_type: Custom
    oracle_client_custom_install_components:
      - sqlj
      - rdbms.util
      - sqlplus
      - network.client
  roles:
    - ChristopherDavenport.universal-oracle-client
```

Or the baseline which is an InstantClient

```
- hosts: oracle_clients
  vars:
    oracle_client_databases:
      - sid: TEST
        host: fakedb.christopherdavenport.io
        port: 2322
        service_name: TEST
  roles:
    - ChristopherDavenport.universal-oracle-client
```

## License

MIT

### Author Information
This role was created in 2016 by ChristopherDavenport.
