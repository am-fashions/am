# 🚀 Quick Deployment Reference Card

**Print this and keep it handy during deployment!**

---

## ⚡ Critical Migration SQL (MUST RUN!)

```sql
ALTER TABLE order_items DROP FOREIGN KEY order_items_ibfk_2;
ALTER TABLE order_items MODIFY COLUMN product_id INT NULL;
ALTER TABLE order_items ADD CONSTRAINT order_items_ibfk_2 
FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE SET NULL;
```

**Without this, orders will fail!**

---

## 📝 Environment Variables

### Backend (.env)
```env
PORT=5000
NODE_ENV=production
DB_HOST=your-database-host
DB_USER=your-db-user
DB_PASSWORD=your-db-password
DB_NAME=ecommerce_admin
DB_PORT=3306
FRONTEND_URL=https://yourdomain.com
ADMIN_URL=https://admin.yourdomain.com
JWT_SECRET=generate-long-random-string-here
```

### Frontend (.env)
```env
REACT_APP_API_URL=https://api.yourdomain.com/api
```

### Admin (.env in admin-dashboard/client)
```env
REACT_APP_API_URL=https://api.yourdomain.com/api
```

---

## 🏗️ Build Commands

### Main Website
```bash
npm install
npm run build
# Upload 'build' folder
```

### Admin Dashboard
```bash
cd admin-dashboard/client
npm install
npm run build
# Upload 'dist' folder
```

### Backend
```bash
cd admin-dashboard/server
npm install --production
# Start with: node server.js or pm2 start server.js
```

---

## ✅ Quick Checklist

### Database
- [ ] Database created
- [ ] Schema imported
- [ ] **Migration run** (product_id nullable)
- [ ] Verified with `DESCRIBE order_items;`

### Backend
- [ ] Code deployed
- [ ] .env configured
- [ ] Dependencies installed
- [ ] Server running
- [ ] `/api/health` returns 200

### Frontend
- [ ] Built successfully
- [ ] Uploaded to hosting
- [ ] .env configured
- [ ] Website loads

### Admin
- [ ] Built successfully
- [ ] Uploaded to hosting
- [ ] .env configured
- [ ] Dashboard loads

### Testing
- [ ] Place test order
- [ ] Check admin dashboard
- [ ] Verify in database

---

## 🔍 Verification Commands

### Check Database Structure
```sql
DESCRIBE order_items;
-- product_id should show NULL = YES
```

### Test Backend
```bash
curl https://api.yourdomain.com/api/health
# Should return: {"status":"OK"}
```

### Check Tables
```sql
SHOW TABLES;
-- Should show: admins, customers, orders, order_items, payments, etc.
```

---

## 🐛 Quick Troubleshooting

### Orders Fail?
→ Run the migration SQL

### CORS Error?
→ Check FRONTEND_URL and ADMIN_URL in backend .env

### Database Connection Failed?
→ Verify DB credentials in .env

### 404 on Page Refresh?
→ Add .htaccess for React Router

---

## 📞 Important URLs

### Local
- Frontend: http://localhost:3002
- Admin: http://localhost:3001
- Backend: http://localhost:5000

### Production (Example)
- Frontend: https://yourdomain.com
- Admin: https://admin.yourdomain.com
- Backend: https://api.yourdomain.com

---

## 🔐 Security Checklist

- [ ] HTTPS enabled (SSL)
- [ ] Strong database password
- [ ] JWT secret generated (32+ chars)
- [ ] CORS limited to your domains
- [ ] .env not in git
- [ ] Firewall configured (VPS)

---

## 📊 Files You Need

1. `admin-dashboard/database/schema.sql`
2. Migration SQL (above)
3. Backend code (`admin-dashboard/server`)
4. Frontend build (`build` folder)
5. Admin build (`dist` folder)

---

## ⏱️ Estimated Time

- Database setup: 10 min
- Backend deploy: 15 min
- Frontend deploy: 10 min
- Admin deploy: 10 min
- Testing: 10 min
**Total: ~1 hour**

---

## 🎯 Success Criteria

✅ Website loads  
✅ Can add to cart  
✅ Can place order  
✅ Order appears in admin  
✅ Order saved in database  

---

## 🆘 Emergency SQL

### If migration fails, try:
```sql
-- Find foreign key name
SHOW CREATE TABLE order_items;

-- Use the actual name you see
ALTER TABLE order_items DROP FOREIGN KEY <actual_name>;
ALTER TABLE order_items MODIFY COLUMN product_id INT NULL;
```

---

## 💰 Hosting Costs

- Railway + Vercel: $5-20/month
- Shared Hosting: $3-10/month
- VPS: $6-12/month

---

## 📱 Test on Mobile

After deployment:
- [ ] Test on phone
- [ ] Test on tablet
- [ ] Test different browsers
- [ ] Check responsive design

---

## 🔄 Update Process

To update after deployment:

### Code Changes
1. Make changes locally
2. Test locally
3. Build: `npm run build`
4. Upload new build files

### Database Changes
1. Test SQL locally
2. Backup production DB
3. Run SQL on production
4. Verify changes

---

## 📋 Post-Deployment

### First Hour
- Monitor error logs
- Test all features
- Check database

### First Day
- Monitor orders
- Check performance
- Verify emails (if any)

### First Week
- Daily log checks
- Backup database
- Monitor uptime

---

## 🎉 You're Ready!

**Everything you need is here. Follow the guides, run the migration, and you'll be live!**

**Good luck!** 🚀

---

## Quick Links to Full Guides

- **Full Deployment:** `PRODUCTION_DEPLOYMENT_GUIDE.md`
- **Database Setup:** `PRODUCTION_DATABASE_SETUP.md`
- **Complete Checklist:** `DEPLOYMENT_CHECKLIST.md`
- **Confirmation:** `YES_IT_WILL_WORK_IN_PRODUCTION.md`
