# Payment Verification System - Flow Diagram

## Complete System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                         CUSTOMER JOURNEY                             │
└─────────────────────────────────────────────────────────────────────┘

1. SHOPPING
   ┌──────────┐
   │ Browse   │
   │ Products │
   └────┬─────┘
        │
        ▼
   ┌──────────┐
   │ Add to   │
   │  Cart    │
   └────┬─────┘
        │
        ▼

2. CHECKOUT
   ┌──────────────┐
   │ Fill Address │
   │   Details    │
   └──────┬───────┘
          │
          ▼
   ┌──────────────┐
   │   Proceed    │
   │ to Payment   │
   └──────┬───────┘
          │
          ▼

3. PAYMENT MODAL (3 Steps)
   
   ┌─────────────────────────────────────────┐
   │ STEP 1: QR Code Payment                 │
   ├─────────────────────────────────────────┤
   │ • Display QR Code                       │
   │ • Show Payment Instructions             │
   │ • Customer scans with UPI app           │
   │ • Customer completes payment            │
   │ • Click "I've Completed Payment"        │
   └──────────────┬──────────────────────────┘
                  │
                  ▼
   ┌─────────────────────────────────────────┐
   │ STEP 2: Submit Payment Details          │
   ├─────────────────────────────────────────┤
   │ • Enter Transaction ID (12 digits)      │
   │ • Select Payment Method (UPI/Bank)      │
   │ • Upload Payment Screenshot             │
   │ • Click "Submit"                        │
   └──────────────┬──────────────────────────┘
                  │
                  ▼
   ┌─────────────────────────────────────────┐
   │ STEP 3: Confirmation                    │
   ├─────────────────────────────────────────┤
   │ • Order Placed Successfully             │
   │ • Order Number: ORD-123456              │
   │ • Status: Pending Verification          │
   │ • Verification within 24-48 hours       │
   └──────────────┬──────────────────────────┘
                  │
                  ▼

┌─────────────────────────────────────────────────────────────────────┐
│                      DATABASE OPERATIONS                             │
└─────────────────────────────────────────────────────────────────────┘

4. ORDER CREATION
   ┌──────────────────────────────────────────┐
   │ POST /api/orders                         │
   ├──────────────────────────────────────────┤
   │ INSERT INTO orders                       │
   │   order_status = 'pending'               │
   │   payment_status = 'pending_verification'│
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ INSERT INTO order_items                  │
   │   (product details, quantities)          │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ INSERT INTO payments                     │
   │   payment_status = 'pending_verification'│
   │   transaction_id = 'TXN123...'           │
   └──────────────┬───────────────────────────┘
                  │
                  ▼

5. VERIFICATION SUBMISSION
   ┌──────────────────────────────────────────┐
   │ POST /api/payment-verification/submit    │
   ├──────────────────────────────────────────┤
   │ • Upload screenshot to server            │
   │ • Save to: uploads/payment-screenshots/  │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ CALL sp_submit_payment_verification()    │
   ├──────────────────────────────────────────┤
   │ INSERT INTO payment_verifications        │
   │   verification_status = 'pending'        │
   │   transaction_id = 'TXN123...'           │
   │   screenshot_path = 'uploads/...'        │
   │   customer_name, email, phone            │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ INSERT INTO payment_verification_logs    │
   │   action = 'submitted'                   │
   │   performed_by_type = 'customer'         │
   └──────────────┬───────────────────────────┘
                  │
                  ▼

┌─────────────────────────────────────────────────────────────────────┐
│                       ADMIN VERIFICATION                             │
└─────────────────────────────────────────────────────────────────────┘

6. ADMIN REVIEWS
   ┌──────────────────────────────────────────┐
   │ GET /api/payment-verification/pending    │
   ├──────────────────────────────────────────┤
   │ SELECT * FROM vw_pending_verifications   │
   │                                          │
   │ Shows:                                   │
   │ • Order Number                           │
   │ • Customer Name                          │
   │ • Transaction ID                         │
   │ • Payment Amount                         │
   │ • Screenshot                             │
   │ • Days Pending                           │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ Admin Decision                           │
   └──────┬───────────────────────────┬───────┘
          │                           │
          ▼                           ▼
   ┌──────────┐              ┌──────────────┐
   │ VERIFY   │              │   REJECT     │
   └────┬─────┘              └──────┬───────┘
        │                           │
        ▼                           ▼

7A. PAYMENT VERIFIED
   ┌──────────────────────────────────────────┐
   │ POST /api/payment-verification/:id/verify│
   ├──────────────────────────────────────────┤
   │ CALL sp_verify_payment()                 │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ UPDATE payment_verifications             │
   │   verification_status = 'verified'       │
   │   verified_by = admin_id                 │
   │   verified_at = NOW()                    │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ UPDATE orders                            │
   │   payment_status = 'paid'                │
   │   order_status = 'confirmed'             │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ UPDATE payments                          │
   │   payment_status = 'success'             │
   │   payment_date = NOW()                   │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ INSERT INTO payment_verification_logs    │
   │   action = 'verified'                    │
   │   performed_by = admin_id                │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ ✅ PAYMENT SUCCESSFUL                    │
   │ Order processing begins                  │
   │ Customer receives confirmation email     │
   └──────────────────────────────────────────┘

