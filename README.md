# Linux-Server-Configuration
Udacity Full Stack

## Server Information
This server is hosted on the Amazon AWS Lightsail platform
* The ip address is 18.221.242.20 or 18.221.242.20:80
* ssh port 2200
* The url to access the catalog project web page is 18.221.24.20 or 18.221.24.20:80

## Installed Packages
**using $ sudo apt-get install <package name>**

* finger - *can be used to view user info, not required*
* apache2
* libapache2-mod-wsgi
* postgresql
* postgresql-contrib
* postgresql-server-dev-9.5
* python2.7

* python-flask
* python-pip 
* httplib2
* python-oauth2client
* virtualenv 16.4.3 - *can be used if a virtual environment is desired, not required*

**using $ pip install <package name> or $ sudo -H pip install <package name> as required**

- psycopg2
- psycopg2-binary
- flask
- requests
- SQLAlchemy
- google-api-python-client oauth2client

**Other Software**

- PuTTY 0.71 - [Get PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Git Bash - [Get Git Bash](https://git-scm.com/downloads)
- Typora markdown editor - [Get Typora](https://typora.io/)   (this is a great editor !!)



## Configurations

### Amazon Lightsail Server Acquisition
* Amazon Lightsail create an AWS account https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/
* Create Instance, Select platform (linux) and blueprint OS only (ubuntu 16.04 LTS).
* Select the option for a statis ip address.
* Download the default private key (.pem file) to be used with PuTTY or Git Bash to access the server via ssh. This key will be used by the ubuntu user.
* Select the Networking tab and add port 123/udp and port 2200/tcp, **DO NOT** remove any ports at this time.
* Follow the Lightsail instructions on how to configure PuTTY for ssh access to your server. The Lightsail terminal window is not suitable for configuration steps as it will timeout too often. Configure PuTTY for ssh port 22 and verify access. The port will be change later to port 2200.

### Secure the Server
* Update all packages that are currently installed.
  * Update package list: `$ sudo apt-get update`
  * Perform an upgrade: `$ sudo apt-get upgrade`
  * Remove old packages: `$ sudo apt-get autoremove`
  
* Verify that you have added port 123/udp and port 2200/tcp in the the networking tab of your Lightsail instance.

* Update server ssh Configurations

  * `$ sudo nano /etc/ssh/sshd_config`

  * Update the *'Port'* from 22 to 2200:

    ```
    # What ports, IPs and protocols we listen for
    Port 2200
    ```

  - Disable root login by updating *'PermitRootLogin'* to no

    ```
    # Authentication:
    LoginGraceTime 120
    PermitRootLogin no
    StrictModes yes
    ```

  - Enforce key-based authentication by updating  *'PasswordAuthentication'*

    ```
    # Change to no to disable tunnelled clear text passwords
    PasswordAuthentication no
    ```

  - Save the changes in nano using `<ctrl> x` for help with nano go here - [nano help](<https://www.nano-editor.org/>)

  - Make sure you have port 2200 configured in the Lightsail firewall before executing the next command to invoke the changes just made in the *sshd_config* file. 

  - Restart ssh `$ sudo service sshd restart`

* Configure the ubuntu Uncomplicated Firewall (UFW) https://help.ubuntu.com/community/UFW

  * Verify that UFW is not running  `$ sudo ufw status` , stop UFW if it is running `$ sudo ufw disable`
  * Disable all incoming ports `$ sudo ufw default deny incoming`
  * Allow all outgoing ports `$ sudo ufw default allow outgoing`
  * Enable the desired ports
    * `$ sudo ufw allow 80/tcp`
    * `$ sudo ufw allow 2200/tcp`
    * `$ sudo ufw allow 123/udp`
  * Verify UFW ports `$ sudo ufw status`
  * Enable UFW `$ sudo ufw enable`
  * View UFW status `$ sudo ufw status`

  ```
  ubuntu@ip-172-26-6-239:~$ sudo ufw status
  Status: active
  
  To                         Action      From
  ------
  2200/tcp                   ALLOW       Anywhere
  80/tcp                     ALLOW       Anywhere
  123/udp                    ALLOW       Anywhere
  2200/tcp (v6)              ALLOW       Anywhere (v6)
  80/tcp (v6)                ALLOW       Anywhere (v6)
  123/udp (v6)               ALLOW       Anywhere (v6)
  
  ubuntu@ip-172-26-6-239:~$
  ```

* Reconfigure PuTTY to access the server on ssh port 2200 and validate your connection.

* You can now go into Lightsail and delete port 22.


### Add a new user as grader
* Add a new user named *'grader'*  `$ sudo adduser grader` 

  * answer the questions 

* Grant the new user sudo privileges  [add user and grant sudo](<https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04>)

* Create RSA key-pair on your desktop using Git Bash:

  * `user~ $ ssh-keygen` 

    ```
    user ~
    $ ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/c/Users/user/.ssh/id_rsa):
    ```

    ```
    user ~
    $ cd .ssh
    user .ssh
    $ ls 
    id_rsa.pub  id_rsa
    ```

  * copy the contents of *'id_rsa.pub'* to the clipboard and exit Git Bash

* From a PuTTY terminal logged in as ubuntu perform the following tasks to enable the grader user to use the newly created RSA key:

  * Move to the grader's folder  `$ cd /home/grader`

  * Create a directory named .ssh  `$ mkdir .ssh`

  * Create and edit the file to store the RSA key   `$ nano .ssh/authorized_keys`

  * Copy the RSA key in the clipboard into the file created by nano and save it.

  * Change permissions on the directory and file.

    * `$ sudo chmod 700 /home/grader/.ssh`
    * `$ sudo chmod 644 /home/grader/.ssh/authorized_keys`

  * Change the owner from root to grader.

    * `sudo chown -R grader:grader /home/grader/.ssh`

  * Restart the ssh service   `$ sudo service ssh restart`

  * Open a Git Bash terminal window  and access the server as grader

    * `$  ssh grader@18.221.242.20 -p 2200 -i ~/id_rsa `

    ```
    user ~
    $  ssh grader@18.221.242.20 -p 2200 -i ~/id_rsa
    Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.4.0-1079-aws x86_64)
    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    
      Get cloud support with Ubuntu Advantage Cloud Guest:
        http://www.ubuntu.com/business/services/cloud
    
    5 packages can be updated.
    0 updates are security updates.
    
    New release '18.04.2 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
    
    Last login: Sat Apr 20 15:04:33 2019 from 99.62.103.54
    grader@ip-172-26-6-239:~$
    ```

    

## Prepare Sever for the Application

- Set Time Zone to UTC

  - use this command   `$ date`   to view current server date/time/time zone

    ```
    ubuntu@ip-172-26-6-239:/$ date
    Sat Apr 20 22:46:47 UTC 2019
    ubuntu@ip-172-26-6-239:/$
    ```

  - If it needs to be changed   `sudo dpkg-reconfigure tzdata`

- Installed required packages listed in the ***Installed Packages*** section above. 

- Enable mod_wsgi  `$ sudo a2enmod wsgi`

- Start the web server `$ sudo service apache2 start`  or  `$ sudo service apache2 restart`

- Enter the public IP address from Lightsail into a browser and the apache2 default page should be displayed. Do not proceed if it does not display, fix the issues.

- Configure PostgreSQL

  - Create the role postgres and password   `$ sudo -u postgres psql postgres`

  - password `\password postgres`

  - Exit the postgreSQL prompt  `\q` or `<ctrl>D`

  - Since the postgres user is a super user we need to create a user with limited access to our database.  `$ sudo -i -u postgres`

  - Create a user named catalog  `createusercatalog -P --interactive`

    ```
    postgres@ip-172-26-6-239:~$ createuser catalog -P --interactive
    Enter password for new role: xxxxxxxx
    Enter it again: xxxxxxxx
    Shall the new role be a superuser? (y/n) n
    Shall the new role be allowed to create databases? (y/n) y
    Shall the new role be allowed to create more new roles? (y/n) y
    postgres@ip-172-26-6-239:~$
    ```

  - Exit with `<ctrl>D`

  - Create the itemcatalog database  `sudo -u postgres createdb -O catalog itemcatalog

  - Verify the creation with `sudo -u postgres psql itemcatalog` and then `\l`

    ```
                                       List of databases
        Name     |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges
    -------------+----------+----------+-------------+-------------+-----------------------
     itemcatalog | catalog  | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
     postgres    | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
     template0   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
                 |          |          |             |             | postgres=CTc/postgres
     template1   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
                 |          |          |             |             | postgres=CTc/postgres
    (4 rows)
    ```

  - Use `q` to quit and `<ctrl>D` to exit the postgres prompt

  - Verify that remote connections are disabled 

    - `$ sudo cat /etc/postgresql/9.5/main/pg_hba.conf`

  ```
  # PostgreSQL Client Authentication Configuration File
  # ===================================================
  #
  # Refer to the "Client Authentication" section in the PostgreSQL
  # documentation for a complete description of this file.  A short
  # synopsis follows.
  #
  # This file controls: which hosts are allowed to connect, how clients
  # are authenticated, which PostgreSQL user names they can use, which
  # databases they can access.  Records take one of these forms:
  #
  # local      DATABASE  USER  METHOD  [OPTIONS]
  # host       DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
  # hostssl    DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
  # hostnossl  DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
  #
  
  # Database administrative login by Unix domain socket
  local   all             postgres                                peer
  
  # TYPE  DATABASE        USER            ADDRESS                 METHOD
  
  # "local" is for Unix domain socket connections only
  local   all             all                                     peer
  # IPv4 local connections:
  host    all             all             127.0.0.1/32            md5
  # IPv6 local connections:
  host    all             all             ::1/128                 md5
  # Allow replication connections from localhost, by a user with the
  # replication privilege.
  #local   replication     postgres                                peer
  #host    replication     postgres        127.0.0.1/32            md5
  #host    replication     postgres        ::1/128                 md5
  
  ```

  

## Deploy the Catalog Project to the Server

- Create a directory for the catalog project  `sudo mkdir /var/www/catalog`

- Move to the new directory  `sudo cd /var/www/catalog`

- Clone the application  `sudo git clone <yourlink>`

  - This structure should now exist /var/www/catalog/catalog/

- Create the tables in the itemcatalog database using database.py

  - `$ cd /var/www/catalog/catalog  `
  - `$ python database_setup.py`

- Populate the tables

  - `$ python loaddatabasecontent.py `

- Create and populate the wsgi file

  - `$ sudo nano /var/www/catalog/catalog.wsgi`

  - Populate nano with the following and save:

    ```
    #!/usr/bin/python
    import sys
    import logging
    logging.basicConfig(stream=sys.stderr)
    sys.path.insert(0,"/var/www/catalog/catalog/")
    
    from catalog import app as application
    application.secret_key = 'Add your secret key'
    ```

- Create the apache2 config file to serve the catalog web site

  - `$ sudo nano /etc/apache2/sites-enabled/catalog.conf`

  ```
  <VirtualHost *:80>
                  ServerName 18.221.242.20
                  ServerAdmin grader@18.221.242.20
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

  

- Enable the catalog.conf with `$ sudo a2ensite catalog.conf`

- Restart apache server  `$ sudo service apache2 restart`

- Enter the public IP address from Lightsail into a browser and the catalog web page should be rendered

  - If the page does not render check the error.log for errors such as missing packages, load them and restart the server.
  - `$ cat /var/log/apache2/error.log`

## Various Commands

* clear the screen: $ clear

* edit ssh settings and ports to listen on: $ sudo nano /etc/ssh/sshd_config

* restart the ssh service: $ sudo service sshd restart

* determine server time zone: $ date

* check firewall status and allowed ports: $ sudo ufw status

* Allow ports: $ sudo ufw allow <port>/<optional: protocol>

* deny ports : $ sudo ufw deny <port>/<optional: protocol>

* Reload apache after installing software: $ sudo  service apache2 reload

* Verify database tables were created 

  ```
  $ sudo -u postgres psql itemcatalog
  itemcatalog=# \dt
  ```

* Check the error log for issues to find bad configuration or missing packages: $ cd /var/log/apache2  cat error.log


## Acknowledgements

* DigitalOcean:  https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps
* DigitalOcean: <https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04>
* Ubuntu help on UFW: https://help.ubuntu.com/community/UFW
* Stack Overflow
* Amazon Lightsail
* Udacity Full Stack lessons
* Udacity Knowledge Center
* Udacity Student Hub
