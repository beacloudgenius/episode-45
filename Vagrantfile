# Defines our Vagrant environment
#
# -*- mode: ruby -*-
# vi: set ft=ruby :
DO_TOKEN = '4b988ae809e42b0e535ccd98286ba44e32399012c65475c47f9e765d6b714fe0'

Vagrant.configure("2") do |config|

  # create mgmt node
  config.vm.define :mgmt do |mgmt_config|
      mgmt_config.vm.hostname = "mgmt.cloudgeni.us"
      mgmt_config.vm.provider :digital_ocean do |provider, override|
        override.ssh.private_key_path = '~/.ssh/id_rsa'
        override.vm.box = 'digital_ocean'
        override.vm.box_url = "https://github.com/smdahlen/vagrant-digitalocean/raw/master/box/digital_ocean.box"

        provider.token = DO_TOKEN
        provider.image = 'ubuntu-14-04-x64'
        provider.region = 'sfo1'
        provider.size = '512mb'
      end
      mgmt_config.vm.provision :shell, path: "bootstrap-mgmt.sh"
  end

  # create load balancer
  config.vm.define :lb do |lb_config|
      lb_config.vm.hostname = "lb.cloudgeni.us"
      lb_config.vm.provider :digital_ocean do |provider, override|
        override.ssh.private_key_path = '~/.ssh/id_rsa'
        override.vm.box = 'digital_ocean'
        override.vm.box_url = "https://github.com/smdahlen/vagrant-digitalocean/raw/master/box/digital_ocean.box"

        provider.token = DO_TOKEN
        provider.image = 'ubuntu-14-04-x64'
        provider.region = 'sfo1'
        provider.size = '512mb'
      end
  end

  # create some web servers
  # https://docs.vagrantup.com/v2/vagrantfile/tips.html
  (1..2).each do |i|
    config.vm.define "web#{i}" do |node|
        node.vm.hostname = "web#{i}.cloudgeni.us"
        node.vm.provider :digital_ocean do |provider, override|
          override.ssh.private_key_path = '~/.ssh/id_rsa'
          override.vm.box = 'digital_ocean'
          override.vm.box_url = "https://github.com/smdahlen/vagrant-digitalocean/raw/master/box/digital_ocean.box"

          provider.token = DO_TOKEN
          provider.image = 'ubuntu-14-04-x64'
          provider.region = 'sfo1'
          provider.size = '512mb'
        end
    end
  end

end
