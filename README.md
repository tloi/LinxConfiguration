# Linux Server Configuration

### Properties

- IP address: 34.207.122.22

- Port: 2200

### Summary

1. Create user: grader 
  - Execute `$ sudo adduser grader`

2. Give grader sudo rights
  - Execute `sudo nano /etc/sudoers.d/grader`
  - Update text `grader ALL=(ALL) NOPASSWD:ALL`
  
3. Update all currently installed packages
  - Execute `sudo apt-get update`
  - Execute `sudo apt-get upgrade`

4. Change SSH port from 22 to 2200
  - Execute `sudo nano /etc/ssh/sshd_config`
  - Comment  `#Port 22` and add `Port 2200`
  
5. Configure Firewall (UFW)
  - `sudo ufw allow www`
  - `sudo ufw allow ssh`
  - `sudo ufw allow 2200/tcp`
  - `sudo ufw allow 80/tcp`  
  - `sudo ufw enable`
   
6. Disable ssh login for root user
  - Execute `sudo nano /etc/ssh/sshd_config`
  - Edit `PermitRootLogin without-password` line to `PermitRootLogin no`
  
7. Install Apache
  - `sudo apt-get install apache2`

8. Install mod_wsgi
  - Run `sudo apt-get install libapache2-mod-wsgi python-dev`

9. Clone the Catalog app from Github
  - Clone your project from github `git clone https://github.com/tloi/itemCatalog /var/www/python/itemCatalog`  

10. Install pip
  - Execute `sudo apt-get install python-pip`

11. Install Flask
  - `pip install Flask`
  
12. Install essential libraries
  - `sudo pip install httplib2 oauth2client sqlalchemy`

14. Configure and enable a new virtual host
  - Execute `sudo nano /etc/apache2/sites-available/catalog.conf`
  - Paste this code: 
  ```
  <VirtualHost *:80>
      ServerName 35.167.27.204
      ServerAlias ec2-35-167-27-204.us-west-2.compute.amazonaws.com
      ServerAdmin admin@35.167.27.204
      WSGIDaemonProcess catalog python-path=/var/www/catalog:/var/www/catalog/venv/lib/python2.7/site-packages
      WSGIProcessGroup catalog
      WSGIScriptAlias / /var/www/catalog/catalog.wsgi
      <Directory /var/www/catalog/catalog/>
          Order allow,deny
          Allow from all
      </Directory>
      Alias /static /var/www/catalog/catalog/static
      <Directory /var/www/catalog/catalog/static/>
          Order allow,deny
          Allow from all
      </Directory>
      ErrorLog ${APACHE_LOG_DIR}/error.log
      LogLevel warn
      CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
  ```
  - Enable the virtual host `sudo a2ensite catalog`
14. Configure the local timezone to UTC
  - Run `sudo dpkg-reconfigure tzdata` and then choose UTC

15. Install and configure PostgreSQL
  - `sudo apt-get install libpq-dev python-dev`
  - `sudo apt-get install postgresql postgresql-contrib`
  - `sudo su - postgres`
  - `psql`
  - `CREATE USER catalog WITH PASSWORD 'password';`
  - `ALTER USER catalog CREATEDB;`
  - `CREATE DATABASE catalog WITH OWNER catalog;`
  - `\c catalog`
  - `REVOKE ALL ON SCHEMA public FROM public;`
  - `GRANT ALL ON SCHEMA public TO catalog;`
  - `\q`
  - `exit`
  - Change create engine line in your `__init__.py` and `database_setup.py` to: 
  `engine = create_engine('postgresql://catalog:password@localhost/catalog')`
  - `python /var/www/catalog/catalog/database_setup.py`
  - Make sure no remote connections to the database are allowed. Check if the contents of this file `sudo nano /etc/postgresql/9.3/main/pg_hba.conf` looks like this:
  ```
  local   all             postgres                                peer
  local   all             all                                     peer
  host    all             all             127.0.0.1/32            md5
  host    all             all             ::1/128                 md5
  ```
  
