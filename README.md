ansible-Postfix
=========

This role installs and configures Postfix.

Supported Platforms
-------------------

* RHEL 6
* RHEL 7
* Ubuntu 14.04 lts
* FreeBSD 10
* OpenBSD 6.0

It will likely run on other platforms, just drop in vars/ a new file to support your os variant, vars are parsed in the following order/format:
* {{ ansible_distribution }}_{{ ansible_distribution_major_version }}.yml
* {{ ansible_distribution }}.yml
* {{ ansible_os_family }}_{{ ansible_distribution_major_version }}.yml
* {{ ansible_os_family }}.yml"

Requirements
------------

* Ansible 2.x or later
* For FreeBSD a working pkgng setup is required (see: https://www.freebsd.org/doc/handbook/pkgng-intro.html )
* For OpenBSD, you must have PKG_PATH or installpath in `/etc/pkg.conf` set. (see: https://www.openbsd.org/faq/faq15.html#Easy )


Role Variables
--------------

By default this role will install Postfix and provide a minimal, safe configuration, however override is possible passing additional
directives in order to configure **$config_directory/main.cf** and **$config_directory/master.cf** or using a custom template.
For additional informations please have a look to configuration examples below and the **defaults/main.yml** file.


Dependencies
------------

None at this time.

Examples
----------------

1) Just install Postfix, use the defaults
```yaml
- hosts: all
  roles:
    - role: postfix
```

2) Install Postfix with a customized main.cf and the default master.cf
NOTES: 
Values like **yes** should be double quoted.
Keys in dicts must be unique (see postfix_master example).
```yaml
- hosts: all
  roles:
    - role: postfix
      postfix_main:
        myhostname: prod-srv1
        mydomain: mydomain
        myorigin: prod-srv1
        inet_interfaces: all
        mydestination: localhost.localdomain, localhost, localdomain
        mynetworks: 127.0.0.0/8, 172.31.2.0/24
        relayhost: "[mail2-relay.mydomain.tld]:465"
        smtp_use_tls: "yes"
        smtp_tls_CAfile: /etc/ssl/certs/ca-bundle.crt
      postfix_master:
        smtp: smtp inet n - - - - smtpd
        cleanup: cleanup unix n - - - 0 cleanup
        qmgr: qmgr unix - - - 300 1 qmgr
        rewrite: rewrite unix - - - - - trivial-rewrite
        proxymap: proxymap unix - - - - - proxymap
        stmp2: smtp
        virtual: virtual unix - n - - - virtual
        anvil: anvil unix - - - - 1 anvil
        local: local unix - n - - - local
```

3) Install Postfix, using custom, user provided, templates for main.cf and master.cf
```yaml
- hosts: all
  vars:
    postfix_master_template: templates/postfix_stdmaster.cf.j2
    postfix_main_template: templates/postfix_localspooler.cf.j2
  roles:
    - role: postfix
```

License
-------

GPLv2

Author Information
------------------

Alessio Cassibba (x-drum) http://blog.zerodev.it.
