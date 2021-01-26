# **How to Install LAMP on CentOS 8/7 / RHEL 8**

- yum install epel-release -y
  - if 'no match for argument: epel-release', sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
- yum install -y httpd httpd-tools mariadb-server mariadb php php-fpm php-json php-opcache php-gd php-curl php-mysqlnd php-mbstring
- systemctl start httpd
- systemctl start mariadb 
- systemctl start php-fpm
- systemctl enable httpd
- systemctl enable mariadb
- systemctl enable php-fpm 
- systemctl status httpd
- systemctl status mariadb
- systemctl status php-fpm 

Secure MariaDB

- mysql\_secure\_installation 

Rules to allow ports (80 & 443) via Firewall

- firewall-cmd --permanent --zone=public --add-service=http
- firewall-cmd --permanent --zone=public --add-service=https
- firewall-cmd --reload

Testing


- http://<IP/localhost>/ ,via browser
- mysql -uroot -p , to access mariadb
- vim /var/www/html/test.php 
  - <?php phpinfo(); ?>
- Systemctl restart httpd
