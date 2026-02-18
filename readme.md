# 🚀 WordPress Deployment on AWS EC2 (Amazon Linux 2023) with RDS MySQL

## 📌 Project Overview

This project demonstrates deploying a production-style WordPress application using:

- EC2 for Application Layer
- RDS (MySQL Engine) for Database Layer
- Apache + PHP on Amazon Linux 2023

This architecture follows real-world cloud deployment standards with separation of concerns.

---

## 🏗 Architecture

User → EC2 (Apache + PHP + WordPress) → RDS (MySQL)

Services Used:

- Amazon EC2
- Amazon RDS (MySQL)
- WordPress
- MySQL
- AWS Cloud

---

## 🛠 Implementation Steps

### 1️⃣ Connect to EC2
```bash
sudo -s
dnf update -y
```

---

### 2️⃣ Install MariaDB Client (To Connect RDS)

```bash
sudo dnf install mariadb105 -y
mysql --version
```

Connect to RDS:

```bash
mysql -h <RDS-EndPoint> -P 3306 -u admin -p
```

---

### 3️⃣ Create Database & User

```sql
create database wordpressdb;
create user 'wpuser' IDENTIFIED BY 'root1234';
grant all privileges on wordpressdb.* to wpuser;
flush privileges;
exit;
```

---

### 4️⃣ Install Apache

```bash
dnf install httpd -y
systemctl start httpd
systemctl enable httpd
```

---

### 5️⃣ Install PHP & Dependencies

```bash
dnf install php php-mysqlnd php-fpm wget -y
systemctl restart httpd
```

---

### 6️⃣ Download & Configure WordPress

```bash
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
cd wordpress
cp wp-config-sample.php wp-config.php
vi wp-config.php
```

Update DB details:

```php
define('DB_NAME', 'wordpressdb');
define('DB_USER', 'wpuser');
define('DB_PASSWORD', 'root1234');
define('DB_HOST', '<RDS-EndPoint>');
```

---

### 7️⃣ Move Files to Web Root

```bash
cd ..
cp -r wordpress/* /var/www/html
systemctl restart httpd
```

---

### 8️⃣ Access Application

```
http://<EC2-Public-IP>:80
```

Complete WordPress setup via browser.

---

## 🔐 Why I Used `admin` to Connect

While launching RDS, I configured a master user named `admin`.

Since Amazon RDS is a managed database service, SSH access is not allowed.  
So I connected remotely from EC2 using:

```bash
mysql -h <RDS-EndPoint> -P 3306 -u admin -p
```

The master user was used only for initial setup.  
For application security, I created a dedicated user (`wpuser`) following the least privilege principle.

---

## 🧠 Key Learnings

- EC2 + RDS integration
- Security group configuration (80 & 3306)
- Database privilege management
- Linux service management (systemctl)
- Production-style cloud architecture

---

## 📷 Screenshots

Screenshots are available inside the `/screenshots` directory.

---

## 🚀 Future Improvements

- Make RDS private
- Add Nginx instead of Apache
- Add Load Balancer
- Automate using Terraform
