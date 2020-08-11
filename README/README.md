## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![TODO: Update the path with the name of your diagram](Images/network_diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the filebeat-playbook.yml file may be used to install only certain pieces of it, such as Filebeat.

  - _TODO: Enter the playbook file._

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly availability, in addition to restricting access to the network.
- _TODO: What aspect of security do load balancers protect? What is the advantage of a jump box?_

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the servers/logs and system metrics.
- _TODO: What does Filebeat watch for?_
- _TODO: What does Metricbeat record?_

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name       | Function       | IP Address | Operating System |
|------------|----------------|------------|------------------|
| Jump Box   | Gateway        | 10.0.0.4   | Linux            |
| Elk Server | Monitor        | 10.1.0.4   | Linux            |
| Web 1      | DVWA Container | 10.0.0.5   | Linux            |
| Web 2      | DVWA Container | 10.0.0.6   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Elk Server machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:24.55.10.229
- _TODO: Which machine did you allow to access your ELK VM? What was its IP address?_

Machines within the network can only be accessed by Jumpbox. 52.152.134.107, 10.0.0.4
- _TODO: Add whitelisted IP addresses_

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | No                  | 24.55.10.229         |
| Web 1    | No                  | 10.0.0.4             |
| Web 2    | No                  | 10.0.0.4             |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...it allows scaling.
- _TODO: What is the main advantage of automating configuration with Ansible?_

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- ...Download Docker
- ...Install python
- ...Install Docker module
- ...Download & install elk container
- ...Enable docker service

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/elk_docker_ps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines: 10.0.0.5, 10.0.0.6
- _TODO: List the IP addresses of the machines you are monitoring_

We have installed the following Beats on these machines: filebeat, metricbeat
- _TODO: Specify which Beats you successfully installed_

These Beats allow us to collect the following information from each machine: filebeat monitors logs. It is currently configured to collect system logs from VMs Web 1 & 2 which can collect sudo commands & ssh logins. Metricbeat can collect metrics from the OS and services running. It is currently setup to monitor VMs Web 1 & 2 for CPU usage and Memory Usage.
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat-config.yml file to /etc/ansible/.
- Update the hosts file to include "ELK" webserver.
- Run the playbook, and navigate to http://<elk_vm_ip>:5601/app/kibana to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._


edit "hosts" file from /etc/ansible/
	add IPs of VM webservers

Create a playbook to install ELK.

---
- name: Config Elk VM with Docker
  hosts: elk
  become: true
  tasks:
  - name: User more memory
    sysctl:
      name: vm.max_map_count
      value: '262144'
      state: present
      reload: yes

  - name: docker.io
    apt:
      update_cache: yes
      force_apt_get: yes
      name: docker.io
      state: present

  - name: Install pip
    apt:
      update_cache: yes
      force_apt_get: yes
      name: python3-pip
      state: present

  - name: Install Docker python module
    pip:
      name: docker
      state: present

  - name: download and launch a docker elk container
    docker_container:
      name: elk
      image: sebp/elk:761
      state: started
      restart_policy: always
      published_ports: 5601:5601, 9200:9200, 5044:5044

  - name: Enable docker service
    systemd:
      name: docker
      enabled: yes

Run anisble-playbook elkdocker.yml

