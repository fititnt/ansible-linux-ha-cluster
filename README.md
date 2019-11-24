# ap-alb-cluster-demo
**[working draft] Demo of [Águia Pescadora Application Load Balancer ("AP-ALB")](https://github.com/fititnt/ap-application-load-balancer) in a clusterized mode**

For non-clusterized setup, check <https://github.com/fititnt/ap-alb-demo>.

## ASCIInema demo

<!--
Demo:

    asciinema rec ap-alb-demo-004 --idle-time-limit 5 --title "ap-alb-demo (AP-ALB v0.6.4-beta)"

    cat hosts && sleep 4 && cat main.yml && sleep 4 && cat apps-server.yml && sleep 4 && cat db-server.yml && sleep 4 && cat group_vars/all.yml && sleep 6 && cat group_vars/apps_servers.yml && sleep 6 && cat group_vars/apps_servers.yml

    ansible-playbook -i hosts main.yml


Em caso de falha:
    ansible-playbook -i hosts main.yml --start-at-task="ALB/UFW clusterfuck-pre-check.yml"
    ansible-playbook -i hosts main.yml --start-at-task="Configure the kernel to keep connections alive when enabling the firewall"

-->

The **ap-alb-cluster-demo** does not have one ASCIInema demonstration... yet.
The following is from [ap-alb-demo](https://github.com/fititnt/ap-alb-demo).

[![asciicast](https://asciinema.org/a/281411.svg)](https://asciinema.org/a/281411)

## How to download this ap-alb-demo to your machine

```bash
git clone https://github.com/fititnt/ap-alb-cluster-demo.git .

# This will download https://github.com/fititnt/https://github.com/fititnt/ap-application-load-balancer
# on roles/ap-application-load-balancer
ansible-galaxy install -r requirements.yml --roles-path roles/
```

> TIP: on [requirements.yml](requirements.yml) have extra information.

## How to customize and use

```bash
# Edit to your hosts files. The default values where used on Etica.AI test servers
# You can also create a new one and change `-i hosts`
vim hosts.yml

# This will run the playbooks
ansible-playbook -i hosts.yml main-alb-ha-cluster-infra.yml
ansible-playbook -i hosts.yml main-alb-ha-cluster-apps.yml
```

## License
[![Public Domain](https://i.creativecommons.org/p/zero/1.0/88x31.png)](UNLICENSE)

To the extent possible under law, [Etica.AI](https://etica.ai/) has waived all
copyright and related or neighboring rights to this work to
[Public Domain](UNLICENSE).
