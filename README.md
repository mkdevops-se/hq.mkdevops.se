
`hq.mkdevops.se`
================

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

    OS: AlmaLinux 8.9 minimal (install 2024-03-21)
    CPUs: 4 (i5-7200 2.50 GHz, fanless)
    Memory: 32 GB (DDR4 SDRAM, 2133 MHz)
    Disk: 2 TB SSD (Kingston NV1 M.2)
    GPU: Intel HD Graphics 620
    Network:
      - enp3s0, Gigabit Ethernet (default)
      - enp0s31f6, Gigabit Ethernet
      - wlp2s0, 802.11ac


*LVM Partitioning:*

| Volume                   | Pool  |    Size |   FS   | Mount Point |
|:-------------------------|:-----:|--------:|:------:|:------------|
| `/dev/almalinux_hq/root` | `sys` |  `100G` | `xfs`  | `/`         |
| `tmpfs`                  | `sys` |   `16G` | `xfs`  | `/dev/shm/` |
| `/dev/almalinux_hq/home` | `sys` |  `500G` | `xfs`  | `/home`     |
| `/dev/nvme0n1p1`         |       |  `599M` | `vfat` | `/boot/efi` |
| `/dev/nvme0n1p2`         |       | `1024M` | `xfs`  | `/boot`     |


Hostname-Port Allocations (DEPRECATED, REMOVE!)
-----------------------------------------------

| Hostname                   |   Port | Comment                                        |
|:---------------------------|-------:|:-----------------------------------------------|
| `hq.mkdevops.se`           | `8070` | Reserved                                       |
| `test.mkdevops.se`         | `8071` | Reserved (misc testing)                        |
| `id.mkdevops.se`           | `8072` | Reserved (for OAuth2 provider project)         |
| `www.mkdevops.se`          | `8073` | www.mkdevops.se WordPress site                 |
| `staging-www.mkdevops.se`  | `8074` | www.mkdevops.se WordPress staging site         |
| `bamboo.mkdevops.se`       | `8085` | Test server for `mkdevops-se/bamboo-ci-health` |
| `konfigurator.mkdevops.se` | `3000` | See `mkdevops-se/konfigurator` project         |
| `kibana.mkdevops.se`       | `5601` | Kibana setup for Titan-Elastic                 |


Getting Started
---------------

Clone the repo:

    git clone git@github.com:mkdevops-se/hq.mkdevops.se.git
    cd hq.mkdevops.se/

Create a virtual environment and install the dependencies:

    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    ansible-galaxy install -r requirements.yml

Add the Ansible Vault password to a file named `.ansible_vault_password` and restrict readability:

    echo theSecretAnsibleVaultPassword > .ansible_vault_password
    chmod og-r .ansible_vault_password

Run through the bootstrap playbook to configure the server:

    ansible-playbook bootstrap.yml --ask-become-pass --diff

Optionally, install and configure `rclone` (**WARNING: The Ansible Galaxy role used here spills
sensitive data in logs, do not include in GitHub Actions run!**):

    ansible-playbook rclone-setup.yml -e @rclone-secrets.yml --ask-become-pass --diff

