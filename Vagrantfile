# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.hostname = "wp-lemp.local"
  config.vm.box = "ubuntu/bionic64"


  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
    vb.cpus = 2
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--ioapic", "on"]
  end


  config.vm.provision "ansible" do |ansible|
    ansible.become = true
    ansible.galaxy_role_file = "requiremets.yml"
    ansible.playbook = "site.yml"
  end
end
