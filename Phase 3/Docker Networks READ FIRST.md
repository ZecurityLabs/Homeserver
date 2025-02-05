# Docker Networks Configuration for Project 

## Introduction
I know we are supposed to start deploying our DNS and reverse proxy containers but first! We must understand a little how docker networks actually work and how to use them in order to expose only the containers we want our clients to access while the rest of them remain in a isolated network without any ports exposed through the server IP.

## Purpose
Docker Bridge networks are isolated networks within the docker engine, the containers there are isolated from the local network unless their ports are mapped to the host machine by declaring port "80:80" with the format hostpost:containerport in the docker run command or in our docker compose file. A big advantage on bridge networks is that containers can resolve each other using their container names or container ids, for example if we name our DNS container "Adguard" and our reverse proxy server "NPM" and put both in the same bridge network we can easily test using "ping NPM" from the Adguard container or vice versa and the containers can communicate without any exposure at all!

Docker macvlan networks are networks that allow containers to have their own MAC addresses and IP addresses distinct from the host machine, this setup is particularly useful for applications that require direct access to hardware resources or need to be treated as physical devices on the network. This network will be used only by our DNS container (Adguard) so our clients (in my case a windows/ubuntu laptop) can access it through the container IP and later we will also encrypt this connection with DNS over HTTPS. For our DNS though we will connect the container on both the macvlan and the bridge network, so that our DNS accepts requests from our local network using macvlan and forward them to our reverse proxy inside the bridge network without exposing any ports for the reverse proxy to our local network.

## Configuration
This document outlines the configuration of two new Docker networks within the context of Project X. These networks are designed to provide isolated environments tailored to specific application requirements, ensuring security and optimal resource management.

## Network 1: examplemacvlan
**Description:**  
`examplemacvlan` is a Macvlan network that allows containers to have their own MAC addresses and IP addresses distinct from the host machine. This setup is particularly useful for applications that require direct access to hardware resources or need to be treated as physical devices on the network.

**Command to Build:**  
To create `examplemacvlan` with a specified range of IPs, use the following command:
```bash
docker network create \
 --driver=macvlan \
 --subnet=192.168.0.0/24 \ (Your local ip range may be different this example reflects the local ip range mentioned as example in Phase 1: Server Setup )
 --gateway=192.168.0.1 \
 examplemacvlan
```  
**Note:** Replace `192.168.1.0/24` with the actual IP range of your local network. Ensure this range does not conflict with existing networks on your system.

**Usage:**  
This network will primarily serve as a bridge for certain containers to communicate directly with hardware or other devices outside Docker's default bridge network, thus enhancing performance in specific scenarios. It should be used sparingly due to potential IP address conflicts and the inherent complexity of managing multiple MAC addresses.

## Network 2: exampleproxybridge
**Description:**  
`exampleproxybridge` is a Bridge network that isolates containers from the local network while allowing internal communication among them. This setup is ideal for applications where security and resource management are critical, especially when using a reverse proxy to handle external traffic.

**Command to Build:**  
To create `exampleproxybridge`, use the following command:
```bash
docker network create \
 --driver=bridge \
 exampleproxybridge
```  
**Note:** This command creates a basic bridge network without additional configuration options for IP addressing, as it is expected that traffic will be managed by an external reverse proxy.

**Usage:**  
All other containers in the project should connect to `exampleproxybridge`. This setup ensures that these containers do not expose any ports directly to the outside world, instead relying on a central reverse proxy (not covered here) for all inbound and outbound traffic encryption and management.

## Conclusion
These networks are designed with flexibility in mind regarding naming. However, it is advisable to adhere to project conventions when naming Docker entities to maintain clarity and organization. The setup provides a robust foundation for applications that require different levels of network isolation and resource access based on their specific needs.
