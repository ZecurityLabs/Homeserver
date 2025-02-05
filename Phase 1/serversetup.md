# Setting Up a Barebone Server
## Hardware Overview
For this project, I utilized my old laptop which has:
- No dedicated GPU
- Modest RAM (~8GB)
- An older CPU
- Reliable Ethernet port for network connectivity
- SSD for faster loading times and storage.
## OS Choice
I opted for Debian 12 due to its stability and extensive support lifecycle, despite CentOS being a popular choice. However, considering the end of life cycle for CentOS, I decided on a more future-proof option by choosing Debian.
## Headless Mode
Given the focus on cybersecurity, I chose to install the server in headless mode. This decision significantly reduces the attack surface since there's no graphical interface. While at first, it might have been useful to start with a GUI for easier setup (especially when learning), the reduced interface is beneficial in the long run. We will later access Docker containers and other administrative tools through client browsers as part of our project.
## Installation Steps (Pseudo Code)

1. **Set Static IP Address**:
   - Edit network configuration to set a static IP address `192.168.0.5` for your server:
     ```bash
     sudo nano /etc/netplan/01-netcfg.yaml
     # Add or modify the following lines:
     network:
       version: 2
       ethernets:
         eth0:
           dhcp4: false
           addresses:
             - 192.168.0.5/24
           gateway4: 192.168.0.1
           nameservers:
             addresses:
               - 8.8.8.8
               - 8.8.4.4
     # Apply the changes:
     sudo netplan apply
     ```
   - Verify that the IP address has been set correctly with `ip addr` or `ifconfig`.

2. **Create a Local User**:
- Use the following command to create a new user:
```bash
sudo adduser username
```
3. **Update and Upgrade Sudo**:
- Update package lists and upgrade installed packages with:
```bash
sudo apt update && sudo apt upgrade -y
```
4. **Enable Automatic Security Updates**:
- Install `unattended-upgrades` to enable automatic security updates:
```bash
sudo apt install unattended-upgrades -y
```
- Configure the package manager to automatically apply security updates by editing `/etc/apt/apt.conf.d/50unattended-upgrades`.
5. **Get Docker Repository**:
- Add Docker's official GPG key and set up the stable repository:
```bash
sudo apt install ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
6. **Get Docker Dependencies**:
- Update the package list and install Docker:
```bash
sudo apt update && sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y
```
7. **Add User to Docker Group**:
- Add your user to the `docker` group to allow non-root usage:
```bash
sudo usermod -aG docker username
```
8. **Install SSH**:
- Install OpenSSH server using:
```bash
sudo apt install openssh-server -y
```
9. **Configure SSH**:
   - Change the default SSH port to a custom one (e.g., 2222):
     ```bash
     sudo nano /etc/ssh/sshd_config
     # Change Port from 22 to 2222
     Port 2222
     ```
   - Generate private and public key with a passphrase:
     ```bash
     ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
     # Follow the prompts to save the key in ~/.ssh/id_rsa with a passphrase.
     ```
   - Disable root login and password login through SSH:
     ```bash
     sudo nano /etc/ssh/sshd_config
     # Change or add the following lines:
     PermitRootLogin no
     PasswordAuthentication no
     PubkeyAuthentication yes
     # Restart SSH service to apply changes:
     sudo systemctl restart ssh
     ```
   - Example of how to connect to the server using SSH with the key (using terminal):
     ```bash
     ssh -i ~/.ssh/id_rsa username@192.168.0.5
     ```
10. **Configure hosts.conf for Visual Studio Code**:
    - Add the following line to `/etc/hosts`:
       ```plaintext
      Host name (Name your connection)
      HostName 192.168.0.5 (the static ip of your server)
      Port 2222 (the port you configured)
      User username (your username)
      IdentityFile ~/.ssh/id_rsa (the path to your private key)
      ```
   Then, in VSCode, configure SSH remote by entering `ssh://username@myserver.local` and follow the prompts.
    - Then, in VSCode, configure SSH remote by entering `ssh://username@myserver.local` and follow the prompts.
    
11. **Install UFW Firewall**:
    - Install ufw and enable it:
      ```bash
      sudo apt install ufw -y
      sudo ufw enable
      ```
12. **Configure UFW Firewall**:
    - Allow outgoing traffic but deny inbound traffic, and allow inbound traffic only from the users local IP range (e.g., 192.168.0.0/24):
      ```bash
      sudo ufw default deny incoming
      sudo ufw default allow outgoing
      sudo ufw allow from 192.168.0.0/24 to any port 2222
      sudo ufw status verbose
      ```
## Conclusion
We achieved headless server installation, created a user, installed the minimum services (SSH, Docker, UFW), and secured our server by making sure no one can SSH into it especially not as root. Configured the firewall so only local devices can access our server and installed Docker and added the user to the Docker group so the user can make and run images and containers.
