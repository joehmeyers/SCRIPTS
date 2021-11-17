# WK13-ELK-Project
This repository will include diagrams, Linux and Ansible scripts, descriptions of each process.

# Cloud Network
The majority of scripts used were to enable/configure different cloud servers with docker containers for the virtual machines being used.

I started with a setup of the my Azure portal which allowed me to create my network starting with the creation of my Resource Group then added four virtual machines, virtual networks, load balancers (front end and back end) and network security groups. For each virtual machine, vnet, load balancers and resource group a private and public address was assigned to each one accordingly. 

Using the BASH command line I used my docker container to access my virtual machines using specific commands that enabled me to use ansible and .yml scripts to install filebeat and metricbeat. Finally connecting my jumpbox to connect to my repository in GitHub.

In the final step of the process we needed to use four servers that were running vulnerable DVWA containers. Included with the DVWA's were a jump-box and an ELK stack container.

I created a diagram that shows how my cloud network uses the virtual machines, load balancers, and docker containers that were created.


#Azure Portal Setup
- First, I created my account in Azure.com
- Second, I created my resource group in this case I called it: batmansknight, create a user name: azureuser
- Third, I created my Virtual Machines which includes: jumpboxprovisioner, web-1, web-2, web-3, ELK-SERVER
   - Each VM will have inbound security rules specific to their networks (SSH, deny-all).
- After creating all VM's network security groups will need to be set up in this case mine is: jumpboxprovisioner-nsg
- I created a public which will be used to connect my network to the jump-box (see below for public key).
keyssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDAbtr+u4kIa82xsMxugbm7AGJYXKAH+zUmPS8kexEhCBbfoimB9OcDQNgAJylfqN17e0bV/4EhtvnwJ/C1ztKG9ngsdJMRzVZ02GjB2v7JnGJ4BOAB74crA29byXCM1jKRkZSM++rOV90OqY1037pWNx9BRUmCGIQxyi8Hj1dybumituy3AL/jfYxHuELfK9/8u5GY5yAm0p18e+cR/5EHiEerzjeyLEx63VfiRsyUB5w2p6Vby8Wsa16xOzwQN5QM0AjPPvIWIeN4fNgR4QvwARqufrqxgK+qi+Oe10ODXMwBbdA0y+tZUP+61lQ3UVdA2Ac+bdn0aLFWYwBZ28YtO77tgUrQ6Znz4Te/2AK8WhDy+ImErj7/haAgZl/qdvppFgt85l2oqBsUCTjg1awxjGVMXbh+43P2EdK4cDeGq/IPWGkC2e48+Uu3ydwY28jCRGz/4wLTW+OQsEZFb+CoqV7VTL/azBTXIdxH3QsQ/JuxiQTbd6Y7WB34Dl2yjUk= joehm@DESKTOP-NBEUETC
- Using Bash and the ssh -i ~/azurekey azureuser@20.115.28.206 command I can now access my jump-box.
  
# Bash Command Line and Commands for Containers, .yml Configurations and Playbooks
- Once inside the jump-box I use sudo su command to access root user
- From here I can create another public key that will be used for my VM's
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDfToNolHAml8JmEYJCAsnfb0PtPbZLiu3R2B9LUeq34XuGgB2UdKVbuvp0OWxA2Ol8gUvpCLpCPckX6lFlYOjuJKg2UNi0hD1XrBofBpZw9VLD9KDUvHP8Axipi2KlOCH2W0fu0Ou9P/F1bDGcKAy3F3Hp7r6qvBFmWfyOROcG4Fd6aIMrKfU+Lq86sqgzIpchxaxrIfCeOu7vFT2rSmFtg64m5YLLsAK1IC3u1auFAiJ8E4b3MmuWFlrSG23S/IXZ/Fatvyo7HTgKC/ALEUocA0cDRx6mZ58tXsb9vq1LCA99dnzJDHF3V+tIVsg9BVDDHXV5p1Pp+4xcYvx1KXE1kNi1WBOrHgPyfmb23pvJTUiXyl999qRbTDHguiCelEndxQ8QFrOrzTOPG3sA8T9HJmA+HjzTdSarldumknL33MRoF+kDUbZvwxwEe3BIq0D5h5z01SljE8RGD1J8O96bBpKGjcqop8UmY0i6f+ynED0OnQ3OgrUrQw0Eta2F6+0= root@d106743a847e
- Now that I am in my Ansible Container I can access my docker container: priceless_jemison
  - To access the docker container priceless_jemison I used commands: docker start preceless_jemison, docker attach priceless_jemison then you will be root user: root@d106743a847e:~#
