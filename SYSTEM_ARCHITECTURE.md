# System Architecture - Integrated E-Commerce Platform

## Overview

Your e-commerce platform consists of three main components working together through a shared backend API and database.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────┐
│                         USER INTERFACES                              │
├─────────────────────────────────┬───────────────────────────────────┤
│                                 │                                   │
│  MAIN WEBSITE (Port 3000)       │  ADMIN DASHBOARD (Port 3001)      │
│  ─────────────────────────      │  ────────────────────────────     │
│                                 │                                   │
│  Customer Features:             │  Admin Features:                  │
│  • Browse Products              │  • Dashboard Overview             │
│  • Shopping Cart                │  • Order Management               │
│  • Checkout                     │  • Product Management             │
│  • Order Tracking               │  • Customer Insights              │
│  • Apply Coupons                │  • Inventory Tracking             │
│                                 │  • Sales Analytics                │
│  Technology:                    │  • Coupon Management              │
│  • React 18                     │  • Returns & Refunds              │
│  • React Router                 │                                   │
│  • Tailwind CSS                 │  Technology:                      │
│  • Axios                        │  • React 18                       │
│                                 │  • React Router                   │
│                                 │  • Tailwind CSS                   │
│                                 │  • Recharts (Analytics)           │
│                                 │  • Axios                          │
│                                 │                                   │
└─────────────┬───────────────────┴───────────────┬───────────────────┘
              │                                   │
              │         HTTP Requests             │
              │         (REST API)                │
              │                                   │
              └───────────────┬───────────────────┘
                              │
                              ▼
              ┌───────────────────────────────────┐
              │   BACKEND API (Port 5000)         │
              │   ─────────────────────────       │
              │                                   │
              │   Technology Stack:               │
              │   • Node.js                       │
              │   • Express.js                    │
              │   • MySQL2                        │
              │   • CORS                          │
              │   • dotenv                        │
              │                                   │
              │   API Endpoints:                  │
              │   • /api/products                 │
              │   • /api/orders                   │
              │   • /api/customers                │
              │   • /api/dashboard                │
              │   • /api/analytics                │
              │   • /api/coupons                  │
              │                                   │
              │   Features:                       │
              │   • RESTful API                   │
              │   • Connection Pooling            │
              │   • Error Handling                │
              │   • Request Logging               │
              │   • CORS Support                  │
              │                                   │
              └───────────────┬───────────────────┘
                              │
                              │ SQL Queries
                              │
                              ▼
              ┌───────────────────────────────────┐
              │   MySQL DATABASE (XAMPP)          │
              │   ────────────────────────        │
              │                                   │
              │   Database: ecommerce_admin       │
              │                                   │
              │   Tables:                         │
              │   ┌─────────────────────────┐     │
              │   │ • admins                │     │
              │   │ • customers             │     │
              │   │ • products              │     │
              │   │ • product_variants      │     │
              │   │ • orders                │     │
              │   │ • order_items           │     │
              │   │ • payments              │     │
              │   │ • coupons               │     │
              │   │ • returns               │     │
              │   └─────────────────────────┘     │
              │                                   │
              │   Features:                       │
              │   • Relational Structure          │
              │   • Foreign Keys                  │
              │   • Indexes for Performance       │
              │   • Timestamps                    │
              │   • Transaction Support           │
              │                                   │
              └───────────────────────────────────┘
```

## Data Flow Examples

### Example 1: Customer Places Order

```
1. Customer (Main Website)
   └─► Adds products to cart
   └─► Proceeds to checkout
   └─► Fills in details
   └─► Clicks "Place Order"
        │
        ▼
2. Frontend (React)
   └─► Validates form data
   └─► Calls orderAPI.createOrder()
        │
        ▼
3. API Request
   └─► POST http://localhost:5000/api/orders
   └─► Body: { customer_info, items, total, etc. }
        │
        ▼
4. Backend (Express)
   └─► Receives request
   └─► Validates data
   └─► Starts database transaction
        │
        ▼
