# vim: set ft=ruby sw=2 ts=2:
# -*- mode: ruby -*-

DB_IP  = '192.168.3.42'
WEB_IP = '192.168.3.43'

SERVER_NAME = WEB_IP

# this is for centos 6 / el 6
VENDOR_BUNDLE_URL = "https://moz-devservices-bmocartons.s3.amazonaws.com/bmo/vendor.tar.gz"

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "vagrant_support/playbook.yml"
    ansible.extra_vars = {
      WEB_IP:            WEB_IP,
      DB_IP:             DB_IP,
      SERVER_NAME:       SERVER_NAME,
      VENDOR_BUNDLE_URL: VENDOR_BUNDLE_URL,
    }
  end

  config.vm.define "db" do |db|
    db.vm.box = 'centos/6'
    db.vm.hostname = 'bmo-db.vm'
    db.vm.network "private_network", ip: DB_IP
    db.vm.synced_folder ".", "/vagrant", disabled: true
  end

  config.vm.define "web", primary: true do |web|
    # Every Vagrant development environment requires a box. You can search for
    # boxes at https://atlas.hashicorp.com/search.
    web.vm.box = "centos/6"
    web.vm.hostname = 'bmo-web.vm'

    # Create a private network, which allows host-only access to the machine
    # using a specific IP.
    web.vm.network "private_network", ip: WEB_IP

    web.vm.synced_folder ".", "/vagrant", type: 'rsync',
      rsync__exclude: ["local/", "vendor/", "vagrant_support/", "data/", "localconfig"]

    web.vm.provider "virtualbox" do |v|
      v.memory = 2048
      v.cpus = 2
    end

    web.vm.provider "parallels" do |prl|
      prl.memory = 2048
      prl.cpus = 2
    end

    web.vm.provider "vmware_fusion" do |v|
      v.vmx["memsize"] = "2048"
      v.vmx["numvcpus"] = "2"
    end
  end
end
