
## **How to Set Up a Public Ubuntu Server with Nginx**

**By rMi99**

### **Introduction**

Ubuntu Server is an excellent choice for hosting websites, applications, and services due to its stability, security, and performance. This guide will take you through the process of installing Ubuntu Server, setting up Nginx as a web server, and configuring your Ubuntu PC to be accessible as a public server over the internet.

### **Step 1: Prepare for Installation**

Before you begin, ensure the following prerequisites:

- A computer with at least 4GB of available storage for Ubuntu.
- A bootable USB drive with Ubuntu Server 20.04 LTS.
- An internet connection for the server.
- A basic understanding of networking concepts, particularly public and private IP addresses.

#### **Creating a Bootable USB Drive**

1. Download the **Ubuntu Server 20.04 LTS** ISO from the official [Ubuntu download page](https://ubuntu.com/download/server).
2. Use a tool like **Rufus** to create a bootable USB drive with the ISO file.
3. Insert the USB drive into the server and restart, ensuring the server boots from the USB.

---

### **Step 2: Install Ubuntu Server**

1. After booting from the USB, follow the installation prompts:
   - Select your preferred language and keyboard layout.
   - Choose **Normal Installation** and continue.
   - Choose **Install updates and other software** to ensure you have the latest updates.
   - Select the target disk for installation.
   - Create a user account and set a strong password.

2. After the installation completes, restart the server. Your system will now be running Ubuntu Server 20.04 LTS.

---

### **Step 3: Set Static IP Address**

To make your server accessible over the internet, you need to assign it a **static IP address**. Here's how:

1. Log in to your server and identify the network interface with the `ip addr` or `ifconfig` command.

2. Edit the network configuration file:
   ```bash
   sudo nano /etc/netplan/00-installer-config.yaml
   ```

3. Set your static IP address configuration:
   ```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       ens160:  # Replace with your network interface
         addresses:
           - 192.168.1.100/24  # Replace with your IP
         gateway4: 192.168.1.1  # Replace with your gateway
         nameservers:
           addresses: [8.8.8.8, 8.8.4.4]  # Google's DNS servers
   ```

4. Apply the network configuration:
   ```bash
   sudo netplan apply
   ```

---

### **Step 4: Set Up Port Forwarding on Your Router**

To allow traffic from the internet to reach your server, configure **port forwarding** on your router.

1. Log in to your router’s admin interface (often at `192.168.1.1`).
2. Navigate to **Port Forwarding** or **NAT settings**.
3. Forward the following ports to your server’s local IP address:
   - **Port 80 (HTTP)** for web traffic.
   - **Port 443 (HTTPS)** for secure web traffic (if using SSL).
   - **Port 22 (SSH)** for remote access.

---

### **Step 5: Install Nginx Web Server**

Now that your server is prepared, install **Nginx** to serve your website.

1. Update your system and install Nginx:
   ```bash
   sudo apt update
   sudo apt install nginx
   ```

2. Enable and start the Nginx service:
   ```bash
   sudo systemctl enable nginx
   sudo systemctl start nginx
   ```

---

### **Step 6: Configure the Firewall**

To allow web traffic (HTTP and HTTPS), configure your firewall:

1. Allow HTTP and HTTPS ports:
   ```bash
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   ```

2. Enable the firewall:
   ```bash
   sudo ufw enable
   ```

3. Check the firewall status:
   ```bash
   sudo ufw status
   ```

---

### **Step 7: Configure Nginx for Your Website**

Edit the Nginx configuration to serve your website.

1. Open the default configuration file:
   ```bash
   sudo nano /etc/nginx/sites-available/default
   ```

2. Configure the `server` block to serve your website:
   ```nginx
   server {
       listen 80;
       server_name yourdomain.com;  # Replace with your public IP or domain

       root /var/www/html;  # Default web root
       index index.html;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

3. Test the Nginx configuration:
   ```bash
   sudo nginx -t
   ```

4. Restart Nginx:
   ```bash
   sudo systemctl restart nginx
   ```

---

### **Step 8: Secure Your Server with SSL**

For secure HTTPS access, obtain an SSL certificate from **Let’s Encrypt**:

1. Install Certbot:
   ```bash
   sudo apt install certbot python3-certbot-nginx
   ```

2. Run Certbot to configure SSL:
   ```bash
   sudo certbot --nginx -d yourdomain.com
   ```

3. Verify SSL by accessing your website at `https://yourdomain.com`.

---

### **Step 9: Enable SSH for Remote Access**

To manage your server remotely, ensure SSH is installed and enabled.

1. Install OpenSSH server:
   ```bash
   sudo apt install openssh-server
   ```

2. Check SSH status:
   ```bash
   sudo systemctl status ssh
   ```

3. Allow SSH in the firewall (if not already allowed):
   ```bash
   sudo ufw allow ssh
   ```

4. Connect remotely using SSH:
   ```bash
   ssh username@your_public_ip
   ```

---

### **Step 10: Set Up DNS (Optional)**

If you're using a custom domain, configure DNS settings with your domain registrar:

1. Log into your domain registrar and create an **A record** pointing to your public IP:
   - Host: `@` (or `www` for subdomains)
   - Value: `your_public_ip`

---

### **Conclusion**

Congratulations! Your **Ubuntu PC** is now set up as a **public server** using **Nginx**. You can now access it from anywhere via your **public IP** or **domain name**. By following this guide, you’ve configured your server to serve websites, secured it with SSL, and enabled remote access using SSH.

---

