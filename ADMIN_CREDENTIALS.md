# Admin Dashboard Credentials

## 🔐 Admin Login Information

### Default Admin Account

```
Username: admin
Email: admin@shop.com
Password: admin123
Role: Super Admin
```

### Manager Account

```
Username: manager
Email: manager@shop.com
Password: admin123
Role: Admin
```

## 📝 Important Notes

### Current Setup (Development)

⚠️ **The admin dashboard currently does NOT have authentication enabled.**

This means:
- You can access the dashboard directly at http://localhost:3001
- No login page is shown
- No password is required
- Anyone can access all features

### For Production

Before deploying to production, you MUST:

1. **Implement Authentication**
   - Add login page
   - Verify credentials against database
   - Use JWT tokens for session management
   - Protect all admin routes

2. **Change Default Passwords**
   - Never use "admin123" in production
   - Use strong, unique passwords
   - Hash passwords with bcrypt (already set up in schema)

3. **Add Authorization**
   - Implement role-based access control
   - Super Admin: Full access
   - Admin: Limited access
   - Moderator: View-only access

## 🔒 Password Hashing

The database uses bcrypt for password hashing:

```javascript
const bcrypt = require('bcryptjs');

// Hash a password
const hashedPassword = await bcrypt.hash('admin123', 10);

// Verify a password
const isValid = await bcrypt.compare('admin123', hashedPassword);
```

## 👥 Sample Customer Accounts

For testing the customer-facing website:

### Customer 1
```
Email: john.doe@example.com
Password: customer123
Name: John Doe
Phone: +919876543210
```

### Customer 2
```
Email: jane.smith@example.com
Password: customer123
Name: Jane Smith
Phone: +919876543211
```

### Customer 3
```
Email: mike.johnson@example.com
Password: customer123
Name: Mike Johnson
Phone: +919876543212
```

### Customer 4
```
Email: sarah.williams@example.com
Password: customer123
Name: Sarah Williams
Phone: +919876543213
```

### Customer 5
```
Email: david.brown@example.com
Password: customer123
Name: David Brown
Phone: +919876543214
```

## 🚀 How to Access Admin Dashboard

### Current Setup (No Authentication)

1. Start the admin dashboard:
   ```bash
   cd admin-dashboard/client
   npm run dev
   ```

2. Open browser and go to:
   ```
   http://localhost:3001
   ```

3. You'll see the dashboard directly (no login required)

### Future Setup (With Authentication)

When authentication is implemented:

1. Go to: http://localhost:3001/login
2. Enter username: `admin`
3. Enter password: `admin123`
4. Click "Login"
5. You'll be redirected to the dashboard

## 🛠️ Implementing Authentication (For Developers)

### Step 1: Create Login Page

Create `admin-dashboard/client/src/pages/Login.jsx`:

```jsx
import { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import api from '../services/api';

function Login() {
  const [credentials, setCredentials] = useState({ email: '', password: '' });
  const [error, setError] = useState('');
  const navigate = useNavigate();

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const response = await api.post('/auth/login', credentials);
      localStorage.setItem('token', response.data.token);
      localStorage.setItem('user', JSON.stringify(response.data.user));
      navigate('/dashboard');
    } catch (err) {
      setError('Invalid credentials');
    }
  };

  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-100">
      <div className="bg-white p-8 rounded-lg shadow-md w-96">
        <h2 className="text-2xl font-bold mb-6">Admin Login</h2>
        {error && <div className="bg-red-100 text-red-700 p-3 rounded mb-4">{error}</div>}
        <form onSubmit={handleSubmit}>
          <div className="mb-4">
            <label className="block text-gray-700 mb-2">Email</label>
            <input
              type="email"
              className="w-full px-3 py-2 border rounded"
              value={credentials.email}
              onChange={(e) => setCredentials({...credentials, email: e.target.value})}
              required
            />
          </div>
          <div className="mb-6">
            <label className="block text-gray-700 mb-2">Password</label>
            <input
              type="password"
              className="w-full px-3 py-2 border rounded"
              value={credentials.password}
              onChange={(e) => setCredentials({...credentials, password: e.target.value})}
              required
            />
          </div>
          <button
            type="submit"
            className="w-full bg-blue-600 text-white py-2 rounded hover:bg-blue-700"
          >
            Login
          </button>
        </form>
      </div>
    </div>
  );
}

export default Login;
```

### Step 2: Create Auth Controller

Create `admin-dashboard/server/controllers/authController.js`:

