# Ansible Role: Prometheus

[![ci](https://github.com/miarec/ansible-role-prometheus/actions/workflows/ci.yml/badge.svg)](https://github.com/miarec/ansible-role-prometheus/actions?query=workflow%3Aci)

Ansible role to install Prometheus

Prometheus is a time series database that aggregates reported data from node_exporter clients

## Role Variables

### Prometheus Mode
`prometheus_mode` defines the behavior of the prometheus instance


- `agent` Prometheus server that will remote write metrics to a `server` Prometheus
- `server` Prometheus Server expecting to recieve metrics from an `agent` prometheus
- `standalone` Prometheus server that will directly scrape and store metrics locally


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


## Test using Molecule

Create Python virtual environment, which will be used to install molecule package:

    python3 -m venv molecule-venv

Activate Python virtula environment:

    python3 -m venv molecule-venv

Install molecule package:

    pip install "molecule[docker,lint]"


Specify a distro, you would like to test the playbook against:

    export MOLECULE_DISTRO=centos7
    export MOLECULE_DISTRO=centos8
    export MOLECULE_DISTRO=ubuntu1804
    export MOLECULE_DISTRO=ubuntu2004

Run tests:

    molecule test



The above command will run multiple steps at once (destroy, create, converge, destroy)

If you would like to run each step manually, run:

    molecule create
    molecule converge
    molecule verify
    molecule destroy

Use `login` command to enter the instance for troubleshooting:

    molecule login


## Test against an instance with SSH access

You need to have an instance that is accessible via SSH, where the role will be installed.
For example, you can create one with Terraform (not covered here).

Create an inventory file (test-inventory.ini) with the following content:

    [all]
    my-server ansible_ssh_host=1.2.3.4 ansible_user=ubuntu

    [all:vars]
    prometheus_version = 2.28.1

    [prometheus]
    my-server

Create a playbook file (test-provision.yml) with the following content:

    ---
    - hosts: prometheus
      roles:
        - ansible-role-prometheus

Run playbook:

    export ANSIBLE_ROLES_PATH=../

    ansible-playbook -i test-inventory.ini test-provision.yml
