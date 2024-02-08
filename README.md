# isolut


### Deploying Ghost CMS on Google Cloud Platform

#### Step 1: Set Up Google Cloud Platform (GCP) Project

1. Create a new project on Google Cloud Console if you don't have one.
2. Enable billing for your project.
3. Enable the Compute Engine API for your project.

#### Step 2: Create and Configure Compute Engine Instance

1. Navigate to Compute Engine in the Google Cloud Console.
2. Click "Create Instance" and configure machine type, boot disk, and networking.
3. Allow HTTP and HTTPS traffic.
4. Select "Ubuntu" as the operating system.
5. Click "Create" to create the instance.

#### Step 3: Connect to Your Instance

1. Click on the SSH button in the console to open a terminal window.
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

2. Paste the Nginx configuration, replacing `your_domain`:

   ```nginx
   server {
       listen 80;
       server_name your_domain;
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

3. Update the storage section:

   ```json
   "storage": {
       "active": "gcs",
       "gcs": {
           "projectId": "your-project-id",
           "keyFilename": "/path/to/your/keyfile.json",
           "bucket": "your-bucket-name"
       }
   }
   ```

4. Restart Ghost:

   ```bash
   sudo systemctl restart ghost
   ```

#### Step 9: Set Up HTTPS with Let's Encrypt

```bash
sudo apt install certbot
sudo certbot --nginx -d your_domain
```

#### Step 10: Implement Load Balancing

1. Set up an HTTP(S) Load Balancer in the Google Cloud Console.
2. Configure backend and frontend services for HTTPS traffic.

#### Step 11: Allow Only Whitelisted IP Addresses

1. Update Nginx configuration to allow specific IP addresses:

   ```nginx
   server {
       ...
       allow 1.2.3.4;  # Add your whitelisted IP address
       deny all;
       ...
   }
   ```

