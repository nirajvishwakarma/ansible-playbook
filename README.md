# Ansible-Environment-Setup



Ansible

#1. Create 3 server
Ansiblecontroller
Target1
Target2

#2. SHH all server
sudo ssh -i "mykey.pem" ubuntu@ec2-18-205-155-149.compute-1.amazonaws.com

#3. Change host and hostname for all 3 server
ansiblecontroller
$ sudo vim /etc/hostname
 
Ansiblecontroller
 
$ sudo vim /etc/hosts
 
127.0.0.1 localhost ansiblecontroller
::1       localhost ansiblecontroller
 

Target1
$ sudo vim /etc/hostname
 
target2
 
$ sudo vim /etc/hosts
 
127.0.0.1 localhost target2
::1           localhost target2
 


target2
$ sudo vim /etc/hostname
 
target2
 
$ sudo vim /etc/hosts
 
127.0.0.1 localhost target2
::1           localhost target2
 

#4.Installing Ansible on Master (ubuntu)

$ sudo apt update
$ sudo apt upgrade -y
$ sudo apt install software-properties-common
$ sudo add-apt-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible -y

On Hosts
$ sudo apt-get update
$ sudo apt-get install python3 -y

#5. Verify ansible

$ ansible --version


#5.configure SSH access to ansible host

->Go to ansible controller try to ssh target1 or target2
$ ssh ubuntu@<target_IP>

-> generate key for the ansible controller 
$ cd .ssh/
$ ssh-keygen
-> two files will be generated 
    1.id_rsa
    2.id_rsa.pub
-> copy id_rsa.pub and go to target1 or target2 and paste in .ssh/authorized_keys
$ cat id_rsa.pub

-> now try to ssh target1 or target2
$ shh ubuntu@<target_IP>










#6.Setting Up Ansible host and testing connection

Inventory file
Default inventory file is located at /etc/ansible/hosts

$ sudo vim /etc/ansible/hosts
 
[production]
54.174.249.234
3.83.115.157
 
 

We can also create own inventory file
vim inventory.txt
 
[production]
54.174.249.234
3.83.115.157
 

$ ansible -m ping all
$ ansible -m ping production



Sample Playbook

Sample playbook to ping the targets

$ mkdir ansible-demo
$ cd ansible-demo
$ vim playbook-pingtest.yaml
 
-
  name: Test connectivity to target servers
  hosts: all
  tasks:
    - name: ping test
      ping:
 
    
$ ansible-playbook playbook-pingtest.yaml



1.Playbook to insall Apache2

$ vim apache2.yaml

-
  hosts: all
  tasks:
    - name: install apache2
      apt: name=apache2 state=latest
      become: yes
 
The above playbook will work but its not a Good Practice
 
-
  hosts: all
  tasks:
    - name: install apache2
      apt:
        name: apache2
        state: latest
      become: yes
The above playbook is good practice
    Run the playbook
    $ ansible-playbook apache2.yaml -i inventary.txt

2. Install apache2 with contents
-
  hosts: all
  tasks:
     - name: install apache2
       apt:
           name: apache2
           state: latest
       become: yes
     - name: index.html
       copy:
           content: " This playbook is working Fine"
           dest: /var/www/html/index.html
       become: yes
     - name: restart apache2
       service:
           name: apache2
           state: restarted
           enabled: yes
       become: yes
 
Since the root permission is required at every task so instead of writing for each single task we can write globally
-
  hosts: all
  become: yes
  tasks:
     - name: install apache2
       apt:
           name: apache2
           state: latest
     - name: index.html
       copy:
           content: " This playbook is working Fine"
           dest: /var/www/html/index.html
     - name: restart apache2
       service:
           name: apache2
           state: restarted
           enabled: yes
 



2.Ansible playbook to install 2 different things on different server

---
- name: installing apache2 on server1
  hosts: server1
  become: yes
  tasks:
    - name: install apache2
      apt:
        name: apache2
        state: latest

    - name: index.html
      copy:
        content: " This playbook is working Fine for server1"
        dest: /var/www/html/index.html

    - name: restart apache2
      service:
        name: apache2
        state: restarted
        enabled: yes

- name: installing apache2 on server2
  hosts: server2
  become: yes
  tasks:
    - name: install apache2
      apt:
        name: apache2
        state: latest

    - name: index.html
      copy:
        content: " This playbook is working Fine for server2"
        dest: /var/www/html/index.html

    - name: restart apache2
      service:
        name: apache2
        state: restarted
        enabled: yes



