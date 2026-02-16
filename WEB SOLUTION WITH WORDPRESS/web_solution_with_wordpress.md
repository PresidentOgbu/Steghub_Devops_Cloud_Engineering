# Web Solution With WordPress

## Step 1 - Prepare a Web Server

__1.__ __Launch a RedHat EC2 instance that serve as ```Web Server```. Create 3 volumes in the same availabilty zone as the web server Ec2 each of 10GB and attach all 3 volumes one by one to the web server__.

![Instance detail](./images/ec2-details.png)
![Instance detail](./images/security-rules.png)
![Web volumes](./images/web-volumes.png)

__2.__ __Open up the Linux terminal to begin configuration__.

```bash
ssh -i "webserver_key_pair.pem" ec2-user@16.171.250.17
```
![web ssh](./images/ssh-web.png)

__3.__ __Use ```lsblk``` to inspect what block devices are attached to the server. All devices in Linux reside in /dev/ directory. Inspect with ```ls /dev/``` and ensure all 3 newly created devices are there. Their name will likely be ```xvdf```, ```xvdg``` and ```xvdh```__.

```bash
lsblk
```
![List Block](./images/list-block1.png)

__4.__ __Use ```df -h``` to see all mounts and free space on the server__.

```bash
df -h
```
![](./images/mount-free-space.png)

__5a.__ __Use ```fdisk``` utility to create a single partition on each of the 3 disks__.

```bash
sudo fdisk /dev/nvme1n1
```
![partition](./images/nvme1.png)

```bash
sudo fdisk /dev/nvme2n1
```
![partition](./images/nvme2.png)

```bash
sudo fdisk /dev/nvme3n1
```
![partition](./images/nvme3.png)

__5b.__ __Use ```lsblk``` utility to view the newly configured partitions on each of the 3 disks__
```bash
lsblk
```
![View partitions](./images/list-block2.png)

__6.__ __Install ```lvm``` package__
```bash
sudo yum install lvm2 -y
```
![Install lvm](./images/install-lvm.png)

__7.__ __Use ```pvcreate``` utility to mark each of the 3 dicks as physical volumes (PVs) to be used by LVM. Verify that each of the volumes have been created successfully__.
```bash
sudo pvcreate /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1

sudo pvs
```
![PV](./images/pv-create.png)

__8.__ __Use ```vgcreate``` utility to add all 3 PVs to a volume group (VG). Name the VG ```data-vg```. Verify that the VG has been created successfully__
```bash
sudo vgcreate data_vg /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1

sudo vgs
```
![VG](./images/vg-create.png)

__9.__ __Use ```lvcreate``` utility to create 2 logical volume, ```apps-lv``` (__Use half of the PV size__), and ```logs-lv``` (__Use the remaining space of the PV size__). Verify that the logical volumes have been created successfully__.

__Note__: apps-lv is used to store data for the Website while logs-lv is used to store data for logs.
```bash
sudo lvcreate -n apps-lv -L 14G data_vg

sudo lvcreate -n logs-lv -L 14G data_vg

sudo lvs
```
![LV](./images/lv-create.png)

__10a.__ __Verify the entire setup__
```bash
sudo vgdisplay -v   #view complete setup, VG, PV and LV
```
![VG display](./images/vg-display.png)

```bash
lsblk
```
![List Block](./images/list-block3.png)

__10b.__ __Use ```mkfs.ext4``` to format the logical volumes with ext4 filesystem__

```bash
sudo mkfs.ext4 /dev/data_vg/apps-lv

sudo mkfs.ext4 /dev/data_vg/logs-lv
```
![filesystem](./images/mkfs.png)

__11.__ __Create ```/var/www/html``` directory to store website files and ```/home/recovery/logs``` to store backup of log data__
```bash
sudo mkdir -p /var/www/html

sudo mkdir -p /home/recovery/logs
```
#### Mount /var/www/html on apps-lv logical volume
```bash
sudo mount /dev/data-vg/apps-lv /var/www/html
```
![Mount apps-lv](./images/mount-apply.png)

__12.__ __Use ```rsync``` utility to backup all the files in the log directory ```/var/log``` into ```/home/recovery/logs``` (This is required before mounting the file system)__

```bash
sudo rsync -av /var/log /home/recovery/logs
```
![Back logs](./images/back-logs.png)

__13.__ __Mount ```/var/log``` on ```logs-lv``` logical volume (All existing data on /var/log is deleted with this mount process which was why the data was backed up)__

```bash
sudo mount /dev/data-vg/logs-lv /var/log
```
![Mount logs-lv](./images/mount-logslv.png)

__14.__ __Restore log file back into ```/var/log``` directory__
```bash
sudo rsync -av /home/recovery/logs/log/ /var/log
```
![Restore backed files](./images/recover-logs.png)

__15.__ __Update ```/etc/fstab``` file so that the mount configuration will persist after restart of the server__

