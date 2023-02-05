#!/bin/bash
sudo su -
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-0b9c626fb897f9937 fs-035fad5f96752ef13:/ /var/www/
yum install -y httpd 
systemctl start httpd
systemctl enable httpd
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
git clone https://github.com/dreyabraham/tooling-1.git
mkdir /var/www/html
cp -R tooling-1/html/ /var/www/html/
cd tooling-1
mysql -h project15-rds.c2gn43ailhgc.us-east-1.rds.amazonaws.com -u ACSadmin -p admin12345 < tooling-db.sql
cd /var/www/html/
touch healthstatus
sed -i "s/$db = mysqli_connect('mysql.tooling.svc.cluster.local', 'admin', 'admin', 'tooling');/$db = mysqli_connect('project15-rds.c2gn43ailhgc.us-east-1.rds.amazonaws.com', 'ACSadmin', 'admin12345', 'toolingdb');/g" functions.php
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
systemctl restart httpd






