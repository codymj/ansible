# Ansible

Pods in my network are grouped by cluster and 0-indexed. Each pod is a Lenovo ThinkCentre M900 running a minimal Debian OS with the following specs:

- Quad-core Intel i5-6500T
- 32 GB DDR4-2400 RAM
- 250 GB WD Blue SN570

Cluster 0 contains the following pods:

- `c0p0` = cluster 0, pod 0
- `c0p1` = cluster 0, pod 1
- `c0p2` = cluster 0, pod 2
- `c0p3` = cluster 0, pod 3

where `c0p0`, `c0p1`, `c0p2` and `c0p3` are hostnames defined in `/etc/hosts`. There are plans to stand up more clusters in the future for redundancy.

## All Pods

### Update

    ansible-playbook playbooks/update.yml --ask-become-pass

### Install Docker

    ansible-playbook playbooks/docker.yml --ask-become-pass

## `c0p0` - Control Plane, CI/CD

- `[todo]` Traefik
- `[todo]` Kubernetes
- `[todo]` Jenkins

## `c0p1` - Business Services

- `[todo]` App
- `[todo]` App
- `[todo]` App

## `c0p2` - Persitence, Messaging

- `[todo]` Kafka
- `[todo]` Redis
- MariaDB

### MariaDB

#### Installation

    ansible-playbook playbooks/c0p2/mariadb.yml --ask-become-pass

#### Connect to MariaDB container

    mariadb -h c0p2 -P3306 -u {MARIADB_USER} -p{MARIADB_PASSWORD}

## `c0p3` - Metrics, Alerts

- `[todo]` Prometheus
- `[todo]` Grafana
- `[todo]` Jaeger
