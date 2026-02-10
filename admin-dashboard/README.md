# E-Commerce Admin Dashboard

A full-stack admin dashboard for managing an online clothing store with React, Node.js, Express, and MySQL.

## Features

- **Dashboard Overview** - KPIs, revenue, orders count, low stock alerts
- **Orders Management** - View, update order status, customer info, payment details
- **Products Management** - Add, edit, delete products with inventory tracking
- **Inventory Tracking** - Real-time stock levels and low stock alerts
- **Customer Insights** - Customer list with purchase history
- **Sales Analytics** - Monthly revenue charts and top products
- **Coupons & Offers** - Create and manage discount coupons
- **Returns & Refunds** - Handle return requests
- **Notifications System** - Real-time alerts
- **Settings** - Admin profile and security settings

## Tech Stack

**Frontend:**
- React 18
- React Router
- Tailwind CSS
- Recharts (for analytics)
- Axios
- Lucide React (icons)

**Backend:**
- Node.js
- Express.js
- MySQL
- REST API

## Prerequisites

- Node.js (v16 or higher)
- XAMPP (for MySQL database)
- npm or yarn

## Installation

### 1. Install Dependencies

```bash
# Install root dependencies
npm install

# Install client dependencies
cd client
npm install

# Install server dependencies
cd ../server
npm install
cd ..
```

### 2. Setup Database

1. Start XAMPP and ensure MySQL is running
2. Open phpMyAdmin (http://localhost/phpmyadmin)
3. Import the database schema:
   - Go to SQL tab
   - Copy and paste contents from `database/schema.sql`
   - Click "Go" to execute
4. Import seed data:
   - Copy and paste contents from `database/seed.sql`
   - Click "Go" to execute

### 3. Configure Environment

Edit `server/.env` file if needed:

```env
PORT=5000
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=
DB_NAME=ecommerce_admin
JWT_SECRET=your_jwt_secret_key_here
```

## Running the Application

### Option 1: Run Both (Recommended)

```bash
npm run dev
```

This will start both frontend (port 3000) and backend (port 5000) concurrently.

### Option 2: Run Separately

**Terminal 1 - Backend:**
```bash
npm run server
```

**Terminal 2 - Frontend:**
```bash
npm run client
```

## Access the Application

- **Frontend:** http://localhost:3000
- **Backend API:** http://localhost:5000

## API Endpoints

### Dashboard
- `GET /api/dashboard/stats` - Get dashboard statistics
- `GET /api/dashboard/recent-orders` - Get recent orders

### Orders
- `GET /api/orders` - Get all orders
- `GET /api/orders/:id` - Get order by ID
- `PUT /api/orders/:id/status` - Update order status

### Products
- `GET /api/products` - Get all products
- `GET /api/products/:id` - Get product by ID
- `POST /api/products` - Create new product
- `PUT /api/products/:id` - Update product
- `DELETE /api/products/:id` - Delete product

### Customers
- `GET /api/customers` - Get all customers
- `GET /api/customers/:id` - Get customer by ID

### Analytics
- `GET /api/analytics/sales` - Get sales data
- `GET /api/analytics/top-products` - Get top selling products

### Coupons
- `GET /api/coupons` - Get all coupons
- `POST /api/coupons` - Create new coupon
- `DELETE /api/coupons/:id` - Delete coupon

## Project Structure

```
├── client/                 # React frontend
│   ├── src/
│   │   ├── components/    # Reusable components
│   │   ├── pages/         # Page components
│   │   ├── services/      # API services
│   │   └── App.jsx        # Main app component
│   └── package.json
├── server/                # Node.js backend
│   ├── config/           # Database configuration
│   ├── controllers/      # Route controllers
│   ├── routes/           # API routes
│   ├── server.js         # Express server
│   └── package.json
├── database/             # SQL files
│   ├── schema.sql       # Database schema
│   └── seed.sql         # Sample data
└── README.md

```

## Default Admin Credentials

- Username: `admin`
- Email: `admin@shop.com`
- Password: (hashed in database)

## Features Overview

### Dashboard
- Total revenue with percentage change
- Total orders count
- Total customers
- Low stock alerts
- Recent orders table

### Orders Management
- View all orders with customer details
- Update order status (pending, processing, shipped, completed, cancelled)
- Filter and search orders
- Payment method and status tracking

### Products Management
- Add new products with details
- Edit existing products
- Delete products
- Track stock quantities
- Product categories and SKU management

### Inventory Tracking
- Real-time stock levels
- Low stock alerts (< 10 items)
- Last updated timestamps
- Stock status indicators

### Customer Insights
- Customer list with contact information
- Total orders per customer
- Total amount spent
- Customer registration dates

### Sales Analytics
- Monthly revenue line chart
- Top selling products bar chart
- Visual data representation using Recharts

### Coupons & Offers
- Create discount coupons
- Percentage or fixed amount discounts
- Minimum purchase requirements
- Expiration dates
- Active/inactive status

## Troubleshooting

**Database Connection Error:**
- Ensure XAMPP MySQL is running
- Check database credentials in `server/.env`
- Verify database name exists

**Port Already in Use:**
- Change port in `server/.env` (backend)
- Change port in `client/vite.config.js` (frontend)

**CORS Issues:**
- Ensure backend is running on port 5000
- Check API_URL in `client/src/services/api.js`

## License

MIT License
