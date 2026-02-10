# ✅ Deployment Checklist

Use this checklist when deploying to production to ensure nothing is missed.

---

## 📦 Before Deployment

### Code Preparation
- [ ] All features tested locally
- [ ] No console errors in browser
- [ ] Orders working correctly
- [ ] Admin dashboard accessible
- [ ] All environment variables documented

### Files to Prepare
- [ ] `admin-dashboard/database/schema.sql` - Database structure
- [ ] Migration SQL for `product_id` nullable
- [ ] Production `.env` files ready
- [ ] Build commands tested locally

---

## 🗄️ Database Setup (CRITICAL!)

### Create Database
- [ ] Database created on hosting
- [ ] Database user created
- [ ] User has all privileges
- [ ] Connection credentials saved

### Import Schema
- [ ] `schema.sql` imported successfully
- [ ] All tables created (check with `SHOW TABLES;`)
- [ ] No errors during import

### Run Migration (MUST DO!)
- [ ] Foreign key dropped
- [ ] `product_id` changed to NULL
- [ ] Foreign key re-added
- [ ] Verified with `DESCRIBE order_items;`
- [ ] `product_id` shows `NULL = YES`

### Optional: Seed Data
- [ ] Sample data imported (if needed)
- [ ] Test admin user created

---

## 🔧 Backend Deployment

### Environment Setup
- [ ] `.env` file created with production values
- [ ] `DB_HOST` set correctly
- [ ] `DB_USER` set correctly
- [ ] `DB_PASSWORD` set correctly
- [ ] `DB_NAME` set correctly
- [ ] `DB_PORT` set (usually 3306)
- [ ] `PORT` set (usually 5000)
- [ ] `NODE_ENV=production`
- [ ] `FRONTEND_URL` set to your website URL
- [ ] `ADMIN_URL` set to your admin URL
- [ ] `JWT_SECRET` generated (32+ characters)

### Deployment
- [ ] Code uploaded/deployed
- [ ] Dependencies installed (`npm install`)
- [ ] Server started successfully
- [ ] Health check endpoint working (`/api/health`)
- [ ] Database connection successful
- [ ] No errors in logs

### Testing
- [ ] GET `/api/health` returns 200
- [ ] GET `/api/products` works
- [ ] GET `/api/customers` works
- [ ] GET `/api/orders` works
- [ ] POST `/api/customers` works
- [ ] POST `/api/orders` works

---

## 🎨 Frontend Deployment (Main Website)

### Environment Setup
- [ ] `.env` file created
- [ ] `REACT_APP_API_URL` set to backend URL
- [ ] Example: `https://api.yourdomain.com/api`

### Build
- [ ] Run `npm install`
- [ ] Run `npm run build`
- [ ] Build completed without errors
- [ ] `build` folder created

### Deployment
- [ ] Build files uploaded to hosting
- [ ] `.htaccess` configured (if needed)
- [ ] Website accessible at your domain
- [ ] No 404 errors on page refresh

### Testing
- [ ] Homepage loads correctly
- [ ] All images display
- [ ] Navigation works
- [ ] Product pages load
- [ ] Cart functionality works
- [ ] Add to cart works
- [ ] Cart page displays items
- [ ] No console errors

---

## 👨‍💼 Admin Dashboard Deployment

### Environment Setup
- [ ] `.env` file created in `admin-dashboard/client`
- [ ] `REACT_APP_API_URL` set to backend URL

### Build
- [ ] Navigate to `admin-dashboard/client`
- [ ] Run `npm install`
- [ ] Run `npm run build`
- [ ] Build completed without errors
- [ ] `dist` folder created

### Deployment
- [ ] Build files uploaded to hosting
- [ ] `.htaccess` configured (if needed)
- [ ] Admin dashboard accessible
- [ ] No 404 errors on page refresh

### Testing
- [ ] Login page loads
- [ ] Can login with credentials
- [ ] Dashboard displays stats
- [ ] Orders page works
- [ ] Customers page works
- [ ] Products page works
- [ ] No console errors

---

## 🔒 Security Configuration

### Backend Security
- [ ] CORS configured with production URLs only
- [ ] Strong JWT secret used
- [ ] Database password is strong
- [ ] `.env` file not committed to git
- [ ] SQL injection protection (using prepared statements)
- [ ] Rate limiting configured (optional but recommended)

### Frontend Security
- [ ] HTTPS enabled (SSL certificate)
- [ ] API calls use HTTPS
- [ ] No sensitive data in frontend code
- [ ] Environment variables used correctly

### Server Security (if using VPS)
- [ ] Firewall enabled
- [ ] Only necessary ports open (80, 443, 22)
- [ ] SSH key authentication enabled
- [ ] Root login disabled
- [ ] Regular security updates enabled

---

## 🌐 DNS & Domain Configuration

