## Disable ssh authenticity check for ansible. Recommended for cases where servers are short lived
### Global Configuration
1. Edit /etc/ansible/ansible.cfg
2. search for parameter `host_key_checking` and set it to false  
   `host_key_checking = False`

Above parameter might be commented, so you might want to uncomment it.

### Local Configuration
1. Create file '~/.ansible.cfg' (this file is not created automatically in newer version of ansible). The best and recommended way is to create a ansible.cfg file in each individual project, so that configurations for every project can be maintained separately without any conflict. Example is in my another repo named ansible
2. Add below parameter to the file  
   `host_key_checking` = False
3. Save and close.

## To run ansible playbook
`ansible-playbook -i hosts <playbook-name.yaml>`

## To execute tasks as root user, you will have to switch to root from the playbook, for that you have to use 'become' in your playbook yaml. below is the example of such playbook:

```yaml
- name: Deploy and configure nginx web server
  hosts: local
  tasks: 
  - name: Install nginx server
    become: True
    become_user: root
    apt: 
      name: nginx
      state: latest```

## If you have any password set for root user, then you have to provide -K parameter while running the playbook so that you will be prompted to input the passowrd  
`ansible-playbook -i hosts <playbook-name.yaml> -K`