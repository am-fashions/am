# 🚀 Production Deployment Guide

## Overview
This guide will help you deploy your e-commerce application to production hosting. Your app has 3 components:
1. **Frontend Website** (React - Port 3002)
2. **Admin Dashboard** (React - Port 3001)
3. **Backend API** (Node.js/Express - Port 5000)
4. **Database** (MySQL)

---

## 🎯 Recommended Hosting Options

### Option 1: All-in-One Platform (Easiest)
**Vercel (Frontend) + Railway/Render (Backend + Database)**

### Option 2: Traditional Hosting
**Hostinger/Namecheap (Frontend) + VPS (Backend + Database)**

### Option 3: Cloud Platform (Most Scalable)
**AWS/Google Cloud/Azure (Everything)**

---

## 📋 Pre-Deployment Checklist

### 1. Environment Variables Setup

#### Backend (.env for production)
```env
# Server Configuration
PORT=5000
NODE_ENV=production

# Database Configuration (from your hosting provider)
DB_HOST=your-database-host.com
DB_USER=your_db_user
DB_PASSWORD=your_secure_password
DB_NAME=ecommerce_admin
DB_PORT=3306

# Frontend URLs (your actual domains)
FRONTEND_URL=https://yourdomain.com
ADMIN_URL=https://admin.yourdomain.com

# JWT Configuration
JWT_SECRET=generate_a_very_long_random_string_here
JWT_EXPIRES_IN=7d
```

#### Frontend (.env for production)
```env
REACT_APP_API_URL=https://api.yourdomain.com/api
```

#### Admin Dashboard (.env for production)
```env
REACT_APP_API_URL=https://api.yourdomain.com/api
```

### 2. Database Migration
You need to run the updated schema on your production database:
- Use the `admin-dashboard/database/schema.sql` file
- Make sure `product_id` is nullable in `order_items` table
- Run the seed data if needed

---

## 🔧 Step-by-Step Deployment

## OPTION 1: Vercel + Railway (Recommended for Beginners)

### Part A: Deploy Backend to Railway

1. **Create Railway Account**
   - Go to https://railway.app
   - Sign up with GitHub

2. **Create New Project**
   - Click "New Project"
   - Select "Deploy from GitHub repo"
   - Connect your repository

3. **Add MySQL Database**
   - In your project, click "New"
   - Select "Database" → "MySQL"
   - Railway will create a database and provide credentials

4. **Configure Backend Service**
   - Click "New" → "GitHub Repo"
   - Select your repository
   - Set root directory: `admin-dashboard/server`
   - Add environment variables from Railway's MySQL database:
     ```
     DB_HOST=<from Railway>
     DB_USER=<from Railway>
     DB_PASSWORD=<from Railway>
     DB_NAME=<from Railway>
     DB_PORT=<from Railway>
     PORT=5000
     NODE_ENV=production
     FRONTEND_URL=https://yourdomain.vercel.app
     ADMIN_URL=https://admin-yourdomain.vercel.app
     JWT_SECRET=<generate random string>
     ```

5. **Run Database Migration**
   - Use Railway's MySQL client or connect via MySQL Workbench
   - Run your `schema.sql` file
   - Run the migration to make `product_id` nullable

6. **Deploy**
   - Railway will auto-deploy
   - Note your backend URL (e.g., `https://your-app.railway.app`)

### Part B: Deploy Frontend to Vercel

1. **Create Vercel Account**
   - Go to https://vercel.com
   - Sign up with GitHub

2. **Deploy Main Website**
   - Click "New Project"
   - Import your repository
   - Configure:
     - Root Directory: `./` (root)
     - Framework Preset: Create React App
     - Build Command: `npm run build`
     - Output Directory: `build`
   - Add environment variable:
     ```
     REACT_APP_API_URL=https://your-backend.railway.app/api
     ```
   - Click "Deploy"

3. **Deploy Admin Dashboard**
   - Click "New Project" again
   - Import same repository
   - Configure:
     - Root Directory: `admin-dashboard/client`
     - Framework Preset: Vite
     - Build Command: `npm run build`
     - Output Directory: `dist`
   - Add environment variable:
     ```
     REACT_APP_API_URL=https://your-backend.railway.app/api
     ```
   - Click "Deploy"

4. **Update Backend CORS**
   - Go back to Railway
   - Update environment variables with your Vercel URLs:
     ```
     FRONTEND_URL=https://your-site.vercel.app
     ADMIN_URL=https://your-admin.vercel.app
     ```

