# Quick Start Guide

## Step-by-Step Setup

### 1. Database Setup (XAMPP)

1. **Start XAMPP Control Panel**
   - Start Apache and MySQL services

2. **Open phpMyAdmin**
   - Go to: http://localhost/phpmyadmin

3. **Create Database**
   - Click "SQL" tab
   - Copy entire content from `database/schema.sql`
   - Click "Go"

4. **Add Sample Data**
   - Click "SQL" tab again
   - Copy entire content from `database/seed.sql`
   - Click "Go"

### 2. Start the Application

**Option A: Run Everything at Once**
```bash
npm run dev
```

**Option B: Run Separately**

Terminal 1 (Backend):
```bash
cd server
npm run dev
```

Terminal 2 (Frontend):
```bash
cd client
npm run dev
```

### 3. Access the Dashboard

Open your browser and go to:
```
http://localhost:3000
```

The backend API runs on:
```
http://localhost:5000
```

## What You'll See

✅ Dashboard with KPIs (Revenue, Orders, Customers, Alerts)
✅ Recent orders table
✅ Sidebar navigation with all modules
✅ Fully functional UI with sample data

## Test the Features

1. **Dashboard** - View statistics and recent orders
2. **Orders** - Change order status from dropdown
3. **Products** - View all products, see low stock alerts
4. **Inventory** - Check stock levels
5. **Customers** - View customer list with purchase history
6. **Analytics** - See sales charts (monthly revenue, top products)
7. **Coupons** - View active discount coupons

## Sample Data Included

- 5 Customers
- 10 Products (clothing items)
- 7 Orders with different statuses
- 5 Active coupons
- Product variants (sizes, colors)

## Troubleshooting

**Can't connect to database?**
- Make sure MySQL is running in XAMPP
- Check `server/.env` file has correct credentials

**Port 3000 already in use?**
- Stop other React apps or change port in `client/vite.config.js`

**Port 5000 already in use?**
- Change PORT in `server/.env`

**Blank page or errors?**
- Check browser console (F12)
- Make sure backend is running
- Verify database has data

## Next Steps

- Customize the UI colors and branding
- Add authentication/login system
- Implement image upload for products
- Add more analytics charts
- Create email notifications
- Add export to CSV/PDF features

Enjoy your admin dashboard! 🚀
