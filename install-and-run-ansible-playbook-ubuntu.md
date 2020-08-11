Install And Run Ansible On Ubuntu
===

Prerequisites
---
- 2 Server Node (For Master Node and Slave Node)
 - We will use master node for install nginx on the slave node
 - For this case I have deploy the slave node on IP : `10.10.10.208`
- Internet Access
- Basic knowledge of Linux Command Line

Initialization
---
For common cloud service like GCP or AWS, you already have SSH Public Key when you launch the instances.
So for this case, we will manually generate and copy the ssh public key to slave node.

Generate SSH Key on Master Node
---
Paste this command to terminal
```sh
$ ssh-keygen
$ ls -l ~/.ssh/
```
Copy SSH Key from Master to Slave Node
---
You have many options to copy ssh key from Master to Slave<br>
Option 1:
```sh
$ ssh-copy-id ubuntu@10.10.10.208
```
Option 2:
Copy content of your `~/.ssh/id_rsa.pub`. Then SSH to slave node
```sh
$ ssh root@10.10.10.208
```
After that, do all of this command on Slave Node
```sh
 $ mkdir -p /home/ubuntu/.ssh
 $ vim authorized_keys # Paste content from ~/.ssh/id_rsa.pub
 $ chown -Rf ubuntu:ubuntu /home/ubuntu
 $ exit
```

Try to login to slave node from master node by passing your generated ssh key to make Passwordless SSH
```sh
$ ssh ubuntu@10.10.10.208 -i ~/.ssh/id_rsa
```
Once you can passwordless login, we can continue to next step.

Make Passwordless SUDO on Slave Node
---
Login to your slave node then do the magic
```sh
$ ssh ubuntu@10.10.10.208
$ sudo visudo
```
It will show text editor and change this line 
```
%sudo   ALL=(ALL:ALL) ALL
```
to
```
%sudo   ALL=(ALL:ALL) ALL
ubuntu ALL=(ALL) NOPASSWD: ALL
```
Save it!

**Note: When you do this, every people that use `ubuntu` user for login will able do to everything that `root` user can do without password.**

Install Ansible on Master Node
---
Okay if you already done with command above, we can continue to install the ansible to our Master Node. Ansible use python for its dependencies so we need python as well to make ansible run. Go to Master Node and paste this command
```sh
$ sudo apt update
$ sudo apt install python3-dev python3-setuptools python3-pip
```

Then install `ansible` from `pip` with this following command
```sh
$ sudo pip install ansible
```

And test it with command `ansible --version`
If you can see the output similiar to this, it mean you have successfully install it
```sh
ansible 2.9.10
  config file = None
  configured module search path = [u'/root/.ansible/plugins/modules',u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/local/lib/python2.7/dist-packages/ansible
  executable location = /usr/local/bin/ansible
  python version = 2.7.12 (default, Apr 15 2020, 17:07:12) [GCC 5.4.0 20160609]
```

Playbook and Inventory
---
**NOTE : Do all command on Master Node**
You must create playbook and define the inventory before you can provision the server (slave node).
In this section, I'll teach you how to create playbook and define the inventory on master node

Inventory
---
Inventory is used for "register" your server to make ansible can communicate with it.<br>
To create it paste this following command.
```sh
$ mkdir -p ~/learn-ansible
$ cd ~/learn-ansible
$ vim inventory.yaml
```
Then paste this line into your `inventory.yaml`
```yaml
---
all:
  hosts:
    dev-server:
      ansible_host: 10.10.10.208
      ansible_ssh_private_key_file: ~/.ssh/id_rsa
      ansible_user: ubuntu  
```

Playbook
---
Playbook is used for collection for your `tasks` that will executed on the remote server<br>
Create playbook file in your previously created directory.
```sh
$ cd ~/learn-ansible
$ vim install_nginx.yaml
```

Paste this following line
```yaml
---
- name: Provision Server
  hosts: dev-server
  become: true
  tasks:
    - name: Install NGINX Web Server
      apt:
        name: nginx
        update_cache: true
```

Okay, you have created 2 files `install_nginx.yaml` and `inventory.yaml`. So now its time to magic show.
Go to your previously created directory and run this following command
```sh
$ cd ~/learn-ansible
$ ansible-playbook -i inventory.yaml install_nginx.yaml
```

If you not get any error, you can check with `curl` to make sure that your NGINX is installed.
```sh
$ curl 10.10.10.208
```
And now you can setup and provision server with only single command in no time.
You can explore more like:

- Provision Multiple Server 
- Restart Web Server
- etc. 

For more about ansible you can refer to this [Official Ansible Website](https://docs.ansible.com/ansible/latest/user_guide/index.html) 
