﻿# **How to Install LAMP on CentOS 8/7 / RHEL 8**

- yum install epel-release -y
  - if 'no match for argument: epel-release', sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
- yum install -y httpd httpd-tools mariadb-server mariadb php php-fpm php-json php-opcache php-gd php-curl php-mysqlnd php-mbstring mod_ssl php-ldap
- systemctl start httpd
- systemctl start mariadb 
- systemctl start php-fpm
- systemctl enable httpd
- systemctl enable mariadb
- systemctl enable php-fpm 
- systemctl status httpd
- systemctl status mariadb
- systemctl status php-fpm 

SSL Installation
- openssl req -newkey rsa:2048 -nodes -keyout /etc/pki/tls/private/httpd.key -x509 -days 365 -out /etc/pki/tls/certs/httpd.crt
- ls -l /etc/pki/tls/private/httpd.key /etc/pki/tls/certs/httpd.crt
- To include your newly created SSL certificate into the Apache web-server configuration open the /etc/httpd/conf.d/ssl.conf file with administrative privileges and change the following lines:
```
	FROM:
	SSLCertificateFile /etc/pki/tls/certs/localhost.crt
	SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
	TO:
	SSLCertificateFile /etc/pki/tls/certs/httpd.crt
	SSLCertificateKeyFile /etc/pki/tls/private/httpd.key
```
- systemctl reload httpd

Secure MariaDB

- mysql_secure_installation 

Rules to allow ports (80 & 443) via Firewall

- firewall-cmd --permanent --zone=public --add-service=http
  - If firewall-cmd command not found. yum install firewalld
  - sudo systemctl start firewalld
  - sudo systemctl enable firewalld
  - sudo systemctl status firewalld
- firewall-cmd --permanent --zone=public --add-service=https
- firewall-cmd --reload

Testing


- http://<IP/localhost>/ ,via browser
  - curl -4 icanhazip.com , to find your IP
- mysql -uroot -p , to access mariadb
- vim /var/www/html/test.php 
  - <?php phpinfo(); ?>
- Systemctl restart httpd

*If LDAP can't connect:* 
- getsebool -a | grep httpd
- setsebool -P httpd_can_connect_ldap 1
	- If error: dnf reinstall "selinux-policy*"


# **Configuring Apache virtual hosts in Red Hat 8 / CentOS 8**

- vim /etc/httpd/conf/httpd.conf 
- cd /etc/httpd/conf.d/
- vim website1.conf >>
```
	<Directory /srv/websites/website1>
		Require all granted
		AllowOverride None
	</Directory>

	<VirtualHost *:80>
		DocumentRoot /srv/websites/website1
		ServerName website1.com
		ServerAlias web1.com
		ServerAdmin root@admin.com
		ErrorLog "logs/website1_error_log"
		CustomLog "logs/website1_access_log" combined
	</VirtualHost>
```
  
- ls -Z , to manage the SELinux Context
- semanage fcontext -a -t httpd_sys_content_t "/srv/websites(/.*)?"  , to manage SELinux
  - yum install policycoreutils-python-utils , if semanage command not found
- restorecon -R -v /srv/websites/
- systemctl restart httpd
- ip addr show eth0 | grep inet | awk '{ print $2; }' | sed 's/\/.*$//'
- vi /etc/hosts , DNS Resolution 
  - <IP> website1.com
- http://website1.com in browser 

Webiste 2

- cat/etc/httpd/conf.d/website1.conf, copy the code
- cd /etc/httpd/conf.d
- vim website2.conf, paste the code, replace website1 with website2
- cd /srv/websites
- mkdir website2
- vim index.html >> add sample data
- systemctl restart httpd
- vim /etc/hosts
  - Add wesbite2.com next to website1.com
 - systemctl restart httpd
- http://website2.com


 
