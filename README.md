![Logo-OCS](http://cdn.ocsinventory-ng.org/common/banners/banner300px.png)

## Some Links
  - [ASK question](https://ask.ocsinventory-ng.org)
  - [Website](https://www.ocsinventory-ng.org/?utm_source=github-ocs)
  - [Github](https://github.com/OCSInventory-NG)
  - [OCS Professional](https://ocsinventory-ng.org/?page_id=140&lang=en)



OCS (Open Computers and Software Inventory Next Generation) is an assets management and deployment solution.
Since 2001, OCS Inventory NG has been looking for making software and hardware more powerful.
OCS Inventory NG asks its agents to know the software and hardware composition of every computer or server.

----------


## Assets management

Since 2001, OCS Inventory NG has been looking for making software and hardware more powerful. OCS Inventory NG asks its agents to know the software and hardware composition of every computer or server. OCS Inventory also ask to discover network’s elements which can’t receive an agent. Since the version 2.0, OCS Inventory NG take in charge the SNMP scans functionality.
This functionality’s main goal is to complete the data retrieved from the IP Discover scan. These SNMP scans will allow you to add a lot more informations from your network devices : printers, scanner, routers, computer without agents, …

----------

## Deployment

OCS Inventory NG includes the packet deployment functionality to be sure that all of the softwares environments which are on the network are the same. From the central management server, you can send the packets which will be downloaded with HTTP/HTTPS and launched by the agent on client’s computer. The OCS deployment is configured to make the packets less impactable on the network. OCS is used as a deployment tool on IT stock of more 100 000 devices.

----------

### Docker Compose Setup for OCS Inventory NG

To set up OCS Inventory NG using Docker, follow these steps:

1. **Create the necessary directories** for persistent storage:
   ```bash
   mkdir -p /volume1/docker/ocsinventory/{apache/{perlcomdata,ocsreportsdata,varlibdata,httpdconfdata,profilesconfdata},nginx/{conf,certs,auth},sql,sqldata}
   ```

2. **Edit the `docker-compose.yml`** with the following structure:
   ```yaml
   version: '3'

   services:
     ocsapplication:
       image: ocsinventory/ocsinventory-docker-image:2.12.3
       container_name: ocsinventory-server
       restart: always
       ports:
         - 28890:80
       volumes:
         - /volume1/docker/ocsinventory/apache/perlcomdata:/etc/ocsinventory-server
         - /volume1/docker/ocsinventory/apache/ocsreportsdata:/usr/share/ocsinventory-reports/ocsreports/extensions
         - /volume1/docker/ocsinventory/apache/varlibdata:/var/lib/ocsinventory-reports
         - /volume1/docker/ocsinventory/apache/httpdconfdata:/etc/apache2/conf-available
         - /volume1/docker/ocsinventory/apache/profilesconfdata:/usr/share/ocsinventory-reports/ocsreports/config/profiles
       environment:
         OCS_DB_SERVER: ocsinventory-db
         OCS_DB_USER: ocsuser
         OCS_DB_PASS: ocspass
         OCS_DB_NAME: ocsweb
         OCS_SSL_ENABLED: 0
       depends_on:
         - ocsdb
       networks:
         - localocs

     ocsdb:
       image: mariadb:10.11
       container_name: ocsinventory-db
       restart: always
       expose:
         - "3306"
       volumes:
         - /volume1/docker/ocsinventory/sql:/docker-entrypoint-initdb.d
         - /volume1/docker/ocsinventory/sqldata:/var/lib/mysql
       environment:
         MYSQL_ROOT_PASSWORD: rootpass
         MYSQL_USER: ocsuser
         MYSQL_PASSWORD: ocspass
         MYSQL_DATABASE: ocsweb
       networks:
         - localocs

   networks:
     localocs:
   ```

3. **Deploy the Docker Stack** by running:
   ```bash
   docker-compose up -d
   ```

4. **Access the OCS Web Interface**:  
   Once the containers are up and running, you can access the OCS Inventory web interface at:
   ```
   http://<your-docker-host-ip>:28890/ocsreports/
   ```

### Manual `install.php` Restoration

If you encounter errors related to missing `install.php`, you can manually restore it by following these steps:

1. **Log into the container**:
   ```bash
   docker exec -it ocsinventory-server bash
   ```

2. **Navigate to the OCS Reports directory**:
   ```bash
   cd /usr/share/ocsinventory-reports/ocsreports/
   ```

3. **Download the `install.php` file**:
   ```bash
   wget https://raw.githubusercontent.com/OCSInventory-NG/OCSInventory-ocsreports/master/install.php
   ```

4. **Set appropriate permissions**:
   ```bash
   chown www-data:www-data install.php
   chmod 644 install.php
   ```

5. **Access the installer**:  
   Go to the web interface:
   ```
   http://192.168.118.2:28890/ocsreports/install.php
   ```
   Follow the installation steps.

6. **After installation**, remove the `install.php` to avoid accidental re-installation:
   ```bash
   rm /usr/share/ocsinventory-reports/ocsreports/install.php
   ```


## Usage

For usage, please use our official documentation : 
[Docker documentation](http://wiki.ocsinventory-ng.org/13.Docker-documentation/Using-the-docker-image/)

## Contributing

1. Fork it!
2. Create your feature branch: git checkout -b my-new-feature
3. Add your changes: git add folder/file1.php
4. Commit your changes: git commit -m 'Add some feature'
5. Push to the branch: git push origin my-new-feature
6. Submit a pull request !


## License

OCS Inventory Docker Image is GPLv3 licensed
