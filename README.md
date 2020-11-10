philranzato.elk-utils
=========

This role serves as utility functions for the elk stack configuration

Requirements
------------

- A CA certificate and key must be on the remote host when setting the respective parameter.
- Elasticsearch certificates must be on the remote host when setting the respective parameter.
- Logstash certificates must be on the remote host when setting the respective parameter.

Role Variables
--------------


```yaml
---
# selector for the utils to run
# utils: share-certificates | logstash-key-conversion
utils: "share-certificates"

# Utils: share-certificates

# enables fetching of specified certificates
fetch:
  enabled: false
  ca: false
  elasticsearch: false
  logstash: false
# enables copying of specified certificates
copy: 
  enabled: false
  ca: false
  elasticsearch: false
  logstash: false

# CA locations and destination on remote host
ca:
  dir: /opt/private/ssl
  cert:
    file: "elastic-CA.crt"
  key:
    file: "elastic-CA.key"
  # used only when copying
  dest: /path/to/dest

# Elasticsearch .zip certificates location and destination on remote host 
elasticsearch:
  src:
    dir: "{{ ca.dir }}"
    file: "elasticsearch-certificates.zip"
  dest_dir: /etc/elasticsearch

# Logstash .zip certificates location and destination on remote host 
logstash:
  src:
    dir: "{{ ca.dir }}"
    file: "logstash-certificates.zip"
  dest_dir: /etc/logstash

# Kibana .zip certificates location and destination on remote host 
kibana:
  src:
    dir: "{{ ca.dir }}"
    file: "kibana-certificates.zip"
  dest_dir: /etc/kibana

# The node name in the elasticsearch cluster
node:
  name: "test-node"

# Use it as group variables for each group (elasticsearch masters, logstash instances and kibana instances)
# component: < elasticsearch | logstash | kibana >
component: elasticsearch

##### utils: logstash-key-conversion
logstash_server_certificates:
  dir: /etc/logstash
  cert:
    file: "logstash-server"
    # extension of logstash_server_certificates.cert.file
    extension: "crt"
  key:
    file: "logstash-server"
    # extension of logstash_server_certificates.key.file
    extension: "key"
    pass: "P4ssword!"
    owner: root
    group: logstash
    mode: "0640"
```

Dependencies
------------

None.

Example Playbook
----------------

```yaml
---
- name: "Fetch certificates"
  hosts: elasticsearch_primary_master
  roles:
  - { role: pllr.elk-utils }
  vars_files:
  - vars/fetch.yml

- name: "Copy certificates and keys to all hosts"
  hosts: all:!localhost
  roles:
  - { role: pllr.elk-utils }
  vars_files:
  - vars/copy.yml
  # Needed if you want to see the correct host in the task name
  serial: 1

- name: "Convert Logstash key to PKCS8"
  hosts: elasticsearch_primary_master
  roles:
  - { role: pllr.elk-utils }
  vars_files:
  - vars/convert.yml
```

License
-------

MIT

Author Information
------------------

Check me on [LinkedIn](www.linkedin.com/in/phil-ranzato-47b8bb194)
