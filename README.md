# isolut


### Deploying The Ghost Application on Google Cloud Platform

#### Step 1: Set Up Google Cloud Platform (GCP) Project

1. Create a new project on Google Cloud Console.

#### Step 2: Create and Configure Compute Engine Instance

1."Create Instance" and configure machine .
2. Allow HTTP and HTTPS traffic.
3. Select "Ubuntu" as the operating system.
4. Click "Create" to create the instance.

#### Step 3: Connect to Your Instance

1. Click on the SSH button in the console.
2. Update the system packages:

   ```bash
   sudo apt update
   sudo apt upgrade
   ```

#### Step 4: Install and Configure Nginx

```bash
sudo apt install nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

#### Step 5: Install Node.js and Ghost-CLI

```bash
sudo apt install nodejs npm
sudo npm install -g ghost-cli
```

#### Step 6: Set Up Ghost

```bash
sudo mkdir -p /var/www/ghost
cd /var/www/ghost
sudo ghost install
```

#### Step 7: Configure Nginx for Ghost

1. Create Nginx site configuration:

   ```bash
   sudo nano /etc/nginx/sites-available/ghost
   ```

2. In  the Nginx configuration:

   ```nginx
   server {
       listen 80;
       server_name example.com;
     # IP Whitelisting
       allow 1.2.3.4;  # Add your whitelisted IP address
       deny all;
       location / {
           proxy_pass http://127.0.0.1:2368;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }

       location ~ /.well-known {
           allow all;
       }

       client_max_body_size 50m;
   }
   ```

3. Create a symbolic link and test Nginx configuration:

   ```bash
   sudo ln -s /etc/nginx/sites-available/ghost /etc/nginx/sites-enabled
   sudo nginx -t
   ```

4. Reload Nginx:

   ```bash
   sudo systemctl reload nginx
   ```

#### Step 8: Configure Ghost to Use Google Cloud Storage

1. Create a bucket in Google Cloud Storage.
2. Edit Ghost config:

   ```bash
   sudo nano /var/www/ghost/config.production.json
   ```

3. Restart Ghost:

   ```bash
   sudo systemctl restart ghost
   ```


# Step 9: Configure Ghost Database

1. Install MySQL Server:
sudo apt install mysql-server

2. Secure your MySQL installation:
sudo mysql_secure_installation

4. Create a MySQL database and user for Ghost:
sudo mysql

5. Update Ghost configuration to use the MySQL database:
sudo nano /var/www/ghost/config.production.json

6. Restart Ghost:
sudo systemctl restart ghost

