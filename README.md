## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![TODO: Update the path with the name of your diagram](AzureResourceDiagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YML file may be used to install only certain pieces of it, such as Filebeat.

---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azadmin
  become: true
  tasks:
    # Use apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

      # Use docker_container module
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

      # Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes


This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
- _TODO: What aspect of security do load balancers protect? What is the advantage of a jump box? Protects assets and creates separation betweenn workstations and priviledged assets that may be at high risk within the network. 

Advantage of a jump box establishes a clear funnel and a secure perimeter around IT resources.  

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the metric and system files.
- What does Filebeat watch for? Forwarding and centralising  log data. Filebeat Elasticsearch module can handle audit logs, deprecation logs, gc logs, server logs, and slow logs.
- What does Metricbeat record? Metric takes the metrics and statistics that it collects and ships them to the output that you specify, such as Elasticsearch or Logstash

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web 1     | Web 1    | 10.0.0.5  | Linux            |
| Web 2     | Web 2    | 10.0.0.6  | Linux            |
| Web 3     | Web 3    | 10.0.0.7  | Linux            |
| ELK       | Server    | 10.1.0.5 | Linux            |
### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the elk machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 10.0.0.4 (Jumpbox)


Machines within the network can only be accessed by JumpBox.
- Which machine did you allow to access your ELK VM? What was its IP address? Jumpbox

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | My public IP         |
|  Web 1   |     No              |    10.0.0.4          |
|  Web 2   |   No                |    10.0.0.4          |
|  Web 3   |  No                 |   10.0.0.4           |
| ELK      | No                  |   my public IP       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because the playbook implements the following tasks such as configuration of system logs to use more memory. 
- What is the main advantage of automating configuration with Ansible? Ansible roles provide a framework for fully independent, or interdependent collections of variables, tasks, files, templates, and modules. The role of Ansible is the primary mechanism for breaking a playbook into multiple files. This simplifies writing complex playbooks, and it makes them easier to reuse.

The playbook implements the following tasks:
- _In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- Use apt module
    - Install docker.io
Use apt module
    - Install pip3
Use pip module
    - Install Docker python module
Use sysctl module
    - Use more memory
Use docker_container module
    - download and launch a docker elk container
Use systemd module
    - Enable service docker on boot
-

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Dockerps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
-  List the IP addresses of the machines you are monitoring

Web Server 1 10.0.0.5
Web Serer 2 10.0.0.6
Web Server 3 10.0.0.7

We have installed the following Beats on these machines:
-  Specify which Beats you successfully installed_
Filebeat
Metricbeat
Packetbeat


These Beats allow us to collect the following information from each machine:
-  In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

Filebeat: Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.
Metricbeat: Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed sudo escalations, and CPU/RAM statistics.
Packetbeat: Packetbeat collects packets that pass through the NIC, similar to Wireshark. We use it to generate a trace of all activity that takes place on the network, in case later forensic analysis should be warranted.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the SSH file to Ansible Playbook.
- Update the Ansible file to include SSH code
- Run the playbook, and navigate to Jumpbox to check that the installation worked as expected.

_Answer the following questions to fill in the blanks:_
- _Which file is the playbook? /etc/ansible folder/
Where do you copy it? elkinstallyml

- _Which file do you update to make Ansible run the playbook on a specific machine? hosts file 
How do I specify which machine to install the ELK server on versus which to install Filebeat on? hosts file 
- _Which URL do you navigate to in order to check that the ELK server is running?  http://10.0.0.8:5601. This is the kibana address

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc 
ansible-playbook elkinstall.yml
ansible-playbook elkconfig.yml 
ansible-playbook pentest.yml 
ansible-playbook filebeatconfig.yml
ansible-playbook filebeatplaybook.yml