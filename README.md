# AM Fashions - E-Commerce Platform

A full-stack e-commerce platform with customer-facing website and admin dashboard.

![AM Fashions](public/desktop.PNG)

## 🚀 Features

### Customer Website
- 🛍️ Product browsing and filtering
- 🛒 Shopping cart functionality
- 📦 Order placement with COD
- 📱 Responsive design
- 💬 AI Chatbot support
- 🎨 Modern UI with smooth animations

### Admin Dashboard
- 📊 Sales analytics and statistics
- 📦 Order management
- 👥 Customer management
- 🏷️ Product management
- 💰 Coupon management
- 📈 Real-time dashboard

## 🏗️ Tech Stack

### Frontend
- **React** - UI library
- **React Router** - Navigation
- **Tailwind CSS** - Styling
- **Axios** - API calls

### Backend
- **Node.js** - Runtime
- **Express** - Web framework
- **MySQL** - Database
- **mysql2** - Database driver

### Development
- **Vite** - Build tool (Admin)
- **Create React App** - Build tool (Website)
- **Nodemon** - Development server

## 📁 Project Structure

```
am-fashions/
├── src/                          # Main website source
│   ├── components/              # React components
│   ├── pages/                   # Page components
│   ├── services/                # API services
│   └── data/                    # Product data
├── admin-dashboard/
│   ├── client/                  # Admin dashboard frontend
│   │   └── src/
│   ├── server/                  # Backend API
│   │   ├── controllers/        # Route controllers
│   │   ├── routes/             # API routes
│   │   ├── config/             # Configuration
│   │   └── middleware/         # Middleware
│   └── database/               # Database schemas
├── public/                      # Static assets
└── docs/                        # Documentation
```

## 🚀 Quick Start

### Prerequisites
- Node.js 16+ installed
- MySQL 8+ installed
- Git installed

### 1. Clone Repository
```bash
git clone https://github.com/am-fashions/am-fashions.git
cd am-fashions
```

### 2. Setup Database
```bash
# Login to MySQL
mysql -u root -p

# Create database and import schema
source admin-dashboard/database/schema.sql

# IMPORTANT: Run this migration
ALTER TABLE order_items DROP FOREIGN KEY order_items_ibfk_2;
ALTER TABLE order_items MODIFY COLUMN product_id INT NULL;
ALTER TABLE order_items ADD CONSTRAINT order_items_ibfk_2 
FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE SET NULL;
```

### 3. Setup Backend
```bash
cd admin-dashboard/server
npm install
cp .env.example .env
# Edit .env with your database credentials
npm run dev
```

### 4. Setup Main Website
```bash
# In a new terminal, from project root
npm install
npm start
```

### 5. Setup Admin Dashboard
```bash
# In a new terminal
cd admin-dashboard/client
npm install
npm run dev
```

### 6. Access Applications
- **Main Website:** http://localhost:3002
- **Admin Dashboard:** http://localhost:3001
- **Backend API:** http://localhost:5000

## 📖 Documentation

- **[START_HERE.md](START_HERE.md)** - Quick start guide
- **[STEP_BY_STEP_GUIDE.md](STEP_BY_STEP_GUIDE.md)** - Detailed setup instructions
- **[PRODUCTION_DEPLOYMENT_GUIDE.md](PRODUCTION_DEPLOYMENT_GUIDE.md)** - Production deployment
- **[DEPLOYMENT_CHECKLIST.md](DEPLOYMENT_CHECKLIST.md)** - Deployment checklist
- **[SYSTEM_ARCHITECTURE.md](SYSTEM_ARCHITECTURE.md)** - System architecture
- **[ADMIN_CREDENTIALS.md](ADMIN_CREDENTIALS.md)** - Admin login details

## 🔧 Configuration

### Backend Environment Variables
```env
PORT=5000
NODE_ENV=development
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=
DB_NAME=ecommerce_admin
DB_PORT=3306
FRONTEND_URL=http://localhost:3000
ADMIN_URL=http://localhost:3001
JWT_SECRET=your_secret_key
```

### Frontend Environment Variables
```env
REACT_APP_API_URL=http://localhost:5000/api
```

## 🗄️ Database Schema

The application uses MySQL with the following main tables:
- `customers` - Customer information
- `orders` - Order records
- `order_items` - Order line items
- `products` - Product catalog
- `product_variants` - Product variations
- `payments` - Payment records
- `coupons` - Discount coupons
- `admins` - Admin users

## 🔐 Security Features

- ✅ SQL injection protection (prepared statements)
- ✅ CORS configuration
- ✅ Environment variable usage
- ✅ Password hashing ready
- ✅ Input validation
- ✅ Error handling

## 🚀 Deployment

### Recommended Hosting
- **Frontend:** Vercel, Netlify, or shared hosting
- **Backend:** Railway, Render, or VPS
- **Database:** Railway, PlanetScale, or managed MySQL

### Quick Deploy with Railway + Vercel
1. Deploy backend to Railway (includes MySQL)
2. Deploy frontend to Vercel
3. Deploy admin to Vercel
4. Update environment variables
5. Run database migration

See [PRODUCTION_DEPLOYMENT_GUIDE.md](PRODUCTION_DEPLOYMENT_GUIDE.md) for detailed instructions.

## 📊 API Endpoints

### Customers
- `GET /api/customers` - Get all customers
- `GET /api/customers/:id` - Get customer by ID
- `GET /api/customers/email/:email` - Get customer by email
- `POST /api/customers` - Create customer

### Orders
- `GET /api/orders` - Get all orders
- `GET /api/orders/:id` - Get order by ID
- `POST /api/orders` - Create order
- `PATCH /api/orders/:id/status` - Update order status

### Products
- `GET /api/products` - Get all products
- `GET /api/products/:id` - Get product by ID

### Dashboard
- `GET /api/dashboard/stats` - Get dashboard statistics
- `GET /api/dashboard/recent-orders` - Get recent orders

## 🧪 Testing

### Test Order Flow
1. Visit http://localhost:3002
2. Add products to cart
3. Fill delivery address
4. Place order
5. Check admin dashboard for order

### Test Admin Dashboard
1. Visit http://localhost:3001
2. Login with admin credentials
3. View orders, customers, products
4. Update order status

## 🐛 Troubleshooting

### Orders Failing?
- Ensure database migration is run (product_id nullable)
- Check backend logs for errors
- Verify database connection

### CORS Errors?
- Update FRONTEND_URL and ADMIN_URL in backend .env
- Restart backend server

### Database Connection Failed?
- Verify MySQL is running
- Check database credentials in .env
- Ensure database exists

See [TROUBLESHOOTING.md](admin-dashboard/TROUBLESHOOTING.md) for more solutions.

## 📝 Recent Updates

- ✅ Fixed order placement (product_id nullable)
- ✅ Added phone number validation
- ✅ Improved error handling
- ✅ Added production deployment guides
- ✅ Enhanced admin dashboard
- ✅ Added comprehensive documentation

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📄 License

This project is private and proprietary.

## 👥 Team

- **Developer:** AM Fashions Team
- **Contact:** [Your Contact Information]

## 🙏 Acknowledgments

- React community
- Express.js team
- MySQL team
- All open-source contributors

## 📞 Support

For support, email [your-email] or open an issue in the repository.

---

**Made with ❤️ by AM Fashions Team**
