## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

Path to Topology: github.com/thomas-gallo2013/ElkStackProject/tree/Images/'2021-03-28 Network Topology - gallo'

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the ansible playbook file may be used to install only certain pieces of it, such as Filebeat.

All located and Run within the docker container you will create from Jump-Box-Provisioner VM 
	#you will store your ssh credentials within your docker container for secure shell connections bewteen your Jump-Box-Provisioner VM and your webservers
   Setup webservers - You will create a total of 3 webservers located on the same Virtual Network (VN) each having a container to host the DVWA - these will have private IPs, and will be placed behind a load balancer for redundancy purposes
	#Although this topology includes 3 webservers, you very well can add additional servers fur increased redundancy should that become necessary
   	-web1 - hosts the DVWA 
	-web2 - hosts the DVWA
	-DVWA-VM3 - hosts the DVWA
                    - You will create a total of 1 webserver on a seperate VN having a container to host the Elkstack application - this will have a public IP 
	-elkvm - hosts the ELKStack services for access by pre-determined IP address through the Elk network security group (NSG) 
This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly redundant, in addition to restricting access to the network.
 - Load balancing ensures availability of functionality of web applications by allowing for redundancy.  
	Placing proper health probes on the backend machines will allow the Load Balancer to consolidate all incoming traffic to the network, and distribute requests to servers that pass the health probe test...if one machine hosting a service goes down, the load balancer will redirect traffic

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the hosts and system hardware.
 - Filebeat is an extenstion for the ELK stack that monitors log files and allows for efficient organization and forwarding of log files to the ELK function for indexing
-  Metricbeat is an extension for the ELK stack that monitors system hardware and allows for efficient visualization and forwarding of hardware utilization and health to the ELK function for indexing

The configuration details of each machine may be found below.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web-1    | host     | 10.0.0.5   | Linux            |
| Web-2    | host     | 10.0.0.6   | Linux            |
| DVWA-VM3 | host     | 10.0.0.7   | Liunx            |
| elkVM    | host     | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- Home network IP from which you are connecting into the network

Machines within the network can only be accessed by the Jump Box. Accessing the applications is only available from predetermined IPs set within the firewall
- Only the JumpBox has access to the elkstack server through SSH
- Only the JumpBox has access to the DVWA hosts through SSH

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 |   whitelisted IP     |
|  web-1   | No                  |     10.0.0.4         |
|  web-2   | No                  |     10.0.0.4         |
| DVWA-VM3 | No                  |     10.0.0.4         |
|  elkVM   | No                  |     10.0.0.4         |

Note - applications running on web-1 web-2 DVWA-VM3 and elkVM can be made available to whitelisted source IPs over specificed ports.  
### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- you can make changes to many machines at one time by running one script (ansible-playbook) 
- you can test updates/changes to the machines on one machine before submiting network-wide updates
- you can increase accuracy between each machine - more consistent setup across multiple machines

The playbook implements the following tasks:
- 1. installs docker and python onto the machine
- 2. increases allocated virtual memory onto the machine for ensuring appropriate hardware to being directed to the ELK application
- 3. installs the ELK container modules, enables them, and runs them upon machine bootup

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

ElkStackProject/Images/'2021-03-28 elkstack container elkserver - gallo.jpg'

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- 10.0.0.5 | 10.0.0.6 | 10.0.0.7

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat manages log records from each machine, and we would expect to see log information regarding changes made to the above mentioned IPs
- Metricbeat monitors hardware utilization and health, and we would expect to see information regarding CPU usage, temp., etc.
### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Use the install-elk.yml file on the elkserver, and the specific beat playbooks on the DVWA host machines
	ensure specify which server you will be running each playbook on through the 'ansible-playbook [header] <name of playbook.yml>'
- Update the hosts file to include the appropriate subnet IPs for the target machines you wish to install the elk container
	ensure as well to include all desired hosts for the various 'beats' you may install into your subnetwork.  This/these will be different than your elkserver.
- Run the playbook, and navigate to the browser web page: *Your public IP of Elk Server*:5601/app/kibana to check that the installation worked as expected.

