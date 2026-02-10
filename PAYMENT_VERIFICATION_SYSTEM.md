# Payment Verification System - AM Fashions

## Overview
This document explains the complete payment verification system implemented for AM Fashions e-commerce platform.

## System Flow

### 1. Customer Places Order
- Customer adds items to cart
- Fills in delivery address details
- Clicks "Proceed to Payment"

### 2. Payment Modal Opens
**Step 1: QR Code Payment**
- Customer sees UPI QR code
- Payment instructions displayed
- Customer scans QR code with UPI app (GPay, PhonePe, Paytm)
- Customer completes payment
- Customer clicks "I've Completed Payment"

**Step 2: Submit Payment Details**
- Customer enters 12-digit transaction ID
- Selects payment method (UPI/Bank Transfer/Other)
- Uploads payment screenshot
- Clicks "Submit"

**Step 3: Confirmation**
- Order placed successfully
- Status: "Pending Verification"
- Customer receives order number

### 3. Database Storage
When customer submits payment:
1. **Order created** with status: `pending`, payment_status: `pending_verification`
2. **Payment record created** with status: `pending_verification`
3. **Payment verification record created** with:
   - Transaction ID
   - Screenshot file path
   - Customer details
   - Verification status: `pending`
4. **Verification log created** tracking the submission

### 4. Admin Verification
Admin dashboard shows pending verifications with:
- Order number
- Customer name and email
- Transaction ID
- Payment amount
- Screenshot (viewable)
- Days pending

Admin can:
- **Verify Payment**: Marks payment as successful, order status changes to "confirmed"
- **Reject Payment**: Marks payment as failed with rejection reason

### 5. Payment Success
Only after admin verification:
- Order payment_status: `pending_verification` → `paid`
- Order order_status: `pending` → `confirmed`
- Payment status: `pending_verification` → `success`
- Customer receives confirmation email
- Order processing begins

## Database Schema

### Tables Created

#### 1. `payment_verifications`
Stores all payment verification submissions
```sql
- verification_id (PK)
- order_id (FK)
- payment_id (FK)
- transaction_id
- payment_method
- payment_amount
- screenshot_path
- screenshot_filename
- verification_status (pending/verified/rejected/under_review)
- customer_name, customer_email, customer_phone
- verified_by (admin_id)
- verified_at
- rejection_reason
- admin_notes
- submitted_at
```

#### 2. `payment_verification_logs`
Audit trail of all verification actions
```sql
- log_id (PK)
- verification_id (FK)
- action (submitted/verified/rejected/under_review)
- performed_by (admin_id)
- performed_by_type (customer/admin/system)
- notes
- created_at
```

### Stored Procedures

#### 1. `sp_submit_payment_verification`
Called when customer submits payment details
- Creates verification record
- Updates order and payment status to `pending_verification`
- Logs the submission

#### 2. `sp_verify_payment`
Called when admin verifies payment
- Updates verification status to `verified`
- Updates order status to `confirmed` and payment_status to `paid`
- Updates payment status to `success`
- Logs the verification

#### 3. `sp_reject_payment`
Called when admin rejects payment
- Updates verification status to `rejected`
- Updates order and payment status to `failed`
- Logs the rejection with reason

### Views

#### 1. `vw_pending_verifications`
Shows all pending payment verifications for admin dashboard

#### 2. `vw_all_verifications`
Shows complete verification history with all statuses

## API Endpoints

### Customer Endpoints

#### POST `/api/payment-verification/submit`
Submit payment verification with screenshot
```javascript
FormData:
- order_id
- transaction_id
- payment_method
- payment_amount
- customer_name
- customer_email
- customer_phone
- screenshot (file)
```

#### GET `/api/payment-verification/order/:orderId`
Get verification status for an order

### Admin Endpoints

#### GET `/api/payment-verification/pending`
Get all pending verifications

#### GET `/api/payment-verification/all`
Get all verifications (with optional status filter)

#### GET `/api/payment-verification/:id`
Get specific verification details with logs

#### POST `/api/payment-verification/:id/verify`
Verify a payment (admin action)
```json
{
  "admin_id": 1,
  "admin_notes": "Payment verified successfully"
}
```

#### POST `/api/payment-verification/:id/reject`
Reject a payment (admin action)
```json
{
  "admin_id": 1,
  "rejection_reason": "Invalid transaction ID"
}
```

#### GET `/api/payment-verification/stats`
Get verification statistics

