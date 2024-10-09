# Updating Production Deployment

This guide outlines how to update the deployment in production after any changes have been made to the codebase, configuration, or dependencies. These steps assume that the initial setup has already been completed and you are updating an existing deployment.

## Step-by-Step Update Process

### 1. Checkout the Production Branch

Ensure that you are on the `production` branch.

```
git checkout production
```

### 2. Pull the Latest Changes
Pull the latest code from the production branch to update the project files.

```
git pull
```

### 3. Take Down Existing Containers
Stop and remove the currently running containers to ensure that all changes are applied during the restart.

```
docker compose -f docker-compose.yml down
```

### 4. Rebuild and Start Updated Containers
Rebuild and bring up the containers with the latest changes in detached mode.

```
docker compose -f docker-compose.yml up -d
```

### 5. Restart Nginx
To ensure that any changes in your Nginx configuration are applied, restart the Nginx service.

```
sudo systemctl restart nginx
```

### 6. Check Nginx Logs
Verify that Nginx is working properly after the restart by checking the logs.

```
docker compose logs nginx
```

