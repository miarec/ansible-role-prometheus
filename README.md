# Ansible Role: Prometheus

![ci](https://github.com/miarec/ansible-role-prometheus/actions/workflows/ci.yml/badge.svg?event=push)

Ansible role to install Prometheus

Prometheus is a time series database that aggregates reported data from node_exporter clients

Prometheus can be deployed in one of 3 models
- `agent` Prometheus server that will write metrics to a remote `server` Prometheus.
- `server` Prometheus Server expecting to recieve metrics from an `agent` prometheus
- `standalone` (Rare) Prometheus server that will directly scrape and store metrics locally

## Role Variables
- `promehteus_mode` Define mode of this prometheus instance, `agent`, `server`, or `standalaone`
- `prometheus_scrape_configs` Define what prometheus is going to scrape

Example
```yaml
  prometheus_scrape_configs:
    - job_name: prometheus
      static_configs:
        - targets:
          - localhost:9090
    - job_name: node_exporter
      static_configs:
        - targets:
          - localhost:9100
          - 1.2.3.4:9100
          - 5.6.7.8:9100
```

### Variables for `agent` mode
 - `prometheus_mode` = `agent`
 - `prometheus_write_target` Define the destination for remote writes
 - `client_auth_user` (Optional) username used for basic authentication with Prometheus
 - `client_auth_password` (Optional) password used for basic authentication with Prometheus


Example
```yaml
  prometheus_write_target: "https://prometheus.example.com"
```
 - `external_labels` Define labels that applied to all metrics that are written to a remote prometheus, thes should be unique per cluster of monitored servers

Example
```yaml
  external_labels:
    - prometheus_agent: "prometheus-agent.region1.example.com"
    - environtment: "YOUR_ENVIRONMENT"
```

### Other variables

Check `defaults/main.yml` file for a list of all available variables.

## Dependencies

None.

## Example Playbooks

### `server` mode
```yaml
    ---
    - name: Install Prometheus Server
      hosts: prometheus
      vars:
        prometheus_mode: "server"
        prometheus_scrape_configs:
          - job_name: prometheus
            static_configs:
              - targets:
                - localhost:9090
      roles:
        - ansible-role-prometheus
```

### `agent` mode
```yaml
    ---
    - name: Install Prometheus Agent
      hosts: prometheus
      vars:
        prometheus_mode: "agent"
        prometheus_write_target: "https://prometheus.example.com"
        external_labels:
          - prometheus_agent: "prometheus-agent.region1.example.com"
          - environtment: "YOUR_ENVIRONMENT"
        prometheus_scrape_configs:
          - job_name: prometheus
            static_configs:
              - targets:
                - localhost:9090
          - job_name: node_exporter
            static_configs:
              - targets:
                - localhost:9100
                - 10.0.0.10:9100
                - 10.0.0.20:9100
      roles:
        - ansible-role-prometheus
```

### `standalone` mode
```yaml
    ---
    - name: Install Prometheus Agent
      hosts: prometheus
      vars:
        prometheus_mode: "standalone"
        prometheus_scrape_configs:
          - job_name: prometheus
            static_configs:
              - targets:
                - localhost:9090
          - job_name: node_exporter
            static_configs:
              - targets:
                - localhost:9100
                - 10.0.0.10:9100
                - 10.0.0.20:9100
      roles:
        - ansible-role-prometheus
```


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
