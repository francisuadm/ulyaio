# ulyaio
ulyaio test


### compose.yml

```
docker network create nginxproxyman
```

```
services:
  app:
    image: "jc21/nginx-proxy-manager:latest"
    container_name: "nginxproxymanager"
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "81:81"
    environment:
      DB_SQLITE_FILE: "/data/database.sqlite"
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
  portainer:
    image: portainer/portainer-ce:latest
    command: -H unix:///var/run/docker.sock
    restart: always
    ports:
      - "9000:9000"
      - "8000:8000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
  nextcloud:
    image: nextcloud/all-in-one:latest
    restart: unless-stopped
    container_name: nextcloud-aio-mastercontainer
    volumes:
      - nextcloud_aio_mastercontainer:/mnt/docker-aio-config
      - /var/run/docker.sock:/var/run/docker.sock:ro
    ports:
      - 8080:8080
    environment:
      - APACHE_PORT=11000
      - SKIP_DOMAIN_VALIDATION=false
      - APACHE_DISABLE_REWRITE_IP=1
      - NEXTCLOUD_UPLOAD_LIMIT=10G
      - NEXTCLOUD_ENABLE_DRI_DEVICE=true
      - NEXTCLOUD_TRUSTED_DOMAINS=ulyaio.duckdns.org 10.17.76.37 #Your domain name + Proxy host IP
      - TRUSTED_PROXIES=10.17.76.37 #Proxy Host IP
volumes:
  portainer_data:
  nextcloud_aio_mastercontainer:
    name: nextcloud_aio_mastercontainer
networks:
  default:
    name: nginxproxyman
    external: true

```

### I can guide you through the process of installing Nginx Proxy Manager on your Ubuntu Server 22.04 using Docker. Here are the steps:

Step 1: Setup the Database and Data Directories Before installing Nginx Proxy Manager, you need to set up the necessary directories and create a database. Follow these steps to get started:
```
# Create the Nginx Proxy Manager directory in a widely accessible location like /opt.
mkdir /opt/nginxproxymanager
# Under the directory, create a new databases subdirectory.
mkdir /opt/nginxproxymanager/databases
# Create a new SQLite database file.
touch /opt/nginxproxymanager/databases/nginxproxy.db
```
Step 2: Install Nginx Proxy Manager Now that the database and data directories are set up, it’s time to install Nginx Proxy Manager. Follow these steps:

# Create a custom Docker network.
```
docker network create nginxproxyman
# Using a text editor, create and edit a docker-compose.yml file in the main /opt/NginxProxy directory.
nano /opt/nginxproxymanager/docker-compose.yml
```
Enter the following configurations to the file:
```
version: "3"
services:
  app:
    image: "jc21/nginx-proxy-manager:latest"
    container_name: "nginxproxymanager"
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "81:81"
    environment:
      DB_SQLITE_FILE: "/data/database.sqlite"
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    networks:
      default:
        external:
          name: nginxproxyman
```
Nginx Proxy Manager listens on ports 80 and 443 for HTTP and HTTPS traffic, respectively. Port 81 provides access to the web management dashboard. To tighten server security, change the management port to a random combination. Save and close the file.
```
# Switch to the Nginx Proxy Manager directory.
cd /opt/nginxproxymanager
# Install Nginx Proxy Manager by starting docker-compose in detached mode.
docker-compose up -d
# Verify that Nginx Proxy Manager is up and running.
docker ps
```
### The command output should display the running container.

Step 3: Configure Firewall To ensure that your server is properly protected, you need to configure the firewall to allow access to the necessary ports. Follow these steps:
```
# If you use UFW (enabled by default), allow HTTP access.
ufw allow 80
# Allow HTTPS access.
ufw allow 443
# Allow access to the Nginx Proxy Manager web management dashboard.
ufw allow 81
```
#### That’s it! You should now have Nginx Proxy Manager installed on your Ubuntu Server 22.0

### I can guide you through the process of installing Portainer along with Nginx Proxy Manager on your Ubuntu Server 22.04 using Docker. Here are the steps:

Step 1: Setup the Database and Data Directories Follow the same steps as mentioned in the previous message.

Step 2: Install Nginx Proxy Manager and Portainer Now that the database and data directories are set up, it’s time to install Nginx Proxy Manager and Portainer. Modify the docker-compose.yml file to include Portainer:
```
nano /opt/nginxproxymanager/docker-compose.yml
```
Enter the following configurations to the file:
```
version: "3"
services:
  app:
    image: "jc21/nginx-proxy-manager:latest"
    container_name: "nginxproxymanager"
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "81:81"
    environment:
      DB_SQLITE_FILE: "/data/database.sqlite"
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
  portainer:
    image: portainer/portainer-ce:latest
    command: -H unix:///var/run/docker.sock
    restart: always
    ports:
      - "9000:9000"
      - "8000:8000"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
volumes:
  portainer_data:
networks:
  default:
    external:
      name: nginxproxyman
```
Nginx Proxy Manager listens on ports 80 and 443 for HTTP and HTTPS traffic, respectively. Port 81 provides access to the web management dashboard. Portainer listens on port 9000 for its web UI and port 8000 for its agent12. Save and close the file.
```
# Switch to the Nginx Proxy Manager directory.
cd /opt/nginxproxymanager
# Install Nginx Proxy Manager and Portainer by starting docker-compose in detached mode.
docker-compose up -d
# Verify that Nginx Proxy Manager and Portainer are up and running.
docker ps
```

The command output should display the running containers.

Step 3: Configure Firewall To ensure that your server is properly protected, you need to configure the firewall to allow access to the necessary ports. Follow these steps:

# If you use UFW (enabled by default), allow HTTP access.
```
ufw allow 80
# Allow HTTPS access.
ufw allow 443
# Allow access to the Nginx Proxy Manager web management dashboard.
ufw allow 81
# Allow access to the Portainer web management dashboard.
ufw allow 9000
```

That’s it! You should now have Nginx Proxy Manager and Portainer installed on your Ubuntu Server 22.04



### To stop and remove all containers at once, you can use:
```
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
docker volume ls
docker volume prune -f
docker network ls
docker network prune -f
docker image prune -a -f
docker volume prune --filter all=1 -f
docker volume ls --filter "dangling=true"
docker ps --filter "status=exited"
docker ps --format {{.Names}}
```


```
docker compose up -d --force-recreate
docker compose up -d --rebuild
```

