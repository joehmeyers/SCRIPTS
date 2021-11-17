# WK13-ELK-Project
This repository will include diagrams, Linux and Ansible scripts, descriptions of each process.

# Cloud Network
The majority of scripts used were to enable/configure different cloud servers with docker containers for the virtual machines being used.

I started with a setup of the my Azure portal which allowed me to create my network starting with the creation of my Resource Group then added four virtual machines, virtual networks, load balancers (front end and back end) and network security groups. For each virtual machine, vnet, load balancers and resource group a private and public address was assigned to each one accordingly. 

Using the BASH command line I used my docker container to access my virtual machines using specific commands that enabled me to use ansible and .yml scripts to install filebeat and metricbeat. Finally connecting my jumpbox to connect to my repository in GitHub. Docker is a container that assists you when you create, deploy and run apps.

In the final step of the process we needed to use four servers that were running vulnerable DVWA containers. Included with the DVWA's were a jump-box and an ELK stack container.

I created a diagram that shows how my cloud network uses the jump-box, virtual machines, load balancers, and docker containers that were created. 
(see diagrams folder in github repository)


#Azure Portal Setup
- First, I created my account in Azure.com
- Second, I created my resource group in this case I called it: batmansknight, create a user name: azureuser
- Third, I created my Virtual Machines which includes: jumpboxprovisioner, web-1, web-2, web-3, ELK-SERVER
   - Each VM will have inbound security rules specific to their networks (SSH, deny-all).
- After creating all VM's the network security groups will need to be set up, in this case mine is: jumpboxprovisioner-nsg
- I created a public key which will be used to connect my network to the jump-box (see below for public key).

Use command ssh-keygen to generate key.

keyssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDAbtr+u4kIa82xsMxugbm7AGJYXKAH+zUmPS8kexEhCBbfoimB9OcDQNgAJylfqN17e0bV/4EhtvnwJ/C1ztKG9ngsdJMRzVZ02GjB2v7JnGJ4BOAB74crA29byXCM1jKRkZSM++rOV90OqY1037pWNx9BRUmCGIQxyi8Hj1dybumituy3AL/jfYxHuELfK9/8u5GY5yAm0p18e+cR/5EHiEerzjeyLEx63VfiRsyUB5w2p6Vby8Wsa16xOzwQN5QM0AjPPvIWIeN4fNgR4QvwARqufrqxgK+qi+Oe10ODXMwBbdA0y+tZUP+61lQ3UVdA2Ac+bdn0aLFWYwBZ28YtO77tgUrQ6Znz4Te/2AK8WhDy+ImErj7/haAgZl/qdvppFgt85l2oqBsUCTjg1awxjGVMXbh+43P2EdK4cDeGq/IPWGkC2e48+Uu3ydwY28jCRGz/4wLTW+OQsEZFb+CoqV7VTL/azBTXIdxH3QsQ/JuxiQTbd6Y7WB34Dl2yjUk= joehm@DESKTOP-NBEUETC

- Using Bash and the ssh -i ~/azurekey azureuser@20.115.28.206 command I can now access my jump-box.
  
# Bash Command Line and Commands for Containers, .yml Configurations and Playbooks
- Once inside the jump-box I use sudo su command to access root user
- From here I can create another public key that will be used for my VM's(see key below)

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDfToNolHAml8JmEYJCAsnfb0PtPbZLiu3R2B9LUeq34XuGgB2UdKVbuvp0OWxA2Ol8gUvpCLpCPckX6lFlYOjuJKg2UNi0hD1XrBofBpZw9VLD9KDUvHP8Axipi2KlOCH2W0fu0Ou9P/F1bDGcKAy3F3Hp7r6qvBFmWfyOROcG4Fd6aIMrKfU+Lq86sqgzIpchxaxrIfCeOu7vFT2rSmFtg64m5YLLsAK1IC3u1auFAiJ8E4b3MmuWFlrSG23S/IXZ/Fatvyo7HTgKC/ALEUocA0cDRx6mZ58tXsb9vq1LCA99dnzJDHF3V+tIVsg9BVDDHXV5p1Pp+4xcYvx1KXE1kNi1WBOrHgPyfmb23pvJTUiXyl999qRbTDHguiCelEndxQ8QFrOrzTOPG3sA8T9HJmA+HjzTdSarldumknL33MRoF+kDUbZvwxwEe3BIq0D5h5z01SljE8RGD1J8O96bBpKGjcqop8UmY0i6f+ynED0OnQ3OgrUrQw0Eta2F6+0= root@d106743a847e

- Now that I am in my Ansible Container I can access my docker container: priceless_jemison
  - To access the docker container priceless_jemison I used commands: docker start preceless_jemison, docker attach priceless_jemison then you will be root user: root@d106743a847e:~#

# Next is setting up the ELK container using a script.
  - The elk_config.yml will be located here: root@d106743a847e:/etc/ansible/playbooks# ls
ansible_config.yml  apacheInstallUninstall.yml  elk_config.yml  filebeat-playbook.yml  metricbeat-playbook.yml
  - The script should look something like this:
  Last login: Tue Nov 16 23:27:03 2021 from 10.0.0.4
azureuser@web-1:~$ exit
logout
Connection to 10.0.0.5 closed.
root@d106743a847e:/etc/ansible/playbooks# ls
ansible_config.yml  apacheInstallUninstall.yml  elk_config.yml  filebeat-playbook.yml  metricbeat-playbook.yml