---

## OPTION 2: Shared Hosting (Hostinger/Namecheap)

### Requirements
- Shared hosting with Node.js support
- MySQL database
- SSH access (recommended)

### Part A: Setup Database

1. **Create MySQL Database**
   - Login to cPanel
   - Go to MySQL Databases
   - Create new database: `ecommerce_admin`
   - Create user and grant all privileges
   - Note: hostname, username, password, database name

2. **Import Schema**
   - Go to phpMyAdmin
   - Select your database
   - Import `schema.sql`
   - Run the migration SQL to make `product_id` nullable:
     ```sql
     ALTER TABLE order_items DROP FOREIGN KEY order_items_ibfk_2;
     ALTER TABLE order_items MODIFY COLUMN product_id INT NULL;
     ALTER TABLE order_items ADD CONSTRAINT order_items_ibfk_2 
     FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE SET NULL;
     ```

### Part B: Deploy Backend

1. **Setup Node.js Application**
   - In cPanel, go to "Setup Node.js App"
   - Create new application:
     - Node.js version: 18.x or higher
     - Application mode: Production
     - Application root: `admin-dashboard/server`
     - Application URL: `api.yourdomain.com` or `/api`
     - Application startup file: `server.js`

2. **Upload Files**
   - Use FTP/SFTP or File Manager
   - Upload `admin-dashboard/server` folder
   - Upload `.env` file with production settings

3. **Install Dependencies**
   - SSH into your server
   - Navigate to server directory
   - Run: `npm install --production`

4. **Start Application**
   - In cPanel Node.js App manager, click "Start"

### Part C: Deploy Frontend

1. **Build Frontend**
   - On your local machine:
     ```bash
     # Main website
     npm run build
     
     # Admin dashboard
     cd admin-dashboard/client
     npm run build
     ```

2. **Upload to Hosting**
   - Main website: Upload `build` folder contents to `public_html`
   - Admin dashboard: Upload `admin-dashboard/client/dist` to `public_html/admin`

3. **Configure .htaccess** (for React Router)
   Create `.htaccess` in both directories:
   ```apache
   <IfModule mod_rewrite.c>
     RewriteEngine On
     RewriteBase /
     RewriteRule ^index\.html$ - [L]
     RewriteCond %{REQUEST_FILENAME} !-f
     RewriteCond %{REQUEST_FILENAME} !-d
     RewriteRule . /index.html [L]
   </IfModule>
   ```

---

## OPTION 3: VPS (DigitalOcean/Linode/AWS EC2)

### Part A: Server Setup

1. **Create VPS Instance**
   - Ubuntu 22.04 LTS recommended
   - Minimum: 2GB RAM, 1 CPU

2. **Initial Server Setup**
   ```bash
   # Update system
   sudo apt update && sudo apt upgrade -y
   
   # Install Node.js
   curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
   sudo apt install -y nodejs
   
   # Install MySQL
   sudo apt install -y mysql-server
   sudo mysql_secure_installation
   
   # Install Nginx
   sudo apt install -y nginx
   
   # Install PM2 (process manager)
   sudo npm install -g pm2
   ```

3. **Setup MySQL Database**
   ```bash
   sudo mysql -u root -p
   ```
   ```sql
   CREATE DATABASE ecommerce_admin;
   CREATE USER 'ecommerce_user'@'localhost' IDENTIFIED BY 'strong_password';
   GRANT ALL PRIVILEGES ON ecommerce_admin.* TO 'ecommerce_user'@'localhost';
   FLUSH PRIVILEGES;
   EXIT;
   ```

4. **Import Database Schema**
   ```bash
   mysql -u ecommerce_user -p ecommerce_admin < schema.sql
   ```

### Part B: Deploy Backend

1. **Upload Backend Code**
   ```bash
   # On your local machine
   scp -r admin-dashboard/server user@your-server-ip:/var/www/
   ```

2. **Setup Backend**
   ```bash
   # On server
   cd /var/www/server
   npm install --production
   
   # Create .env file
   nano .env
   # Add production environment variables
   
   # Start with PM2
   pm2 start server.js --name "ecommerce-api"
   pm2 save
   pm2 startup
   ```

### Part C: Deploy Frontend

1. **Build and Upload**
   ```bash
   # Local machine - Main website
   npm run build
   scp -r build/* user@your-server-ip:/var/www/frontend/
   
   # Local machine - Admin dashboard
   cd admin-dashboard/client
   npm run build
   scp -r dist/* user@your-server-ip:/var/www/admin/
   ```

