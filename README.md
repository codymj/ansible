# Ansible

Pods in my network are grouped by cluster and 0-indexed. Each pod is a Lenovo ThinkCentre M900 running a minimal Debian OS with the following specs:

- Quad-core Intel i5-6500T
- 32 GB DDR4-2400 RAM
- 250 GB WD Blue SN570

Cluster 0 contains the following nodes:

- `c0p0` = cluster 0, pod 0
- `c0p1` = cluster 0, pod 1
- `c0p2` = cluster 0, pod 2
- `c0p3` = cluster 0, pod 3

where `c0p0`, `c0p1`, `c0p2` and `c0p3` are hostnames defined in `/etc/hosts`. There are plans to stand up more clusters in the future for redundancy.

This setup is not set in stone and is a work in progress.

## Create Secrets Vault

    ansible-vault create secrets.yml

Add the following secrets:

- `mariadb_root_password`
- `mariadb_password`

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

## `c0p2` - Persistence, Messaging

- `[todo]` Kafka
- `[todo]` Redis
- MariaDB

### MariaDB

#### Installation

    ansible-playbook playbooks/c0p2/mariadb.yml --ask-become-pass --ask-vault-pass

Note: You will need to add `mariadb_root_password` and `mariadb_password` to a `secrets.yml` vault.

#### Connect to MariaDB container

    mariadb -u {MARIADB_USER} -p -h c0p2

## `c0p3` - Metrics, Alerts

- `[todo]` Prometheus
- `[todo]` Grafana
- `[todo]` Jaeger
