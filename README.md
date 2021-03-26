## Project 1

### Project Overview

The project consists of two parts: a web service and a monitoring stack.  The web service serves HTTP requests from the wider internet, while the monitoring stack tracks request metrics and can help identify potential malicious activity.  Both components were deployed in Microsoft Azure on peered virtual networks.

#### Web Service Architecture

The web service consisted of 2 VMs running servers within docker containers.  The VMs existed within a virtual network and network security group.  Traffic was routed from the internet to a load balancer, which distributed traffic between the VMs.  The VMs also ran Metricbeat and Filebeat monitoring, which reported out to the monitoring stack (details to follow).  A jump box VM within the same virtual network enabled the provisioning and administration of the web servers.  The jump box VM ran Ansible within a docker container, which provisioned the web servers over SSH.

#### Monitoring Stack Architecture

The monitoring stack consisted of a single VM running ELK (Kibana, elasticsearch and logcache).  This VM existed on a separate virtual network, and within a separate network security group, from the web servers, but bidirectional peering between the virtual networks enabled the web servers to send metrics to the ELK VM.  The ELK VM was made accessible exclusively to me to access the Kibana dashboards and identify any anomalies on the web servers that might indicate malicious activity.

#### Access Control

Network security groups in each virtual network controlled the flow of traffic to the VMs.  Rules were as follows:

##### Web Service Stack

The following traffic was allowed (all other traffic denied):

| Protocol | Port | Source          | Destination     |
|----------|------|-----------------|-----------------|
| TCP      | 22   | Student PC      | Jump Box VM     |
| TCP      | 80   | Student PC      | Web Service VMs |
| Any      | Any  | Virtual Network | Virtual Network |

##### Monitoring Stack

The following traffic was allowed (all other traffic denied):

| Protocol | Port | Source                | Destination         |
|----------|------|-----------------------|---------------------|
| TCP      | 5601 | Student PC            | ELK Virtual Network |
| Any      | Any  | Both Virtual Networks | ELK Virtual Network |
