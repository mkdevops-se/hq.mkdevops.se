
hq.mkdevops.se
==============

Ansible bootstrapping for bare-metal `hq.mkdevops.se` server.


Purpose
-------

1. Provide secure, reliable and capable jump-server for the nice guys at Midsommakransen DevOps AB
2. Offload long-running background jobs, e.g. cross Atlantic data transfers, batch processing
3. Learn efficient use of VPN clients from Linux command line
4. Permanent hosting of solutions that could be moved away from our Google Cloud Platform projects



Getting Started
---------------

    git clone git@github.com:mkdevops-se/hq.mkdevops.se.git && cd hq.mkdevops.se/
    virtualenv venv --python=python3.6 && . venv/bin/activate
    pip install -r requirements.txt
    ansible-playbook bootstrap.yml --ask-become-pass --diff

