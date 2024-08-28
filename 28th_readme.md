```

 
  conditionals :
      
      
      
      
      root@master:~# cat sixth.yml 
---
- name: configure cisco dev
  hosts: devices
  gather_facts: no

  tasks:
  - name: gather the facts abt interface
    ios_facts: 
      gather_subset: interfaces
    register: raman_ios_facts  
  - name: print ios_facts for debugging
    debug: 
      var: raman_ios_facts
  #   var: raman_ios_facts.ansible_facts.ansible_net_hostname
  #   msg: " hostname: {{ raman_ios_facts.ansible_facts.ansible_net_hostname }}"
  - name: configure interface1 if lineprotocol is down
    ios_config:
      lines:
      - description Configured by Ansible
      - no shutdown
    ignore_errors: true    
    when: >
       raman_ios_facts.ansible_facts.ansible_net_interfaces is defined and
       raman_ios_facts.ansible_facts.ansible_net_interfaces['GigabitEthernet1'] is defined and 
       raman_ios_facts.ansible_facts.ansible_net_interfaces['GigabitEthernet1'].lineprotocol == 'down'
  - name: print ios_facts for debugging
    debug:
      msg: " lineprotocol : {{ raman_ios_facts.ansible_facts.ansible_net_interfaces['GigabitEthernet1'].lineprotocol }} "     
         
         
         
         
         
         
         
         
         
         
         
         
         vi sixth.yml
  386  vi inv
  387  ansible -m ping -i inv
  388  ansible all -m ping -i inv
  389  clear
  390  vi /etc/ansible/hosts 
  391  ansible all -m ping
  392  clear
  393  vi sixth.yml 
  394  ansible-playbook sixth.yml --check
  395  vi sixth.yml 
  396  ansible-playbook sixth.yml --check
  397  vi sixth.yml 
  398  clear
  399  ansible-playbook sixth.yml --check
  400  vi sixth.yml 
  401  ansible-playbook sixth.yml --check
  402  ansible-playbook sixth.yml 
  403  vi sixth.yml 
  404  clear
  405  ansible-playbook sixth.yml 
  406  clear
  407  ansible-playbook sixth.yml 
  408  vi sixth.yml 
  409  clear
  410  ansible-playbook sixth.yml 
  411  vi sixth.yml 
  412  clear
  413  ansible-playbook sixth.yml 
  414  cat sixth.yml 
  415  clear
  416  ansible-playbook sixth.yml -vv
  417  clear
  418  ansible-playbook sixth.yml -vvv
  419  clear
  420  cat /etc/ansible/hosts 
         
         
         
         
         ==============================================
  
  
  
  
  
  
  root@master:~# cat sixth.yml 
---
- name: configure cisco dev
  hosts: devices
  gather_facts: no

  tasks:
  - name: gather the facts abt interface
    ios_facts: 
      gather_subset: interfaces
    register: raman_ios_facts  
  - name: print ios_facts for debugging
    debug: 
      var: raman_ios_facts
  #   var: raman_ios_facts.ansible_facts.ansible_net_hostname
  #   msg: " hostname: {{ raman_ios_facts.ansible_facts.ansible_net_hostname }}"
  - name: configure interface1 if lineprotocol is down
    ios_config:
      lines:
      - description Configured by Ansible
      - no shutdown
    ignore_errors: true    
    when: >
       raman_ios_facts.ansible_facts.ansible_net_interfaces is defined and
       raman_ios_facts.ansible_facts.ansible_net_interfaces['GigabitEthernet1'] is defined and 
       raman_ios_facts.ansible_facts.ansible_net_interfaces['GigabitEthernet1'].lineprotocol == 'down'
  - name: print ios_facts for debugging
    debug:
      msg: " lineprotocol : {{ raman_ios_facts.ansible_facts.ansible_net_interfaces['GigabitEthernet1'].lineprotocol }} "     
  - name: generate CSV content
    set_fact:
      csv_content: |
        interface,lineprotocol
        {% for interface, details in raman_ios_facts.ansible_facts.ansible_net_interfaces.items() %}
        {{ interface }},{{ details.lineprotocol }}
        {% endfor %}
  - name: write the CSV file
    copy:
      content: "{{ csv_content }}"
      dest: "/root/interfaces.csv"      
    
    
    
    
      ===========================================================================
      
  
  
  
  
  
  root@master:~# cat sev.yml 
---
- name: configure cisco dev
  hosts: devices
  gather_facts: no

  tasks:
  - name: gather the facts abt interface
    ios_facts: 
      gather_subset: interfaces
    register: raman_ios_facts  
  - name: print ios_facts for debugging
    debug: 
      var: raman_ios_facts
  #   var: raman_ios_facts.ansible_facts.ansible_net_hostname
  #   msg: " hostname: {{ raman_ios_facts.ansible_facts.ansible_net_hostname }}"
  #
  #
  - name: get the runnig conf
    ios_command: 
      commands: show running-config | include hostname
    register: running_config

  - name: output of run-config
    debug:
      var: running_config
  
  - name: hostame if not already set
    ios_config:
      lines:
        - hostname ramanrouter
    when: "'hostname ramanrouter' not in  running_config.stdout"

  - name: configure interface1 if lineprotocol is down
    ios_config:
      lines:
      - description Configured by Ansible
      - no shutdown
    ignore_errors: true    
    when: >
       raman_ios_facts.ansible_facts.ansible_net_interfaces is defined and
       raman_ios_facts.ansible_facts.ansible_net_interfaces['GigabitEthernet1'] is defined and 
       raman_ios_facts.ansible_facts.ansible_net_interfaces['GigabitEthernet1'].lineprotocol == 'down'
  - name: print ios_facts for debugging
    debug:
      msg: " lineprotocol : {{ raman_ios_facts.ansible_facts.ansible_net_interfaces['GigabitEthernet1'].lineprotocol }} "    
      
      
      
      
      
      ===================================================================================================
      
      
      loops :
          
          
          
    
  
  

- name: Configure multiple interfaces on Cisco IOS devices
  hosts: devices
  gather_facts: no
  connection: network_cli

  vars:
    interfaces:
      - name: GigabitEthernet1
        description: Configured by RamanKhanna
      - name: GigabitEthernet2
        description: Configured by RamanKhanna
      - name: GigabitEthernet3d by RamanKhanna
        description: Configured by Ansible
      - name: GigabitEtherneted by RamanKhanna
        description: Configured by Ansible    

  tasks:
    - name: Gather facts about interfaces
      cisco.ios.ios_facts:
        gather_subset: interfaces
      register: ios_facts

    - name: Print ios_facts for debugging
      debug:
        var: ios_facts

    - name: Configure interfaces
      cisco.ios.ios_config:
        lines:
  #        - "no shutdown"
        parents: "interface {{ item.name }}"
      loop: "{{ interfaces }}"

      when: >
        ios_facts.ansible_facts.ansible_net_interfaces is defined and
        ios_facts.ansible_facts.ansible_net_interfaces[item.name] is defined and
        # ios_facts.ansible_facts.ansible_net_interfaces[item.name].operstatus == 'administratively downi'
        ios_facts.ansible_facts.ansible_net_interfaces[item.name].operstatus == 'up'
        
        
        
        
        
        
        
        ansible devices -m ios_facts -a "gather_subset=interfaces"
        
        
        
    -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    
    
    root@master:~# cat ninth.yml 
---
- name: Create multiple users on Cisco IOS devices
  hosts: devices
  gather_facts: no
  connection: network_cli

  vars:
    users:
      - name: admin1
        password: admin1password
        privilege: 15
      - name: admin2
        password: admin2password
        privilege: 15
      - name: operator
        password: operatorpassword
        privilege: 5

  tasks:
    - name: Create users
      cisco.ios.ios_user:
        name: "{{ item.name }}"
          #    configured_password: "{{ item.password }}"
          #   privilege: "{{ item.privilege }}"
        state: absent
          #     update_password: always
      loop: "{{ users }}"


===============================================    
  
  
  
  handlers :
      
      
      root@master:~# cat 11th.yml 
---
- name: Configure Cisco IOS Devices
  hosts: devices
  gather_facts: no
  connection: network_cli
  tasks:
    - name: Check if GigabitEthernet1 is present
      cisco.ios.ios_command:
        commands: show ip interface brief
      register: interface_status

    - name: output_first task
      debug: 
        var: interface_status

    - name: Configure interface GigabitEthernet1
      cisco.ios.ios_interfaces:
        config:  
        - name: GigabitEthernet1
          description: "Configured by RamanK "
          enabled: yes
        state: merged
      when: "'GigabitEthernet1' in interface_status.stdout[0]"
      notify: Restart network

    - name: Configure interface GigabitEthernet2 if present
      cisco.ios.ios_interfaces:
        config:
        - name: GigabitEthernet2
          description: "Configured by Ansible"
          enabled: yes
        state: merged
      when: "'GigabitEthernet2' in interface_status.stdout[0]"
      notify: Restart network

    - name: Get interface descriptions
      cisco.ios.ios_command:
        commands:
          - show interfaces description
      register: interface_descriptions

    - name: Display interface descriptions
      debug:
        msg: "{{ interface_descriptions.stdout[0] }}"    

  handlers:
    - name: Restart network
      cisco.ios.ios_command:
        commands:
          - "write memory"
          - "reload"
      timeout: 300  # Increase timeout to 300 seconds (5 minutes)
      
      
      
      
      ---------------------------------------------------------------------------------------------------------
      
      
  
  root@master:~# cat 12th.yml 
- hosts: demo
  gather_facts: true
  vars:
    package: apache2
  tasks:
    - name: Update apt package index
      apt:
        update_cache: yes

    - name: Install apache2 package
      package:
        name: "{{ package }}"
        state: present

    - name: Configure hostname and IP in webpage
      template:
        src: index.html.j2
        dest: /var/www/html/index.html
      notify: Restart apache2 service

  handlers:
    - name: Restart apache2 service
      service:
        name: apache2
        state: restarted
        
        
        
        
        
root@master:~# cat index.html.j2 
<!DOCTYPE html>
<html>
  <head>
    <title>Server Information</title>
  </head>
  <body>
    <h1>Server Information</h1>
    <p>Hostname: {{ ansible_facts['ansible_nodename'] | default('Unknown') }}</p>
    <p>IP Address: {{ ansible_facts['default_ipv4']['address'] | default('Unknown') }}</p>
  </body>
</html>

=====================================================================
  
  
      
      Roles :

-- Role Creation:

ansible-galaxy init ios_config


root@master:~/ios_config# tree
.
├── README.md
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   └── main.yml
├── templates
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml

8 directories, 8 files






-- Role Based Tasks :

root@master:~/ios_config/tasks# cat main.yml 
---
# tasks file for ios_config


#- name: "Include backup role if user is network_admin"
#  include_role:
#    name: backup_config
#  when: ansible_user == "network_admin"


- name: "Check if {{ interface1 }} is present"
  cisco.ios.ios_command:
    commands: show ip interface brief
  register: interface_status

- name: "Configure interface {{ interface1 }}"
  cisco.ios.ios_interfaces:
    config:  
      - name: "{{ interface1 }}"
        description: "Configured by {{ ansible_user }} "
        enabled: yes
    state: merged
  when: "'GigabitEthernet1' in interface_status.stdout[0]"
  notify: Restart network

- name: "Configure interface {{ interface2 }} if present"
  cisco.ios.ios_interfaces:
    config:
      - name: "{{ interface2 }}"
        description: "Configured by Ansible"
        enabled: yes
    state: merged
  when: "'GigabitEthernet2' in interface_status.stdout[0]"
  notify: Restart network

- name: Get interface descriptions
  cisco.ios.ios_command:
    commands:
      - show interfaces description
  register: interface_descriptions

- name: Display interface descriptions
  debug:
    msg: "{{ interface_descriptions.stdout[0] }}"







-- Task Order - Pre and Post Tasks


root@master:~# cat playbook.yml 
# playbook.yml
---
- hosts: ios_devices
  gather_facts: no

  pre_tasks:
    - name: Pre-task example
      debug:
        msg: "Starting configuration of Cisco devices"

  roles:
    - ios_config

  post_tasks:
    - name: Post-task example
      debug:
        msg: "Configuration complete"




-- •  Roles - Variable Substitution :


root@master:~/ios_config# cat vars/main.yml 
# vars file for ios_config
---
interface1: GigabitEthernet1
interface2: GigabitEthernet2


root@master:~/ios_config# cat defaults/main.yml 
---
# defaults file for ios_config
ansible_user: "default_user"




-- •  Roles - Handlers :


root@master:~/ios_config# cat handlers/main.yml 
---
# handlers file for ios_config
- name: Restart network
  cisco.ios.ios_command:
    commands:
      - "write memory"
      - "reload"
  timeout: 300  # Increase timeout to 300 seconds (5 minutes)





-- •  Roles - Passing Variables from Command Line

ansible-playbook -i inventory playbook.yml -e "interface1=GigabitEthernet3 ansible_user=admin_user"





root@master:~# cat sec.yml 
---
- name: starting
# connection: network_cli
  hosts: devices
# roles_path: 

  gather_facts: false
  vars_prompt:
    - name: "ansible_user"
      prompt: "Please enter the Ansible user"
  tasks:
  - name: get config for ios dev
    ios_facts:
      gather_subset: interfaces
  - name: display the conf 
    debug: 
      #   msg: "the hostname is {{ ansible_net_hostname }} and the os is {{ ansible_net_version }}"
      msg: "all ipv4 addresses: {{ ansible_net_all_ipv4_addresses }} " 
  - name: Execute raman-role
    include_role:
      name: raman_role
      role: /root/        (# else u can mention path in cfg file as roles_path tab there or above under hosts tab like shown  )
    ignore_errors: true
    register: ntp_result
  - name: output of roles execution
    debug: 
     var: ntp_result
     
     
     
     ==============================

```