root@d106743a847e:/etc/ansible/playbooks# cat elk_config.yml
---
- name: Config elk VM with Docker
  hosts: elk
  remote_user: azureuser
  become: true
  tasks:

    # use the apt module

    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

    # use the apt module

    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

    # use the pip module

    - name: Install Docker python module
      pip:
        name: docker
        state: present

      # use sysctl to configure the memory

    - name: Use more virtual memory configuration
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Launch docker elk container

    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

      # Use systemd to enable docker on boot

    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes
    - Status the elk container by using command: ssh azureuser@10.1.0.4
    - Now that the elk container is installed and running we can move on to filebeat and metricbeat.
 
Now that my containers are running I can now access my web-1(10.0.0.5), web-2(10.0.0.6), web-3(10.0.0.7) and ELK-SERVER(10.1.0.4) by using the command ssh azureuser@ip.address

Next I created my filebeat-config.yml script in order to connect to Kibana.
   - Kibana is a program that tracks or monitors web traffic coming into my network.
   - Location of this file is in root@d106743a847e:/etc/ansible# ls
                                 ansible.cfg  filebeat-config.yml  files  hosts  playbooks
                                 cd /etc/ansible/files
   - In this script my ELK server IP needed to be changed to 10.1.0.4:5601 and 10.1.0.4:9200
   
output.elasticsearch:
  # Boolean flag to enable or disable the output module.
  #enabled: true

  # Array of hosts to connect to.
  # Scheme and port can be left out and will be set to the default (http and 9200)
  # In case you specify and additional path, the scheme is required: http://localhost:9200/path
  # IPv6 addresses should always be defined as: https://[2001:db8::1]:9200
  hosts: ["10.1.0.4:9200"]
  username: "elastic"
  password: "changeme"

# Starting with Beats version 6.0.0, the dashboards are loaded via the Kibana API.
# This requires a Kibana endpoint configuration.
setup.kibana:
  host: "10.1.0.4:5601"

# TODO: Change this to the IP address of your ELK server
  # Kibana Host
  # Scheme and port can be left out and will be set to the default (http and 5601)
  # In case you specify and additional path, the scheme is required: http://localhost:5601/path
  # IPv6 addresses should always be defined as: https://[2001:db8::1]:5601
  #host: "localhost:5601"
  
  Next I had to create and wrote the filebeat-playbook.yml which is located in:  root@d106743a847e:/etc/ansible# ls
                                                                                 ansible.cfg  filebeat-config.yml  files  hosts  playbooks
                                                                                 cd /etc/ansible/playbooks
                                                                                 
                                                                                 root@d106743a847e:/etc/ansible/playbooks# ls
                                                                                 ansible_config.yml  apacheInstallUninstall.yml  elk_config.yml  filebeat-playbook.yml                                                                                            metricbeat-playbook.yml
                                                                                 
  The script should look similar to this:nano filebeat-playbook.yml, this is a .deb file, I used the command for both filebeat and metricbeat-playbook.yml
  command is: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb
  
  ---
- name: Installing and Launch Filebeat
  hosts: webservers
  become: yes
  tasks:

    # Use command module; apt if needed from Ubuntu/Debian repository

    - name: Download filebeat .deb file
      command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

    # Use command module

    - name: Install filebeat .deb
      command: dpkg -i filebeat-7.6.1-amd64.deb

    # Use copy module

    - name: Drop in filebeat.yml
      copy:
        src: /etc/ansible/files/filebeat-config.yml
        dest: /etc/filebeat/filebeat.yml

    # Use command module

    - name: Enable and Configure System Module
       command: filebeat modules enable system

    # Use command module

    - name: Setup filebeat
      command: filebeat setup

    # Use command module

    - name: Start filebeat service
      command: service filebeat start

    # Use systemd module

    - name: Enable service filebeat on boot
      systemd:
         name: filebeat
         enabled: yes
         
  # Now that filebeat has been installed you can now connect to Kibana.
    - After running the script and installing the filebeat-playbook.yml you will go to Kibana using your ELK-SERVER public ip, mine is 104.40.74.253
    - After connecting to Kibana and selecting system logs and running the script you should be able to Check Data for your Module Status.
    - After connecting the data you will have sys logs running and tracking your data.

 # Filebeat is now connected. The next step is to configure, install and run metricbeat.
   - command to access metricbeat-playbook.yml: root@d106743a847e:/etc/ansible/playbooks#nano metricbeat-playbook.yml
   - Like the filebeat-config.yml you will change the IP addresses and ports to be: 10.1.0.4:9200, 10.1.0.4:5601
   - Next is to run the metricbeat-playbook.yml script
---
- name: Installing and Launch metric beat
  hosts: webservers
  become: yes
  tasks:

    # Use command module; apt if needed from Ubuntu/Debian repository

    - name: Download mettricbeat .deb file
      command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.6.1-amd64.deb


    # Use command module

    - name: Install metricbeat.deb
      command: dpkg -i metricbeat-7.6.1-amd64.deb

    # Use copy module

    - name: drop in metricbeat config
      copy:
        src: /etc/ansible/files/metricbeat-config.yml
        dest: /etc/metricbeat/metricbeat.yml

    # Use command module

    - name: enable and configure docker module for metric beat
      command: metricbeat modules enable system

    # Use command module

    - name: setup metricbeat
      command: metricbeat setup

    # Use command module

    - name: start metricbeat
      command: service metricbeat start

    # Use systemd module

    - name: enable service metricbeat on boot
      systemd:
         name: metricbeat
         enabled: yes
         
    - Similar to filebeat you will go to Kibana and select docker and connect to the Module Status to check data and connections.
    - At this point the filebeat and metricbeat are connected to Kibana which will allow you to see the traffic through graphs that are selected in Kibana. (see repository for diagrams)

# Load Balancers
 - Now I can attach my web -1, web-2 and web-3 to my backend loadbalancer: Gotham-BE-LB
 - This will help distribute traffic evenly through my web 1,2,3

  
  

