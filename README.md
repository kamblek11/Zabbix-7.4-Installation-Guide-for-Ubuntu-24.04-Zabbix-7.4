# Zabbix-Installation-Guide-for-Ubuntu-24.04-Zabbix-7.4

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
1. **Update System & Install Required Packages:**

      ```bash
      sudo apt update && sudo apt upgrade -y
      ```
     ```bash
      sudo apt install openssh-server
      ```
     ```bash
      sudo systemctl status ssh
      ```
     ```bash
      sudo ufw allow ssh
      ```


3. **(Optional) Enable SSH root login:**

   ```bash
   sudo vi /etc/ssh/sshd_config
   ```
   
Update the following parameter:
  **PermitRootLogin yes**

Restart SSH:

```bash
      sudo systemctl enable ssh
```   
```bash
      sudo systemctl restart ssh
 ```

# 3. Install Apache and PHP:

```bash
sudo apt install apache2
```
```bash
sudo apt install php php-{cgi,common,mbstring,net-socket,gd,xml-util,mysql,bcmath,imap,snmp}
```
```bash
sudo apt install libapache2-mod-php
```
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 4. Install & Configure Zabbix:

a. Switch to root:

 ```bash
 sudo -s
 ```

b. **Install Zabbix Repository:**

```bash
wget https://repo.zabbix.com/zabbix/7.4/release/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.4+ubuntu24.04_all.deb
```
```bash
dpkg -i zabbix-release_latest_7.4+ubuntu24.04_all.deb
```
```bash
apt update
```

c. **Install Zabbix Components:**

```bash
apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# 3. Install & Configure MySQL

a. **Install MySQL Server:**

```bash
sudo apt update
```
```bash
sudo apt install mysql-server -y
```
```bash
sudo systemctl start mysql
```
```bash
sudo systemctl enable mysql
```
```bash
sudo systemctl status mysql
```
b. **Secure MySQL Installation:**

```bash
sudo mysql_secure_installation
```
**Recommended options:**

      Enter current root password → Press Enter
      Set root password? → n                   
      Remove anonymous users? → y              
      Disallow remote root login? → y          
      Remove test database? → y                 
      Reload privilege tables? → y             

4. **Create Zabbix Database & User:**

Log in to MySQL:

```bash
mysql -u root -p
```
# Run the following inside the MySQL shell:

mysql -uroot -p
password
```bash
create database inzabbix character set utf8mb4 collate utf8mb4_bin;
create user inzabbix@localhost identified by 'password@321';
grant all privileges on inzabbix.* to inzabbix@localhost;
set global log_bin_trust_function_creators = 1;
quit;
```
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

5. **Import Zabbix Database Schema:**

```bash
zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uinzabbix -p inzabbix
```
**Put The Password: database user:**

Disable trust flag:

```bash
mysql -u root -p
```
```bash
SET GLOBAL log_bin_trust_function_creators = 0;
```
```bash
QUIT;
```
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

6. **Configure the Zabbix Server:**

Edit the configuration:

```bash
sudo vi /etc/zabbix/zabbix_server.conf
```
Set the database password:

      DBPassword=Password@321
      DBName=inzabbix
      DBUser=inzabbix

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

7. **Start & Enable Zabbix Services:**

```bash
sudo systemctl restart zabbix-server zabbix-agent apache2
```
```bash
sudo systemctl enable zabbix-server zabbix-agent apache2
```
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 8. Open the Zabbix UI web page

The default URL for Zabbix UI when using the Apache web server is **http://host/zabbix**

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 9. Configure the Zabbix Agent (Client Machine):

(Optional) **Install Zabbix Agent on Additional Client Machines:**

```bash
wget https://repo.zabbix.com/zabbix/7.4/release/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.4+ubuntu24.04_all.deb
```
```bash
dpkg -i zabbix-release_latest_7.4+ubuntu24.04_all.deb
```
```bash
apt update
```

**Edit the agent configuration:**

```bash
sudo nano /etc/zabbix/zabbix_agentd.conf
```

**Update these settings:**

Server=<Zabbix_Server_IP>
ServerActive=<Zabbix_Server_IP>
Hostname=<Client_Hostname>

Restart and enable the agent:

```bash
sudo systemctl restart zabbix-agent
```
```bash
sudo systemctl enable zabbix-agent
```

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Thank You
