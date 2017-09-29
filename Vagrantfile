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
	# boxes at https://vagrantcloud.com/search.
	config.vm.box = "boxcutter/ubuntu1604"

	# The url from where the 'config.vm.box' box will be fetched if it
	# doesn't already exist on the user's system.
	config.vm.box_url = "https://app.vagrantup.com/boxcutter/boxes/ubuntu1604"



	# CUSTOMISATION OF THE VM HERE

	PATH_VAGRANT_PROJECT=File.dirname(__FILE__)

	# use my own ssh key, dont generate own keys
	config.ssh.insert_key = false
	config.ssh.private_key_path = ["#{PATH_VAGRANT_PROJECT}\\ssh\\myexperimental.openssh.ppk", "~/.vagrant.d/insecure_private_key"]
	
	# here the private key is stored with its original name, just for the case
	config.vm.provision "file", source: "#{PATH_VAGRANT_PROJECT}\\ssh\\myexperimental.openssh.ppk", destination: "~/myexperimental.openssh.ppk"
	
	# here ansible will look for the private key, when connection form master to the slave
	config.vm.provision "file", source: "#{PATH_VAGRANT_PROJECT}\\ssh\\myexperimental.openssh.ppk", destination: "/home/vagrant/.ssh/id_rsa"
	
	# here we store the public key, so that it is authorized when connecting via ssh
	config.vm.provision "file", source: "#{PATH_VAGRANT_PROJECT}\\ssh\\myexperimental.openssh.public", destination: "~/.ssh/authorized_keys"

	BOX_IMAGE = "boxcutter/ubuntu1604"
  
	# you can ssh via "vagrant ssh master"
	config.vm.define "master" do |subconfig|
		subconfig.vm.box = BOX_IMAGE
		subconfig.vm.hostname = "master"
		subconfig.vm.network :private_network, ip: "10.0.0.10"
		
		subconfig.vm.provision "shell", inline: <<-SHELL
			sudo apt-get update
			sudo apt-get -y install software-properties-common
			sudo apt-add-repository -y ppa:ansible/ansible
			sudo apt-get -y update
			sudo apt-get -y install ansible
			
			# configure ansible hosts. Store away the original example hosts file.
			mv /etc/ansible/hosts /etc/ansible/hosts.original
			# add the localhost so that we can reach it via ansible as "local"
			echo -e "\n[local]\n127.0.0.1   ansible_connection=local\n\n" | sudo tee --append /etc/ansible/hosts
			# add the slave so that we can reach it via ansible as "slave"
			echo -e "\n[slave]\n10.0.0.11\n\n" | sudo tee --append /etc/ansible/hosts
			
			# configure the ssh key permissions to 700. Otherwise ansible will decline the usage of the key
			chmod -R 700 /home/vagrant/.ssh
			
		SHELL
	end

	# you can ssh via "vagrant ssh slave"
	config.vm.define "slave" do |subconfig|
		subconfig.vm.box = BOX_IMAGE
		subconfig.vm.hostname = "slave"
		subconfig.vm.network :private_network, ip: "10.0.0.11"
	end
	
  
  # Use shell script to provision
  config.vm.provision "shell", inline: <<-SHELL
    
	# do here the provisioning for all nodes
	
  SHELL
  
end
