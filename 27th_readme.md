```

    
    
Second_day :
    
    
    
    ansible-doc -l | grep ping
  149  clear
  150  ansible-doc -v ping
 
  155  ansible demo -m user -a "name=raman state=present comment=\"created via ansible cli\" group=root shell=/bin/bash"
  156  ansible demo -m user -a "name=raman state=present comment=\"created by Raman\" group=root shell=/bin/bash"

  160  ansible devices -m ios_user -a "name=raman state=present privilege=15 configured_password=admin1 password_type=password"

  164  ansible demo -m user -a "name=raman state=absent comment=\"created by Raman\" group=root shell=/bin/bash"
  165  ansible s1 -m setup
  166  clear
  167  ansible demo -m apt -a "name=telnet state=installed"
  168  ansible demo -m apt -a "name=telnet state=present"
  169  ansible demo -m apt -a "name=telnet state=absent"
  170  ansible demo -m apt -a "name=telnet state=present"
  171  clear
  172  ansible demo -m setup -a filter="ansible_distribution"
  173  clear
  174  ansible devices -m setup 
  175  clear
  176  ansible r1 -m setup 
  177  ansible demo -m setup -a filter="ansible_default_ipv4"
  178  clear
  179  ansible devices -m setup -a filter="ansible_default_ipv4"

  182  ansible demo -m file -a "path=/var/tmp/gagandeep mode=777 group=root state=touch" 
  183  ansible demo -m file -a "path=/var/tmp/gagandeep mode=777 group=root state=absent" 
    
    

    
    ansible demo -m command -a uptime
    ansible demo -m command -a uptime
    
    
    
    ==============================================================================
    
    
    
    
    ansible devices -m ios_command -a "commands='sh run | i user '"
  199  vi /etc/ansible/ansible.cfg 
  200  ansible devices -m ios_command -a "commands='sh run | i user '"
  201  vi /etc/ansible/hosts 
  202  ansible devices -m ios_facts 
  203  ansible devices -m ios_facts -a "gather_subset=hardware"
  204  clear
  205  cat first.yml 
  206  ansible devices -m ios_command -a "commands='sh ip int brief'"
  207  clear
  208  cat first.yml 
  209  vi sec.tf
  210  vi /etc/ansible/ansible.cfg 
  211  ls
  212  mv sec.tf sec.yml
  213  ls
  214  clear
  215  vi /etc/ansible/ansible.cfg 
  216  vi sec.yml 
  217  ansible-playbook sec.yml 
  218  ls
  219  vi sec.yml 
  220  ansible-playbook sec.yml 
  221  ansible devices -m ios_facts -a "gather_subset=interface"
  222  ansible devices -m ios_facts -a "gather_subset=interfaces"
    
    
    
    
    
    
    
    root@master:~# cat first.yml 
---
- hosts: demo
  tasks:
  - name: Install apache2 on server
    action: apt name=apache2 state=present
  - name: add file
    action: file path=/opt state=directory
  - name: Install nginx on server
    action: apt name=nginx state=present
    
    
    
    
    
    
    
root@master:~# cat sec.yml 
---
- name: starting
# connection: network_cli
  hosts: devices
  gather_facts: false
  tasks:
  - name: get config for ios dev
    ios_facts:
      gather_subset: all
  - name: display the conf 
    debug: 
      msg: "the hostname is {{ ansible_net_hostname }} and the os is {{ ansible_net_version }}"
    
    
    
    
    
    
    
    
    root@master:~# cat sec.yml 
---
- name: starting
# connection: network_cli
  hosts: devices
  gather_facts: false
  tasks:
  - name: get config for ios dev
    ios_facts:
      gather_subset: interfaces
  - name: display the conf 
    debug: 
      #   msg: "the hostname is {{ ansible_net_hostname }} and the os is {{ ansible_net_version }}"
      msg: "all ipv4 addresses: {{ ansible_net_all_ipv4_addresses }} " 
    
    
    ansible-playbook sec.yml
    
    
    ======================================================================================
    
    
    previlege escalation :
        
        
        root@master:~# cat first.yml 
---
- hosts: demo
  user: raman
  become_user: khanna
  become_method: true
    #  become: true  
  tasks:
  - name: Install apache2 on server
    action: apt name=apache2 state=absent
  - name: add file
    action: file path=/root/test state=present
  - name: Install nginx on server
    action: apt name=nginx state=absent
    
    
    
    =======================================================
    
    
    
    
    
    
    
    
    root@master:~# cat third.yml 
- hosts: demo
  vars:
    pkgname: tree
    user1: gagan
    user2: ""
    uid1: 9876
    uid2: 8766
  tasks:
  - name: install tree 
    snap: 
      name: '{{pkgname}}' 
      state: present

  - name: Creating a user1
    user: name={{user1}} uid={{uid1}} state=present
  - name: Creating user {{user2}}
    user: name={{user2}} uid={{uid2}} state=present
    
    
    
    
    ansible-playbook --syntax-check first.yml 
  286  ansible-playbook  first.yml --check
  287  vi first.yml 
  288  ansible-playbook  first.yml --check
  289  ansible-playbook  first.yml --step
  290  clear
  291  ls
  292  vi third.yml
  293  ansible-playbook third.yml --check
  294  vi third.yml 
  295  ansible-playbook third.yml --check
  296  vi third.yml 
  297  clear
  298  ansible-playbook third.yml --check
  299  ansible-playbook third.yml 
  300  cat third.yml 
  301  ansible -h
  302  clear
  303  ansible-playbook third.yml -e user2="gagan"
  304  ansible-playbook third.yml -e user1="raman"
  305  ansible-playbook third.yml -e user1="raman" -e user2="gaga"
  306  vi third.yml 
  307  ansible-playbook third.yml -e user1="raman" -e user2="gagan"
  308  cat /etc/ansible/hosts 
  309  clear
  310  cat /etc/ansible/hosts 
  311  clear
  05  ansible-playbook third.yml -e user1="raman" -e user2="gaga"
  306  vi third.yml 
  307  ansible-playbook third.yml -e user1="raman" -e user2="gagan"
  
  
  ----------------------------------
  
  
  
  
  
  
  
  
  
  
  
  ==========================================
  
  
  
  
  
  
  root@master:~# cat inv
[dev01]
d1 ansible_host=devnetsandboxiosxe.cisco.com

[dev02]
d2 ansible_host=18.230.113.45

[dev_group:children]
dev01
dev02

[dev01:vars]
ansible_user="admin"
ansible_connection=network_cli
ansible_password="C1sco12345"
ansible_network_os="ios"

[dev02:vars]
ansible_user="ec2-user"
ansible_ssh_private_key_file="/root/cisco.pem"
ansible_connection=network_cli
ansible_network_os="ios"










root@master:~# cat fifth.yml 
- name: Network Getting Started First Playbook Extended
  connection: network_cli
  gather_facts: false
  hosts: dev_group
  vars:
    username: ansible
    privilege: 15
    password: ansible
  tasks:
    - name: Get config for IOS devices
      cisco.ios.ios_facts:
        gather_subset: all

    - name: Display the config
      debug:
        msg: "The hostname is {{ ansible_net_hostname }} and the OS is {{ ansible_net_version }}"

    - name: chngehostname 
      ios_config:
        lines:
        - hostname ANSIBLE_LUMEN

    - name: Create Username
      cisco.ios.ios_config:
        backup: yes
        lines:
          - username {{ username }} priv {{ privilege }} secret {{ password }}

    - name: Get changed config for IOS devices
      cisco.ios.ios_facts:
        gather_subset: all
      register: res

    - name: Display changed config
      debug:
        #  msg: "{{ res.ansible_facts.ansible_net_config }}"
        var: res.ansible_facts.ansible_net_config

    - name: run show users
      ios_command:
        commands: sh run | i user      
      register: rk

    - name: Display changed config
      debug:
        var: rk    
  
  
  
  
  
  ansible-playbook -i inv fifth.yml 
  360  clear
  361  ls
  362  cd backup/
  363  ls
  364  cd ..
  365  ls
  366  cat inv
  367  ansible-playbook -i inv fifth.yml -l dev01
  368  ansible-playbook -i inv fifth.yml -l dev02
  369  vi fifth.yml 
  370  ansible-playbook -i inv fifth.yml -l dev02
  371  vi fifth.yml 
  372  ansible-playbook -i inv fifth.yml -l dev02
  373  vi fifth.yml 
  374  clear
  375  ansible-playbook -i inv fifth.yml -l dev02
  376  vi fifth.yml 
  377  ansible-playbook -i inv fifth.yml -l dev02
  378  vi fifth.yml 
  379  ansible-playbook -i inv fifth.yml -l dev02
  380  ls
  381  cat inv
  
  ==============================================

```
