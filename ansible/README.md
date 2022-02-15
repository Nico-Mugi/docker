# Connect via ssh
Modify rights for ssh key : ``sudo chmod 400 <path_to_key>``   
Connect : ``sudo ssh -i <path_to_key> centos@nicolas.thouvenin.takima.cloud``   

# Edit host file 
/etc/ansible/hosts : add centos@nicolas.thouvenin.takima.cloud

# Setup
## Apache
Install apache : ``ansible all -m yum -a "name=httpd state=present" --private-key=<path_to_key> -u centos --become``   
Start apache : ``ansible all -m service -a "name=httpd state=started" --private-key=./id_rsa -u centos --become``   
## YML
In ansible/inventories/setup.yml :   
```yml
all:
  vars:
    ansible_user: centos
    ansible_ssh_private_key_file: <path_to_key>
  children:
    prod:
      hosts: nicolas.thouvenin.takima.cloud
```    
In ansible/playbook :
```yml 
- hosts : all
  gather_facts: false
  become: yes

  roles: 
  #order is important
    - docker
    - network
    - database
    - backend
    - httpd
```   
Launch a playbook : ``ansible-playbook -i ansible/inventories/setup.yml ansible/playbook.yml``   
## Roles
Create roles : ``ansible-galaxy init ansible/roles/docker``   
In each role you created, in `roleName/tasks`, configure your tasks : 
### Network 
```yml
# tasks file for roles/network
- name: Create network app-network
  docker_network:
    name: app-network
```
### HTTPD 
```yml
# tasks file for roles/httpd
- name: Run httpd
  docker_container:
    name: httpd
    image: nicolastvn/tp-devops-cpe-httpd
    ports: 80:80
    state: started
    networks:
      - name: app-network
```
### Front 
```yml
# tasks file for roles/front

- name: Launch frontend
  docker_container:
    name: front
    image: nicolastvn/tp-devops-cpe-front
    state: started
    networks:
      - name: app-network
```
### Docker 
```yml
# tasks file for roles/docker
- name: Clean packages
  command:
    cmd: dnf clean -y packages

- name: Install device-mapper-persistent-data
  dnf:
    name: device-mapper-persistent-data
    state: latest
- name: Install lvm2
  dnf:
    name: lvm2
    state: latest
- name: add repo docker
  command:
    cmd: sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
- name: Install Docker
  dnf:
    name: docker-ce
    state: present
- name: install python3
  dnf:
    name: python3
- name: Pip install
  pip:
    name: docker
- name: Make sure Docker is running
  service: name=docker state=started
  tags: docker
```
### Database 
```yml
# tasks file for roles/database
- name: Launch database
  docker_container:
    name: database
    image: nicolastvn/tp-devops-cpe-database
    state: started
    env:
      POSTGRES_DB: db
      POSTGRES_USR: usr
      POSTGRES_PASSWORD: pwd
    networks:
      - name: app-network
```
### Backend 
```yml
# tasks file for roles/backend
- name: Launch backend
  docker_container:
    name: backend
    image: nicolastvn/tp-devops-cpe-backend
    state: started
    networks:
      - name: app-network
```

