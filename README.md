# Ansible

Nodes in my network are grouped by cluster and 0-indexed. Each node is a Lenovo
ThinkCentre M900 running a minimal AlmaLinux 9 OS with the following specs:

- Quad-core Intel i5-6500T (4C/4T)
- 32GB DDR4-2400 RAM
- 250GB WD Blue SN570

`c0` (cluster 0) currently contains the following nodes:

- `c0n0` = cluster 0, node 0
- `c0n1` = cluster 0, node 1
- `c0n2` = cluster 0, node 2
- `c0n3` = cluster 0, node 3

where `c0n*` are hostnames defined in `/etc/hosts`.

I also have a remote VPS node: `cpx11n0`, which acts as the gateway node to cluster 0. It has relatively modest specs:

- 2 vCPU
- 2GB RAM
- 40GB SSD

## Prerequisites

Generate an SSH key for Ansible:

    ssh-keygen -t ed25519 -f ansible

Initialize `inventory` file with your host names:

    vim inventory

Add:

    c0n0
    c0n1
    c0n2
    c0n3
    cpx11n0

Initialize `ansible.cfg` file:

    vim ansible.cfg

Add:

    [defaults]
    inventory = inventory
    private_key_file = ~/.ssh/ansible

## Node Setup

After installing AlmaLinux 9, there are a few things which need to be done.

### Allow SSH Traffic Through Firewall

    sudo firewall-cmd --permanent --add-port=22/tcp
    sudo firewall-cmd --reload

### Create `authorized_keys` File and Populate

    mkdir ~/.ssh && chmod 700 ~/.ssh
    touch ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys

Add your ansible user's public key inside `authorized_keys`.

### Secure SSH

We want to make sure we secure our nodes by disabling SSH password authentication.

    sudo vim /etc/ssh/ssh_config

Uncomment and/or set `PasswordAuthentication no`

    sudo systemctl restart sshd

### Add Other Nodes to `/etc/hosts`

On each node, we need to add the static IPs of other nodes in the cluster.

    sudo vim /etc/hosts

Add nodes:

    192.168.50.10   c0n0
    192.168.50.11   c0n1
    192.168.50.12   c0n2
    192.168.50.13   c0n3

## Create Secrets Vault

    ansible-vault create group_vars/all/vault.yml

Add the following secrets:

- `k3s_token`

## All Nodes

Applicable to all nodes.

### Install Base Packages

    ansible-playbook playbooks/install.yml --ask-become-pass

### Install Fail2Ban

    ansible-playbook playbooks/fail2ban.yml --ask-become-pass

### Install Docker

    ansible-playbook playbooks/docker.yml --ask-become-pass

### Install k3s

I opted to disable Traefik and ServiceLB as I have HAProxy on `cpx11n0` loadbalancing requests directly to my web application instances in the cluster.

    ansible-playbook playbooks/k3s.yml --ask-become-pass --ask-vault-pass

Check nodes:

    $ kubectl get nodes
    NAME   STATUS   ROLES                       AGE   VERSION
    c0n0   Ready    control-plane,etcd,master   94m   v1.32.5+k3s1
    c0n1   Ready    control-plane,etcd,master   77m   v1.32.5+k3s1
    c0n2   Ready    control-plane,etcd,master   70m   v1.32.5+k3s1
    c0n3   Ready    <none>                      63m   v1.32.5+k3s1

### Update a Node

    ansible-playbook playbooks/<node>/update.yml --ask-become-pass

### Reboot a Node

    ansible-playbook playbooks/<node>/reboot.yml --ask-become-pass
