# Linux-Server-Configuration
Udacity Full Stack

## Server Information
This server is hosted on the Amazon AWS Lightsail platform
* The ip address is 52.70.251.254 or 52.70.251.254:80
* ssh port 2200
* The url to access the catalog project web page is http://52.70.251.254/catalog/ or http://52.70.251.254:80/catalog/

## Installed Packages
**using $ sudo apt-get install <package name>**

* finger - *can be used to view user info, not required*
* apache2
* libapache2-mod-wsgi-py3
* python3
* python3-pip

* python-flask
* httplib2
* python-oauth2client

**using $ pip install <package name> or $ sudo -H pip install <package name> as required**

- flask
- SQLAlchemy
- oauth2client

**Other Software**

- PuTTY 0.71 - [Get PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Git Bash - [Get Git Bash](https://git-scm.com/downloads)



## Configurations

### Amazon Lightsail Server Acquisition
* Amazon Lightsail create an AWS account https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/
* Create Instance, Select platform (linux) and blueprint OS only (ubuntu 18.04 LTS).
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
  ubuntu:~$ sudo ufw status
  Status: active
  
  To                         Action      From
  ------
  2200/tcp                   ALLOW       Anywhere
  80/tcp                     ALLOW       Anywhere
  123/udp                    ALLOW       Anywhere
  2200/tcp (v6)              ALLOW       Anywhere (v6)
  80/tcp (v6)                ALLOW       Anywhere (v6)
  123/udp (v6)               ALLOW       Anywhere (v6)
  
  ubuntu:~$
  ```

* Reconfigure PuTTY to access the server on ssh port 2200 and validate your connection.

* You can now go into Lightsail and delete port 22.


### Add a new user as grader
* Add a new user named *'grader'*  `$ sudo adduser grader` 

  * answer the questions 

* Grant the new user sudo privileges  [add user and grant sudo](<https://www.digitalocean.com/community/tutorials/how-to-add-and-delete-users-on-ubuntu-16-04>)

* Create RSA key-pair on your desktop using PuttyGen:

  https://www.ssh.com/ssh/putty/windows/puttygen
  
  

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

    '''
    λ ssh grader@52.70.251.54 -p 2200 -i ~/id_rsa
Warning: Identity file C:\Users\sting/id_rsa not accessible: No such file or directory.
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-1021-aws x86_64)Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-1021-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Dec  3 17:58:22 UTC 2019

  System load:  0.0                Processes:           111
  Usage of /:   12.4% of 19.32GB   Users logged in:     1
  Memory usage: 58%                IP address for eth0: 172.26.7.28
  Swap usage:   0%

 * Overheard at KubeCon: "microk8s.status just blew my mind".

     https://microk8s.io/docs/commands#microk8s.status

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

0 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Dec  3 17:31:46 2019 from 65.74.233.212
grader@ip-172-26-7-28:~$
    '''
## Prepare Sever for the Application

- Set Time Zone to UTC

  - use this command   `$ date`   to view current server date/time/time zone

    ```
    student@ip-172-26-7-28:/home/grader$ date
    Tue Dec  3 17:59:59 UTC 2019

    ```

  - If it needs to be changed   `sudo dpkg-reconfigure tzdata`

- Installed required packages listed in the ***Installed Packages*** section above. 

- Enable mod_wsgi  `$ sudo a2enmod wsgi`

- Start the web server `$ sudo service apache2 start`  or  `$ sudo service apache2 restart`

- Enter the public IP address from Lightsail into a browser and the apache2 default page should be displayed. Do not proceed if it does not display, fix the issues.

  


- Create the apache2 config file to serve the catalog web site

  - `$ sudo nano /etc/apache2/sites-enabled/ExampleFlask.conf`

  ```
  <VirtualHost *:80>
     # Add machine's IP address (use ifconfig command)
     ServerName 52.70.251.54.xip.io
     # Give an alias to to start your website url with
     WSGIScriptAlias /catalog /home/student/fullstack-nanodegree-vm/vagrant/project2/app.wsgi
     WSGIDaemonProcess catalog user=student group=student threads=5
     <Directory /home/student/fullstack-nanodegree-vm/vagrant/project2/>
                # set permissions as per apache2.conf file
            Options Indexes FollowSymLinks
            Order allow,deny
            Allow from all
            AllowOverride None
            Require all granted
     </Directory>
     Alias /static /home/student/fullstack-nanodegree-vm/vagrant/project2/static/
     <Directory /home/student/fullstack-nanodegree-vm/vagrant/project2/static/>
            Order allow,deny
            Allow from all
     </Directory>
     ErrorLog ${APACHE_LOG_DIR}/error.log
     LogLevel warn
     CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

    ```

  

- Enable the ExampleFlask.conf with `$ sudo a2ensite ExampleFlask.conf`

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

* Check the error log for issues to find bad configuration or missing packages: $ cd /var/log/apache2  tail error.log


