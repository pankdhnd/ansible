### Disable ssh authenticity check for ansible. Recommended for cases where servers are short lived
#### Global Configuration
1. Edit /etc/ansible/ansible.cfg
2. search for parameter `host_key_checking` and set it to false  
   `host_key_checking = False`

Above parameter might be commented, so you might want to uncomment it.

#### Local Configuration
1. Create file '~/.ansible.cfg' (this file is not created automatically in newer version of ansible). The best and recommended way is to create a ansible.cfg file in each individual project, so that configurations for every project can be maintained separately without any conflict. Example is in my another repo named ansible
2. Add below parameter to the file  
   `host_key_checking` = False
3. Save and close.

### Simple connectivity check using ping
```bash
ansible all -i <inventory file name> -m ping
```

### To run ansible playbook
```bash
ansible-playbook -i hosts <playbook-name.yaml>
```

### To execute tasks as root user, you will have to switch to root from the playbook, for that you have to use 'become' in your playbook yaml. below is the example of such playbook:

```yaml
- name: Deploy and configure nginx web server
  hosts: local
  tasks: 
  - name: Install nginx server
    become: True
    become_user: root
    apt: 
      name: nginx
      state: latest
   ```

### If you have any password set for root user, then you have to provide -K parameter while running the playbook so that you will be prompted to input the passowrd  
```bash
ansible-playbook -i hosts <playbook-name.yaml> -K
```

### Instead of writing each module command on new line, we can also club it as given below example:
```yaml
 - name: Deploy NodeJs application on remote machine
   hosts: local
   tasks:
   - name: Perform sudo apt update
     become: True
     become_user: root
     apt: update_cache=yes force_apt_get=yes
```

### To install multiple packages, we can provide a list of packages in the playbook as given below. Refer documentation on Ansible website to know more
```yaml
   - name: Instll NodeJs and npm
     become: True
     become_user: root
     apt:
       pkg:
       - nodejs
       - npm
```
### Copy module to copy a file from local machine to remote
```yaml
  tasks:
  - name: Copy the tar file on remote
    copy:
      src: /home/pankaj/data/nodejs-app-1.0.0.tgz
      dest: /home/pankaj   #Optionally we can specify a filename here. If the filename is different from the source filename, then module will rename it after copy
```

### Unarchieve module to untar the tarballs
```yaml
  - name: Unpack tar file
    unarchive:
      src: /home/pankaj/nodejs-app-1.0.0.tgz
      dest: /home/pankaj   
      remote_src: yes      #specifies that the package to be unarchieved is located on remote machine, otherise it look for the pckage on local machine
```

### We can perform copy and unarchieve both operations in a single task using unarchieve module as below
```yaml
tasks:
  - name: Copy and unpack tar file
    unarchive:
      src: /home/pankaj/data/nodejs-app-1.0.0.tgz   #Source is local machine. Unarchieve will copy file to remote, and will then un-tar it.
      dest: /home/pankaj
```

### Command module vs Shell module
```Command module just executes the command on remote machine, but if you want to use shell environment variables, pipe operators, redirect operators or boolean operators then we must use shell module. The shell module executes command inside the shell. But command module is more secure becuase it run commands in isolated manner and does not run directly on shell. The shell module is more open to use and hence vulnerable to shell injection. So for security concerns, better to use command wherever we can```