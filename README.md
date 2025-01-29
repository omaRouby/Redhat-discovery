### Preparing for the installation of **Discovery** on a dedicated **RHEL system** .  

---

## **🔹 Hardware Requirements**  
The system should meet or exceed the following specifications:  
- **CPU:** Minimum **2 cores**, recommended **4 cores**  
- **RAM:** Minimum **2 GB**, recommended **4 GB**  
- **Disk Storage:** **30 GB** (Ensure the partition containing `${HOME}/.local/share/containers` has sufficient space)  

---

## **🔹 Software Requirements**  
- **Operating System:** Latest **RHEL 8** or **RHEL 9** (clean install, not upgraded from RHEL 7)  
- **File System:** Must support **d_type** (Podman requirement)  
- **cgroups v2:** Must be enabled  
- **Podman Version:** **4.9.4 or later**  
- **Sudo Privileges:** Required for Podman installation  

---

## **🔹 Environment & Network Requirements**  
1️⃣ **Dedicated System**:  
   - The system must be used **exclusively** for Discovery (no other workloads).  

2️⃣ **Internet & Subscription Access**:  
   - The system must have an **active Red Hat subscription** to access repositories.  
   - Must be able to connect to the **Red Hat Ecosystem Catalog**.  
   - For disconnected installations, another system must meet this requirement.  

3️⃣ **SSH Access for Network Scans**:  
   - The Discovery system must have **SSH access** to all target systems for scanning.  
   - Target systems must support SSH and have a **valid user account** with necessary permissions.  

4️⃣ **Firewall & Port Access**:  
   - If remote access is needed, **TCP port 9443** must be open.  
   - Ensure **firewall rules** allow incoming connections to this port.  

---

## **🔹 Additional Post-Installation Considerations**  
- The user account running Podman for Discovery **must not be root** and must not use `sudo` or `su`.  
- If persistent login is required, enable lingering for the user:  
  ```bash
  loginctl enable-linger $USER
  ```
### **📌 Steps to Install Discovery on a Connected RHEL System**  

#### **🔹 Prerequisites**  
Ensure the following before starting the installation:  
✅ The system meets **hardware, software, and network requirements**.  
✅ The system has **internet access** and is registered with **Red Hat Subscription Management**.  
✅ You have **sudo privileges** to install required packages.  
✅ The system can connect to the **Red Hat Ecosystem Catalog**.  


## **🚀 Installation Steps**  

### **Step 1️⃣: Enable the Required Repository**  
Run the following command based on your RHEL version:  
```bash
# For RHEL 8
sudo subscription-manager repos --enable discovery-1-for-rhel-8-x86_64-rpms

# For RHEL 9
sudo subscription-manager repos --enable discovery-1-for-rhel-9-x86_64-rpms
```

---

### **Step 2️⃣: Install the Discovery Installer**  
```bash
sudo dnf install -y discovery-installer
```

---

### **Step 3️⃣: Log in to Podman**  
```bash
podman login registry.redhat.io
```
🔹 Enter your **Red Hat Customer Portal credentials** when prompted.  

---

### **Step 4️⃣: Install Discovery Using the Installer**  
```bash
discovery-installer install
```
🔹 You will be prompted to **set a server password**—enter a secure password.  

---

### **Step 5️⃣: Start the Discovery Application**  
```bash
systemctl --user start discovery-app
```
⚡ **Note:** The first startup may take some time.  

---

### **Step 6️⃣: Verify the Installation & Running Status**  
Check the application status:  
```bash
systemctl --user status discovery-app
```
Check running containers:  
```bash
podman ps -a
```
Check logs if needed:  
```bash
journalctl -t discovery-app
journalctl -t discovery-server
podman logs --since 1m -f discovery-server
```

---

## **🔹 Post-Installation Steps**  

### **1️⃣ Change the Discovery Admin Password (Optional but Recommended)**  
To change the Discovery password:  
```bash
discovery-installer create-server-password
```
🔹 Follow the prompts to set a new password.  

Restart the Discovery service to apply changes:  
```bash
systemctl --user restart discovery-app
```
⚠ **Important:** Red Hat does **not** provide support for lost passwords.  

---

### **2️⃣ Enable Persistent Login for Discovery**  
To ensure Discovery remains running after logging out:  
```bash
loginctl enable-linger $USER
```

---

### **3️⃣ Allow Remote Access (If Needed)**  
To allow access from another computer in the same network, open **TCP port 9443**:  
```bash
sudo firewall-cmd --add-port=9443/tcp --permanent
sudo firewall-cmd --reload
```



### **✅ Installation Complete!**  
You can now access Discovery and start using it. Let me know if you need any further assistance! 🚀
---

