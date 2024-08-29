```


  
  vault :
      
      
      
      
      ansible-vault -h
  634  clear
  635  ls
  636  ansible-vault -h
  637  ls
  638  cat first.yml 
  639  ansible-vault encrypt first.yml 
  640  ls
  641  cat first.yml 
  642  ls
  643  cat sec.yml 
  644  clear
  645  cat sec.yml 
  646  clear
  647  ls
  648  cat first.yml 
  649  ansible-playbook first.yml 
  650  ansible-playbook first.yml --ask-vault-pass
  651  ansible-vault decrypt first.yml 
  652  cat first.yml 
  653  clear
  654  cat first.yml 
  655  clear
  656  ls
  657  ansible-vault encrypt sec.yml 
  658  ansible-playbook sec.yml --ask-vault-pass
  659  clear
  660  ansible-vault -h
  661  ansible-vault view sec.yml 
  662  cat sec.yml 
  663  vi sec.yml 
  664  ansible-vault -h
  665  ansible-vault edit sec.yml 
  666  ansible-playbook --ask-vault-pass
  667  ansible-playbook sec.yml--ask-vault-pass
  668  ansible-playbook sec.yml --ask-vault-pass
  669  clear
  670  ansible-vault -h
  671  ansible-vault rekey sec.yml 
  672  cat sec.yml 
  
  
  
  
  =============================================
  
  
  
  with_dict :
      
    root@master:~# cat test2.yml 
---
- name: configure cisco dev
  hosts: devices
  gather_facts: no

  tasks:
    - name: gather the facts about interfaces
      ios_facts: 
        gather_subset: interfaces
      register: raman_ios_facts  

    - name: output all info
      debug:
        var: raman_ios_facts

    - name: print lineprotocol status for all interfaces
      debug:  
        msg: "Interface {{ item.key }} has lineprotocol: {{ item.value.lineprotocol }}"
      with_dict: "{{ raman_ios_facts.ansible_facts.ansible_net_interfaces }}"

    - name: configure interfaces if lineprotocol is down
      ios_config:
        lines:
          - description Configured by Khanna
          - no shutdown
        parents: 
          - "interface {{ item.key }}"
      ignore_errors: true
      with_dict: "{{ raman_ios_facts.ansible_facts.ansible_net_interfaces }}"
      when: item.value.lineprotocol == 'up'
      
      
      ==================================================================
      
      
      vaut - multi passwrd managemnt :
          
  
  

  712  clear
  713  cat /etc/ansible/hosts 
  714  vi inv 
  715  ls
  716  cat sec.yml 
  717  clear
  718  ansible-vault decrypt sec.yml 
  719  clear
  720  ls
  721  ansible-vault encrypt --vault-id invpassword@prompt inv 
  722  sec
  723  cat sec.yml 
  724  ansible-playbook sec.yml -i inv
  
  ansible-vault encrypt --vault-id ymlpassword@prompt sec.yml 
  
  ansible-playbook sec.yml -i inv --vault-id invpassword@prompt --vault-id ymlpassword@prompt


  
  725  ansible-playbook sec.yml -i inv --vault-id invpassword@prompt --vault-id ymlpassword@prompt
  726  ls
  727  clear
  728  ls
  729  ansible-vault encrypt --vault-id ymlpassword@prompt third.yml
  730  ansible-vault encrypt --vault-id ymlpassword2@prompt third.yml
  731  ansible-playbook third.yml -i inv --vault-id invpassword@prompt --vault-id ymlpassword2@prompt
  
  
  
  
  ============================================================================================
  
  encrypt_string :
      
  
  
     ansible devices -m ios_command -a "commands='sh run | i user'" 
     
     
     -- created a user manually from term :
         
         
         Cat8000V#sh run | i user
username admin privilege 15 secret 9 $9$lgJxy7Ga.Th5FU$gocFhcHC/8pvixGr.s2wB7X59FiGVvwYawfCPrmaJuY
Cat8000V#config
Configuring from terminal, memory, or network [terminal]?
Enter configuration commands, one per line.  End with CNTL/Z.
Cat8000V(config)#username testuser privilege 15 secret testpassword
Cat8000V(config)#end
Cat8000V#write memory
Building configuration...
[OK]
Cat8000V#sh run | i user
username admin privilege 15 secret 9 $9$lgJxy7Ga.Th5FU$gocFhcHC/8pvixGr.s2wB7X59FiGVvwYawfCPrmaJuY
username testuser privilege 15 secret 9 $9$gUi7KRbYad2.xU$mfBC/Ay2UEQ94o0TPL6oxrVs68q7cPv3cBzOyUDpt7I





root@master:~# ansible-vault encrypt_string --vault-id r1_userid@prompt 'testpassword' --name 'ansible_password'
New vault password (r1_userid): 
Confirm new vault password (r1_userid): 
Encryption successful
ansible_password: !vault |
          $ANSIBLE_VAULT;1.2;AES256;r1_userid
          33396430386434303066356534303266663636343130333733366639323438313463643430636263
          3730633765313661646163663935653265353537636338620a643936663166643062313761636663
          39326334636266623331613435356664626633366330623763646231393830333037636534633339
          6664383135353766300a623934336461663864343238623931663038613430326366636339306231
          6336

     
     
     
     
     -- we have to convert our host file into yaml format :and  replace the encrypted string in your hosts file : :
         
         
     previ inv file ini format :
         
         
         
         root@master:~# cat inv
[dev01]
r1 ansible_host=devnetsandboxiosxe.cisco.com ansible_network_os=ios ansible_connection=network_cli
[dev02]
r2 ansible_host=15.228.223.216  ansible_ssh_private_key_file="/root/cisco.pem" ansible_user=ec2-user ansible_network_os=ios ansible_connection=network_cli

[dev01:vars]
ansible_user=testuser 
ansible_password=testpassword

[demo]
s1 ansible_host=172.31.16.54
s2 ansible_host=172.31.19.132

[demo:vars]
ansible_user=root
ansible_password=raman




------new one with yaml format :
    
    root@master:~# cat hosts 
all:
  children:
    dev01:
      hosts:
        r1:
          ansible_host: devnetsandboxiosxe.cisco.com
          ansible_network_os: ios
          ansible_connection: network_cli
      vars:
        ansible_user: testuser
        ansible_password: !vault |
          $ANSIBLE_VAULT;1.2;AES256;r1_userid
          35373664656364656433373831323836396164373365323939313561343232343532396237376131
          3461333964626264613130626630626335393234656663320a376232613130333265306435626361
          64633664656334646163313735323264663031386461336563653638643338316134623130633761
          6366346335653065630a316237363862633832313530383235333562343262386265623339663935
          3362
    
    dev02:
      hosts:
        r2:
          ansible_host: 15.228.223.216
          ansible_ssh_private_key_file: "/root/cisco.pem"
          ansible_user: ec2-user
          ansible_network_os: ios
          ansible_connection: network_cli

    demo:
      hosts:
        s1:
          ansible_host: 172.31.16.54
        s2:
          ansible_host: 172.31.19.132
      vars:
        ansible_user: root
        ansible_password: raman
    
     
     
     

         
     
     
     
     ---- run playbook :
         
         ansible-playbook -i hosts fifth.yml --vault-id r1_userid@prompt
         
         
         root@master:~# cat fifth.yml 
- name: Network Getting Started First Playbook Extended
  connection: network_cli
  gather_facts: false
  hosts: dev01
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
        
         
         ===============================================================================================
     
     
     encrypt_string : without need of replacing whle string in inventry file :
         
         
         
          816  ansible-vault encrypt_string --vault-id r1_userid@prompt 'testpassword' --name 'enc_password' > vault_secrets.yml
  817  ls
  818  cat vault_secrets.yml 
  819  vi hosts
  820  ansible-playbook -i hosts fifth.yml --vault-id r1_userid@prompt 
  821  cat hosts
  822  cat vault_secrets.yml 
  823  vi hosts
  824  ansible-playbook -i hosts fifth.yml --vault-id r1_userid@prompt 
  825  vi hosts
  826  vi fifth.yml 
  827  ansible-playbook -i hosts fifth.yml --vault-id r1_userid@prompt 
  828  clear
  829  history
  
  
  
root@master:~# cat vault_secrets.yml 
enc_password: !vault |
          $ANSIBLE_VAULT;1.2;AES256;r1_userid
          62366163636165343066363733616362616335336565383538313066333935613435613332323434
          3833653439626531323830633038333261386439343866610a376532373465383634376637623763
          34373061646161643531643535666533353665363963626635623830643961323337646134363333
          3261306331646130650a336531343462393064343330646533383438326631643032386138656135
          6338
          
          
          
          
          
root@master:~# cat hosts
all:
  children:
    dev01:
      hosts:
        r1:
          ansible_host: devnetsandboxiosxe.cisco.com
          ansible_network_os: ios
          ansible_connection: network_cli
      vars:
        ansible_user: testuser
        ansible_password: "{{ enc_password }}"
    
    dev02:
      hosts:
        r2:
          ansible_host: 15.228.223.216
          ansible_ssh_private_key_file: "/root/cisco.pem"
          ansible_user: ec2-user
          ansible_network_os: ios
          ansible_connection: network_cli

    demo:
      hosts:
        s1:
          ansible_host: 172.31.16.54
        s2:
          ansible_host: 172.31.19.132
      vars:
        ansible_user: root
        ansible_password: raman
        
        
        
        
        
        
        
        
        
root@master:~# cat fifth.yml 
- name: Network Getting Started First Playbook Extended
  connection: network_cli
  gather_facts: false
  hosts: dev01
  vars_files:
    - vault_secrets.yml
  tasks:
    - name: Get config for IOS devices
      cisco.ios.ios_facts:
        gather_subset: all

    - name: Display the config
      debug:
        msg: "The hostname is {{ ansible_net_hostname }} and the OS is {{ ansible_net_version }}"
        
        
        
        
        
        
root@master:~# ansible-playbook -i hosts fifth.yml --vault-id r1_userid@prompt 
Vault password (r1_userid): 

PLAY [Network Getting Started First Playbook Extended] ********************************************************************************************************************

TASK [Get config for IOS devices] *****************************************************************************************************************************************
[WARNING]: ansible-pylibssh not installed, falling back to paramiko
ok: [r1]

TASK [Display the config] *************************************************************************************************************************************************
ok: [r1] => {
    "msg": "The hostname is Cat8000V and the OS is 17.12.02"
}

PLAY RECAP ****************************************************************************************************************************************************************
r1                         : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
     
     
     
     =========================================================
     
     
    
     • Asynchronous Action and Polling :


- name: Asynchronously Upgrade Cisco IOS Image
  hosts: cisco_ios
  gather_facts: no
  tasks:
    - name: Start IOS image upgrade
      ios_command:
        commands:  
          #start copying a new IOS image from a TFTP server to the device's flash memory and then reload the device.
          - copy tftp://192.168.1.100/ios_image.bin flash:
          - reload
      async: 1800  # Timeout in seconds (30 minutes)
      poll: 15     # Check task status every 15 seconds
      register: async_result

    - name: Check the status of the asynchronous upgrade
      async_status:
        jid: "{{ async_result.ansible_job_id }}"
      register: job_result
      until: job_result.finished
      retries: 30
      delay: 30  # Check every 30 seconds

    - name: Verify the new IOS image is present
      ios_command:
        commands:
          - dir flash:
      register: dir_output

    - name: Display the IOS image directory
      debug:
        var: dir_output.stdout_lines




============================================




root@master:~# cat 13th.yml 
---
- name: Manage Cisco IOS device
  hosts: devices
  gather_facts: no
  connection: network_cli
  tasks:

    - name: Ensure the device is reachable
      ping:
      tags: 
        - connectivity

    - name: Backup the current configuration
      ios_config:
        backup: yes
        backup_options:
          filename: backup.cfg
      tags: 
        - backup

    - name: Configure hostname
      ios_config:
        lines:
          - hostname Router01
      tags: 
        - config
        - hostname

    - name: Apply interface configuration
      ios_config:
        lines:
          - description Connected to LAN
        parents: interface GigabitEthernet1
      tags: 
        - config
        - interface

    - name: Save running configuration to startup
      ios_command:
        commands:
          - write memory
      tags: 
        - save
        
        
        =====================================================
        
        upload my role :
            
            
    --- custm role was already present 
    -- i created a github repo and cloned it on my local as well
    -- moved the contents of my custom role to the cloned directory
    -- add , commit , push to remote repo
    
    --- went to import role > ansible galaxy :   just provide the name of rle and import ...
    
    
            
        
        
        ansible-galaxy search cisco
  852  lear
  853  clearclear
  854  learcc
  855  clear
  856  ls
  857  mv raman_role/ lumen_role_26-30th
  858  ls
  859  cd lumen_role_26-30th/`
  860  cd lumen_role_26-30th/ls
  861  cd lumen_role_26-30th/
  862  ls
  863  vi README.md 
  864  cd ..
  865  ls
  866  git clone https://github.com/ramannkhanna2/lumen_role_26-30th.git
  867  ls
  868  mv lumen_role_26-30th/ lumen_role_26-30
  869  ls
  870  git clone https://github.com/ramannkhanna2/lumen_role_26-30th.git
  871  ls
  872  mv lumen_role_26-30/* lumen_role_26-30th/
  873  ls
  874  cd lumen_role_26-30
  875  ls
  876  cd ..
  877  ls
  878  cd lumen_role_26-30
  879  cd ..
  880  cd lumen_role_26-30th/
  881  ls
  882  ls -a
  883  git remote -v
  884  ls
  885  git status
  886  ls
  887  git add .
  888  git status
  889  git commit -m "files added by raman"
  890  git config --global user.email "ramanmail.com"
  891  git config --global user.name "raman"
  892  git commit -m "files added by raman to local git repo"
  893  git status
  894  git push origin main
  895  clear
        
        
        
        ==================================================================
        
        
        download and test :
            
            
            
            ansible-galaxy role install geerlingguy.apache
  899  ls
  900  cd ..
  901  ls
  902  vi /etc/ansible/ansible.cfg 
  903  ls -a
  904  cd .ansible/
  905  ls
  906  cd roles/
  907  ls
  908  cd geerlingguy.apache/
  909  ls
  910  clear
  911  cd ..
  912  ls
  913  cd ..
  914  ls
  915  cd ..
  916  ls
  917  vi playbook.yml 
  918  ansible-playbook -i inv playbook.yml 
  919  ansible demo -a "apt remove apache"
  920  ansible demo -a "apt remove apache2"
  921  ansible demo -a "apt remove apache2" -i inv
  922  cd .ansible/roles/geerlingguy.apache/
  923  ls
  924  cd defaults/
  925  ls
  926  vi main.yml 
  927  cd ~
  928  ansible-playbook -i inv playbook.yml 
  
  
  ============================================

```
