# Zabbix-7.4-Installation-Guide-for-Ubuntu-24.04-Zabbix-7.4

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
  PermitRootLogin yes

Restart SSH:

```bash
      sudo systemctl enable ssh
```   
```bash
      sudo systemctl restart ssh
 ```

3. Install Apache and PHP:

      sudo apt install apache2
      sudo apt install php php-{cgi,common,mbstring,net-socket,gd,xml-util,mysql,bcmath,imap,snmp}
      sudo apt install libapache2-mod-php
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
4. Install & Configure Zabbix:

a. Switch to root:

      sudo -s

b. Install Zabbix Repository:

      wget https://repo.zabbix.com/zabbix/7.4/release/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.4+ubuntu24.04_all.deb
      dpkg -i zabbix-release_latest_7.4+ubuntu24.04_all.deb
      apt update

c. Install Zabbix Components:

      apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
3. Install & Configure MySQL

a. Install MySQL Server:

      sudo apt update
      sudo apt install mysql-server -y
      sudo systemctl start mysql
      sudo systemctl enable mysql
      sudo systemctl status mysql

b. Secure MySQL Installation:

      sudo mysql_secure_installation

Recommended options:

Enter current root password → Press Enter
Set root password? → n
Remove anonymous users? → y
Disallow remote root login? → y
Remove test database? → y
Reload privilege tables? → y

4. Create Zabbix Database & User:

Log in to MySQL:

      mysql -u root -p

Run the following inside the MySQL shell:

# mysql -uroot -p
password
mysql> create database inzabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user inzabbix@localhost identified by 'password@321';
mysql> grant all privileges on inzabbix.* to inzabbix@localhost;
mysql> set global log_bin_trust_function_creators = 1;
mysql> quit;
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

5. Import Zabbix Database Schema:

# zcat /usr/share/zabbix/sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uinzabbix -p inzabbix

Put The Password: database user

Disable trust flag:

      mysql -u root -p

      SET GLOBAL log_bin_trust_function_creators = 0;
      QUIT;

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

6. Configure the Zabbix Server:

Edit the configuration:

sudo vi /etc/zabbix/zabbix_server.conf
Set the database password:

      DBPassword=Password@321
      DBName=inzabbix
      DBUser=inzabbix

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

7. Start & Enable Zabbix Services

      # sudo systemctl restart zabbix-server zabbix-agent apache2
      # sudo systemctl enable zabbix-server zabbix-agent apache2

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

8. Open the Zabbix UI web page

The default URL for Zabbix UI when using the Apache web server is http://host/zabbix

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

9. Configure the Zabbix Agent (Client Machine):

(Optional) Install Zabbix Agent on Additional Client Machines:

# wget https://repo.zabbix.com/zabbix/7.4/release/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.4+ubuntu24.04_all.deb
# dpkg -i zabbix-release_latest_7.4+ubuntu24.04_all.deb
# apt update
Edit the agent configuration:

sudo nano /etc/zabbix/zabbix_agentd.conf

Update these settings:

      # Server=<Zabbix_Server_IP>
      # ServerActive=<Zabbix_Server_IP>
      # Hostname=<Client_Hostname>

Restart and enable the agent:

      # sudo systemctl restart zabbix-agent
      # sudo systemctl enable zabbix-agent

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
