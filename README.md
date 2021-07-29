# ansible-role-prometheus
Ansible role for installation of Prometheus

This role installs Prometheus

## Role Variables

    prometheus_bind_port: 9090
    hello_world	

## Dependencies

None.

## Example Playbook

    ---
    - hosts:  prometheus
      become: yes
      vars:
        prometheus_bind_port: 8080
    
      roles:
        - ansible-role-prometheus

