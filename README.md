# Ansible

Pods in my network are grouped by cluster and 0-indexed. Each node is a Lenovo
ThinkCentre M900 running a minimal AlmaLinux 9 OS with the following specs:

- Quad-core Intel i5-6500T (4C/4T)
- 32GB DDR4-2400 RAM
- 250GB WD Blue SN570

Cluster 0 currently contains the following nodes:

- `c0n0` = cluster 0, node 0
- `c0n1` = cluster 0, node 1
- `c0n2` = cluster 0, node 2
- `c0n3` = cluster 0, node 3

where `c0n*` are hostnames defined in `/etc/hosts`. There are plans to stand up
more clusters in the future for redundancy.

## Node Setup

After installing AlmaLinux 9, there are a few things which need to be done.

### Allow SSH Traffic Through Firewall

    # firewall-cmd --permanent --add-port=22/tcp
    # firewall-cmd --reload

### Create `authorized_keys` File and Populate

    $ mkdir ~/.ssh && chmod 700 ~/.ssh
    $ touch ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys

Add your ansible user's public key inside `authorized_keys`.

### Secure SSH

We want to make sure we secure our nodes by disabling SSH password authentication.

    # vim /etc/ssh/ssh_config

Uncomment and/or set the following properties

    # systemctl restart sshd

## Create Secrets Vault

    $ ansible-vault create secrets.yml

Add the following secrets:

- `redis_pass`

## All Pods

### Update

    $ ansible-playbook playbooks/update.yml --ask-become-pass

### Install Necessary Packages

    $ ansible-playbook playbooks/install.yml --ask-become-pass
