##This guide covers:
1. Creating a Linux VM on Azure
2. Installing PostgreSQL
3. Configuring PostgreSQL for local and remote access

---

## ✅ Prerequisites

- Active **Azure subscription**
- Access to [Azure Portal](https://portal.azure.com)
- SSH client (Linux on Windows)

---

## 🧰 Part 1: Create a Linux VM on Azure

### 1. Go to Azure Portal
- Navigate to: [https://portal.azure.com](https://portal.azure.com)
- Go to **"Virtual Machines"**
- Click **"+ Create" > "Azure virtual machine"**

### 2. Configure VM Basics
- **Subscription**: Choose your active subscription ( subscribe if you do not have an active subscription)
- **Resource Group**: Create/select one
- **VM Name**: e.g., `linux-postgres-vm`
- **Region**: Closest to your location
- **Image**: `Ubuntu 22.04 LTS`
- **Size**: e.g., `Standard B1s`
- **Authentication**: - `SSH public key` (recommended)
  - **Password**:e.g, `1234`
- **Username**: e.g., `myLinuxServer`
- **SSH public key**: Paste your **public key** if using SSH
    > You can generate one using:
    ```bash
    ssh-keygen -t rsa -b 2048
    ```


### 3. Networking
- **Public IP**: Enabled
- **NSG (firewall)**: Allow SSH (port 22) 

### 4. Create the VM
- Click **"Review + create"** then **"Create"**

---

## 🔌 Part 2: SSH into the VM

```bash
ssh azureuser@<your-vm-public-ip- e.g, 192.168.20.139>
```

---

## 🐘 Part 3: Install PostgreSQL

### 1. Update Packages
```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install PostgreSQL
```bash
sudo apt install postgresql postgresql-contrib -y
```

### 3. Enable and Start the Service
```bash
sudo systemctl enable postgresql
sudo systemctl start postgresql
```

---

## 🔐 Part 4: Configure PostgreSQL

### 1. Switch to postgres User
```bash
sudo -i -u postgres
```

### 2. Access PostgreSQL Terminal
```bash
psql
```

### 3. Create User and Database
```sql
CREATE ROLE e.g, myPostgres WITH LOGIN PASSWORD e.g, '1234';
ALTER ROLE myPostgres CREATEDB;
CREATE DATABASE student OWNER myPostgres;
\q
exit
```

---

## 🌐 Part 5: Allow Remote Access (Optional)

### 1. Edit postgresql.conf
```bash
sudo nano /etc/postgresql/15/main/postgresql.conf
```

Find:
```
listen_addresses = 'localhost'
```

Change to:
```
listen_addresses = '*'
```

### 2. Edit pg_hba.conf
```bash
sudo nano /etc/postgresql/15/main/pg_hba.conf
```

Add this line:
```
host    all             all             0.0.0.0/0               md5
```

### 3. Allow Port 5432 in Azure NSG
- Go to Azure portal > VM > **Networking**
- Click **"Add inbound port rule"**
  - Port: `5432`
  - Protocol: TCP
  - Action: Allow

### 4. Restart PostgreSQL
```bash
sudo systemctl restart postgresql
```

---

## 🧪 Part 6: Connect Remotely

From your local pc:
```bash
psql -U myPostgres -d student -h <192.168.20.139> -p 5432
```

Or use GUI tools such as **DBeaver**. 

---

Your Linux VM is now running PostgreSQL and ready to accept connections.