### DNS Records
- [ ] A record for main domain → Frontend IP
- [ ] A record for `api.yourdomain.com` → Backend IP
- [ ] A record for `admin.yourdomain.com` → Admin IP
- [ ] DNS propagation complete (check with `nslookup`)

### SSL Certificates
- [ ] SSL certificate installed for main domain
- [ ] SSL certificate installed for API subdomain
- [ ] SSL certificate installed for admin subdomain
- [ ] HTTPS redirect enabled
- [ ] Mixed content warnings resolved

---

## 🧪 End-to-End Testing

### Customer Flow
- [ ] Visit website
- [ ] Browse products
- [ ] Add items to cart
- [ ] View cart
- [ ] Fill delivery address
- [ ] Place order
- [ ] Order success message shown
- [ ] Order number received

### Admin Flow
- [ ] Login to admin dashboard
- [ ] View new order in orders list
- [ ] Order details display correctly
- [ ] Customer information visible
- [ ] Order items show correctly
- [ ] Can update order status

### Database Verification
- [ ] Check `customers` table - new customer added
- [ ] Check `orders` table - new order created
- [ ] Check `order_items` table - items saved with NULL product_id
- [ ] Check `payments` table - payment record created

---

## 📊 Monitoring & Maintenance

### Setup Monitoring
- [ ] Error logging configured
- [ ] Uptime monitoring (UptimeRobot, etc.)
- [ ] Database backup scheduled
- [ ] Server resource monitoring

### Documentation
- [ ] Admin credentials saved securely
- [ ] Database credentials saved securely
- [ ] Hosting account details documented
- [ ] Deployment process documented

---

## 🐛 Common Issues Checklist

If something doesn't work, check:

### Orders Failing?
- [ ] Database migration run? (`product_id` nullable?)
- [ ] Backend environment variables correct?
- [ ] Database connection working?
- [ ] CORS configured correctly?

### Frontend Not Loading?
- [ ] Build completed successfully?
- [ ] `.htaccess` configured for React Router?
- [ ] API URL in `.env` correct?
- [ ] HTTPS/HTTP mismatch?

### Backend Not Responding?
- [ ] Server running?
- [ ] Port accessible?
- [ ] Firewall blocking requests?
- [ ] Database connection working?

### CORS Errors?
- [ ] Backend CORS includes frontend URL?
- [ ] URLs match exactly (http vs https)?
- [ ] Trailing slashes consistent?

---

## 🎉 Launch Checklist

### Final Verification
- [ ] Place a real test order
- [ ] Verify order in admin dashboard
- [ ] Check database for order data
- [ ] Test on mobile device
- [ ] Test on different browsers
- [ ] Check page load speed
- [ ] Verify all links work
- [ ] Check contact forms (if any)

### Go Live!
- [ ] Announce to stakeholders
- [ ] Monitor for first few hours
- [ ] Check error logs
- [ ] Verify orders coming through
- [ ] Customer support ready

---

## 📞 Emergency Contacts

Keep these handy:
- Hosting provider support
- Domain registrar support
- Database backup location
- Server access credentials
- Admin dashboard credentials

---

## 🔄 Post-Deployment

### Week 1
- [ ] Monitor error logs daily
- [ ] Check order success rate
- [ ] Verify database backups
- [ ] Test all features again

### Monthly
- [ ] Review server resources
- [ ] Check for security updates
- [ ] Backup database
- [ ] Review error logs
- [ ] Test critical features

---

## ✅ Deployment Complete!

Once all items are checked:
- ✅ Your application is live
- ✅ Orders are working
- ✅ Admin dashboard is functional
- ✅ Database is properly configured
- ✅ Security measures in place

**Congratulations! Your e-commerce platform is now in production!** 🚀

---

## Quick Reference

### Most Important Steps
1. **Database Migration** - Make `product_id` nullable
2. **Environment Variables** - Set correct API URLs
3. **CORS Configuration** - Allow your frontend domains
4. **SSL Certificates** - Enable HTTPS
5. **Test Order Flow** - Verify end-to-end

### If Orders Fail
```sql
-- Run this on production database
ALTER TABLE order_items DROP FOREIGN KEY order_items_ibfk_2;
ALTER TABLE order_items MODIFY COLUMN product_id INT NULL;
ALTER TABLE order_items ADD CONSTRAINT order_items_ibfk_2 
FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE SET NULL;
```

### Environment Variables Template
```env
# Backend
DB_HOST=your-host
DB_USER=your-user
DB_PASSWORD=your-password
DB_NAME=ecommerce_admin
FRONTEND_URL=https://yourdomain.com
ADMIN_URL=https://admin.yourdomain.com

# Frontend
REACT_APP_API_URL=https://api.yourdomain.com/api
```

---

**Print this checklist and check off items as you complete them!** ✓
