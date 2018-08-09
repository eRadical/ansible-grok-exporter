grok-exporter
=========

Installs with Ansible Grok exporter for Prometheus from https://github.com/fstab/grok_exporter


Role Variables
--------------

- grok_exporter_version - exporter version to be downloaded from the above repo

- grok_exporter_system_group - save & run the executable under this group, defaults to `grok_exporter`
- grok_exporter_system_user - save & run the executable under this group, defaults to the name of the group `grok_exporter_system_group`

- group_exporter_export_ip - the IP address which will be exposed by the exporter, defaults to `ansible_eth0.ipv4.address`

- grok_services - For each log that you want to process you must define a service, a config file will be generated and a systemd service will be activated for each one

You can then use `grok_services` in your Prometheus server to tell it what to scrape.

Example Playbook
----------------

Add it to your playbook

- hosts: servers
  roles:
    - role: eRadical.grok-exporter

Example of pools:

grok_services:
  - name: www
    export_port: 8080
    export_address:

License
-------

- GPLv3
- based mostly on https://github.com/cloudalchemy/ansible-node-exporter
