grok-exporter
=========

Installs with Ansible Grok exporter for Prometheus from https://github.com/fstab/grok_exporter

Requirments
--------------

`unzip` on the target machine

Role Variables
--------------

- grok_exporter_version - exporter version to be downloaded from the above repo

- grok_exporter_system_group - save & run the executable under this group, defaults to `grok_exporter`
- grok_exporter_system_user - save & run the executable under this group, defaults to the name of the group `grok_exporter_system_group`

- grok_services - For each log that you want to process you must define a service, a config file will be generated and a systemd service will be activated for each one. See defaults/main.yml for a detailed example of a service.

You can then use `grok_services` in your Prometheus server to tell it what to scrape.

Example Playbook
----------------

Add it to your playbook

- hosts: servers
  roles:
    - role: eRadical.grok-exporter

Example of services:

grok_services:
  - name: php-fpm-metrics-game-app
    config_version: 2
    input:
      type: file
      path: /www/app/php-fpm-logs/www-access.log
      readall: true
    additional_patterns:
      - 'FPMDATE %{MONTHDAY}/%{MONTH}/%{YEAR}:%{TIME} %{ISO8601_TIMEZONE}'
    metrics:
      - type: gauge
        name: php_fpm_request_duration_miliseconds
        help: Total number of rejected recipients, partitioned by error message.
        match: '%{FPMDATE:requestdate} ~ %{WORD:verb} ~ %{URIPATH:apirequest} ~ %{NUMBER:statuscode} ~ %{NUMBER:durationmili} ~ %{NUMBER:memorykilo} ~ %{NUMBER:cpuperc}'
        value: "{{ '{{' }}.durationmili{{ '}}' }}"
        cumulative: false
        labels:
          http_verb: "{{ '{{' }}.verb{{ '}}' }}"
          api_request: "{{ '{{' }}.apirequest{{ '}}' }}"
          status_code: "{{ '{{' }}.statuscode{{ '}}' }}"
      ..........................................................................
    server:
      host: "{{ ansible_eth0.ipv4.address }}"
      port: 9144

License
-------

- GPLv3
- insipred by on https://github.com/eRadical/ansible-php-fpm-exporter
