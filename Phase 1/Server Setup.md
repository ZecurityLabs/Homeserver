# Setting Up a Barebone Server
## Hardware Overview
For this project, we will utilizing my old laptop which has:
- No dedicated GPU
- Modest RAM (~8GB)
- An older CPU
- Reliable Ethernet port for network connectivity
- SSD for faster loading times and storage.
## OS Choice
We will opt for Debian 12 due to its stability and extensive support lifecycle, despite CentOS being a popular choice. However, considering the end of life cycle for CentOS, I decided on a more future-proof option by choosing Debian.
## Headless Mode
Given the focus on cybersecurity, we shall install the server in headless mode. This decision significantly reduces the attack surface since there's no graphical interface. While at first, it might have been useful to start with a GUI for easier setup (especially when learning), the reduced interface is beneficial in the long run. We will later access Docker containers and other administrative tools through client browsers as part of our project.

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


## Conclusion
We achieved headless server installation, created a user, set a static ip address to our server, installed the minimum services and dependencies (SSH, Docker) and added the user to the docker group. 

On Phase 2 we will secure our access to the server and take some security procautions.

## Phase 1 Reflection
Phase 1 was pretty straightforward on the goals and how to reach them. My network and linux background made this phase pretty short and easy. As I did not have to look up much information to complete this phase.
 