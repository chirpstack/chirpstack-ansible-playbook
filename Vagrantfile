# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "vagrant" do |box|
    box.vm.box = "debian/bullseye64"

    box.vm.network "forwarded_port", guest: 80,   host: 8080, protocol: "tcp"
    box.vm.network "forwarded_port", guest: 443,  host: 4443, protocol: "tcp"
    box.vm.network "forwarded_port", guest: 8883, host: 8883, protocol: "tcp"
    box.vm.network "forwarded_port", guest: 1700, host: 1700, protocol: "udp"

    box.vm.provision "ansible_local" do |ansible|
      ansible.install         = true
      ansible.playbook        = "deploy.yml"
      ansible.config_file     = "ansible.cfg"
    end
  end
end
