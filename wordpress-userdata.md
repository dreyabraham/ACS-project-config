#!/bin/bash
sudo su -
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-0e7ae24b14e4fcf7f fs-035fad5f96752ef13:/ /var/www/
sudo yum update -y
sudo yum install httpd -y
systemctl enable httpd
yum module reset php -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
wget http://wordpress.org/latest.tar.gz
tar xzvf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
rm -rf latest.tar.gz
mkdir /var/www/html/
cp -R wordpress/ /var/www/html/
cd /var/www/html/
touch healthstatus
sed -i "s/localhost/project15-rds.c2gn43ailhgc.us-east-1.rds.amazonaws.com/g" wp-config.php 
sed -i "s/username_here/ACSadmin/g" wp-config.php 
sed -i "s/password_here/admin12345/g" wp-config.php 
sed -i "s/database_name_here/wordpressdb/g" wp-config.php 
chcon -t httpd_sys_rw_content_t /var/www/html/ -R 
systemctl restart httpd




