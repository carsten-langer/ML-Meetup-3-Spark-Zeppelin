# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "ubuntu/xenial64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
  # For Apache Spark/Zeppelin
  config.vm.network "forwarded_port", guest: 8080, host: 8080, host_ip: "127.0.0.1"	# Apache Zeppelin GUI
  config.vm.network "forwarded_port", guest: 4040, host: 4040, host_ip: "127.0.0.1" # Zeppelin's Apache Spark Driver GUI

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

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
  # For Apache Zeppelin/Spark, 3 GiB seems to be the minimum.
  config.vm.provider "virtualbox" do |vb|
	# Set the name in the VirtualBox GUI
	vb.name = "ml-spark_" + (Time.now.strftime "%Y%m%dT%H%M%S")
    # Customize the amount of memory on the VM:
    vb.memory = 3072
	# Customize number of CPUs available inside the VM
	vb.cpus = 2
  end


  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # CDL
  config.vm.provision "shell", inline: <<-SHELL
    # Break if any command fails
    set -o errexit
    set -o nounset
  
    # General setup of ML box as discussed in 2nd meeting
    apt-get update
    # apt-get install -y apache2 # was optional in 2nd meeting and we do not need it for Apache Spark, so commented out to save time/space
	apt-get install -y python3-pip
	pip3 install --upgrade pip
  
    apt-get --yes upgrade  # comment out if you do not want to always have the latest versions
  
    # Setup of Apache Zeppelin incl. Apache Spark in local mode, as discussed in 3rd meeting
	# Get Java
	sudo apt-get --yes install openjdk-8-jre-headless

    # To speed up operations, the zeppelin-0.7.2-bin-all.tgz can be downloaded upfront to the host OS folder which is mapped to the shared folder /vagrant inside the guest machine.
    # If it is not already downloaded to /vagrant, download it now to that folder. As this folder is on the host, the downloaded file can be reused even after this box is destroyed.
    cd /vagrant
	sudo --user=ubuntu wget --no-verbose --timestamping http://www-eu.apache.org/dist/zeppelin/zeppelin-0.7.2/zeppelin-0.7.2-bin-all.tgz
    # Always download the checksum, overwriting any previously downloaded file
    sudo --user=ubuntu wget --no-verbose --output-document=zeppelin-0.7.2-bin-all.tgz.sha512 https://www.apache.org/dist/zeppelin/zeppelin-0.7.2/zeppelin-0.7.2-bin-all.tgz.sha512
    # Compare checksum
    sudo --user=ubuntu sha512sum -c zeppelin-0.7.2-bin-all.tgz.sha512
	cd /home/ubuntu
	sudo --user=ubuntu tar --extract --skip-old-files --file /vagrant/zeppelin-0.7.2-bin-all.tgz
	sudo --user=ubuntu ln --symbolic --force /home/ubuntu/zeppelin-0.7.2-bin-all /home/ubuntu/zeppelin
  SHELL
end
