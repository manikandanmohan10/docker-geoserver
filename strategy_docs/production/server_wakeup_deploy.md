Correct the file path and run deploy.sh file

1. Setting up the systemd service:
Create the server_wakeup_deploy.sh script and make it executable:

```
chmod +x /path/to/server_wakeup_deploy.sh
```

2. Create a systemd service to trigger the script upon wakeup.

```
sudo nano /etc/systemd/system/server_wakeup_deploy.service
```

3. Add the following content to the service file:

ini

```
[Unit]
Description=Start server, backup configuration, and deploy after wakeup
After=suspend.target

[Service]
Type=simple
ExecStart=/path/to/server_wakeup_deploy.sh
Restart=no

[Install]
WantedBy=suspend.target
Reload systemd to recognize the new service:
```

```
sudo systemctl daemon-reload
```

4. Enable the service so it runs on wakeup:

```
sudo systemctl enable server_wakeup_deploy.service
```

5. Optionally, start the service manually to test it:

```
sudo systemctl start server_wakeup_deploy.service
```
