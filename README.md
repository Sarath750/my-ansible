# my-ansible

Ansible
------- 
Configuration Management Tool


Automation in Devops
--------------------
1. Infrastructure Management Tool --> Terraform (automate creating servers/instances)
"Server"

2. Configuration Management Tool --> Ansible (Automate installing softwares)
Install packages --> java

3. CICD      --> Jenkins
deploying --> java -jar <jar file> 

Automation through scripting
-----------------------------
Scripting tools --> shell/python


There will be so many servers/machines in our organization, so if we want to install same software in all machines at once, then we can use ansible. 


Lets say i have 1 master machine and 5 node machines. 

We can connect to the 5 node machines using passwordless authentication.

Generate a public key in master machine using ssh-keygen and place it in node machines under .ssh/authorized-keys.

Ansible will be working on passwordless authentication.

its agentless tool.

Even we are connecting to our ec2-instance from our laptop through this passwordless authentication. 
we keep pem file(private key) in our laptop and in ec2-instance there will be public key.

Master machine
--------------
sudo hostnamectl set-hostname master
  (sudo also not imp to give)  
  (to set hostname to master)
sudo -i  
  (switched to root user)
ssh-keygen
  (place the public key in node machine /root/.ssh/authorized_keys)
  /root/.ssh/authorized_keys = ~/.ssh/authorized_Keys = ${HOME}/.ssh/authorized_keys
ssh root@<node_public-ip>
  (connected to node machine)
exit
  (exited from node machine)
python --version
  (python is the pre-requisite for ansible. python is auto-installed in amazon-linux, if its not present then we need to install it manually)
amazon-linux-extras install ansible2
  (installed ansible)
ansible version
  (to check anisble version - 2.9.23)
vi /etc/ansible/hosts
    (place node machine public-ip under [webservers])
    # [webservers]
    [nodes]
    44.204.63.185
ansible -m ping nodes

  we need to keep all node machine ip address  in the inventory file(default inventory file, dynamic inventory file)

  for default inventory file
  --------------------------
  ansible -m ping nodes
  (for the nodes if it shows success, then we are able to communicate with our node machines successfully)

  for dynamic inventory file
  ---------------------------
  vi hostfile
  [webservers]
  <node1 public ip>
  <node2 public ip>

  [node1]
  <node1 public ip>

  ansible -i hostfile -m ping webservers
  ansible -i hostfile -m ping node1


Installing softwares/packages without a playbook
------------------------------------------------
ansible -m yum -a 'name=java-1.8.0-openjdk state=present' nodes
     (java is installed in the node machine)

ansible -i hostfile -m yum -a 'name=maven state=present' webservers
    (maven will be installed in both nodes node1, node2)

ansible -i hostfile -m yum -a 'name=maven state=present' node1
    (maven will be installed only in node1)

ansible -i hostfile -m yum -a 'name=git state=present' node1
     (git will be installed only in node1)


ansible -m copy -a 'src=/root/text.txt dest=/opt mode=0664' nodes
    (copying a text.txt file from master machine to node server/machine and giving permissions of 664.)

ansible -m copy -a 'src=/opt/text.txt dest=/root mode=0664 remote_src=yes' nodes
     (now the text.txt file present in the /opt of node machine will now be moved to /root of node machine)

touch test123.txt

Installing packages with playbooks
----------------------------------
vi playbook.yml
---------------
---
- hosts : nodes
  tasks : 
  - name : copy files to node machine
    copy : 
      src : /root/test123.txt
      dest : /opt
      mode : '0777'
  - name : copy file from /opt of node_machine to /root of node_machine
    copy :
      src : /opt/test123.txt
      dest : /root
      mode : '0777'
      remote_src : yes
  - name : create file
    file : 
      path : /opt/example.txt
      state : touch
      mode : '0644' 

ansible-playbook playbook.yml --syntax-check
ansible-playbook playbook.yml


vi docker-installation.yml
--------------------------
---
- hosts : nodes
  tasks : 
  - name : install docker
    yum : 
      name : docker
      state : present
  - name : start docker
    service : 
      name : docker
      state : started


ansible-playbook docker-installation.yml --syntax-check
ansible-playbook docker-installation.yml

docker installed and started service in the node machine.
 
