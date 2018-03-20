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
	config.vm.box = "box-cutter/ubuntu1604"
	


	# CUSTOMISATION OF THE VM HERE

	PATH_VAGRANT_PROJECT=File.dirname(__FILE__)

	# use my own ssh key, dont generate own keys
	config.ssh.insert_key = false

	BOX_IMAGE = "boxcutter/ubuntu1604"

	# all files here will be added to home
	config.vm.synced_folder "#{PATH_VAGRANT_PROJECT}\\vagrant-home\\", "//home/vagrant/vagrant-home/"
  
	# you can ssh via "vagrant ssh master"
	config.vm.define "master" do |subconfig|
		subconfig.vm.box = BOX_IMAGE
		subconfig.vm.hostname = "master"
		subconfig.vm.network :private_network, ip: "10.0.0.10"
		
		subconfig.vm.network "forwarded_port", guest: 80, host: 9180
		
		subconfig.vm.provision "shell", inline: <<-SHELL

			###### bulk import the ssh keys dynamically by extension
			cd '/home/vagrant/vagrant-home/ssh/'

			# add private keys *.openssh.ppk to the .ssh
			for i in *.openssh.ppk; do
				[ -f "$i" ] || break
				cp "$i" "/home/vagrant/.ssh/"
			done
			
			sudo apt-get update
			sudo apt-get -y install software-properties-common
			sudo apt-add-repository -y ppa:ansible/ansible
			sudo apt-get -y update
			sudo apt-get -y install ansible
			
					
			# configure ansible hosts. Store away the original example hosts file.
			mv /etc/ansible/hosts /etc/ansible/hosts.original
			
			# add the localhost so that we can reach it via ansible as "local"
			echo -e "\nlocal ansible_host=127.0.0.1   ansible_connection=local \n" | sudo tee --append /etc/ansible/hosts
			
			# add the slave so that we can reach it via ansible as "slave"
			echo -e "\nslave ansible_host=10.0.0.11 ansible_user=vagrant ansible_ssh_private_key_file=/home/vagrant/.ssh/key.myexperimental.openssh.ppk\n" | sudo tee --append /etc/ansible/hosts
			
			
			# configure the ssh key permissions to 700. Otherwise ansible will decline the usage of the key
			chown -R vagrant:vagrant /home/vagrant/.ssh
			chmod -R 700 /home/vagrant/.ssh
			
			
			# those are the required config changes, to enable becoming non-root users in playbooks
			sed -i 's/.*pipelining.*/pipelining = True/' /etc/ansible/ansible.cfg
			sed -i 's/.*allow_world_readable_tmpfiles .*/allow_world_readable_tmpfiles = True/' /etc/ansible/ansible.cfg
			
		SHELL
	end
	
	

	# you can ssh via "vagrant ssh slave"
	config.vm.define "slave" do |subconfig|
		subconfig.vm.box = BOX_IMAGE
		subconfig.vm.hostname = "slave"
		subconfig.vm.network :private_network, ip: "10.0.0.11"
		
		subconfig.vm.network "forwarded_port", guest: 80, host: 9080
		
		
		subconfig.vm.provision "shell", inline: <<-SHELL
    
			# add public keys as *.openssh.public to authorized keys
			cd '/home/vagrant/vagrant-home/ssh/'
			for i in *.openssh.public; do
				[ -f "$i" ] || break
				echo -e "\n" >> "/home/vagrant/.ssh/authorized_keys"
				cat "$i" 	 >> "/home/vagrant/.ssh/authorized_keys"
			done
		
		SHELL
	end
	
  
end
