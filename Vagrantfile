VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Use Ubuntu 14.04 Trusty Tahr 64-bit as our operating system
  config.vm.box = "ubuntu/trusty64"

  # Configurate the virtual machine to use 2GB of RAM
  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "2048"]
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  config.ssh.forward_x11 = true
  config.ssh.forward_agent = true
  # Forward the Rails server default port to the host
  config.vm.network :forwarded_port, guest: 3000, host: 3000
  config.vm.network :forwarded_port, guest: 19360, host: 19360
  config.vm.network :forwarded_port, guest: 8000, host: 8888
  config.vm.network :forwarded_port, guest: 5432, host: 15432
  config.vm.network :forwarded_port, guest: 35729, host: 35729

  config.vm.provision :shell, :inline => 'sudo apt-get update'
  config.vm.provision :shell, :inline => 'sudo apt-get -y install build-essential ruby-dev'
  # Use Chef Solo to provision our virtual machine
  config.vm.provision :chef_solo do |chef|
    chef.cookbooks_path = ["cookbooks", "site-cookbooks"]

    chef.add_recipe "apt"
    chef.add_recipe "nodejs"
    chef.add_recipe "vim"
    chef.add_recipe "postgresql::server_debian"
    chef.add_recipe "postgresql::ruby"
    chef.add_recipe "gswd::database"
    chef.json = {
      :postgresql => {
        :listen_addresses => '*',
        :password => {:postgres => "gswd"}
      },
      rbenv: {
        user_installs: [{
          user: 'vagrant',
          rubies: ["2.1.2"],
          global: "2.1.2",
          gems: {
            "2.1.2" => [
              { name: "bundler" }
            ]
          }
        }]
      }
    }
  end
end
