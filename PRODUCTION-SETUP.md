# Goldsvet Casino - Production Setup Guide

## Server Configuration

**Domain:** srv1196901.hstgr.cloud
**Environment:** Production
**Web Server:** Apache 2.4.58
**PHP Version:** 8.4.15
**Database:** MariaDB 10.11.13
**Framework:** Laravel 10.50.0

## Installation Summary

### 1. Database Setup
- Database Name: `goldsvet_casino`
- Database User: `root`
- Database Password: (empty)
- Tables Imported: 71 tables
- Games Loaded: 2,368 games

### 2. Web Server Configuration

**Apache Virtual Host:** `/etc/apache2/sites-available/goldsvet.conf`

Features:
- HTTP to HTTPS redirect
- SSL enabled (self-signed certificate)
- Reverse proxy to Laravel development server (port 8000)
- Security headers configured

**Enabled Apache Modules:**
- proxy
- proxy_http
- rewrite
- ssl
- headers

### 3. Laravel Optimization

Caches enabled:
- Configuration cache
- Route cache
- View cache
- Framework bootstrap cache

### 4. File Permissions

Storage and cache directories configured with proper permissions:
- Owner: www-data:www-data
- Directory permissions: 775
- File permissions: 664

## Environment Configuration

**`.env` settings:**
```
APP_NAME="Goldsvet Casino"
APP_ENV=production
APP_DEBUG=false
APP_URL=https://srv1196901.hstgr.cloud

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=goldsvet_casino
DB_USERNAME=root
DB_PASSWORD=
```

## Access URLs

- **HTTP:** http://srv1196901.hstgr.cloud (redirects to HTTPS)
- **HTTPS:** https://srv1196901.hstgr.cloud

## Services Running

1. **Apache:** Ports 80, 443
2. **PHP Artisan Serve:** Port 8000
3. **MariaDB:** Port 3306

## Important Notes

### Current Setup
This is a temporary production setup using:
- Apache as reverse proxy
- PHP built-in development server (artisan serve)
- Self-signed SSL certificate

### Recommended Production Improvements

1. **SSL Certificate:**
   - Replace self-signed certificate with Let's Encrypt
   - Install certbot: `apt-get install certbot python3-certbot-apache`
   - Run: `certbot --apache -d srv1196901.hstgr.cloud`

2. **PHP-FPM Setup:**
   - Install PHP-FPM when PPA access is restored
   - Configure Apache to use PHP-FPM instead of reverse proxy
   - Update virtual host to serve files directly

3. **Redis Cache:**
   - Install Redis for better caching
   - Update `.env` CACHE_DRIVER to redis

4. **WebSocket Servers:**
   - Set up PTWEBSOCKET services for real-time features
   - Configure PM2 to manage Node.js processes

5. **Security:**
   - Set strong MySQL root password
   - Configure firewall rules
   - Enable fail2ban for intrusion prevention
   - Review and harden Apache configuration

## Maintenance Commands

### Laravel
```bash
# Clear all caches
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear

# Rebuild caches
php artisan config:cache
php artisan route:cache
php artisan view:cache
php artisan optimize
```

### Apache
```bash
# Restart Apache
service apache2 restart

# Reload configuration
service apache2 reload

# Test configuration
apache2ctl configtest
```

### MariaDB
```bash
# Connect to database
mariadb -u root goldsvet_casino

# Backup database
mysqldump -u root goldsvet_casino > backup_$(date +%Y%m%d).sql
```

## Troubleshooting

### If website is not accessible:
1. Check Apache status: `service apache2 status`
2. Check PHP artisan serve: `ps aux | grep php`
3. Check Apache logs: `tail -f /var/log/apache2/goldsvet_error.log`
4. Verify port 8000 is listening: `ss -tlnp | grep 8000`

### If database connection fails:
1. Check MariaDB status: `service mariadb status`
2. Test connection: `mariadb -u root goldsvet_casino -e "SELECT 1;"`
3. Verify credentials in `/casino/.env`

## Next Steps

1. Point DNS A record for srv1196901.hstgr.cloud to this server's IP
2. Install Let's Encrypt SSL certificate
3. Set up proper PHP-FPM configuration
4. Configure WebSocket servers for casino games
5. Set up automated backups
6. Configure monitoring and logging
