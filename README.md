# Azure ELK Stack Deployment Projects
  GNU nano 4.9.3                                     Automated_ELK_Stack_Deployment.md
## Automated ELK Stack Deployment

The files in this repository were used to configure live ELK Stack on Azure.

![Cloud Security Diagram_DVB](https://user-images.githubusercontent.com/69644053/124221513-c6d8d680-dab4-11eb-9296-6ba3074df990.PNG)

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.
The load balancer ensures that work to process incoming traffic will be shared by all three vulnerable web servers. Access controls will
ensure that only authorized users - namely, ourselves - will be able to connect in the first place.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the file systems of the VMs on the network CPU usage, attempted SSH logins, sudo escalation failures, and etc.


The configuration details of each machine may be found below.


| Name       | Fuction     | IP Address | Operating System |
|------------|-------------|------------|------------------|
| Jump Box   | Gateway     | 10.0.0.5   | Linux            |
| Web-1      | Web Server  | 10.0.0.8   | Linux            |
| Web-2      | Web Server  | 10.0.0.7   | Linux            |
| Web-3      | Web Server  | 10.0.0.10  | Linux            |
| Elk Server | Monitoring  | 10.1.0.4   | Linux            |


### ELK Server Configuration

The ELK VM exposes an Elastic Stack instance. **Docker** is used to download and manage an ELK container.
Rather than configure ELK manually, we opted to develop a reusable Ansible Playbook to accomplish the task. This playbook is duplicated below.


To use this playbook, one must log into the Jump Box, then issue: 'ansible-play install_elk.yml elk'. This runs the 'install_elk.yml' playbook on the 'elk' host.

### Access Policies

The machines on the internal network are not exposed to the public Internet.

Only the **jump box** machine can accept connections from the Internet. Access to this machine is only allowed from the following IP address '76.176.158.118'

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

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantagous because reduces the chance of bugs and error, and its Infrastructure as Code. Using Infrastructure as Code allows you to run your infrastructure script which can be deployed easily in the future.

The playbook implements the following tasks:

-The playbook first installs docker.io

-Next, install python3-pip

-Installs Docker python Module

-Increases Memory for Elk

-Lastly, it downloads and launches the Elk Continer and publishes ports 5601, 9200, and 5044.

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![sudo_docker_ps_output](https://user-images.githubusercontent.com/69644053/124220448-bc1d4200-dab2-11eb-9f20-0121e1ce3d11.PNG)

The playbook is duplicated below.
```yaml
---
# install_elk.yml
- name: Configure Elk VM with Docker
  hosts: elkservers
  remote_user: elk
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

      # Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

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
```

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
Web-1 @10.0.0.8
Web-2 @10.0.0.7
Web-3 @10.0.0.10


We have installed the following Beats on these machines:
-Filebeat
-Metricbeat
-Packetbeat

These Beats allow us to collect the following information from each machine:

-**Filebeat**: Filebeat detects changes to the filesystem. Specifically, we use it to collect Apache logs.

-**Metricbeat**: Metricbeat detects changes in system metrics, such as CPU usage. We use it to detect SSH login attempts, failed `sudo` escalations, and CPU/RAM statistics.

-**Packetbeat**: Packetbeat collects packets that pass through the NIC, similar to Wireshark. We use it to generate a trace of all activity that takes place on the network, in case later forensic analysis should be warranted.

The playbook below installs Metricbeat on the target hosts. The playbook for installing Filebeat is not included, but looks essentially identical — simply replace `metricbeat` with `filebeat`, and it will work as expected.

```yaml
---
- name: Install metric beat
  hosts: webservers
  become: true
  tasks:
    # Use command module
  - name: Download metricbeat
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb

    # Use command module
  - name: install metricbeat
    command: dpkg -i metricbeat-7.4.0-amd64.deb

    # Use copy module
  - name: drop in metricbeat config
    copy:
      src: /etc/ansible/files/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

    # Use command module
  - name: enable and configure docker module for metric beat
    command: metricbeat modules enable docker

    # Use command module
  - name: setup metric beat
    command: metricbeat setup

    # Use command module
  - name: start metric beat
    command: service metricbeat start
```

### Using the Playbooks
In order to use the playbooks, you will need to have an Ansible control node already configured. We use the **jump box** for this purpose.

To use the playbooks, we must perform the following steps:

- Copy the playbooks to the Ansible Control Node

- Run each playbook on the appropriate targets

The easiest way to copy the playbooks is to use Git:

```bash
$ cd /etc/ansible
$ mkdir files
# Clone Repository + IaC Files
$ git clone https://github.com/yourusername/project-1.git
# Move Playbooks and hosts file Into `/etc/ansible`
$ cp project-1/playbooks/* .
$ cp project-1/files/* ./files
```

This copies the playbook files to the correct place.

Next, you must create a `hosts` file to specify which VMs to run each playbook on. Run the commands below:

```bash
$ cd /etc/ansible
$ cat > hosts <<EOF
[webservers]
10.0.0.5
10.0.0.6

[elk]
10.0.0.8
EOF
```

After this, the commands below run the playbook:

 ```bash
 $ cd /etc/ansible
 $ ansible-playbook install_elk.yml elk
 $ ansible-playbook install_filebeat.yml webservers
 $ ansible-playbook install_metricbeat.yml webservers
 ```

To verify success, wait five minutes to give ELK time to start up. 

Then, run: `curl http://10.0.0.8:5601`. This is the address of Kibana. If the installation succeeded, this command should print HTML to the console.


---
