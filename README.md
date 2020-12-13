# UCSD_Bootcamp_Projects
  GNU nano 4.9.3                                     Automated_ELK_Stack_Deployment.md
## Automated ELK Stack Deployment

The files in this repository were used to configure live ELK Stack on Azure.


This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.
The load balancer ensures that work to process incoming traffic will be shared by both vulnerable web servers. Access controls will
ensure that only authorized users - namely, ourselves - will be able to connect in the first place._

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems of the VMs on the networ>CPU usage; attempted SSH logins; sudo ecalation failures; etc.


The configuration details of each machine may be found below.


| Name       | Fuction     | IP Address | Operating System |
|------------|-------------|------------|------------------|
| Jump Box   | Gateway     | 10.0.0.5   | Linux            |
| Web-1      | Web Server  | 10.0.0.8   | Linux            |
| Web-2      | Web Server  | 10.0.0.7   | Linux            |
| Web-3      | Web Server  | 10.0.0.10  | Linux            |
| Elk Server | Monitoring  | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet.

Only the jumpbox machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addr>- 76.176.158.118

Machines within the network can only be accessed by each other. The Web-1, -2, and -3 send traffic to the ELK Server.
The ELK server can only be accessed by the jumpbox machine via IP Address: 168.61.20.44

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 76.176.158.118       |
| ELK      | No                  | 10.0.0.1-254         |
| Web-1    | No                  | 10.0.0.1-254         |
| Web-2    | No                  | 10.0.0.1-254         |
| Web-3    | No                  | 10.0.0.1-254         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantagous because>reduces the chance of bugs and error, and its Infrastructure as Code. Using Infrastructure as Code allows you to run your infrastruc>single script which can be deployed easily in the future.

The playbook implements the following tasks:
-The Playbook first installs docker.io
-Next, install python3-pip
-Installs Docker python Module
-Increases Memory for Elk
-Lastly, it downloads and launches the Elk Continer and publishes ports 5601, 9200, and 5044.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

(Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
Web-1 @10.0.0.8
Web-2 @10.0.0.7
Web-3 @10.0.0.10


We have installed the following Beats on these machines:
-Filebeat
-Metricbeat

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see.
E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._
-Filebeat : Filebeat tracks changes to the filesystem. An example would be detecting changes to Apache Logs.
-Metricbeat: Metricbeat collects metrics from the operating system. We can detect SSH login attempts, failed sudo escalations and CP>
### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control nod>
SSH into the control node and follow the steps below:
- Copy the playbooks to the Ansible Control Node.
- Update the hosts file to include IP Address of Web-1, -2 and -3 which have DVWA.
- Run the playbook, and navigate to 'sudo docker ps' to check that the installation worked as expected.

 
- _Which file is the playbook? Where do you copy it?
   The playbooks are filebeat-playbook.yml, metric-playbook.yml, install-elk.yml which you would copy to etc/ansible/files
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install
 the ELK server on versus which to install Filebeat on? You need to update the hosts file with the ip addresses in the ansible-config file. Within the
 configuration file, create a seperate field for installing elk versus filebeat.
_
- _Which URL do you navigate to in order to check that the ELK server is running? Once installed, navigate to http://<elk.ip.address>:5601/app/kibana