#### GET `/api/payment-verification/screenshot/:filename`
Serve payment screenshot image

## File Structure

```
am-fashions-main/
├── admin-dashboard/
│   ├── database/
│   │   ├── complete_setup.sql          # Complete database setup
│   │   ├── payment_verification.sql    # Payment verification schema
│   │   ├── schema.sql                  # Base schema
│   │   └── seed.sql                    # Sample data
│   └── server/
│       ├── controllers/
│       │   └── paymentVerificationController.js
│       ├── routes/
│       │   └── paymentVerification.js
│       └── uploads/
│           └── payment-screenshots/    # Screenshot storage
└── src/
    └── pages/
        ├── Cart.jsx                    # Updated with payment flow
        └── Payment.jsx                 # Payment modal component
```

## Setup Instructions

### 1. Database Setup
Run the complete setup SQL file:
```bash
mysql -u root -p < admin-dashboard/database/complete_setup.sql
```

Or run individual files in order:
```bash
mysql -u root -p < admin-dashboard/database/schema.sql
mysql -u root -p < admin-dashboard/database/payment_verification.sql
mysql -u root -p < admin-dashboard/database/seed.sql
```

### 2. Install Dependencies
```bash
# Install multer for file uploads
cd admin-dashboard/server
npm install multer
```

### 3. Create Upload Directory
The system automatically creates `uploads/payment-screenshots/` directory when first file is uploaded.

### 4. Environment Variables
Ensure your `.env` file has:
```env
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=your_password
DB_NAME=ecommerce_admin
PORT=5000
```

### 5. Start Servers
```bash
# Start backend API
cd admin-dashboard/server
npm run dev

# Start main website
cd ../..
npm start
```

## Payment Status Flow

```
Customer submits payment
         ↓
Order Status: pending
Payment Status: pending_verification
         ↓
Admin reviews verification
         ↓
    ┌────────┴────────┐
    ↓                 ↓
VERIFIED          REJECTED
    ↓                 ↓
Order: confirmed  Order: pending
Payment: paid     Payment: failed
    ↓                 ↓
Processing        Customer notified
begins            to retry payment
```

## Security Considerations

1. **File Upload Security**
   - Only image files allowed (jpeg, jpg, png, gif, webp)
   - 5MB file size limit
   - Files stored outside public directory
   - Unique filenames generated

2. **Admin Authentication**
   - Admin endpoints should be protected with authentication middleware
   - Only verified admins can approve/reject payments

3. **Data Validation**
   - Transaction ID format validation
   - Amount verification against order total
   - Screenshot required for verification

## Testing

### Test Payment Flow
1. Add items to cart
2. Fill address form
3. Click "Proceed to Payment"
4. View QR code (test image)
5. Enter test transaction ID: `TEST123456789012`
6. Upload a test screenshot
7. Submit payment
8. Check database for verification record

### Verify in Database
```sql
-- Check pending verifications
SELECT * FROM vw_pending_verifications;

-- Check verification logs
SELECT * FROM payment_verification_logs WHERE verification_id = 1;

-- Check order status
SELECT order_number, order_status, payment_status 
FROM orders WHERE order_id = 1;
```

## Admin Dashboard Integration

To integrate with admin dashboard, create a new page:

**Pending Verifications Page**
- List all pending verifications
- Show screenshot preview
- Verify/Reject buttons
- Filter by date, amount, status
- Search by transaction ID or customer

## Future Enhancements

1. **Email Notifications**
   - Send email when verification is pending
   - Send confirmation email when verified
   - Send rejection email with reason

2. **Automatic Verification**
   - Integration with payment gateway APIs
   - Automatic transaction ID verification
   - OCR for screenshot validation

3. **Customer Portal**
   - Track verification status
   - Resubmit if rejected
   - View verification history

4. **Analytics**
   - Verification time metrics
   - Rejection rate analysis
   - Payment method statistics

## Support

For issues or questions:
- Check database logs: `SELECT * FROM payment_verification_logs`
- Check server logs for API errors
- Verify file upload permissions
- Ensure database procedures are created

## Conclusion

This payment verification system ensures:
- ✅ Secure payment processing
- ✅ Admin control over payment approval
- ✅ Complete audit trail
- ✅ Customer transparency
- ✅ Fraud prevention
- ✅ Database integrity

Payment is only marked successful after admin verification, ensuring complete control over the order fulfillment process.
