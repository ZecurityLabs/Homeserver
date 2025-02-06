# Docker Network Configuration for Project 

## Introduction
I know we are supposed to start deploying our DNS and reverse proxy containers but first! We must understand a little how docker networks actually work and how to use them in order to expose only the containers we want our clients to access while the rest of them remain in a isolated network without any ports exposed through the server IP.

## Purpose
Docker Bridge networks are isolated networks within the docker engine, the containers there are isolated from the local network unless their ports are mapped to the host machine by declaring port "80:80" with the format hostpost:containerport in the docker run command or in our docker compose file.



## Configuration
This document outlines the configuration a new Docker networks within the context of the project . This network is designed to provide isolated environments tailored to specific application requirements, ensuring security and optimal resource management.

## Network: exampleproxybridge
**Description:**  
`exampleproxybridge` is a Bridge network that isolates containers from the local network while allowing internal communication among them. This setup is ideal for applications where security and resource management are critical, especially when using a reverse proxy to handle external traffic.

**Command to Build:**  
To create `exampleproxybridge`, use the following command:
```bash
docker network create \
 --driver=bridge \
 exampleproxybridge
```  
**Note:** This command creates a basic bridge network without additional configuration options for IP addressing, as it is expected that traffic will be managed by an external reverse proxy. We will expose specific ports to the host ip and after we configure our dns and reverse proxy we can revisit the compose files delete the port mappings so our services are no longer accessible through the host ip.

**Usage:**  
All other containers in the project should connect to `exampleproxybridge`. This setup ensures that these containers do not expose any ports directly to the outside world, instead relying on a central reverse proxy (not covered here) for all inbound and outbound traffic encryption and management.

# Conclusion
## Summary of Docker Networks Configuration
In this document, we have explored the configuration of the Docker network within the project environment. The network `exampleproxybridge`, is a Bridge network designed to provide an isolated environment for containers while facilitating internal communication among them. This setup enhances security and resource management by keeping containers from directly exposing their ports to the local network.

## Next Steps
1. **DNS:** Deploy the DNS container with Adguard Home, ensuring it is connected to the `exampleproxybridge` network. This will allow the DNS service to resolve internal domain names for the isolated containers. 
2. **Reverse Proxy Integration:** After deploying Adguard Home, proceed with deploying the reverse proxy container Nginx Proxy Manager, also connected to the `exampleproxybridge` network. This will manage the routing of external traffic to the appropriate internal services.
3. **Security Measures:** Maintain a strong focus on security by ensuring Adguard resolves DNS over HTTPS and encrypting the traffic from our clients towards the server.
