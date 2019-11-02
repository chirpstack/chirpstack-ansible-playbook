# ChirpStack stack setup (Ansible & Vagrant)

This repository provides an [Ansible](https://www.ansible.com) playbook to
setup the [ChirpStack](https://www.chirpstack.io/) open-source LoRaWAN Network Server stack.
With the included [Vagrant](https://www.vagrant.com) file, the ChirpStack stack can also be setup
locally inside a VM (e.g. using [VirtualBox](https://www.virtualbox.org)).

It will:

* Setup firewall rules (iptables)
* Setup Mosquitto (MQTT broker) + connection credentials
* Setup Redis
* Setup PostgreSQL + creation of roles and databases
* Setup [ChirpStack Gateway Bridge](https://www.chirpstack.io/gateway-bridge/)
* Setup [ChirpStack Network Server](https://www.chirpstack.io/network-server/)
* Setup [ChirpStack Application Server](https://www.chirpstack.io/application-server/)
* Setup [ChirpStack Geolocation Server](https://www.chirpstack.io/geolocation-server/)
* Request a HTTPS certificate from [Let's Encrypt](https://letsencrypt.org)

## Vagrant (local environment using VirtualBox)

The included `Vagrantfile` will setup a Debian Stretch (9.x) virtual
machine with the latest ChirpStack stack components installed. It will also forward
the following ports to your host system:

* `8080`: ChirpStack Application Server UI and API
* `1700`: UDP listener for the packet-forwarder data
* `1883`: Mosquitto MQTT
* `1884`: Mosquitto Websockets

Note: when using Vagrant, there is no need to install Ansible (this will be
automatically installed inside the Vagrant machine).

### Requirements

When setting up the ChirpStack stack, make sure you have a recent
version of [Vagrant](https://www.vagrantup.com) installed.

Also make sure you have a recent version of [VirtualBox](https://www.virtualbox.org)
installed, including the [VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads).

### Getting started

1. Update `roles/chirpstack-network-server/templates/chirpstack-network-server.toml` so that the 
   `network_server.band.name` matches the LoRaWAN band to use. Depending the
   chosen band, you might also be interested in updating other network-server
   settings listed under the `network_server.network_settings` section.

2. Within the root of this repository execute the following command:
    
    ```bash
    vagrant up
    ```

    As this will import the Vagrant box, install all requirements etc... this
    is going to take a while.

3. Configure your LoRa Gateway so that it points to the IP address of your
   computer (port `1700`).

4. Point your browser to http://localhost:8080/. As a self-signed certificate
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
Refer to the [Ansible installation guide](http://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)
for more installation instructions.

The Ansible playbook has been tested on the following images:

* Debian
    * Buster (10.x)
    * Stretch (9.x)

* Ubuntu
    * Bionic (18.04.x LTS)
    * Xenial (16.04.x LTS)

### Configuration

1. Create a new Debian Stretch 9.x instance and make sure that from your own machine
   on which Ansible is installed, you can ssh to this machine using public-key
   authentication (e.g. `ssh user@ip`).

2. Configure a DNS record for your target instance and wait until this record
   resolves to your IP address. This is required in case you configured
   LetsEncrypt. You can skip this step when not using LetsEncrypt (
   `accept_letsencrypt_tos: False` in `single_server.yml`).

3. Copy the `inventory.example` inside this repository to `inventory` and
   replace `example.com` with the hostname created in step 2.

4. Copy the `group_vars/single_server.example.yml` inside this repository to
   `group_vars/single_server.yml` and change the settings where needed.

5. Update the ChirpStack Gateway Bridge, ChirpStack Application Server and ChirpStack Network Server configuration
   files under:

   * `roles/chirpstack-gateway-bridge/templates/chirpstack-gateway-bridge.toml`
   * `roles/chirpstack-application-server/templates/chirpstack-application-server.toml`
   * `roles/chirpstack-network-server/templates/chirpstack-network-server.toml`

See also the following links for more documentation:

* https://www.chirpstack.io/gateway-bridge/
* https://www.chirpstack.io/network-server/
* https://www.chirpstack.io/application-server/

### Provisioning

Run the following command from your machine to deploy the ChirpStack stack to your
target instance, to upgrade to the latest versions or to update the
configuration:

```bash
ansible-playbook -i inventory full_deploy.yml
```

After the playbook has been completed, the dashboard should be accessible from
http://yourdomain.com/. When you have enabled the LetsEncrypt TLS certificate
setup, this will automatically redirect to https://yourdomain.com/.
