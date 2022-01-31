# Ansible
Ansible is an open-source software provisioning, configuration management, and application-deployment tool enabling infrastructure as code. <br>
It runs on many Unix-like systems, and can configure both Unix-like systems as well as Microsoft Windows. <br>
It includes its own declarative language to describe system configuration.<br>
Ansible was written by Michael DeHaan and acquired by Red Hat in 2015. <br>
Ansible is agentless, temporarily connecting remotely via SSH or Windows Remote Management (allowing remote PowerShell execution) to do its tasks.
### Advantages
1) Provisioning of servers<br>
	The applications that should be installed on server can be done very quickly from a single centralized location.<br>

2) Idempotent <br>
	Configuration management tools are used to bring the server to a particular state, called as desired state. If a server already in the desired state, configuration management tools will not reconfigure that server.<br>
They can be used only for managing the applications on top of the OS.<br>
Configutaion management tools -  Ansible, chef, puppet, salt  etc<br>
### Installation Python2 on node machines
Ubuntu machines default come with Python3<br>
Ansible supports Python2<br>
We need to downgrade the machines from python3  to Python2<br>

Connect to machine<br>
Check the version<br>

$ python3 --version<br>

To Install Python2<br>
$ sudo apt-get update<br>
$ sudo apt-get dist-upgrade ( It will point to  older apt repository  where python2 is available)<br>

$  sudo apt-get install -y python2.7 python-pip<br>

Now check the version of python<br>
$ python --version<br>
### Establish password less ssh connection
$ sudo passwd ubuntu ( lets give the password anything you want only )<br>

$ sudo vim /etc/ssh/sshd_config<br>

change <br>
PasswordAuthentication yes<br>
Save and QUIT<br>
$ sudo service ssh restart<br>
$ exit<br>
*** we need to generate ssh keys in conroller machine and copy the key into nodes which ever we have created.<br>
*** ssh-copy-id node_machine_name@private_ip of_node machines<br>
### Installing Ansible on controller machine
$ sudo apt-get install software-properties-common (  software-properties-common    ,  is a base package which is required to install ansible )<br>

$ sudo apt-add-repository ppa:ansible/ansible<br>

$ sudo apt-get update<br>

$ sudo apt-get install -y ansible<br>

+++++++++++++++++<br>
To check the version of ansible<br>

$ ansible --version<br>
### Adding the ip address of nodes in the inventory file
$ sudo vim /etc/ansible/hosts<br>
insert the private ip addresss of 3 servers, save and quit<br>
### Ansible performs remote configurations in 2 ways
1) using adhoc commands <br>
2) using play books <br>
### Common syntx of adhoc commands
$ ansible  all/group_name/ipaddress -i  path_of_inventory_file -m modulename  -a  'arguments'<br>
-----> Note : we can create our own inventory file if we need and can add the private ip address of the wanted node machines<br>
