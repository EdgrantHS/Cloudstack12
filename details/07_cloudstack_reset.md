# CloudStack Password Reset
This tutorial guides you through resetting the CloudStack password when you've forgotten it. Follow these steps carefully to restore access and fix the configuration.

## Step 1: Stop the CloudStack Management Server
Stop the CloudStack management server to prevent any conflicts during the reset process.

```bash
sudo systemctl stop cloudstack-management
```

## Step 2: Reset MySQL/MariaDB Root Password
If you cannot log into MySQL/MariaDB due to a forgotten password, reset it using the following steps.

1. **Stop the MySQL/MariaDB service**:
   ```bash
   sudo systemctl stop mysql
   ```

2. **Start MySQL/MariaDB in safe mode** (skipping grant tables and networking):
   ```bash
   sudo systemctl set-environment MYSQLD_OPTS="--skip-grant-tables --skip-networking"
   sudo systemctl start mysql
   ```

3. **Connect to MySQL/MariaDB as the root user** without a password:
   ```bash
   sudo mysql -u root
   ```

4. **Update the root password** (replace `teep1` with your desired password):
   ```sql
   ALTER USER 'root'@'localhost' IDENTIFIED BY 'teep1';
   FLUSH PRIVILEGES;
   ```

5. **Exit the MySQL/MariaDB prompt**:
   ```sql
   exit
   ```

6. **Stop the MySQL/MariaDB service**:
   ```bash
   sudo systemctl stop mysql
   ```

7. **Remove the safe mode option and restart MySQL/MariaDB normally**:
   ```bash
   sudo systemctl unset-environment MYSQLD_OPTS
   sudo systemctl start mysql
   ```

8. **Verify login with the new password**:
   ```bash
   sudo mysql -u root -p
   ```
   Enter the new password (`teep1`) when prompted. If successful, proceed to the next step.

## Step 3: Reset the CloudStack Database
Reset the CloudStack database to ensure a clean setup.

1. **Connect to MySQL/MariaDB**:
   ```bash
   sudo mysql -u root -p
   ```
   Enter the new root password (`teep1`) when prompted.

2. **Drop the existing CloudStack database**:
   ```sql
   DROP DATABASE cloud;
   exit
   ```

3. **Re-run the CloudStack database setup** (replace `192.168.106.199` with your management server IP):
   ```bash
   sudo cloudstack-setup-databases cloud:cloud@localhost --deploy-as=root:teep1 -i 192.168.106.199
   ```

## Step 4: Re-run CloudStack Management Server Setup
Configure the CloudStack management server:
```bash
sudo cloudstack-setup-management
```

## Step 5: Start the CloudStack Management Server
Restart the management server:
```bash
sudo systemctl start cloudstack-management
```

## Step 6: Monitor Logs and Access the UI
1. **Monitor the management server logs** to ensure the service starts correctly:
   ```bash
   tail -f /var/log/cloudstack/management/management-server.log
   ```

2. **Access the CloudStack UI** using the real IP address (e.g., `http://192.168.106.199:8080/`):
   - Log in with the default credentials or your provided credentials (e.g., `mizuki` / `AkiyamaMizuki1`).
   - **Note**: If you encounter `Error 531: Failed to authenticate user admin in domain 1` when using a Tailscale IP (e.g., `http://100.86.85.32:8080/`), ensure you use the real IP address (`192.168.106.199:8080`) and verify that the credentials are correct.

## Troubleshooting
- **Error 531: Failed to authenticate**: Double-check the username and password. Ensure you're using the real IP (`192.168.106.199:8080`) instead of the Tailscale IP (`100.86.85.32:8080`).
- **Database connection issues**: Verify the MySQL/MariaDB service is running (`sudo systemctl status mysql`) and the root password is correct.

## Conclusion
By following these steps, you should have reset the CloudStack password. Always use the real IP address for accessing the UI and ensure all network configurations are non-overlapping to avoid errors.
