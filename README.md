# LoRa Server setup

This repository provides an [Ansible](https://www.ansible.com) playbook to
setup the [LoRa Server](https://github.com/brocaar/loraserver)
project (including depdendcies). With the included
[Vagrant](https://www.vagrant.com) file, the LoRa Server can also be setup
locally (e.g. on [VirtualBox](https://www.virtualbox.org)).

It will:

* Setup firewall rules, allowing only access on the specified public ports
  and listed IP adresses
* Setup Mosquitto
* Setup Redis
* Setup PostgreSQL (including the creation of a loraserver database)
* Setup [LoRa Gateway Bridge](https://github.com/brocaar/lora-gateway-bridge)
* Setup [LoRa Server](https://github.com/brocaar/loraserver)
* Setup [LoRa App Server](https://github.com/brocaar/lora-app-server)
* Request a HTTPS certificate from [Let's Encrypt](https://letsencrypt.org)

## Requirements

* Debian
    * Jessie (8.x)

* Ubuntu
    * Trusty (14.04.x LTS)
    * Xenial (16.04.x LTS)

### Remote deployment

This playbook has been tested on 
[DigitalOcean.com](https://m.do.co/c/6cd86e9f1cb8) with the following images
(but should also work on bare-metal, AWS, ...):

* Debian Jessie 8.7
* Ubuntu 14.04.x
* Ubuntu 16.04.x

Don't have a DigitalOcean account yet? Use
[this](https://m.do.co/c/6cd86e9f1cb8) link and get $10 in credits for free :-)

On the machine from where you will execute this Ansible playbook, make sure
you have a recent Ansible version installed. You can install Ansible with
pip (`pip install ansible`) or using Homebrew (OS X) (`brew install ansible`).

### Vagrant (local deployment)

When setting up the LoRa Server environment, make sure you have a recent
version of [Vagrant](https://www.vagrant.com) installed. There is no need to
install Ansible as it will be installed within the Vagrant environment.

## Vagrant

1. Confirm that you have a working Vagrant setup.

2. Within the root of this repository execute the following command:
    
    ```bash
    vagrant up
    ```

    As this will import the Vagrant box, install all requirements etc... this
    is going to take a while.

3. Configure your LoRa Gateway so that it points to the IP address of your
   machine (port `1700`).

4. Point your browser to https://localhost:8080/. As a self-signed certificate
   is used, your browser will prompt that the certificate can't be trusted.
   This is ok for testing.

## Remote deployment (DigitalOcean)

### Configuration

1. Create a new Ubuntu 16.04.x image and make sure that you can ssh to this
   machine using public-key authentication.

2. Make sure Python is installed (`sudo apt-get install python`)

3. Configure a DNS record for your image and wait until this record resolves
   to your IP address. This is required in case you configured LetsEcrypt.

4. Copy the `inventory.example` file to `inventory` and replace `example.com`
   with the hostname created in step 2.

5. Copy the `group_vars/loraserver_hosts.example.yml` to
   `group_vars/loraserver_hosts.yml` and change the settings where needed.

See also the following links for more documentation:

* https://docs.loraserver.io/lora-gateway-bridge/
* https://docs.loraserver.io/loraserver/
* https://docs.loraserver.io/lora-app-server/

### Provisioning

Run the following command to deploy your LoRa Server instance or to upgrade
to the latest versions:

```bash
ansible-playbook -i inventory full_deploy.yml
```

After the playbook has been completed, the dashboard should be accessible from
https://yourdomain.com/ (please note the http*s*).
