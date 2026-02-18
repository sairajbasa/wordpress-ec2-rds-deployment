# WordPress Deployment Commands ---> check with readme.md

## Switch to root
```bash
sudo -s
dnf update -y
```

## Install MariaDB client
```bash
sudo dnf install mariadb105 -y
mysql --version
```

## Connect to RDS
```bash
mysql -h <RDS-EndPoint> -P 3306 -u admin -p
```

## Inside MySQL
```mysql
create database wordpressdb;
create user 'wpuser' IDENTIFIED BY 'root1234';
grant all privileges on wordpressdb.* to wpuser;
flush privileges;
exit;
```

## Install Apache
```bash
dnf install httpd -y
systemctl start httpd
systemctl enable httpd
```

## Install PHP
```bash
dnf install php php-mysqlnd php-fpm wget -y
systemctl restart httpd
```

## Download WordPress
```
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
```

## Configure WordPress
```
cd wordpress
cp wp-config-sample.php wp-config.php
vi wp-config.php
```

## Move files
```
cd ..
cp -r wordpress/* /var/www/html
systemctl restart httpd
```