7B. PAYMENT REJECTED
   ┌──────────────────────────────────────────┐
   │ POST /api/payment-verification/:id/reject│
   ├──────────────────────────────────────────┤
   │ CALL sp_reject_payment()                 │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ UPDATE payment_verifications             │
   │   verification_status = 'rejected'       │
   │   verified_by = admin_id                 │
   │   rejection_reason = '...'               │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ UPDATE orders                            │
   │   payment_status = 'failed'              │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ UPDATE payments                          │
   │   payment_status = 'failed'              │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ INSERT INTO payment_verification_logs    │
   │   action = 'rejected'                    │
   │   performed_by = admin_id                │
   └──────────────┬───────────────────────────┘
                  │
                  ▼
   ┌──────────────────────────────────────────┐
   │ ❌ PAYMENT FAILED                        │
   │ Customer notified with reason            │
   │ Customer can retry payment               │
   └──────────────────────────────────────────┘
```

## Database Table Relationships

```
┌─────────────┐
│  customers  │
└──────┬──────┘
       │
       │ 1:N
       │
       ▼
┌─────────────┐         ┌──────────────────┐
│   orders    │◄────────┤  order_items     │
└──────┬──────┘   1:N   └──────────────────┘
       │
       │ 1:1
       │
       ▼
┌─────────────┐
│  payments   │
└──────┬──────┘
       │
       │ 1:1
       │
       ▼
┌──────────────────────┐
│ payment_verifications│
└──────┬───────────────┘
       │
       │ 1:N
       │
       ▼
┌──────────────────────────┐
│payment_verification_logs │
└──────────────────────────┘

┌─────────────┐
│   admins    │ (verifies payments)
└──────┬──────┘
       │
       │ N:1
       │
       ▼
┌──────────────────────┐
│ payment_verifications│
└──────────────────────┘
```

## Status Flow Diagram

```
ORDER STATUS FLOW:
pending ──[admin verifies]──> confirmed ──> processing ──> shipped ──> delivered
   │
   └──[admin rejects]──> pending (can retry)

PAYMENT STATUS FLOW:
pending ──[customer submits]──> pending_verification
                                        │
                                        ├──[admin verifies]──> paid ──> success
                                        │
                                        └──[admin rejects]──> failed

VERIFICATION STATUS FLOW:
pending ──[admin action]──┬──> verified
                          │
                          └──> rejected
```

## File Upload Flow

```
Customer uploads screenshot
         │
         ▼
┌────────────────────┐
│ Browser FormData   │
│ screenshot: File   │
└─────────┬──────────┘
          │
          │ HTTP POST
          │
          ▼
┌────────────────────────────┐
│ Backend: multer middleware │
│ Validates file type/size   │
└─────────┬──────────────────┘
          │
          ▼
┌────────────────────────────────────┐
│ Save to disk:                      │
│ uploads/payment-screenshots/       │
│ payment-1234567890-123456789.jpg   │
└─────────┬──────────────────────────┘
          │
          ▼
┌────────────────────────────────────┐
│ Store path in database:            │
│ payment_verifications.screenshot_path│
└─────────┬──────────────────────────┘
          │
          ▼
┌────────────────────────────────────┐
│ Admin views screenshot:            │
│ GET /api/payment-verification/     │
│     screenshot/:filename           │
└────────────────────────────────────┘
```

## API Request/Response Flow

```
CUSTOMER SUBMITS PAYMENT:

Request:
POST /api/payment-verification/submit
Content-Type: multipart/form-data

FormData {
  order_id: 1
  transaction_id: "TEST123456789012"
  payment_method: "upi"
  payment_amount: 999.00
  customer_name: "John Doe"
  customer_email: "john@example.com"
  customer_phone: "9876543210"
  screenshot: [File]
}

Response:
{
  "success": true,
  "message": "Payment verification submitted successfully",
  "data": {
    "verification_id": 1,
    "order_id": 1,
    "transaction_id": "TEST123456789012",
    "status": "pending_verification"
  }
}

─────────────────────────────────────────────────

ADMIN VERIFIES PAYMENT:

Request:
POST /api/payment-verification/1/verify
Content-Type: application/json

{
  "admin_id": 1,
  "admin_notes": "Payment verified successfully"
}

Response:
{
  "success": true,
  "message": "Payment verified successfully"
}

Database Changes:
• payment_verifications.verification_status = 'verified'
• orders.payment_status = 'paid'
• orders.order_status = 'confirmed'
• payments.payment_status = 'success'
• payment_verification_logs: new entry
```

## Security Flow

```
┌──────────────────┐
│ Customer Action  │
└────────┬─────────┘
         │
         ▼
┌──────────────────────────┐
│ Frontend Validation      │
│ • Required fields        │
│ • File type check        │
│ • File size check        │
└────────┬─────────────────┘
         │
         ▼
┌──────────────────────────┐
│ Backend Validation       │
│ • Multer file filter     │
│ • Size limit (5MB)       │
│ • Type: images only      │
└────────┬─────────────────┘
         │
         ▼
┌──────────────────────────┐
│ Database Validation      │
│ • Foreign key checks     │
│ • Transaction integrity  │
│ • Stored procedures      │
└────────┬─────────────────┘
         │
         ▼
┌──────────────────────────┐
│ Admin Verification       │
│ • Manual review          │
│ • Screenshot check       │
│ • Transaction ID verify  │
└────────┬─────────────────┘
         │
         ▼
┌──────────────────────────┐
│ ✅ Payment Approved      │
└──────────────────────────┘
```

## Summary

**Key Points:**
1. Customer submits payment with screenshot
2. Order created with `pending_verification` status
3. Admin reviews and verifies/rejects
4. **Payment only successful after admin verification**
5. Complete audit trail maintained
6. All actions logged in database

**Database Guarantee:**
- Payment status remains `pending_verification` until admin action
- Order cannot proceed without verification
- All changes are transactional (all-or-nothing)
- Complete history preserved in logs

This ensures **complete control** over payment approval process!
