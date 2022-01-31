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
Notes<br>
 ===> we can create our own inventory file if we need and can add the private ip address of the wanted node machines<br>
 ===> The defualt inventory file is /etc/ansible/hosts  and when using this inventory file, we need not use -i option<br>
 ===> If we do not mention the inventory file, it takes default inventory file<br>
 ===> command module is the default module in ansible<br>
### Adhoc commands
   ===> Important modules in ansible<br>
1) command - This module is used for executing basic linux commands on managed nodes.<br>
2) shell -  This module is used to execute commands which involved redirection and piping and to execute shell scripts on managed nodes.<br>
3) ping  --  This module is used to check if the remote server is pingable or not.<br>
4) user --  This module is used for user management like create user, setting password, assign home directory  etc<br>
5) copy  --  This module is used to copy the files and folders from controller to managed nodes.<br>
6) fetch  --  This module is used to copy files and folder from managed nodes to controller.<br>
7) file  --  This module is used for creating or deleting files and folders on managed nodes.<br>
8) stat  --  Used to capture detailed information about files and folders present in managed nodes.<br>
9) debug --  Used to display output of any module.<br>
10) apt   --  Used for performing package management on managed nodes ie installing softwares / upgrading repositories  etc . It works on ubuntu, debain flavours of linux.<br> 
11) yum  --  similar to apt module. It works on Red hat linux, centos etc<br>
12) git  --  used to perform git version controlling on managed nodes<br>
13) replace -- This is used to replace specific text in configuration file with some other text.<br>
14) service  -- used for starting / stoping / restarting services on managed nodes.<br>
15) include  --  Used for calling child play books  from parent play book.<br>
16) uri    -- useful in checking  if remote url is reachable or not.<br>
17) docker_container  --  used to execute docker  commands related to container management on managed nodes.<br>
18) docker_image  --  used to execute commands related to docker images on managed nodes.<br>
19) docker_login  --  used to login to docker hub from managed nodes.<br>
20) setup   --  used to capturing system information related to the managed nodes.<br>
++++++++++++++++++++++++++++++++++++++++++++++++<br>
If you want to create users in all node machines <br>
$ ansible  all -m user  -a  'name=ria  password=daya'<br>
If we run the above command we will get error ( permission denied )<br>
$ ansible  all -m user  -a  'name=ria password=daya'  -b  ( become , for higher privileges on managed nodes )<br>
++++++++++++++++++++++++++++++++++++++++++++++++<br>
Command to create user and set home directory, user id,  default working shell etc<br>
Another example<br>
$ ansible all -m user  -a 'name=Ravi password=freefree uid=1234 comment="A regular user"  home=/home/ubuntu/Ravi shell=/bin/bash' -b <br>
To check for the new user in node machine <br>
$  ssh 172.31.44.218(private_ip of_node machine)
$ vim  /etc/passwd<br>
+++++++++++++++++++++++++++++++++++++++++++++++<br>
apt module  -- This is used for package management<br>
$ ansible all -m apt  -a 'name=git  state=present' -b<br>
  --->  state=present  is for installation<br>
  --->  state=latest   is for upgradation<br>
  --->  state=absent   is for uninstallation<br>
### Play books
Adhoc commands are capable of working only on one module and one set of arguments.When we want to perform complex configuration management activities, adhoc commands will be difficult to manage.<br>
In such scenarios, we use play books.Play book is combination of plays. Each play is designed to do some activity on the managed nodes. These plays are created to work on single host or a group of hosts or all the hosts.<br>
The main advantage of play books  is reusability.<br>
Play books are created using  yaml files.<br>
++++++++++++++++++++++++++++++++++++++++++++<br>
$ mkdir  playbooks<br>
$ cd playbooks<br>
$ vim playbook1.yml<br>

---<br>
- name: Install git and clone a remote repository<br>
  hosts: all<br>
  tasks:<br>
    - name: Install git<br>
      apt:<br>
       name: git<br>
       state: present<br>
       update_cache: yes<br>
    - name: clone remote git repository<br>
      git:<br>
        repo: https://github.com/sunilkumark11/git-9am-batch.git<br>
        dest: /home/ubuntu/newgit<br>  	
...<br>



To check the syntax:<br>
$ ansible-playbook  playbook1.yml  --syntax-check<br>

note ----> ( Do not use tab  when creating yml file )<br>

<b>To run the playbook</b><br>
$ ansible-playbook  playbook1.yml  -b<br>
+++++++++++++++++++++++++++++++++++++++++<br>












