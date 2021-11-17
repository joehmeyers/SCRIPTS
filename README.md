# WK13-ELK-Project
This repository will include diagrams, Linux and Ansible scripts, descriptions of each process.

# Cloud Network
The majority of scripts used were to enable/configure different cloud servers with docker containers for the virtual machines being used.

I started with a setup of the my Azure portal which allowed me to create my network starting with the creation of my Resource Group then added four virtual machines, virtual networks, load balancers (front end and back end) and network security groups. For each virtual machine, vnet, load balancers and resource group a private and public address was assigned to each one accordingly. 

Using the BASH command line I used my docker container to access my virtual machines using specific commands that enabled me to use ansible and .yml scripts to install filebeat and metricbeat. Finally connecting my jumpbox to connect to my repository in GitHub.

In the final step of the process we needed to use four servers that were running vulnerable DVWA containers. Included with the DVWA's were a jump-box and an ELK stack container.

I created a diagram that shows how my cloud network uses the virtual machines, load balancers, and docker containers that were created.


