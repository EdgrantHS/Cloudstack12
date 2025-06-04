# Reset CloudStack
This tutorial guides you through resetting CloudStack.

## Step 1: Stop the CloudStack Management Server
Stop the CloudStack management server to prevent any conflicts during the reset process.

```bash
sudo systemctl stop cloudstack-management
```

## Step 2: Reset the CloudStack Database
Reset the CloudStack database to ensure a clean setup.

1. **Connect to MySQL/MariaDB**:
   ```bash
   sudo mysql -u root -p
   ```
   Enter the new root password (`password`) when prompted.

2. **Drop the existing CloudStack database**:
   ```sql
   DROP DATABASE cloud;
   exit
   ```

3. **Re-run the CloudStack database setup** (replace `192.168.1.220` with your management server IP):
   ```bash
   sudo cloudstack-setup-databases cloud:cloud@localhost --deploy-as=root:password -i 192.168.1.220
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
   sudo tail -f /var/log/cloudstack/management/management-server.log
   ```

2. **Access the CloudStack UI** using the real IP address (e.g., `http://192.168.106.199:8080/`):
   - Log in with the default credentials or your provided credentials (e.g., `mizuki` / `AkiyamaMizuki1`).
   - **Note**: If you encounter `Error 531: Failed to authenticate user admin in domain 1` when using a Tailscale IP (e.g., `http://100.86.85.32:8080/`), ensure you use the real IP address (`192.168.106.199:8080`) and verify that the credentials are correct.

## Troubleshooting
- **Error 531: Failed to authenticate**: Double-check the username and password. Ensure you're using the real IP (`192.168.106.199:8080`) instead of the Tailscale IP (`100.86.85.32:8080`).
- **Database connection issues**: Verify the MySQL/MariaDB service is running (`sudo systemctl status mysql`) and the root password is correct.
