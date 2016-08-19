# LoRa Server setup

This repository provides a basic [Ansible](https://www.ansible.com) playbook
to setup a working [LoRa Server](https://github.com/brocaar/loraserver)
environment.

It will:

* Setup firewall rules and only whitelist the configured IP addresses in
  iptables (22 for SSH and 80 for HTTP which needs to be accessible by
  [Let's Encrypt](https://letsencrypt.org) will be open for all IPs)
* Setup Mosquitto
* Setup Redis
* Setup PostgreSQL (including the creation of a loraserver database)
* Setup [LoRa Gateway Bridge](https://github.com/brocaar/lora-gateway-bridge)
* Setup [LoRa Server](https://github.com/brocaar/loraserver)
* Request a HTTPS certificate from [Let's Encrypt](https://letsencrypt.org)

## Requirements

This playbook has been tested on a [DigitalOcean.com](https://m.do.co/c/6cd86e9f1cb8)
Ubuntu 16.04.x image. Don't have a DigitalOcean account yet? Use
[this](https://m.do.co/c/6cd86e9f1cb8) link and get $10 in credits for free :-)

On the machine from where you will execute this Ansible playbook, make sure
you have a recent Ansible version installed. You can install Ansible with
pip (`pip install ansible`) or using Homebrew (OS X) (`brew install ansible`).

## Configuration

1. Create a new Ubuntu 16.04.x image
2. Configure a DNS record for your image and wait until this record resolves
3. Copy the `inventory.example` file to `inventory` and replace `example.com`
   with the hostname created in step 2
4. Copy the `group_vars/all.yml.example` to `group_vars/all.yml` and change
   the settings where needed.

See also the following links for more documentation:

* https://docs.loraserver.io/lora-gateway-bridge/
* https://docs.loraserver.io/loraserver/

## Provisioning

Run the following command to deploy your LoRa Server instance:

```bash
ansible-playbook -i inventory full_deploy.yml
```
