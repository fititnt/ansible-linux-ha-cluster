# ansible-linux-ha-cluster
**Ansible deployment of Linux HA (High Availability) cluster, PoC of AP-ALB.
One non-Kubernetes / non-Pacemaker / non-DRBD / non-cloud-Lock-In way to do it
even on cheapest VPSs you could find.** This is a Proof of Concept of
[Águia Pescadora Application Load Balancer ("AP-ALB")](https://github.com/fititnt/ap-application-load-balancer)
in a clusterized mode.

**ASCIInema demo**

<!--
Demo:

    asciinema rec ap-alb-demo-004 --idle-time-limit 5 --title "ap-alb-demo (AP-ALB v0.6.4-beta)"

    cat hosts && sleep 4 && cat main.yml && sleep 4 && cat apps-server.yml && sleep 4 && cat db-server.yml && sleep 4 && cat group_vars/all.yml && sleep 6 && cat group_vars/apps_servers.yml && sleep 6 && cat group_vars/apps_servers.yml

    ansible-playbook -i hosts main.yml


Em caso de falha:
    ansible-playbook -i hosts main.yml --start-at-task="ALB/UFW clusterfuck-pre-check.yml"
    ansible-playbook -i hosts main.yml --start-at-task="Configure the kernel to keep connections alive when enabling the firewall"

Debug

    ansible-playbook ad-hoc/info/show-ufw-status.yml -i hosts.yml

-->

The **ansible-linux-ha-cluster** does not have one ASCIInema demonstration... yet.
The following is from [ap-alb-demo](https://github.com/fititnt/ap-alb-demo).

[![asciicast](https://asciinema.org/a/281411.svg)](https://asciinema.org/a/281411)

---

<!-- TOC depthFrom:2 -->

- [Usage](#usage)
    - [How to download this ansible-linux-ha-cluster to your machine](#how-to-download-this-ansible-linux-ha-cluster-to-your-machine)
    - [How to customize and use](#how-to-customize-and-use)
    - [Ad Hoc ALB](#ad-hoc-alb)
- [ansible-linux-ha-cluster explained](#ansible-linux-ha-cluster-explained)
    - [File organization](#file-organization)
    - [Responsibilities of the Ansible Roles](#responsibilities-of-the-ansible-roles)
- [Requisites](#requisites)
    - [Ansible](#ansible)
    - [Hardware of the cluster nodes](#hardware-of-the-cluster-nodes)
    - [Operatinal System of the cluster nodes](#operatinal-system-of-the-cluster-nodes)
- [License](#license)

<!-- /TOC -->

---

## Usage

Assuming you already have the [Requisites](#requisites) meet, this is how you
use:

### How to download this ansible-linux-ha-cluster to your machine

```bash
git clone https://github.com/fititnt/ansible-linux-ha-cluster.git .

# This will download https://github.com/fititnt/https://github.com/fititnt/ap-application-load-balancer
# on roles/ap-application-load-balancer
ansible-galaxy install -r requirements.yml --roles-path roles/
```

> TIP: on [requirements.yml](requirements.yml) have extra information.

### How to customize and use

```bash
# Edit to your hosts files. The default values where used on Etica.AI test servers
# You can also create a new one and change `-i hosts`
vim hosts.yml

# This will run the playbooks
ansible-playbook -i hosts.yml main-infra.yml
ansible-playbook -i hosts.yml main-apps.yml
```


### Ad Hoc ALB

```bash

ansible-playbook -i hosts.yml roles/ap-application-load-balancer/ad-hoc-alb/show-configurations-syntax-validation.yml

# Tip: you may need replace 'roles/ap-application-load-balancer/' with something like '~/.ansible/roles/ap-application-load-balancer/'
#      these ad-hoc-alb scripts are stored with the role
```

## ansible-linux-ha-cluster explained

### File organization

- [hosts.yml](hosts.yml): Almost all the logic (in Ansible term, _the inventory_)
  is on this single filethe
- [main-infra.yml](main-infra.yml) call the tasks related to infrastructure
  deployment in the ideal order.
  - [infra-wireguard.yml](infra-wireguard.yml),
    [infra-consul.yml](infra-consul.yml) and [infra-alb.yml](infra-alb.yml)
    can be played individualy (or even exchanged for other solutions)
- [main-apps.yml](main-apps.yml) exist because AP-ALB is designed to be an
  Application Load Balancer, so after deployment, is more likely that you would
  change the applications than the infrastructure.

### Responsibilities of the Ansible Roles

- **Features provided by [AP Application Load Balancer Role](https://github.com/fititnt/ap-application-load-balancer)**
  - **HAproxy 2.0.x**
  - **OpenResty/NGinx with automatic HTTPS**
    - Yes, it works out-of-the-box not only with Let's Encrypt, but also with
      load balanced to obtain, renew and share keys with the frontend clusters
- **For cluster communication**
  - Uses Consul:
    - _[Ansible role for Consul clusters by Brian Shumate](https://github.com/brianshumate/ansible-consul)_
  - Maybe Etcd will be implemented eventually
- **For provide virtual private networking**
  - Uses Wireguard
    - _[Ansible role for installing WireGuard VPN. Supports Ubuntu, Debian, Archlinx and CentOS. by Robert Wimmer](https://github.com/githubixx/ansible-role-wireguard)_
  - Note: you can disable wireguard if you already have private networkg, or
    simple use another VPN solution

## Requisites

### Ansible

Follow the [https://docs.ansible.com: Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/index.html)

Windows users can [use Windows WSL (then could choose Ubuntu 18.04 LTS)](https://docs.microsoft.com/windows/wsl/install-win10).
Check this [Windows Frequently Asked Questions](https://docs.ansible.com/ansible/latest/user_guide/windows_faq.html)

**Tip: if is your first time with Ansible, this computer is likely to be own
computer and NOT the server where you want to install ALB**. One way to explain
Ansible would be _it converts YAML variables + tasks on commands to execute
(more often) on remote hosts that can be accessed over SSH_.

### Hardware of the cluster nodes
This is the **bare minimum** on each node considering alreading considering
the space of operational system:

- **1 vCPU**
  - Consul is already using concervative heath checks to reduce CPU usage, so
    it could run fine on Amazon T Nano instances
- **192 MB RAM**
  - But then please add some SWAP. This is not kubernetes that complain about
    Swap.
- **8 GB disk space**
  - Maybe 5 GB if you try harder

### Operatinal System of the cluster nodes
The ansible-linux-ha-cluster is tested mainly on Debian based distros (Ubuntu
18.04 LTS). Some underline Roles already support other OSs. AP-ALB maybe will
have support for RHEL/CentOS 8.

## License
[![Public Domain](https://i.creativecommons.org/p/zero/1.0/88x31.png)](UNLICENSE)

To the extent possible under law, [Etica.AI](https://etica.ai/) has waived all
copyright and related or neighboring rights to this work to
[Public Domain](UNLICENSE).
