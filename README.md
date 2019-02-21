SuperMicro SSE-X24S Switch
========================

This role configures SuperMicro SSE-X24S switches using the
[expect](http://docs.ansible.com/ansible/latest/modules/expect_module.html)
Ansible module. It derives from [stackhpc/ansible-role-dell-powerconnect-switch](https://github.com/stackhpc/ansible-role-dell-powerconnect-switch) public role.
Refer to that project for more information.

This role will install the python `expect` package to the system site packages
on the local machine.

Requirements
------------

The switches should be configured to allow SSH access.

Role Variables
--------------

`sm_ssex24s_switch_provider` is authentication provider information,
similar to the `provider` argument to the `dellos` modules. It should be a dict
containing the following fields:

- `host`: the host or IP address of the switch.
- `username`: the username with which to access the switch via SSH.
- `auth_pass`: the password with which to authenticate.

`sm_ssex24s_switch_config` is a list of configuration lines to apply to
the switch, and defaults to an empty list.

`sm_ssex24s_switch_interface_config` contains interface configuration.
It is a dict mapping switch interface names to configuration dicts. Each dict
may contain the following items:

- `description` - a description to apply to the interface.
- `name` - a name to apply to the vlan interface, if you're configuring a vlan.
- `config` - a list of per-interface configuration.

Dependencies
------------

None

Example Playbook
----------------

The following playbook configures hosts in the `supermicro-switches`
group.  It assumes host variables for each switch holding the host, username
and passwords.  It applies global configuration for system information and configures log forwarding
ethernet interfaces as switchports.

    ---
    - name: Ensure SuperMicro switches are configured
      hosts: supermicro-switches
      gather_facts: no
      roles:
        - role: ansible-role-supermicro-sse-x24s-switch
          sm_ssex24s_switch_provider:
            host: "{{ switch_host }}"
            username: "{{ switch_user }}"
            password: "{{ switch_password }}"
            transport: cli
            authorize: yes
            auth_pass: "{{ switch_auth_pass }}"
          sm_ssex24s_switch_config:
            - "system contact we@everywhere.nl"
            - "system location OurDatacenter"
            - "logging 192.168.1.254"
            - "logging facility local3"
            - "logging trap debugging"
          sm_ssex24s_switch_interface_config:
            "Ex 0/1":
              description: server-1
              config:
                - "no shutdown"
                - "switchport"
            "Ex 0/2":
              description: server-2
              config:
                - "no shutdown"
                - "switchport"
            "vlan 1234":
              config:
                - "ip address 192.168.1.254 255.255.255.0"

Author Information
------------------

- Nuno Tavares (<nuno.tavares@synrix.com>)