5. Database Operations
   └─► INSERT into customers (if new)
   └─► INSERT into orders
   └─► INSERT into order_items (for each product)
   └─► INSERT into payments
   └─► UPDATE product_variants (reduce stock)
   └─► COMMIT transaction
        │
        ▼
6. Response
   └─► Returns order details with order_id
        │
        ▼
7. Frontend Updates
   └─► Shows order confirmation
   └─► Displays order number
   └─► Clears cart
        │
        ▼
8. Admin Dashboard
   └─► Order appears in "Orders" page
   └─► Dashboard stats update
   └─► Notification appears
```

### Example 2: Admin Updates Product

```
1. Admin (Admin Dashboard)
   └─► Goes to Products page
   └─► Clicks "Edit" on a product
   └─► Changes price from $50 to $45
   └─► Clicks "Save"
        │
        ▼
2. Frontend (React)
   └─► Validates new price
   └─► Calls productAPI.updateProduct()
        │
        ▼
3. API Request
   └─► PUT http://localhost:5000/api/products/123
   └─► Body: { base_price: 45.00 }
        │
        ▼
4. Backend (Express)
   └─► Receives request
   └─► Validates product_id exists
   └─► Validates price is valid
        │
        ▼
5. Database Operation
   └─► UPDATE products SET base_price = 45.00 WHERE product_id = 123
        │
        ▼
6. Response
   └─► Returns updated product data
        │
        ▼
7. Admin Dashboard
   └─► Shows success message
   └─► Updates product list
        │
        ▼
8. Main Website
   └─► Next page load shows new price
   └─► Product detail page reflects change
```

### Example 3: Customer Applies Coupon

```
1. Customer (Main Website)
   └─► At checkout page
   └─► Enters coupon code "SAVE20"
   └─► Clicks "Apply"
        │
        ▼
2. Frontend (React)
   └─► Calls couponAPI.validateCoupon()
        │
        ▼
3. API Request
   └─► POST http://localhost:5000/api/coupons/validate
   └─► Body: { coupon_code: "SAVE20", order_amount: 100 }
        │
        ▼
4. Backend (Express)
   └─► Queries database for coupon
   └─► Checks if coupon is active
   └─► Checks if not expired
   └─► Checks minimum order value
   └─► Calculates discount
        │
        ▼
5. Database Query
   └─► SELECT * FROM coupons WHERE coupon_code = 'SAVE20'
        │
        ▼
6. Response
   └─► Returns: { valid: true, discount: 20, final_amount: 80 }
        │
        ▼
7. Frontend Updates
   └─► Shows discount applied
   └─► Updates order total
   └─► Displays savings amount
```

## Component Communication

### Main Website → Backend
- Product browsing: `GET /api/products`
- Product details: `GET /api/products/:id`
- Create order: `POST /api/orders`
- Validate coupon: `POST /api/coupons/validate`
- Customer orders: `GET /api/orders/customer/:email`

### Admin Dashboard → Backend
- Dashboard stats: `GET /api/dashboard/stats`
- All orders: `GET /api/orders`
- Update order status: `PUT /api/orders/:id/status`
- Product CRUD: `GET/POST/PUT/DELETE /api/products`
- Customer list: `GET /api/customers`
- Analytics: `GET /api/analytics/sales`
- Coupon management: `GET/POST/DELETE /api/coupons`

## Database Relationships

```
customers (1) ──────────────► (M) orders
                                  │
                                  │ (1)
                                  │
                                  ▼
                              (M) order_items
                                  │
                                  │ (M)
                                  │
                                  ▼
products (1) ───────────────► (M) product_variants
    │
    │ (1)
    │
    ▼
(M) order_items

orders (1) ──────────────────► (1) payments

orders (1) ──────────────────► (M) returns

