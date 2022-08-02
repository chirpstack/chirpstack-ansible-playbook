# ChirpStack stack setup (Ansible & Vagrant)

This repository provides an [Ansible](https://www.ansible.com) playbook to
setup the [ChirpStack](https://www.chirpstack.io/) open-source LoRaWAN Network Server (v4).
With the included [Vagrant](https://www.vagrant.com) file, ChirpStack can also be setup
locally inside a VM (e.g. using [VirtualBox](https://www.virtualbox.org)).

It will:

* Setup firewall rules (iptables)
* Setup Mosquitto (MQTT broker) + client and server-certificate configuration
* Setup Redis
* Setup PostgreSQL + creation of role and database
* Setup [ChirpStack Gateway Bridge](https://www.chirpstack.io/docs/chirpstack-gateway-bridge/)
* Setup [ChirpStack](https://www.chirpstack.io/docs/chirpstack/)
* Request a HTTPS certificate from [Let's Encrypt](https://letsencrypt.org)

## Vagrant (local environment using VirtualBox)

The included `Vagrantfile` will setup a Debian Bullseye (11.x) virtual
machine with the latest ChirpStack components installed. It will also forward
the following ports to your host system:

* `8080`: ChirpStack UI and gRPC API
* `1700`: ChirpStack Gateway Bridge UDP listener (configured for EU868 region by default)
* `8883`: Mosquitto MQTT (with TLS, client-certificate files can be generated in the ChirpStack UI)

Note: when using Vagrant, there is no need to install Ansible (this will be
automatically installed inside the Vagrant machine).

### Requirements

When setting up ChirpStack, make sure you have a recent
version of [Vagrant](https://www.vagrantup.com) installed.

Also make sure you have a recent version of [VirtualBox](https://www.virtualbox.org)
installed, including the [VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads).

### Getting started

1. Update `host_vars/vagrant.yml` where needed.

2. Within the root of this repository execute the following command:
    
    ```bash
    vagrant up
    ```

    As this will import the Vagrant box, install all requirements etc... this
    is going to take a while.

3. Configure your LoRa Gateway so that it points to the IP address of your
   computer (port `1700`).

4. Point your browser to https://localhost:4443/. As a self-signed certificate
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
computer), make sure you have Ansible 2.10+ installed. You can install Ansible with
pip (`pip install ansible`) or using Homebrew (OS X) (`brew install ansible`).
Refer to the [Ansible installation guide](http://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
for more installation instructions.

The Ansible playbook has been tested on the following images:

* Debian
    * Bullseye (11.x)

* Ubuntu
    * Jammy (22.04 LTS)

### Configuration

1. Create a new Debian Bullseye 11.x instance and make sure that from your own machine
   on which Ansible is installed, you can ssh to this machine using public-key
   authentication (e.g. `ssh user@ip`).

2. Configure a DNS record for your target instance and wait until this record
   resolves to your IP address.

3. Copy the `inventory.example` inside this repository to `inventory` and
   replace `example.com` with the hostname created in step 2.

4. Copy the `group_vars/single_server.example.yml` inside this repository to
   `group_vars/single_server.yml` and change the settings where needed.

For more information, see also:

* https://www.chirpstack.io/docs/

### Provisioning

Run the following command from your machine to deploy ChirpStack to your
target instance, to upgrade to the latest versions or to update the
configuration:

```bash
ansible-playbook -i inventory deploy.yml
```

After the playbook has been completed, ChirpStack should be accessible from
https://yourdomain.com/.
