ansible-Postfix
=========

This role installs and configures Postfix.

Supported Platforms
-------------------

* RHEL 6
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

* For FreeBSD a working pkgng setup is required (see: https://www.freebsd.org/doc/handbook/pkgng-intro.html )
* For OpenBSD, you must have PKG_PATH or installpath in `/etc/pkg.conf` set. (see: https://www.openbsd.org/faq/faq15.html#Easy )


Role Variables
--------------

By default this role will install Postfix and provide a minimal configuration, however variables can be passed to this role
and configuration can be overridden, for additional informations please have a look to **defaults/main.yml**


Dependencies
------------

None at this time.

Examples
----------------

1) Just install Postfix, use the defaults
```yaml
- hosts: all
  remote_user: root
  sudo: no
  roles:
    - { role: postfix }
```

2) Install Postfix with a customized main.cf and the default master.cf
```yaml
- hosts: all
  remote_user: root
  sudo: no
  vars:
    postfix_main:
      - myhostname = prod-srv1
      - mydomain = mydomain
      - myorigin = prod-srv1
      - inet_interfaces = all
      - mydestination = localhost.localdomain, localhost, localdomain
      - mynetworks = 127.0.0.0/8, 172.31.2.0/24
      - relayhost = mail2-relay.mydomain.tld
  roles:
    - { role: postfix }
```

3) Install Postfix, using custom, user provided, templates for main.cf and master.cf
```yaml
- hosts: all
  remote_user: root
  sudo: no
  vars:
    postfix_master_template: templates/postfix_stdmaster.cf.j2
    postfix_main_template: templates/postfix_localspooler.cf.j2
  roles:
    - { role: postfix }
```

License
-------

GPLv2

Author Information
------------------

Alessio Cassibba (x-drum) http://blog.zerodev.it.