Legend:
(1) = One
(M) = Many
```

## Technology Stack Summary

### Frontend Technologies
| Component | Technology | Purpose |
|-----------|-----------|---------|
| Main Website | React 18 | UI Framework |
| Admin Dashboard | React 18 | UI Framework |
| Routing | React Router v6 | Navigation |
| Styling | Tailwind CSS | CSS Framework |
| HTTP Client | Axios | API Requests |
| Charts | Recharts | Data Visualization (Admin) |
| Icons | Lucide React | Icon Library |

### Backend Technologies
| Component | Technology | Purpose |
|-----------|-----------|---------|
| Runtime | Node.js | JavaScript Runtime |
| Framework | Express.js | Web Framework |
| Database Driver | MySQL2 | Database Connection |
| CORS | cors | Cross-Origin Support |
| Environment | dotenv | Config Management |

### Database
| Component | Technology | Purpose |
|-----------|-----------|---------|
| DBMS | MySQL | Relational Database |
| Server | XAMPP | Local Development |
| Charset | utf8mb4 | Unicode Support |

## Port Configuration

| Service | Port | URL |
|---------|------|-----|
| Main Website | 3000 | http://localhost:3000 |
| Admin Dashboard | 3001 | http://localhost:3001 |
| Backend API | 5000 | http://localhost:5000 |
| MySQL | 3306 | localhost:3306 |
| phpMyAdmin | 80 | http://localhost/phpmyadmin |

## Security Considerations

### Current Setup (Development)
- ⚠️ No authentication on API endpoints
- ⚠️ CORS allows multiple origins
- ⚠️ Database has no password
- ⚠️ All endpoints are public

### Production Requirements
- ✅ Add JWT authentication
- ✅ Implement role-based access control
- ✅ Use environment-specific CORS
- ✅ Set strong database password
- ✅ Enable HTTPS
- ✅ Add rate limiting
- ✅ Implement input validation
- ✅ Add API key authentication
- ✅ Enable SQL injection protection (prepared statements)
- ✅ Add request logging and monitoring

## Scalability Considerations

### Current Limitations
- Single server instance
- No caching layer
- No load balancing
- Local file storage
- Synchronous processing

### Future Enhancements
- Add Redis for caching
- Implement CDN for static assets
- Use cloud storage for images
- Add message queue for async tasks
- Implement horizontal scaling
- Add database replication
- Use containerization (Docker)
- Implement microservices architecture

## Deployment Architecture (Future)

```
                    ┌─────────────┐
                    │   CDN       │
                    │  (Static)   │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │ Load        │
                    │ Balancer    │
                    └──────┬──────┘
                           │
        ┏━━━━━━━━━━━━━━━━━┻━━━━━━━━━━━━━━━━━┓
        ▼                                    ▼
┌───────────────┐                   ┌───────────────┐
│  Web Server 1 │                   │  Web Server 2 │
│  (Frontend)   │                   │  (Frontend)   │
└───────┬───────┘                   └───────┬───────┘
        │                                   │
        └───────────────┬───────────────────┘
                        │
                ┌───────▼────────┐
                │  API Gateway   │
                └───────┬────────┘
                        │
        ┏━━━━━━━━━━━━━━━┻━━━━━━━━━━━━━━━┓
        ▼                               ▼
┌───────────────┐              ┌───────────────┐
│  API Server 1 │              │  API Server 2 │
│  (Backend)    │              │  (Backend)    │
└───────┬───────┘              └───────┬───────┘
        │                              │
        └──────────────┬───────────────┘
                       │
        ┏━━━━━━━━━━━━━━┻━━━━━━━━━━━━━━┓
        ▼                              ▼
┌───────────────┐             ┌───────────────┐
│  DB Primary   │────────────►│  DB Replica   │
│  (Master)     │             │  (Read Only)  │
└───────────────┘             └───────────────┘
        │
        ▼
┌───────────────┐
│  Redis Cache  │
└───────────────┘
```

## Monitoring & Logging

### Recommended Tools
- **Application Monitoring**: New Relic, Datadog
- **Error Tracking**: Sentry
- **Log Management**: ELK Stack (Elasticsearch, Logstash, Kibana)
- **Uptime Monitoring**: Pingdom, UptimeRobot
- **Performance**: Google Analytics, Hotjar

## Backup Strategy

### Database Backups
- Daily automated backups
- Weekly full backups
- Monthly archives
- Off-site storage

### Code Backups
- Git version control
- GitHub/GitLab repository
- Automated deployments
- Rollback capability

---

This architecture provides a solid foundation for your e-commerce platform with room for growth and scalability.
