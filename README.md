# first day




```

Welcome to OpenDev Etherpad!


hello its raman khanna


--------------------------------------------------
create 
ansible master :
2 linux nodes




name..
ansible-raman-lumen-

ami :
ami-085f9c64a9b75eed5

t2.micro

-- create a eypair


-- reuse my sg : raman-sg

storage : 10 gb

-------------------------------------------
connect ti all 3 :

sudo -i
root@ip-172-31-16-121:~# hostnamectl set-hostname master
root@ip-172-31-16-121:~# bash

-----------------------------------------------------
installation ansible : only on master ..

https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-ansible-on-ubuntu

root@master:~# ansible --version

======================================

on master :

root@master:~# vi /etc/ansible/hosts  : default inventory

vi /etc/ansible/ansible.cfg 
   10  ansible-config init --disabled > ansible.cfg

  
   20  mv ansible.cfg /etc/ansible/ansible.cfg 


-----------------------------


on managed/worer nodes :
    
    
    $ sudo vi/etc/ssh/sshd_config
Find the following lines and make the necessary changes:

# Comment the following line (add # at the beginning)
Include /etc/ssh/sshd_config.d/*.conf

#Include /etc/ssh/sshd_config.d/*.conf

# Change ‘PasswordAuthentication’ to ‘yes’ to enable password-based
authentication
#PasswordAuthentication yes

PasswordAuthentication yes

# Uncomment the following line and change it to permit root login (not recommended for security reasons)
# PermitRootLogin prohibit-password

PermitRootLogin yes

-----------------------------


passwd root 

systemctl restart ssh
-------------------

==========================

from master :
ssh root@172.31.19.132



==================================



ansible adhoc way of firring :
    
    
vi  /etc/ansible/hosts    

[demo]
172.31.16.54
172.31.19.132



    
    
    ansible all  -m ping 
   46  ansible -h

   50  ansible all  -m ping -k
   51  clear

ansible demo --list-hosts
ansible demo[0] --list-hosts
            [1],[0:5] , [-1]
   53  ansible demo -a "ls -ltr"
   54  ansible demo -a "ls -ltr" -k
   55  ansible demo -a "ls -ltr /home/ubuntu" -k
   56  ansible demo -a "touch file1" -k
   57  ansible all -a "rm -rf file1" -k
   58  ansible demo[0] -a "ls -la" -k

   62  ansible demo -a "snap install tree " -k
   63  ansible demo -a "snap remove tree " -k
   
   ==========================

ansible devices -a "sh run | i iser"  -k -u admin -c network_cli -e ansible_network_os=cisco.ios.ios






vi /etc/ansible/hosts :


[devices]
devnetsandboxiosxe.cisco.com ansible_user=admin ansible_password=C1sco12345
18.231.249.198 ansible_ssh_private_key_file="/root/cisco.pem" ansible_user=ec2-user

[devices:vars]
ansible_network_os=ios
ansible_connection=network_cli

[demo]
172.31.16.54
172.31.19.132

[demo:vars]
ansible_user=root
ansible_password=raman











cat /etc/ansible/hosts 
   89  history
   90  clear
   91  ls
   92  vi /etc/ansible/hosts 
   93  clear
   94  ansible all -m ping
   95  vi /etc/ansible/hosts 
   96  clear
   97  ansible all -m ping
   98  clear
   99  vi /etc/ansible/hosts 
  100  ls
  101  vi cisco.pem
  102  ls
  103  vi /etc/ansible/hosts 
  104  clear
  105  ansible all -m ping
  
  ansible s1,s2 -a "ls -la"
  117  ansible s1,r2 -m ping
  
  ================================================================

```