#### Get the ```UUID``` of the device and Update the ```/etc/fstab``` file with the format shown inside the file using the ```UUID```. Remember to remove the leading and ending quotes.
```bash
sudo blkid   # To fetch the UUID

sudo vi /etc/fstab
```
![Update fstab](./images/update-fstab.png)

__16.__ __Test the configuration and reload daemon. Verify the setup__
```bash
sudo mount -a   # Test the configuration

sudo systemctl daemon-reload

df -h   # Verifies the setup
```
![Verify setup](./images/verify-setup.png)



## Step 3 - Install WordPress on the Web Server EC2

__1.__ __Update the repository__
```bash
sudo yum -y update
```

__2.__ __Install wget, Apache and it's dependencies__

```bash
sudo yum wget httpd php-fpm php-json
```
![Install Apache](./images/install-httpd.png)

__3.__ __Install the latest version of PHP and it's dependencies using the Remi repository__

#### Install the EPEL repository
The package manager ```dnf``` was used here.
It generally offers better performance and more efficient dependency resolution.
```dnf``` is the modern, actively maintained package manager, while yum is older and gradually being phased out.

#### The system version of the RHEL EC2 is version "9"

```bash
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
```
![Install Epel repo](./images/install-epel-repo.png)


#### Install PHP, PHP-FPM (FastCGI Process Manager) and associated PHP modules using the command.

```bash
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
```
![Install PHP](./images/install-php.png)

#### To verify the version installed to run.

```bash
php -v
```
![PHP version](./images/php-version.png)

#### Start, enable and check status of PHP-FPM on boot-up.

```bash
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
sudo systemctl status php-fpm
```
![Start php-fpm](./images/start-php-fpm.png)

__4.__ __Configure SELinux Policies__

To instruct SELinux to allow Apache to execute the PHP code via PHP-FPM run.

```bash
sudo chown -R apache:apache /var/www/html
sudo chcon -t httpd_sys_rw_content_t /var/www/html -R
sudo setsebool -P httpd_execmem 1
sudo setsebool -P httpd_can_network_connect=1
sudo setsebool -P httpd_can_network_connect_db=1
```
![permissions](./images/change-permissions.png)

####  Restart Apache web server for PHP to work with Apache web server.

```bash
sudo systemctl restart httpd
```
![Restart httpd](./images/restart-httpd.png)

#### Test to see the default Apache page on a browser using the public IP address

![Default page](./images/rhel-test-page.png)


__5.__ __Download WordPress__

Download wordpress and copy wordpress content to /var/www/html

```bash
sudo mkdir wordpress && cd wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz   # Extract wordpress
```
![Download wp](./images/download-wordpress.png)

#### After extraction, ```cd``` into the extracted ```wordpress``` and ```Copy``` the content of ```wp-config-sample.php``` to ```wp-config.php```.

This will copy and create the file wp-config.php

```bash
cd wordpress/
sudo cp -R wp-config-sample.php wp-config.php
```
![wp-config](./images/cp-wp-config.png)

#### Exit from the extracted ```wordpress```. Copy the content of the extracted ```wordpress``` to ```/var/www/html```.

```bash
cd ..
sudo cp -R wordpress/. /var/www/html/
```
![Cp /html](./images/cp-wp-html.png)

__6.__ __Install MariaDB on EC2 Server__

#### Update the EC2
```bash
sudo yum update -y
```
![Update RHEL](./images/update-rhel-db.png)

#### Install MariaDB Server
```bash
sudo dnf --disablerepo='epel*' install -y mariadb-server
```
![install MariaDB](./images/install-MariaDB.png)

#### Verify that the service is up and running. If it is not running, restart the service and enable it so it will be running even after reboot.

```bash
sudo systemctl enable --now mariadb
sudo systemctl status mariadb --no-pager
```
![Start MariaDB](./images/start-MariaDB.png)


__7.__ __Configure DB to work with WordPress__

#### Run mysql secure script

```bash
sudo mysql_secure_installation
```
![Secure MySQL](./images/secure-MySQL.png)
![Sudo MariaDB](./images/sudo-MariaDB.png)

#### Create database

The user "wordpress" will be connecting to the database using the Web Server __private IP address__


```bash
mysql -h 172.31.40.241 -u wpuser -p
```
![Create db](./images/create-db.png)

#### Open Wp-config.php file and edit


```bash
sudo vi /var/www/html/wp-config.php
```

Replace with this
```bash
define('DB_NAME', 'wordpress');
define('DB_USER', 'wpuser');
define('DB_PASSWORD', 'StrongPassword123!');
define('DB_HOST', '172.31.40.241');

```

![Edit wp config](./images/wp-config.png)




#### Access the web page again with the Web Server public IP address and install wordpress on the browser

![wp installed](./images/wp-installed.png)
![wp login](./images/login-to-wp.png)
![wp success](./images/wp-success.png)
![wp website](./images/wp-website.png)


## At this point, the implementation of this project is complete and WordPress is available to be used.
