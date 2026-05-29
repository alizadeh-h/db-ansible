# Ansible Automation for MariaDB Galera Cluster & HAProxy

This project provides an automated solution using Ansible to deploy a High Availability (HA) MariaDB Galera Cluster, paired with an HAProxy load balancer in an Active-Passive (automatic failover) configuration.

## 📂 Project Structure
- **`inventory`**: Inventory file defining cluster nodes and their assigned roles (Master/Backup).
- **`roles`**: Modular roles for installing and configuring MariaDB and HAProxy.
- **`main.yml`**: The primary Ansible Playbook to deploy the entire stack across the nodes.
- **`secret.yml.example`**: Template for sensitive variables (database passwords). This must be configured locally as `secret.yml`.

---

## 🚀 Quick Start Guide

1. **Copy the configuration template:**
   Create your local `secret.yml` file from the example and add your actual credentials:
```bash
   cp secret.yml.example secret.yml
   nano secret.yml
   
Edit the Inventory:

Update the inventory file with the correct IP addresses of your servers.

Deploy the cluster:

Run the following command to provision the database cluster and load balancer:
```
 ansible-playbook -i inventory/hosts.ini main.yml --ask-vault-pass
```
Failover Testing Guide
Follow these steps to verify that your system automatically handles database server failure:

1. Check Normal Status
From your client machine or the HAProxy server (192.168.1.125), query the database to identify which node is currently active:
```
mysql -u appuser -p -h HAPROXYIP -e "SELECT @@hostname;"
```

Access your Master node (e.g., 192.168.1.122) and stop the MariaDB service to simulate a crash:

```
sudo systemctl stop mariadb

```
Verify Failover
Immediately run the connection query again from the HAProxy IP:

```
mysql -u appuser -p -h HAPROXYIP -e "SELECT @@hostname;"

```

Check HAProxy Logs
To see real-time failover logs, run this on the HAProxy server:

```
journalctl -u haproxy -f

```

Recovery
Start the service on the Master node again:
```
sudo systemctl start mariadb

```
journalctl -u haproxy -f
