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
  - `sudo ufw default deny incoming`
  - `sudo ufw default allow outgoing`
  - `sudo ufw allow www`
  - `sudo ufw allow ssh`
  - `sudo ufw allow ntp`
  - `sudo ufw allow 2200/tcp`
  - `sudo ufw allow 80/tcp`  
  - `sudo ufw enable`
   
6. Disable ssh login for root user
  - Execute `sudo nano /etc/ssh/sshd_config`
  - Edit `PermitRootLogin without-password` to `PermitRootLogin no`
  
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
  
12. Install dependent libraries
  - `sudo pip install httplib2 oauth2client sqlalchemy requests psycopg2`

13. Configure Web hosting
  - Execute `sudo nano /etc/apache2/sites-enabled/000-default.conf`
  - Add `WSGIScriptAlias / /var/www/python/itemCatalog/Item-Catalog/project.wsgi` 
  
14. Set local timezone to UTC
  - Execute `sudo timedatectl set-timezone UTC`

15. Install PostgreSQL
  - `sudo apt-get install libpq-dev python-dev`
  - `sudo apt-get install postgresql postgresql-contrib`

16. Setup new PostgreSQL database  
  - `sudo su - postgres`
  - `psql`
  - `CREATE USER u WITH PASSWORD 'password';`
  - `ALTER USER u CREATEDB;`
  - `CREATE DATABASE catalog WITH OWNER u;`
  
17. Point application to new PpstgreSQL database
  - Execute `sudo nano database_setup.py` 
  - edit `engine = create_engine('postgresql://u:password@localhost/catalog')`
  - `python /var/www/catalog/catalog/database_setup.py`
