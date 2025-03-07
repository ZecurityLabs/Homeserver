# Phase 2: Secure Local Access

## Introduction
In this phase, we will enhance the security of our local access by implementing several measures to protect our server from unauthorized access and potential threats. We will focus on configuring SSH for secure remote access, managing firewall settings with UFW (Uncomplicated Firewall), and ensuring that only authorized users can connect to our server.

## Steps

1. **Configure SSH**
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

2. **Configure hosts.conf for Visual Studio Code**
  - Add the following line to `/etc/hosts`:
    ```plaintext
    Host name (Name your connection)
    HostName 192.168.0.5 (the static ip of your server)
    Port 2222 (the port you configured)
    User username (your username)
    IdentityFile ~/.ssh/id_rsa (the path to your private key)
    ```
  - Then, in VSCode, configure SSH remote by entering `ssh://username@myserver.local` and follow the prompts.

3. **Install UFW Firewall**
  - Install ufw and enable it:
    ```bash
    sudo apt install ufw -y
    sudo ufw enable
    ```

4. **Configure UFW Firewall**
  - Allow outgoing traffic but deny inbound traffic, and allow inbound traffic only from the users local IP range (e.g., 192.168.0.0/24):
    ```bash
    sudo ufw default deny incoming
    sudo ufw default allow outgoing
    sudo ufw allow from 192.168.0.0/24 to any port
    sudo ufw status verbose
    ```

## Disabling systemd-resolved for Adguard Home on Debian

Most modern Debian-based distributions use `systemd-resolved` as a built-in DNS resolver that listens on port 53. To avoid conflicts when running a Adguard Home container, we need to disable this service.

### Steps to Disable systemd-resolved

1. **Access the Debian Server Command Line**  
   Connect to our Debian server via SSH.

2. **Modify systemd-resolved Configuration**  
   Open the configuration file:
   ```bash
   sudo nano /etc/systemd/resolved.conf
   ```
   Locate the following line:
   ```
   #DNSStubListener=yes
   ```
   Change it to:
   ```
   DNSStubListener=no
   ```
   Next, specify our AdGuard container as the DNS provider by finding the `[Resolve]` section and adding or modifying the following line:
   ```
   DNS=192.168.0.05
   ```
   *(Replace `192.168.0.5` with the IP of your AdGuard container Host IP address.)*  
   
   Save and exit: Press `Ctrl+X`, then `Y`, and hit `Enter`.

3. **Update resolv.conf Symlink**  
   Run the following command to update the symlink:
   ```bash
   rm /etc/resolv.conf && ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
   ```

4. **Restart systemd-resolved**  
   Apply the changes by restarting the service:
   ```bash
   systemctl restart systemd-resolved
   ```

After completing these steps, `systemd-resolved` will stop listening on port 53, allowing Adguard Home to use it without conflicts.

## Conclusion
We have successfully enhanced the security of our local access by configuring SSH for secure remote access, managing firewall settings with UFW, and ensuring that only authorized users can connect to our server. These steps have significantly improved the overall security posture of our system.

## Phase 2 Reflection
This phase was also a short one, as i am already familiar with the linux firewall and how to establish SSH connections. I did had to learn how to implement public key authentication, which was a new concept for me. I also learned how to configure UFW to allow only specific IP ranges to connect to the server. Network security was not a new concept for me, but the hands on practice was great and fun 
