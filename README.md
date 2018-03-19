# ExperimentsProject-4Ansible-usingVagrant
This Project contains a vagrant file which will start a master / slave VMs. So that one can experiment with ansible and control slave from master.

Check the wiki for the details.





## Connect to the vagrant machine
```
vagrant ssh master
```

## Add a new Ansible host

#### Add the private key to the master
An SSH key is required to connect to the remote machine.
Add the ssh private-key in openSSH-format (ppk) here
```
/home/vagrant/.ssh/
```

#### Add the host, ssh-username, private key to the ansible-inventory on the master
The File `/etc/ansible/hosts` contains the list of the hosts by ansible.
You can configure, which username and private-key should be used for th ssh connection.
Reference the private-key file, stored on the machine.
```
slave ansible_host=10.0.0.11 ansible_user=vagrant ansible_ssh_private_key_file=/home/vagrant/.ssh/key.myexperimental.openssh.ppk
```

#### Add the public key to the slave
Add the ssh public-key to the following file.
The public keys in that file are allowed to be used, to connect to that machine.
```
/home/vagrant/.ssh/authorized_keys
```

Example 
```
ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAxQRyoCBSijfLHpuQKR+0/nsO4z66r4xDre0nJZ8FgHwW+ATdzRr/Mu6C5DgS1ENY9No8yOH38zqnqlhoQpTY5d+wypbkkF3AG3N0D0DrHK4CHjjPNylHAm4+ATuU68agYOVTPycY19DXaAgQBqtJlRVHhMB9ZJ+ugAdKINjpW//8uwvPHisH6GYbA5zWugNHmyfNLdYdJcdozTUprHFkRz6E2HyCxEeterbcHtsEEfgCd93fbHn2utRg24VIRFNZF24C6N/OSrsmdKbYsQV/VygHEo6VYM4DUOcz1nzU5f3f5k1pitgBbyVqIID++XwxNCl8wAjUWFEHvA+xyf4Zyw== rsa-key-20170928
```


## Execute a command

```
# ping all hosts
ansible all -m ping

# ping the slave-host using user "ec2-user"
ansible slave -m ping -u "ec2-user"

# execute a playbook. In the playbook you can write down the host, username etc.
ansible-playbook addmycowsay.yaml

```