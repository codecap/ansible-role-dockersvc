Ansible Role: dockersvc
=========
[![Build Status](https://travis-ci.org/codecap/ansible-role-dockersvc.svg?branch=master)](https://travis-ci.org/codecap/ansible-role-dockersvc)
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-codecap.dockersvc-blue.svg)](https://galaxy.ansible.com/codecap/dockersvc)

An ansible role to manage dockerized services.

Requirements
------------

* Before this role can be applied you should ensure docker installed properly. There is no dependency for that, so you can accomplisch this, applying various roles.
* The role uses systemd to configure the service.
* ```hash_behaviour = merge``` has to be added to ansible.cfg

Role Variables
--------------

```yaml
dockersvc_conf:
  # set parameters needed by the service here
  service_parameters:       {}
  # set  docker run parameters
  run_parameters:
    '--log-driver':         journald
  # envirnoment variables
  environment:              {}
  # systemd restart parameter
  restart:                  'on-failure'
  # custom contetn of the unit file
  custom_unit_file_content: "{{None|default(omit)}}"
  # service enabled and state parameters
  # set ensure to 'ignore' to ignore setting parameters
  ensure:
    enabled:                  true
    state:                    started
  # possible service exit codes
  exit_codes:
```
Dependencies
------------

No.

Example Playbook
----------------
The following will install skydns as docerized service

```yaml
- hosts:       all
  remote_user: root
  roles:
    - role: dockersvc
        dockersvc_conf:
            name:                'skydns'
            image:               'skynetservices/skydns:2.5.3a'
            success_exit_status: '0 2 SIGTERM SIGKILL'
            run_parameters:
              '--publish':    [ '53:53/udp', '53:53/tcp' ]
              '--add-host':   'etcd-server:168.254.0.1'
              '--net':        'host'
            service_parameters:
              '-addr':        '0.0.0.0:53'
              '-domain':      'cluster.local'
              '-path-prefix': '/skydns'
              '-machines':    'etcd-server'
              '-nameservers': "{{['8.8.8.8','8.8.4.4']|join(':53,')}}:53"
              '-verbose':     ''
```

License
-------

BSD

Author Information
------------------

Vladislav Nazarenko  
http://codeberry.de

