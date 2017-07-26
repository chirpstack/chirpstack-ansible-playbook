# LoRa Server setup

This repository provides an [Ansible](https://www.ansible.com) playbook to
setup the [LoRa Server](https://github.com/brocaar/loraserver)
project (including dependencies). With the included
[Vagrant](https://www.vagrant.com) file, the LoRa Server can also be setup
locally (e.g. on [VirtualBox](https://www.virtualbox.org)).

It will:

* Setup firewall rules, allowing only access on the specified public ports
  and listed IP adresses
* Setup Mosquitto, including [mosquitto-auth-plug](https://github.com/jpmens/mosquitto-auth-plug)
  so that LoRa App Server user credentials can be used for MQTT authentication
* Setup Redis
* Setup PostgreSQL (including the creation of the databases)
* Setup [LoRa Gateway Bridge](https://github.com/brocaar/lora-gateway-bridge)
* Setup [LoRa Server](https://github.com/brocaar/loraserver)
* Setup [LoRa App Server](https://github.com/brocaar/lora-app-server)
* Request a HTTPS certificate from [Let's Encrypt](https://letsencrypt.org)

## Vagrant (local environment using VirtualBox)

The included `Vagrantfile` will setup an Ubuntu Xenial (16.04) virtual
machine with the latest LoRa Server components installed. It will also forward
the following ports to your host system:

* `8080`: LoRa App Server UI and API
* `1700`: UDP listener for the packet-forwarder data
* `1883`: Mosquitto MQTT
* `1884`: Mosquitto Websockets

Note: when using Vagrant, there is no need to install Ansible (this will be
automatically installed inside the Vagrant machine).

### Requirements

When setting up the LoRa Server environment, make sure you have a recent
version of [Vagrant](https://www.vagrantup.com) installed.

Also make sure you have a recent version of [VirtualBox](https://www.virtualbox.org)
installed, including the [VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads).

### Getting started

1. Update [host_vars/vagrant.yml](host_vars/vagrant.yml) so that the `BAND`
   matches your region.

2. Within the root of this repository execute the following command:
    
    ```bash
    vagrant up
    ```

    As this will import the Vagrant box, install all requirements etc... this
    is going to take a while.

3. Configure your LoRa Gateway so that it points to the IP address of your
   computer (port `1700`).

4. Point your browser to https://localhost:8080/. As a self-signed certificate
   is used, your browser will prompt that the certificate can't be trusted.
   This is ok for testing.

5. For updating your Vagrant environment (e.g. updating the configuration or
   to upgrade installed packages, execute the following command:

    ```bash
    vagrant provision
    ```

6. Other useful commands:

   ```bash
   # stop the vagrant machine
   vagrant halt 

   # restart the vagrant machine
   vagrant reload

   # ssh into the vagrant machine
   vagrant ssh

   # destroy the vagrant machine
   vagrant destroy
   ```

## Remote deployment

This playbook has been tested on 
[DigitalOcean.com](https://m.do.co/c/6cd86e9f1cb8) but should also work on
bare-metal, AWS, ...

Don't have a DigitalOcean account yet? Use
[this](https://m.do.co/c/6cd86e9f1cb8) link and get $10 in credits for free :-)

### Requirements

On the machine from where you will execute this Ansible playbook (e.g. your own
computer), make sure you have Ansible 2.1+ installed. You can install Ansible with
pip (`pip install ansible`) or using Homebrew (OS X) (`brew install ansible`).

The Ansible playbook has been tested on the following images:

* Debian
    * Jessie (8.7)

* Ubuntu
    * Trusty (14.04.x LTS)
    * Xenial (16.04.x LTS)

### Configuration

1. Create a new Ubuntu 16.04.x instance and make sure that from your own machine
   on which Ansible is installed, you can ssh to this machine using public-key
   authentication (e.g. `ssh user@ip`).

2. Make sure Python is installed (`sudo apt-get install python`) on the target
   instance.

3. Configure a DNS record for your target instance and wait until this record
   resolves to your IP address. This is required in case you configured
   LetsEncrypt. You can skip this step when not using LetsEncrypt (
   `accept_letsencrypt_tos: False` in `loraserver_hosts.yml`).

4. Copy the `inventory.example` inside this repository to `inventory` and
   replace `example.com` with the hostname created in step 2.

5. Copy the `group_vars/loraserver_hosts.example.yml` inside this repository to
   `group_vars/loraserver_hosts.yml` and change the settings where needed.

See also the following links for more documentation:

* https://docs.loraserver.io/lora-gateway-bridge/
* https://docs.loraserver.io/loraserver/
* https://docs.loraserver.io/lora-app-server/

### Provisioning

Run the following command from your machine to deploy LoRa Server to your
target instance, to upgrade to the latest versions or to update the
configuration:

```bash
ansible-playbook -i inventory full_deploy.yml
```

After the playbook has been completed, the dashboard should be accessible from
https://yourdomain.com/ (please note the http*s*).


## Changelog (playbook changes)

### 2017-07-26

* `GW_SERVER_JWT_SECRET` configuration option has been added to the example
  configuration file, which will be mandatory for
  [LoRa Server](https://docs.loraserver.io/) 0.20.0.

* Port `8002` (used by [LoRa Gateway Config](https://docs.loraserver.io/lora-gateway-config/))
  has been added as public accessible port in the example configuration.

* `letsencrypt` cli has been changed to `certbot` cli (as per installation
  instructions documented at https://certbot.eff.org).

### 2017-06-20

* Mosquitto authentication / authorization has been added (using
  [mosquitto-auth-plug](https://github.com/jpmens/mosquitto-auth-plug)).
  The `loraserver_hosts.example.yml` has been updated with example
  configuration. Note that anonymous connections will be rejected. This allows
  users to connect to the MQTT broker using their LoRa App Server credentials.

### 2017-03-28

* PostgreSQL 9.6 will now be installed from the [PostgreSQL deb repository](https://www.postgresql.org/download/).
  In case you're upgrading, make sure to migrate your data.

* Mosquitto will be now be installed from either the Mosquitto PPA or
  the [Mosquitto deb repository](https://mosquitto.org/download/).