2. **Configure Nginx**
   ```bash
   sudo nano /etc/nginx/sites-available/ecommerce
   ```
   
   ```nginx
   # Main Website
   server {
       listen 80;
       server_name yourdomain.com www.yourdomain.com;
       root /var/www/frontend;
       index index.html;
       
       location / {
           try_files $uri $uri/ /index.html;
       }
   }
   
   # Admin Dashboard
   server {
       listen 80;
       server_name admin.yourdomain.com;
       root /var/www/admin;
       index index.html;
       
       location / {
           try_files $uri $uri/ /index.html;
       }
   }
   
   # Backend API
   server {
       listen 80;
       server_name api.yourdomain.com;
       
       location / {
           proxy_pass http://localhost:5000;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
       }
   }
   ```

3. **Enable Site and Restart Nginx**
   ```bash
   sudo ln -s /etc/nginx/sites-available/ecommerce /etc/nginx/sites-enabled/
   sudo nginx -t
   sudo systemctl restart nginx
   ```

4. **Setup SSL with Let's Encrypt**
   ```bash
   sudo apt install -y certbot python3-certbot-nginx
   sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
   sudo certbot --nginx -d admin.yourdomain.com
   sudo certbot --nginx -d api.yourdomain.com
   ```

---

## 🔒 Security Checklist

- [ ] Change all default passwords
- [ ] Use strong JWT secret (at least 32 characters)
- [ ] Enable HTTPS/SSL certificates
- [ ] Set up firewall (UFW on Ubuntu)
- [ ] Disable root SSH login
- [ ] Use environment variables (never commit .env)
- [ ] Enable CORS only for your domains
- [ ] Set up database backups
- [ ] Use prepared statements (already done)
- [ ] Implement rate limiting
- [ ] Add security headers

---

## 📊 Post-Deployment Testing

1. **Test Backend API**
   ```bash
   curl https://api.yourdomain.com/api/health
   ```

2. **Test Frontend**
   - Visit your website
   - Add items to cart
   - Place a test order
   - Check admin dashboard

3. **Test Database Connection**
   - Check if orders are being saved
   - Verify customer creation
   - Check order items

---

## 🐛 Common Issues & Solutions

### Issue: CORS Errors
**Solution:** Update backend CORS settings with your production URLs

### Issue: Database Connection Failed
**Solution:** Check DB credentials, ensure MySQL is running, check firewall

### Issue: 502 Bad Gateway
**Solution:** Backend not running, check PM2 status: `pm2 status`

### Issue: React Router 404 on Refresh
**Solution:** Add .htaccess (shared hosting) or configure Nginx properly (VPS)

### Issue: Environment Variables Not Working
**Solution:** Rebuild frontend after changing .env, restart backend

---

## 💰 Estimated Costs

### Railway + Vercel (Easiest)
- Railway: $5-20/month (includes database)
- Vercel: Free for hobby projects
- **Total: $5-20/month**

### Shared Hosting
- Hostinger/Namecheap: $3-10/month
- **Total: $3-10/month**

### VPS
- DigitalOcean Droplet: $6-12/month
- Domain: $10-15/year
- **Total: $6-12/month + domain**

---

## 🎓 Recommended for You

**If you're new to deployment:** Start with **Railway + Vercel**
- Easiest setup
- Automatic deployments
- Good free tier
- Scales automatically

**If you want full control:** Use **VPS (DigitalOcean)**
- More configuration
- Better performance
- Lower cost at scale
- Full server access

---

## 📞 Need Help?

After deployment, test everything thoroughly:
1. Place test orders
2. Check admin dashboard
3. Verify email notifications (if implemented)
4. Test on mobile devices
5. Check browser console for errors

---

## ✅ Deployment Checklist

- [ ] Database created and schema imported
- [ ] Database migration run (product_id nullable)
- [ ] Backend deployed and running
- [ ] Frontend built and deployed
- [ ] Admin dashboard built and deployed
- [ ] Environment variables configured
- [ ] CORS settings updated
- [ ] SSL certificates installed
- [ ] DNS records configured
- [ ] Test order placed successfully
- [ ] Admin dashboard accessible
- [ ] All API endpoints working

---

**Your app is production-ready! The code works locally, and with these steps, it will work on any hosting platform.** 🚀
