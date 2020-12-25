 #### Setup Vagrant and connect to ansible-control server:

 `vagrant up` 
 `vagrant ssh ansible-control`
 
 #### Copy hosts file on ansible-control:

`cp /vagrant/hosts_file /etc/hosts`

 #### Install Ansible:

`sudo apt-get install ansible`

 #### Create a SSH key and copy to all servers:

`ssh-keygen`
`ssh-copy-id localhost`
`ssh-copy-id web01 && ssh-copy-id web02 && ssh-copy-id loadbalancer && ssh-copy-id db01`

 #### Run a ad-hoc command to the webstack group:
 
`ansible webstack -i hosts -m command -a hostname`

 #### Install python-simplejson:
 
`ansible all -i hosts -m command -a 'sudo apt-get -y install python-simplejson' `


 #### Install Services using APT module
https://docs.ansible.com/ansible/latest/modules/apt_module.html

`ansible all -i hosts --become -m apt -a "update_cache=yes"`
`ansible webservers -i hosts --become -m apt -a "name=apache2 state=present"`
`ansible database -i hosts --become -m apt -a "name=mysql-server state=present" `

 #### Restart Services using Service module
https://docs.ansible.com/ansible/latest/modules/service_module.html

`ansible database -i hosts -m service -a "name=mysql state=started"`
`ansible database --become -i hosts -m service -a "name=mysql state=restarted"`

 #### Use Ansible to reboot the webstack
`ansible webstack -i hosts --become -a "reboot --reboot"`

#### Run playbook
`ansible-playbook -i hosts -K playbook1.yml`

#### Test Connectivity to server
`curl web01:8000`


#### Create Apache2 Role, move tasks/handlers/templates to new roles/apache2 structure. Run playbook.
`ansible-galaxy init roles/apache2`
`ansible-playbook -i hosts -K playbook1.yml`

#### Create a 'common' Role and add it to 'web', 'loadbalancer' and 'database' servers
#### Use ansible-galaxy to create nginx role scaffolding
#### Setup tasks/main.yml and tasks/install_tools.yml

`ansible-galaxy init roles/nginx`
`ansible-playbook -i hosts -K playbook1.yml`

#### Create a nginx Role and setup the configuration
#### Use ansible-galaxy to create nginx role scaffolding
#### Setup tasks, handlers and templates

`ansible-galaxy init roles/nginx`
`ansible-playbook -i hosts -K playbook1.yml`

#### Check Current Tags

`ansible-playbook -i hosts -K playbook1.yml --list-tags`

#### Run Playbook for Specific Tags

`ansible-playbook -i hosts -K playbook1.yml --tags configuration`
`ansible-playbook -i hosts -K playbook1.yml --tags proxy`

Notes:
Tags use "OR" logic, not "AND". If any of your tags match the item will run. You should use unique tags if the item should only be run under specific conditions.

Special tags: always, never. Meaning "always" run or "never" run unless explicitly told to do so. Special tag keywords: tagged, untagged and all. By default we run playbooks at -tags all

[Reference](https://github.com/bradmorg/ansible-labs)
