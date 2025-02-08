# Deploying NGINX Proxy Manager with Docker Compose and Configuration Guide

## Introduction and Purpose of a Proxy Container
NGINX Proxy Manager (NPM) is a powerful tool designed to manage reverse proxies with ease. It allows users to create, delete, and modify proxies through a web interface while handling SSL certificates automatically via Let's Encrypt. This setup ensures secure and efficient proxy management without the need for deep technical knowledge.

## Deployment of the Container Using the Compose File Example
This document outlines the process of deploying NGINX Proxy Manager (NPM) utilizing Docker Compose. The configuration provided utilizes the latest NPM image from Docker Hub. Below are detailed steps and explanations based on the `docker-compose.yml` file:

```yaml
services:
  app:
    image: jc21/nginx-proxy-manager:latest 
    container_name: npm # Name of the Docker container
    restart: unless-stopped # Restart policy, container will always start unless stopped manually
    volumes:
      - npm:/data # Volume for NPM data storage
      - npm:/etc/letsencrypt # Volume for SSL certificates
    networks:
      - exampleproxybridge # Network that this service will connect to
    ports:
      - '443:443' # Port mapping for HTTPS traffic (default port 443)
      - '81:81'   # Port mapping for the NPM web interface (default port 81). This line will be removed once proxy setup is complete.
    networks:
      exampleproxybridge:
        external: true # Use an existing network or create one if it doesn't exist

volumes:
  npm:
```

## Explanation of the Deployment Configuration
1. **Service Definition**: The `app` service is defined using the NGINX Proxy Manager image from Docker Hub.
2. **Restart Policy**: The container will automatically restart unless it is manually stopped, ensuring minimal downtime.
3. **Volume Mounts**: Data stored in `/data` (for NPM data) and `/etc/letsencrypt` (for SSL certificates) are persisted using Docker volumes (`npm`), which helps in data persistence across container restarts or updates.
4. **Networks Configuration**: The service connects to the `exampleproxybridge` network, ensuring that it can communicate with other services on this network. The ports 443 and 81 are mapped to the host machine, allowing access to the NPM web interface and proxy management. After we pair everything together we can remove the port 81 so our admin dashboard is available only through encrypted URL.

## Accessing NGINX Proxy Manager 
After deployment, we can access NPM via a web browser at:
- **Web Interface**: `http://192.168.0.5:81` - This is where we manage your proxies and SSL certificates.


## Conclusion
By following this guide, we have successfully deployed NGINX Proxy Manager as a proxy container using Docker Compose, accessible via `http://192.168.0.5:81`. This setup provides an efficient and customizable solution for managing proxies while enhancing security on your local network.

## Next Steps
To further enhance the usability of the service, consider setting up additional features such as:
- **SSL Certificates Management**: Automate SSL certificate renewal to ensure uninterrupted service.
- **Custom Domain Names**: Configure custom domain names for your proxies to make them more user-friendly and accessible.
- **DNS over HTTPS (DoH)**: Implement DoH to enhance privacy and security for DNS queries.
- **Remove port mapping from Adguard and NPM compose files**: This will make the admin dashboards available only through encrypted URLs. Containers will need to restart to apply this change.


By incorporating these enhancements, we will not only improve usability but also optimize the functionality of NGINX Proxy Manager as our proxy solution.
