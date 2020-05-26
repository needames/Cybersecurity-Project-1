## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

!(/Users/ty/desktop/readme/images/Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

  -  (root@44955829848a:/etc/ansible/roles/filebeat-playbook.yml)

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
- Load balancers protect the servers from a denial of service attack. A load balancer distributes traffic amongst the servers. One benefit of using a jump box is that it protects your virtual machines from publix exposure.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the log files and system performance.
- log files monitored by Filebeat


The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jumpbox  | Gateway  | 10.0.1.4   |Linux ubuntu 18.04|
| DVWA-VM1 | VM       | 10.0.1.7   |Linux ubuntu 18.04|
| DVWA-VM2 | VM       | 10.0.1.8   |Linux ubuntu 18.04|
| DVWA-VM3 | VM       | 10.0.1.9   |Linux ubuntu 18.04|
| DVWA-VM4 | VM       | 10.0.1.10  |Linux ubuntu 18.04|
| ELK VM   | ElkStack | 10.0.1.12  |Linux ubuntu 18.04|

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 72.84.225.151

Machines within the network can only be accessed by Port 22.
- 10.0.1.4

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box |     No              | 72.84.225.151        |
| DVWA-VMs |     No              | 10.0.1.4             |
| ELK Stack|     No              | 10.0.1.4             |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- Ansible can be run from the command line and will ensure our provisioning scripts run identically everywhere.

The playbook implements the following tasks:
* Change the memory on the ELK VM
* Install docker.io
* Install python-pip
* Install docker python module
* Download and launch a docker elk stack

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

!(/Users/ty/desktop/readme/images/ELK.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
* 10.0.1.7
* 10.0.1.8
* 10.0.1.9
* 10.0.1.10

We have installed the following Beat on these machines:
- filebeat-7.6.1-amd64.deb

This Beat allow us to collect the following information from each machine:
- Filebeat is used to send your log files to kibana. Filebeat monitors and collects log events on specificed servers.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the Filebeat configuration file to the DVWA VMs.
- Update the hosts file to include webservers 10.0.1.7, 10.0.1.8, 10.0.1.9, 10.0.1.10
- Run the playbook, and navigate to Kibana to check that the installation worked as expected.

Answer the following questions to fill in the blanks:
- _Which file is the playbook? Where do you copy it?_
- filebeat-configuration.yml is the playbook that you copy from your ansible container to your ELK VM.

- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?
- You must update the hosts and ansible.cfg file to make Ansible run the playbook on a specific machine. To specify which machine to install ELK server or Filebeat, you must specify which hosts you would like the playbook to apply to.
- For an example
'''
---
  - name: filebeat installer
    hosts: webservers
    become: true
    tasks:
    '''
- _Which URL do you navigate to in order to check that the ELK server is running?
- You must navigate to the Kibana url to ensure ELK server is running. You do this by going to <http://[public-ip-of-elk-server:5601]>

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
'''
---
  - name: filebeat installer
    hosts: webservers
    become: true
    tasks:
    
    - name: download filebeat
      shell: curl -L -O  https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb
            
    - name: install filebeat
      shell: dpkg -i filebeat-7.6.1-amd64.deb 

    - name: Copy filebeat configuration
      copy:
       src: /etc/ansible/files/filebeat-configuration.yml
       dest: /etc/filebeat/filebeat.yml
       owner: root
       group: root
       mode: '0600'
       backup: yes

    - name: restart filebeat
      shell: filebeat modules enable system
   
    - name: filebeat setup
      shell: filebeat setup
  
    - name: filebeat -e
      shell: filebeat -e &
      '''