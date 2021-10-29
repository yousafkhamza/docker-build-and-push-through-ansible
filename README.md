# Docker Image Build and Push the Image to DockerHub using Ansible 
[![Build](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

---
## Description
It's just an ansible playbook and that the playbook installs docker and builds a nodejs image from a docker file and that docker file fetched from an another repository and after then that image push to your docker hub. I just do this for those who studied ansible and docker from scratch that kind of guy can helpful. It's just creating a docker image through ansible and then pushing that same to the Docker hub.

----
## Feature
- Easy to build a docker image from docker file (from another git repo) and push the image to your docker hub
- All these plays are using inbuilt ansible modules 
- Docker installation and image build and push through the same playbook

----
## Pre-Requests
- Install Ansible on your Master Machine
- Basic Knowledge of ansible
- Basic Knowledge of docker
- Need a docker hub account

##### Installation
[Ansible2](https://docs.ansible.com/ansible/2.3/index.html) (For your reference visit [How to install Ansible](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html))

##### How to Create DockerHub Account.
DockerHub Account [SignUp]("https://hub.docker.com/signup")

### Packages 
```sh
amazon-linux-extras install -y ansbile2
yum install -y git
```

##### Ansible Modules used
- [yum](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yum_module.html) 
- [pip](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/pip_module.html)
- [service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
- [git](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/git_module.html)
- [docker_image](https://docs.ansible.com/ansible/2.8/modules/docker_image_module.html)
- [docker_login](https://docs.ansible.com/ansible/2.9/modules/docker_login_module.html)
----
## How to use
```sh
git clone https://github.com/yousafkhamza/docker-build-and-push-through-ansible.git
cd docker-build-and-push-through-ansible
```
```sh
ansible-playbook docker.yml         <------------- playbook running script
```

----
## Output be like
```sh
# ansible-playbook docker.yml

PLAY [Installing docker through ansible] ************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************
ok: [localhost]

TASK [PIP Installation] *****************************************************************************************************************
changed: [localhost]

TASK [Depandancy Task-Install Dokcer-py] ************************************************************************************************
changed: [localhost]

TASK [Docker Installtion] ***************************************************************************************************************
ok: [localhost]

TASK [Docker Service start and Enable] **************************************************************************************************
changed: [localhost]

TASK [Build-Step - Cloning Repository] **************************************************************************************************
changed: [localhost]

TASK [Build-Step - Login to remote Repository] ******************************************************************************************
changed: [localhost]

TASK [Build Docker image from Dockerfile] ***********************************************************************************************
changed: [localhost]

PLAY RECAP ******************************************************************************************************************************
localhost                  : ok=8    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```
## Image was created from playbok
```sh
[ec2-user@ip-172-31-95-29 ~]$ sudo docker image ls
REPOSITORY            TAG       IMAGE ID       CREATED             SIZE
myimage               v1        80405cc473d0   About an hour ago   50.8MB
yousafkhamza/dapp     1         80405cc473d0   About an hour ago   50.8MB
```

----
## Behind the code
_vim docker.yml_
```sh
---
- name: 'Installing docker through ansible'
  hosts: localhost
  become: true
  vars:
    docker_user: "yousafkhamza"           # Your docker hub account credentials here.
    docker_password: "*******"            # Your docker hub account credentials here.
    repo_url: "https://github.com/yousafkhamza/nodejs-dockerfile.git"
    repo_dest: "/var/repository/"
    repo: "yousafkhamza/dapp:1"
  tasks:

    - name: "PIP Installation"
      yum:
        name: pip
        state: present

    - name: "Depandancy Task-Install Dokcer-py"
      pip:
        name: docker-py
        state: present

    - name: "Docker Installtion"
      yum:
        name: docker
        state: present

    - name: "Docker Service start and Enable"
      service:
        name: docker
        state: restarted
        enabled: true

    - name: "Build-Step - Cloning Repository"
      git:
        repo: "{{ repo_url }}"
        dest: "{{ repo_dest }}"
      register: repo_status

    - name: "Build-Step - Login to remote Repository"
      when: repo_status.changed == true
      docker_login:
        username: "{{ docker_user }}"
        password: "{{ docker_password }}"

    - name: "Build Docker image from Dockerfile"
      docker_image:
        build:
          path: "{{ repo_dest }}"
        name: myimage
        tag: v1
        repository: "{{ repo }}"
        push: yes
        source: build
```

### Output Screenshot
_Playbook Output_
![alt_txt](https://i.ibb.co/BsFBSNh/Screenshot-2021-10-29-165629.png)
_From DockerHub_
![alt_txt](https://i.ibb.co/zr8Kt0C/Screenshot-2021-10-29-181256.png)
----

## Reference
On this playbook was I used my old [nodjs](https://github.com/yousafkhamza/nodejs-dockerfile)  docker file to create image so please be check the same. So, that repo cloned to a destination through ansible and genrated the image from this.

----
## Conclusion
It's just creating a docker image through ansible and then push that same to Docker hub.

### ⚙️ Connect with Me 

<p align="center">
<a href="mailto:yousaf.k.hamza@gmail.com"><img src="https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white"/></a>
<a href="https://www.linkedin.com/in/yousafkhamza"><img src="https://img.shields.io/badge/LinkedIn-0077B5?style=for-the-badge&logo=linkedin&logoColor=white"/></a> 
<a href="https://www.instagram.com/yousafkhamza"><img src="https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white"/></a>
<a href="https://wa.me/%2B917736720639?text=This%20message%20from%20GitHub."><img src="https://img.shields.io/badge/WhatsApp-25D366?style=for-the-badge&logo=whatsapp&logoColor=white"/></a><br />
