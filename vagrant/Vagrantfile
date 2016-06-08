# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/trusty64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"
    #config.vm.network "private_network", ip: "192.168.1.10"
  config.vm.network :forwarded_port, guest: 80, host: 8000

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"
  config.vm.network "public_network", :bridge => "en0: Wi-Fi (AirPort)", :ip => "192.168.1.15"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.
  config.vm.provider "virtualbox" do |v|
    $vm = "mitaka-controller-node1"
    v.customize ["modifyvm", :id, "--memory", "8192"]
    v.customize ["modifyvm", :id, "--cpuexecutioncap", "75"]
  end
  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  if Dir.glob("#{File.dirname(__FILE__)}/.vagrant/machines/#{$vm}/*").empty?
    print "MIE GitHub Username: "
    $username = STDIN.gets.chomp();
    print "MIE GitHub Email: "
    $email = STDIN.gets.chomp();
    $script = <<-SCRIPT
    export DEBIAN_FRONTEND=noninteractive
    sudo timedatectl set-timezone America/New_York
    export LANGUAGE=en_US.UTF-8
    export LANG=en_US.UTF-8
    export LC_ALL=en_US.UTF-8
    locale-gen en_US.UTF-8
    dpkg-reconfigure locales
    sudo apt-get update
    sudo apt-get install python-software-properties software-properties-common python-keyring -y
    sudo apt-get install ubuntu-cloud-keyring -y
    sudo apt-get upgrade -y
    sudo apt-get dist-upgrade
    sudo apt-get install git -y
    sudo apt-get install traceroute
    sudo apt-get install iproute2
    sudo apt-get install mlocate
    sudo apt-get update
    git config --global user.name #{$username}
    git config --global user.email #{$email}
    # Clone the openstack-dev/devstack github repository
    cd /opt
    git clone https://github.com/mieweb/devstack.git
    cd devstack
    # Create development branch
    git checkout -b #{$username}_mitaka_controller_node1 remotes/origin/mitaka_controller_node1
    git remote add upstream https://github.com/openstack-dev/devstack.git
    # Create 'stack' user
    sudo ./tools/create-stack-user.sh
    sudo chown -R stack:stack .
    sudo updatedb
    SCRIPT
    config.vm.provision "shell", inline: $script
  else
    next
  end
end