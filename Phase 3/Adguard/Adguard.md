# Setting Up AdGuard Home with Docker Compose

This guide will help you set up AdGuard Home using Docker Compose. AdGuard Home is a DNS sinkhole and adblocker that can be easily managed through a web interface.

## Prerequisites
- [Docker](https://www.docker.com/products/docker-desktop) installed on your machine.
- Basic understanding of how to use the command line for Docker commands.

## Getting Started

1. **Clone this repository** (or download the `docker-compose.yml` file directly):
   ```sh
   git clone https://github.com/your-repo/adguard-home.git
   cd adguard-home
   ```

2. **Run Docker Compose**:
   ```sh
   docker-compose up -d
   ```
   This command will download the necessary images and start the AdGuard Home service in detached mode.

3. **Access AdGuard Home for Initial Setup** (only on the first run):
   - Open your web browser.
   - Navigate to `http://<your-host-ip>:3000`.
     - Note: Replace `<your-host-ip>` with the actual IP address of your host machine.
   - Follow the instructions to complete the setup, including creating an admin account.

4. **Access AdGuard Home after Setup** (regular use):
   - Navigate to `http://<your-host-ip>:80`.
     - You can now manage AdGuard Home using the web interface without needing port 3000.

5. **Secure Your Access**:
   - Once you have set up DNS and a reverse proxy (like Nginx or Apache), you can secure your access by removing unnecessary port mappings from the `docker-compose.yml` file and configuring your reverse proxy to use encrypted URLs.
     ```yaml
     ports:
       - "53:53/tcp"
       - "53:53/udp"
       # Remove or comment out these lines if not needed for specific services
       - "80:80/tcp"
       - "3000:3000/tcp"
     ```
   - Configure your reverse proxy to handle the traffic through encrypted URLs, ensuring that only secure connections are used.

## Configuration Details

- **Container Name**: `adguard`
- **Image**: `adguard/adguardhome:latest`
- **Networks**: Connects to a network named `exampleproxybridge`. Ensure this network is defined elsewhere in your Docker Compose setup or create it as needed.
- **Ports**:
  - DNS over TCP and UDP on ports 53.
  - HTTP traffic initially mapped to port 80, which will be removed later for enhanced security.
  - The AdGuard Home management interface initially accessible at port 3000, also planned to be removed after setup completion.
- **Volumes**:
  - `adguard_work` and `adguard_conf` volumes are used for persistent storage of AdGuardHome data and configurations respectively.
- **Environment Variables**: You can optionally set the timezone using `TZ`.
- **Restart Policy**: The container will always start unless stopped manually, configured with `restart: unless-stopped`.

By following these steps, you will have AdGuard Home up and running efficiently while securing your access through encrypted URLs.