```javascript
import bcrypt from 'bcryptjs';
import jwt from 'jsonwebtoken';

export const login = async (req, res) => {
  try {
    const { email, password } = req.body;
    const db = req.app.locals.db;

    // Find admin by email
    const [admins] = await db.execute(
      'SELECT * FROM admins WHERE email = ? AND is_active = TRUE',
      [email]
    );

    if (admins.length === 0) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }

    const admin = admins[0];

    // Verify password
    const isValid = await bcrypt.compare(password, admin.password_hash);
    if (!isValid) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }

    // Generate JWT token
    const token = jwt.sign(
      { admin_id: admin.admin_id, email: admin.email, role: admin.role },
      process.env.JWT_SECRET,
      { expiresIn: process.env.JWT_EXPIRES_IN || '7d' }
    );

    // Update last login
    await db.execute(
      'UPDATE admins SET last_login = NOW() WHERE admin_id = ?',
      [admin.admin_id]
    );

    res.json({
      token,
      user: {
        admin_id: admin.admin_id,
        username: admin.username,
        email: admin.email,
        full_name: admin.full_name,
        role: admin.role
      }
    });
  } catch (error) {
    console.error('Login error:', error);
    res.status(500).json({ error: 'Login failed' });
  }
};
```

### Step 3: Create Auth Middleware

Create `admin-dashboard/server/middleware/authMiddleware.js`:

```javascript
import jwt from 'jsonwebtoken';

export const authenticateToken = (req, res, next) => {
  const authHeader = req.headers['authorization'];
  const token = authHeader && authHeader.split(' ')[1];

  if (!token) {
    return res.status(401).json({ error: 'Access token required' });
  }

  jwt.verify(token, process.env.JWT_SECRET, (err, user) => {
    if (err) {
      return res.status(403).json({ error: 'Invalid or expired token' });
    }
    req.user = user;
    next();
  });
};

export const requireRole = (...roles) => {
  return (req, res, next) => {
    if (!req.user || !roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Insufficient permissions' });
    }
    next();
  };
};
```

### Step 4: Protect Routes

Update `admin-dashboard/server/server.js`:

```javascript
import { authenticateToken, requireRole } from './middleware/authMiddleware.js';

// Public routes (no auth required)
app.use('/api/auth', authRoutes);

// Protected routes (auth required)
app.use('/api/dashboard', authenticateToken, dashboardRoutes);
app.use('/api/orders', authenticateToken, orderRoutes);
app.use('/api/products', authenticateToken, productRoutes);
app.use('/api/customers', authenticateToken, requireRole('super_admin', 'admin'), customerRoutes);
```

## 🔐 Security Best Practices

### For Production:

1. **Strong Passwords**
   - Minimum 12 characters
   - Mix of uppercase, lowercase, numbers, symbols
   - No dictionary words

2. **JWT Security**
   - Use strong JWT_SECRET (32+ random characters)
   - Set reasonable expiration (1-7 days)
   - Store tokens securely (httpOnly cookies)

3. **HTTPS Only**
   - Never send credentials over HTTP
   - Use SSL/TLS certificates
   - Enable HSTS headers

4. **Rate Limiting**
   - Limit login attempts (5 per 15 minutes)
   - Block after failed attempts
   - Use CAPTCHA for suspicious activity

5. **Session Management**
   - Implement logout functionality
   - Clear tokens on logout
   - Refresh tokens before expiry

6. **Audit Logging**
   - Log all login attempts
   - Track admin actions
   - Monitor suspicious activity

## 📊 Database Admin Table

The `admins` table structure:

```sql
CREATE TABLE admins (
    admin_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    full_name VARCHAR(100) NOT NULL,
    role ENUM('super_admin', 'admin', 'moderator') DEFAULT 'admin',
    is_active BOOLEAN DEFAULT TRUE,
    last_login DATETIME NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
```

## 🎯 Quick Reference

### Current Access (Development)
- URL: http://localhost:3001
- Authentication: None (direct access)
- All features: Available to everyone

### Future Access (Production)
- URL: https://yourdomain.com/admin
- Authentication: Required
- Login: admin@shop.com / [strong-password]
- Features: Role-based access

## ⚠️ Security Warning

**IMPORTANT**: The current setup is for DEVELOPMENT ONLY!

Never deploy to production without:
- ✅ Implementing authentication
- ✅ Changing default passwords
- ✅ Using HTTPS
- ✅ Adding rate limiting
- ✅ Implementing proper session management
- ✅ Adding audit logging

---

## 📞 Support

For authentication implementation help:
1. Check backend middleware examples above
2. Review JWT documentation
3. Test with Postman before frontend integration
4. Implement logout and token refresh

---

**Remember**: Security is not optional for production systems!
