rabbitmq
========

Ansible Role which installs RabbitMQ from RPM package and configures it with the
YAML format of the config file.

The configuration of the role is done in such way that it should not be necessary
to change the role for any kind of configuration. All can be done either by
changing role parameters or by declaring completely new configuration as a
variable. That makes this role absolutely universal. See the examples below for
more details.

Please report any issues or send PR.


Examples
--------

```
---

# Example of default installation
- hosts: myhost1
  roles:
    - rabbitmq

# Example of how to create a custom configuration
- hosts: myhost1
  vars:
    # Here goes the custom configuration
    rabbitmq_config:
      - rabbit:
        - tcp_listeners:
          - '"0.0.0.0"': 5672
        - ssl_listeners:
          - 5671
        - ssl_options:
          - cacertfile: /path/to/testca/cacert.pem
          - certfile: /path/to/server/cert.pem
          - keyfile: /path/to/server/key.pem
          - verify: verify_peer
          - fail_if_no_peer_cert: true
  roles:
    - rabbitmq
```


Role variables
--------------

List of variables used by the role:

```
# Package to install (you can force certain version here)
rabbitmq_pkg: rabbitmq-server

# Default RabbitMQ config
rabbitmq_config:
  - rabbit:
    - tcp_listeners:
      - '"127.0.0.1"': 5671

# Default RabbitMQ vhosts
rabbitmq_vhosts: []
# Example (see http://docs.ansible.com/rabbitmq_vhost_module.html):
# rabbitmq_vhosts:
#   - name: /myvhost
#     node: rabbit
#     state: present
#     tracing: no

# Default RabbitMQ users
rabbitmq_users: []
# Example (see http://docs.ansible.com/rabbitmq_user_module.html):
# - user: foo
#   password: bar
#   vhost: /myvhost
#   tags:
#     - one
#     - two
#   configure_priv: ^$
#   read_priv: ^$
#   write_priv: ^$
#   node: rabbit
#   state: present
#   force: yes

# Default RabbitMQ policies
rabbitmq_policies: []
# Example (see http://docs.ansible.com/rabbitmq_policy_module.html):
# rabbitmq_policies:
#   - name: HA
#     node: rabbit
#     pattern: .*
#     priority: 1
#     state: present
#     tags:
#       ha-mode: all
#     vhost: /myvhost

# Default RabbitMQ plugins
rabbitmq_plugins: []
# Example (see http://docs.ansible.com/rabbitmq_plugin_module.html):
# rabbitmq_plugins:
#   - names:
#     - rabbitmq_management
#     new_only: no
#     prefix: xyz
#     state: enabled

# Default RabbitMQ parameters
rabbitmq_parameters: []
# Example (http://docs.ansible.com/rabbitmq_parameter_module.html):
# rabbitmq_parameters:
#   - component: federation
#     name: local-username
#     value: '"guest"'
#     node: rabbit
#     vhost: /
#     state: present
```


Dependencies
------------

- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)


License
-------

MIT


Authors
-------

Jiri Tyr
