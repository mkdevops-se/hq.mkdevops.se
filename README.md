
hq.mkdevops.se
==============

Ansible bootstrapping for bare-metal `hq.mkdevops.se` server.


Purpose
-------

1. Provide secure, reliable and capable jump-server for the nice guys at Midsommarkransen DevOps AB
2. Offload long-running background jobs, e.g. cross Atlantic data transfers, batch processing
3. Learn efficient use of VPN clients from Linux command line
4. Permanent hosting of solutions that could be moved away from our Google Cloud Platform projects


Server Baseline
---------------

*Hardware:*

    OS: CentOS 7.5 minimal (install 2018-11-24)
    CPUs: 4 (i5-7200 2.50 GHz, fanless)
    Memory: 32 GB (DDR4 SDRAM, 2133 MHz)
    Disk: 256 GB SSD (Samsung)
    GPU: Intel HD Graphics 620
    Network:
      - enp3s0, Gigabit Ethernet
      - enp0s31f6, Gigabit Ethernet (default)
      - wlp2s0, 802.11ac


*LVM Partitioning:*

| Volume                          | Pool  | Size (MB) | FS     | Mount Point             |
| :---                            | :---: | ---:      | :---:  | :---                    |
| `/dev/sys/root`                 | `sys` | `3072`    | `xfs`  | `/`                     |
| `/dev/sys/swap`                 | `sys` | `4096`    |        |                         |
| `/dev/sys/var_lib_docker`       | `sys` | `32768`   | `xfs`  | `/var/lib/docker`       |
| `/dev/sys/dev_shm`              | `sys` | `512`     | `xfs`  |                         |
| `/dev/sys/home`                 | `sys` | `98304`   | `xfs`  | `/home`                 |
| `/dev/sys/tmp`                  | `sys` | `512`     | `xfs`  | `/tmp`                  |
| `/dev/sys/opt`                  | `sys` | `512`     | `xfs`  | `/opt`                  |
| `/dev/sys/var`                  | `sys` | `2048`    | `xfs`  | `/var`                  |
| `/dev/sys/var_log`              | `sys` | `2048`    | `xfs`  | `/var/log`              |
| `/dev/sys/var_tmp`              | `sys` | `256`     | `xfs`  | `/var/tmp`              |
| `/dev/sys/var_log_audit`        | `sys` | `512`     | `xfs`  | `/var/log/audit`        |
| `/dev/nvme0n1p1`                |       | `200`     | `vfat` | `/boot/efi`             |
| `/dev/nvme0n1p2`                |       | `1024`    | `xfs`  | `/boot`                 |


Hostname-Port Allocations
-------------------------

| Hostname                             | Port   | Comment                                |
| :---                                 | ---:   | :---                                   |
| `hq.mkdevops.se`                     | `8070` | Reserved                               |
| `test.mkdevops.se`                   | `8071` | Reserved (misc testing)                |
| `id.mkdevops.se`                     | `8072` | Reserved (for OAuth2 provider project) |
| `www.mkdevops.se`                    | `8073` | mkdevops.se WordPress site             |
| `konfigurator.mkdevops.se`           | `3000` | See `mkdevops-se/konfigurator` project |
| `kibana.mkdevops.se`                 | `5601` | Kibana setup for Titan-Elastic         |
| `www.mjlife.se`                      | `8090` | mjlife.se WordPress site               |
| `staging.stockholmsaltspa.com`       | `8091` | staging.stockholmsaltspa.com WP site   |


Getting Started
---------------

    git clone git@github.com:mkdevops-se/hq.mkdevops.se.git && cd hq.mkdevops.se/
    python3 -m venv venv && . venv/bin/activate
    pip install -r requirements.txt
    ansible-galaxy install -r requirements.yml
    echo theSecretAnsibleVaultPassword > .vault-pass
    chmod og-r .vault-pass
    ansible-playbook bootstrap.yml --ask-become-pass --diff

