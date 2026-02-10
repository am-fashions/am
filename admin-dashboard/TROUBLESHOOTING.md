# Troubleshooting Guide

## Common Issues and Solutions

### 1. Database Connection Failed

**Error:** `ECONNREFUSED` or `ER_ACCESS_DENIED_ERROR`

**Solutions:**
- ✅ Ensure XAMPP MySQL is running (green indicator in XAMPP Control Panel)
- ✅ Check `server/.env` file has correct credentials:
  ```
  DB_HOST=localhost
  DB_USER=root
  DB_PASSWORD=
  DB_NAME=ecommerce_admin
  ```
- ✅ Verify database exists in phpMyAdmin
- ✅ Re-import `database/schema.sql` if needed

### 2. Port Already in Use

**Error:** `EADDRINUSE: address already in use :::3000` or `:::5000`

**Solutions:**

**For Frontend (Port 3000):**
- Edit `client/vite.config.js`:
  ```js
  server: {
    port: 3001  // Change to any available port
  }
  ```

**For Backend (Port 5000):**
- Edit `server/.env`:
  ```
  PORT=5001  # Change to any available port
  ```
- Update `client/src/services/api.js`:
  ```js
  const API_URL = 'http://localhost:5001/api'
  ```

### 3. Blank Page / White Screen

**Possible Causes:**

**Check Browser Console (F12):**
- Look for JavaScript errors
- Check Network tab for failed API calls

**Solutions:**
- ✅ Ensure backend server is running (`npm run server`)
- ✅ Check API_URL in `client/src/services/api.js` matches backend port
- ✅ Clear browser cache (Ctrl+Shift+Delete)
- ✅ Try incognito/private browsing mode

### 4. API Returns Empty Data

**Error:** Tables show no data or "No data available"

**Solutions:**
- ✅ Verify database has data: Check phpMyAdmin tables
- ✅ Re-import `database/seed.sql`
- ✅ Check browser Network tab (F12) for API response
- ✅ Verify backend console for errors

### 5. CORS Error

**Error:** `Access to XMLHttpRequest blocked by CORS policy`

**Solutions:**
- ✅ Ensure `cors` package is installed in server
- ✅ Check `server/server.js` has `app.use(cors())`
- ✅ Restart backend server
- ✅ If still failing, add specific origin:
  ```js
  app.use(cors({
    origin: 'http://localhost:3000'
  }))
  ```

### 6. Module Not Found Errors

**Error:** `Cannot find module 'express'` or similar

**Solutions:**
```bash
# Reinstall all dependencies
npm install
cd client && npm install
cd ../server && npm install
```

### 7. Tailwind Styles Not Working

**Error:** No styling or plain HTML appearance

**Solutions:**
- ✅ Check `client/src/index.css` has Tailwind imports
- ✅ Verify `client/tailwind.config.js` exists
- ✅ Restart Vite dev server
- ✅ Clear browser cache

### 8. Charts Not Displaying

**Error:** Analytics page shows no charts

**Solutions:**
- ✅ Ensure `recharts` is installed: `cd client && npm install recharts`
- ✅ Check browser console for errors
- ✅ Verify API returns data: Check Network tab
- ✅ Ensure data format matches chart expectations

### 9. MySQL Import Errors

**Error:** SQL syntax errors when importing

**Solutions:**
- ✅ Import `schema.sql` FIRST, then `seed.sql`
- ✅ Ensure database is selected (USE ecommerce_admin)
- ✅ Check MySQL version compatibility
- ✅ Try importing one table at a time

### 10. npm install Fails

**Error:** Various npm installation errors

**Solutions:**
```bash
# Clear npm cache
npm cache clean --force

# Delete node_modules and package-lock.json
rm -rf node_modules package-lock.json
rm -rf client/node_modules client/package-lock.json
rm -rf server/node_modules server/package-lock.json

# Reinstall
npm install
cd client && npm install
cd ../server && npm install
```

### 11. Environment Variables Not Loading

**Error:** `undefined` values for environment variables

**Solutions:**
- ✅ Ensure `.env` file is in `server/` directory (not root)
- ✅ Check file is named exactly `.env` (not `.env.txt`)
- ✅ Restart server after changing `.env`
- ✅ Verify `dotenv` is installed and imported in `server.js`

### 12. React Router Not Working

**Error:** 404 errors or blank pages on navigation

**Solutions:**
- ✅ Ensure `react-router-dom` is installed
- ✅ Check `App.jsx` has `<BrowserRouter>` wrapper
- ✅ Verify all page components are imported
- ✅ Check route paths match sidebar links

## Quick Diagnostic Commands

### Check if ports are in use:
```bash
# Linux/Mac
lsof -i :3000
lsof -i :5000

# Windows
netstat -ano | findstr :3000
netstat -ano | findstr :5000
```

### Test backend API:
```bash
curl http://localhost:5000/api/dashboard/stats
```

### Check MySQL connection:
```bash
mysql -u root -p
USE ecommerce_admin;
SHOW TABLES;
SELECT COUNT(*) FROM products;
```

### Verify Node/npm versions:
```bash
node --version  # Should be v16+
npm --version
```

## Still Having Issues?

1. **Check all services are running:**
   - XAMPP MySQL: Green in control panel
   - Backend: Terminal shows "Server running on..."
   - Frontend: Terminal shows "Local: http://localhost:3000"

2. **Restart everything:**
   ```bash
   # Stop all processes (Ctrl+C in terminals)
   # Restart XAMPP MySQL
   # Run: npm run dev
   ```

3. **Check file structure:**
   - Ensure all files from PROJECT_SUMMARY.md exist
   - Verify no typos in file names
   - Check imports match actual file locations

4. **Browser DevTools (F12):**
   - Console tab: JavaScript errors
   - Network tab: API call failures
   - Application tab: Check if running on correct port

## Need More Help?

- Review README.md for complete setup instructions
- Check QUICKSTART.md for step-by-step guide
- Review PROJECT_SUMMARY.md for architecture overview
- Ensure all prerequisites are installed (Node.js, XAMPP)

## Success Checklist

Before reporting issues, verify:
- [ ] XAMPP MySQL is running
- [ ] Database `ecommerce_admin` exists
- [ ] Tables have data (check phpMyAdmin)
- [ ] Backend server is running (port 5000)
- [ ] Frontend dev server is running (port 3000)
- [ ] No errors in terminal windows
- [ ] Browser console shows no errors
- [ ] API calls return data (check Network tab)
