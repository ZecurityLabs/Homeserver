# Deploying AdGuard DNS with Docker Compose and Configuration Guide

## Introduction and Purpose of a DNS Container
DNS (Domain Name System) serves as the backbone of the internet, translating human-readable domain names into IP addresses used by computers to locate each other. AdGuard, renowned for its effective ad-blocking solutions, offers an integrated DNS service that can be deployed conveniently using Docker Compose. This setup not only blocks ads and potentially harmful websites but also provides advanced filtering options, ensuring a safer browsing experience.

## Deployment of the Container Using the Compose File Example
This document outlines the process of deploying AdGuard Home, a privacy-focused DNS service, utilizing Docker Compose. The configuration provided utilizes the latest AdGuard Home image from Docker Hub. Below are detailed steps and explanations based on the `docker-compose.yml` file:

```yaml
services:
  adguard:
    image: adguard/adguardhome:latest 
    container_name: adguard          # Name of the Docker container
    networks:
      - exampleproxybridge        # Network that this service will connect to (We are using exampleproxybridge as example from phase 3. Networks)
    ports:
      - "53:53/tcp"              # Port mapping for DNS over TCP (default port 53)
      - "53:53/udp"              # Port mapping for DNS over UDP (default port 53)
      - "80:80/tcp"              # Port mapping for HTTP traffic (default port 80) (we will later delete this line)
      - "3000:3000/tcp"          # Port mapping for the AdGuard Home management interface (we will later delete this line)
    volumes:
      - adguard_work:/opt/adguardhome/work  # Volume for AdGuardHome work directory
      - adguard_conf:/opt/adguardhome/conf  # Volume for AdGuardHome configuration files
    environment:
      - TZ=         # Configure the timezone (format: 'Continent/City')
    restart: unless-stopped       # Restart policy, container will always start unless stopped manually
volumes:
  adguard_work:  
  adguard_conf:  

networks:
  exampleproxybridge:          
    external: true              # Use an existing network or create one if it doesn't exist
```

### Explanation of the Deployment Configuration
1. **Service Definition**: The `adguard` service is defined using the AdGuard Home image from Docker Hub. 
2. **Environment Variables**: Setting the timezone (`TZ`) can be customized based on your preference. Adjust this according to your local time zone.
3. **Restart Policy**: The container will automatically restart unless it is manually stopped, ensuring minimal downtime.
4. **Volume Mounts**: Data stored in `/opt/adguardhome/` (work directory and configuration files) are persisted using Docker volumes (`adguard_work` and `adguard_conf`), which helps in data persistence across container restarts or updates.
5. **Networks Configuration**: The service connects to the `exampleproxybridge` network maps the ports 80 and 3000 to the host machine, allowing access to the AdGuard Home web interface and setup page. After we configure our DNS and reverse proxy we can remove those ports so Adguard Home is only accessible through an encrypted URL.

## Accessing AdGuard for the First Time
Upon initial deployment, you can access AdGuard Home via a web browser at:
- **Setup Page**: `http://192.168.0.5:3000` - This is where you set up your user account and configure basic settings such as DNS servers and HTTP dashboard port.
- **Dashboard After Setup**: Once the setup is complete, navigate to `http://192.168.0.5:80` for accessing the web interface of AdGuard Home.

### Setting Up DNS on Windows
To set up DNS on a Windows machine to use AdGuard at `192.168.0.5`, follow these steps:
1. Open **Control Panel** > **Network and Sharing Center**.
2. Click on your active network connection, then click **Properties**.
3. Select **Internet Protocol Version 4 (TCP/IPv4)**, and click **Properties**.
4. Choose **Use the following DNS server addresses**:
   - Primary: `192.168.0.5`
   - Secondary: Optionally, you can add a secondary DNS if needed.
5. Click **OK** to apply these changes.

### Setting Up DNS on Ubuntu/Debian
To configure AdGuard as the primary DNS server on an Ubuntu or Debian machine, update your network settings with the following commands:
1. Open terminal and edit the network configuration file: `sudo nano /etc/network/interfaces` (or use `/etc/netplan/*.yaml` for newer versions).
2. Add or modify the lines to include the DNS server IP:
   ```bash
   dns-nameservers 192.168.0.5
   ```
3. Save and close the file, then restart networking services with `sudo systemctl restart networking` (or apply changes for newer versions).
(At this point is a smart idea to replicate this command on your server, so you can use AdGuard as your DNS server, and if possible on your router so your entire local network uses Adguard, as we later will encrypt the traffic as well with DNS over HTTPS).

## Conclusion
By following this guide, we have successfully deployed AdGuard Home as a DNS container using Docker Compose, accessible via `http://192.168.0.5:80`. This setup provides an efficient and customizable solution for managing DNS queries while enhancing privacy on both Windows and Linux systems.

## Next Steps
To further enhance security and manageability of the service:
- **Reverse Proxy**: Set up a reverse proxy with SSL certificates managed by Traefik, Nginx, or another web server to handle domain access and secure the traffic. This setup involves configuring additional containers and adjusting network configurations to route external requests through AdGuard.
- **Encryption**: Encrypt traffic to protect against eavesdropping. Though port 53 is reserved for DNS, consider using TLS (Transport Layer Security) encryption for DNS over HTTPS (DoH).


By incorporating these enhancements, we will not only improve security but also optimize usability of AdGuard Home as our DNS service for both administrators and users.
