# 🚀 WordPress Deployment on AWS EC2 (Amazon Linux 2023) with RDS MySQL

## 📌 Project Overview

This project demonstrates deploying a production-style WordPress application using:

- EC2 for Application Layer  
- RDS (MySQL Engine) for Database Layer  
- Apache + PHP on Amazon Linux 2023  

This architecture follows real-world cloud deployment standards with proper separation between application and database layers.

---

## 🏗 Architecture

User → EC2 (Apache + PHP + WordPress) → RDS (MySQL)

### Services Used

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

### 3️⃣ Create Database & Application User

```sql
create database wordpressdb;
create user 'wpuser' IDENTIFIED BY 'root1234';
grant all privileges on wordpressdb.* to wpuser;
flush privileges;
exit;
```

---

### 4️⃣ Install Apache Web Server

```bash
dnf install httpd -y
systemctl start httpd
systemctl enable httpd
```

---

### 5️⃣ Install PHP & Required Dependencies

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

#### 🔹 Update Database Configuration

Inside `wp-config.php`, update the database details:

```php
define('DB_NAME', 'wordpressdb');
define('DB_USER', 'wpuser');
define('DB_PASSWORD', 'root1234');
define('DB_HOST', '<RDS-EndPoint>');
```

---

#### 🔐 Generate Authentication Unique Keys (Important Step)

WordPress requires secure authentication keys for login sessions and cookies.

1. Open the following URL in your browser:

```
https://api.wordpress.org/secret-key/1.1/salt/
```

2. Copy all the generated `define()` lines  
3. Inside `wp-config.php`, locate the Authentication Keys section  
4. Delete the existing authentication `define()` lines  
5. Paste the newly generated lines  
6. Save and exit the file  

Restart Apache:

```bash
systemctl restart httpd
```

---

### 7️⃣ Move WordPress Files to Web Root

```bash
cd ..
cp -r wordpress/* /var/www/html
systemctl restart httpd
```

---

### 8️⃣ Access the Application

Open your browser and enter:

```
http://<EC2-Public-IP>:80
```

Complete the WordPress installation wizard.

---

## 🔐 Why I Used `admin` to Connect to RDS

While creating the RDS MySQL instance, I configured a master username called `admin`.

Since Amazon RDS is a managed database service:

- SSH access to the database server is not allowed  
- Direct OS-level access is restricted  

Therefore, I connected remotely from EC2 using:

```bash
mysql -h <RDS-EndPoint> -P 3306 -u admin -p
```

The `admin` user was used only for initial database setup (creating database and user).

For security best practices, I created a dedicated application user (`wpuser`) and configured WordPress to use that user instead of the master account.

This follows the **Principle of Least Privilege**, which is recommended in production environments.

---

## 🧠 Key Learnings

- EC2 and RDS integration  
- Security group configuration (Port 80 & 3306)  
- Remote database connectivity  
- Database user and privilege management  
- Apache and PHP setup on Amazon Linux 2023  
- Production-style cloud architecture design  

---

## 📷 Screenshots

Screenshots for this project are available inside the `/screenshots` directory.

Example screenshots may include:

- EC2 Instance Configuration  
- RDS Instance Setup  
- Security Group Rules  
- Database Creation  
- WordPress Installation Page  
- Final Application Output  

---

## 🚀 Future Improvements

- Configure RDS as Private (inside private subnet)  
- Add Application Load Balancer  
- Replace Apache with Nginx  
- Add HTTPS using ACM  
- Automate entire setup using Terraform  
- Implement CI/CD pipeline  

---

## 📌 Conclusion

This project demonstrates a real-world AWS architecture where the application layer (EC2) and database layer (RDS) are separated for better scalability, security, and maintainability.

It reflects production deployment standards followed in modern cloud environments.
