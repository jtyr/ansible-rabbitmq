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

```yaml
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
        # Use SSL
        - ssl_listeners:
            - 5671
        - ssl_options:
            - cacertfile: /path/to/testca/cacert.pem
            - certfile: /path/to/server/cert.pem
            - keyfile: /path/to/server/key.pem
            - verify: :verify_peer
            - fail_if_no_peer_cert: true
        # Build cluster of 3 nodes
        - cluster_nodes:
            ::
              -
                - :'rabbit@node1'
                - :'rabbit@node2'
                - :'rabbit@node3'
              - :disc
      - rabbitmq_management:
          - listener:
              - port: 15672

    # If you building cluster this must be set (20 random uppercase characters)
    rabbitmq_erlang_cookie: BUXKEWKUZSWIAVRRWQXL

    # Add RabbitMQ plugins
    rabbitmq_plugins:
      - names:
          # Management web UI
          - rabbitmq_management

    # Add RabbitMQ users
    rabbitmq_users:
      # Add admin user
      - user: admin
        password: "MyT0pS3cr3tPa55w0rd"
        vhost: /
        configure_priv: .*
        read_priv: .*
        write_priv: .*
        tags:
          - administrator
      # Delete the guest user
      - user: guest
        state: absent
  roles:
    - rabbitmq
```


Role variables
--------------

List of variables used by the role:

```yaml
# APT repo string
rabbitmq_apt_repo_string: deb https://dl.bintray.com/rabbitmq/debian {{ ansible_distribution_release }} main

# APT repo key
rabbitmq_apt_repo_key: https://www.rabbitmq.com/rabbitmq-release-signing-key.asc

# YUM repo URL
rabbitmq_yum_repo_url: https://dl.bintray.com/rabbitmq/rpm/rabbitmq-server/v3.7.x/el/{{ ansible_distribution_major_version }}/

# YUM repo URL
rabbitmq_yum_repo_url_erlang: https://dl.bintray.com/rabbitmq/rpm/erlang/21/el/{{ ansible_distribution_major_version }}/

# YUM repo key
rabbitmq_yum_repo_key: https://www.rabbitmq.com/rabbitmq-release-signing-key.asc

# Additional YUM repo params
rabbitmq_yum_repo_params: {}

# Package to install (you can force certain version here)
rabbitmq_pkg: rabbitmq-server

# Additional packages to install (required by some modules - e.g. rabbitmq_exchange)
rabbitmq_pkg_add:
  - python-requests

# Service name
rabbitmq_service: rabbitmq-server


# Location of the erlang cookie file
rabbitmq_erlang_cookie_file: /var/lib/rabbitmq/.erlang.cookie

# Content of the Erlang cookie
rabbitmq_erlang_cookie: null


# Path to the config file
rabbitmq_config_file: /etc/rabbitmq/rabbitmq.config

# Default RabbitMQ config
rabbitmq_config:
  - rabbit:
    - tcp_listeners:
      - '"127.0.0.1"': 5671

# Whether to run the post_config actions only on one server
rabbitmq_run_once: "{{ rabbitmq_erlang_cookie != None }}"

# Whether to show logs on those post_config tasks which can contain password
rabbitmq_force_show_log: no

# Default RabbitMQ bindings
rabbitmq_bindings: []
# Example (http://docs.ansible.com/rabbitmq_binding_module.html):
# rabbitmq_bindings:
#   - component: federation
#     name: local-username
#     value: '"guest"'
#     node: rabbit
#     vhost: /
#     state: present

# Default RabbitMQ exchanges
rabbitmq_exchanges: []
# Example (http://docs.ansible.com/rabbitmq_exchange_module.html):
# rabbitmq_exchanges:
#   - component: federation
#     name: local-username
#     value: '"guest"'
#     node: rabbit
#     vhost: /
#     state: present

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

# Default RabbitMQ plugins
rabbitmq_plugins: []
# Example (see http://docs.ansible.com/rabbitmq_plugin_module.html):
# rabbitmq_plugins:
#   - names:
#     - rabbitmq_management
#     new_only: no
#     prefix: xyz
#     state: enabled

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

# Default RabbitMQ queues
rabbitmq_queues: []
# Example (http://docs.ansible.com/rabbitmq_queue_module.html):
# rabbitmq_queues:
#   - component: federation
#     name: local-username
#     value: '"guest"'
#     node: rabbit
#     vhost: /
#     state: present

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

# Default RabbitMQ vhosts
rabbitmq_vhosts: []
# Example (see http://docs.ansible.com/rabbitmq_vhost_module.html):
# rabbitmq_vhosts:
#   - name: /myvhost
#     node: rabbit
#     state: present
#     tracing: no
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
