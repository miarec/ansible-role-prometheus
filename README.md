# ansible-role-prometheus
Ansible role to install Prometheus

Prometheus is a time series database that aggregates reported data from node_exporter clients

## Role Variables

### Version information:

    prometheus_version: "2.28.1" 

Choose the version of Prometheus client to be installed. The available versions can be found at https://prometheus.io/download/  

### Scape targets configuration

  prometheus_scrape_configs:

    - job_name: prometheus
      static_configs:
        - targets: 
          - localhost:9090

    - job_name: node
      static_configs:
        - targets:
          - localhost:9100
          - 1.2.3.4:9100
          - 5.6.7.8:9100

### Other variables

Check `defaults/main.yml` file for a list of all available variables.

## Dependencies

None.

## Example Playbook

    ---
    - name: Install Prometheus
      hosts: prometheus
      roles:
        - ansible-role-prometheus

# Quick test of role (during development)

Create a test inventory file (test-inventory.ini) with the following content:

    [all]
    my-server ansible_ssh_host=1.2.3.4 ansible_user=ubuntu

    [all:vars]
    prometheus_version = 2.28.1

    [prometheus]
    my-server

Create a test playbook file (test-provision.yml) with the following content:

    ---
    - hosts: prometheus
      roles:
        - ansible-role-prometheus

Run playbook:

    export ANSIBLE_ROLES_PATH=../

    ansible-playbook -i test-inventory.ini test-provision.yml