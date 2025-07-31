## First Steps:
1. Spun up a Linux Fedora Server on Proxmox 
2. SSH username@ipofserver 
3. Created new user
	1. sudo useradd -m username 
	2. sudo passwd username 
	3. Changed permissions 
	4. Installed Navigator 
		1. sudo dnf install cockpit-navigator -y
	5. Install docker 
		1. curl -fsSL https://get.docker.com -o get-docker.sh
 
  
		2. `sudo sh get-docker.sh`
	6. Install docker-compose plugin 
		1. sudo dnf install docker-compose-plugin -y

	7. sudo systemctl start docker
	8. sudo systemctl enable docker
	9. Created macvlan
		1. sudo docker network create -d macvlan \
  --subnet=192.168.50.0/24 \
  --gateway=192.168.50.1 \
  -o parent=ens18 \
  treafkik_dev
  1. Created docker-compose.yml under /root/portainer-compose

### Step 1: Adjust Permissions for Critical Files
1. **Check and Adjust Permissions for PAM Configuration File:**
   ```bash
   sudo chmod 644 /etc/pam.d/cockpit
   sudo chown root:root /etc/pam.d/cockpit
   ```

2. **Check and Adjust Permissions for Sudoers File:**
   ```bash
   sudo chmod 440 /etc/sudoers
   sudo chown root:root /etc/sudoers
   ```

### Step 2: Adjust Permissions for Cockpit Configuration
1. **Check and Adjust Permissions for Cockpit Configuration Directory:**
   ```bash
   sudo chmod 755 /etc/cockpit
   sudo chown -R root:root /etc/cockpit
   ```

### Step 3: Restart Cockpit
After adjusting the permissions, restart Cockpit to apply the changes:

```bash
sudo systemctl restart cockpit
```

### Verify Permissions
Try logging into Cockpit again and attempt to escalate to admin. If you're still encountering issues, please provide any additional error messages or logs from Cockpit. This will help in diagnosing the problem further.

If you need any more guidance or run into any issues, I'm here to help!