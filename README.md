# ansible-role-prometheus
Ansible role to install Prometheus

Prometheus is a time series database that aggregates reported data from node_exporter clients

## Role Variables

### Version information:

    prometheus_version: "2.28.1" 

Prometheus binary is manually installed, Choose the version of Prometheus client to be installed, more information can be found at https://prometheus.io/download/  

### Collection Behavior

    prometheus_retention_time: "365d"
    prometheus_scrape_interval: "15s"
    prometheus_node_exporter: true
    prometheus_node_exporter_group: "all"

These settings determine the behavior of the the prometheus instance data collection. 
  - Choose the total retention time that Prometheus will store data
  - Select the ammount of time between data scrapes
  - Choose if the targets are node_exporters
  - Choose the ansible inventory group that prometheus expects to find node_exporter clients

### OS prep

    prometheus_user: prometheus
    prometheus_group: "{{ prometheus_user }}"
    prometheus_bin: /usr/local/bin/prometheus
    promtool_bin: /usr/local/bin/promtool
    prometheus_dir_config: /etc/prometheus
    prometheus_dir_storage: /var/lib/prometheus

These settings prep the user, owner and location of the prometheus install

## Dependencies

None.

## Example Playbook

    ---
    - name: Install Prometheus
      hosts: monitor
      pre_tasks:
        - include_vars: vars/custom.yml
          failed_when: false
      become: yes
      roles:
        - role: 'prometheus'
          tags: 'prometheus'

## Reloading / restarting

Key config changes to the config file /etc/prometheus/prometheus.yml require a full restart of the service

    sudo systemctl restart grafana-server prometheus