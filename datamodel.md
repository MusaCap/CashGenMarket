# Data Model: Marketplace for Cash-Producing Assets

## 1. Users
- **user_id** (PK)
- full_name
- email
- password_hash
- role (buyer, seller, admin, escrow_agent)
- kyc_status (pending, verified, rejected)
- phone_number
- company_name (optional)
- created_at
- updated_at

---

## 2. User_Profile
- **profile_id** (PK)
- user_id (FK → Users.user_id)
- address
- city
- state
- country
- tax_id / business_license
- bank_account_info (encrypted)
- rating (calculated from transactions/reviews)
- profile_photo_url

---

## 3. Assets
- **asset_id** (PK)
- seller_id (FK → Users.user_id)
- type (ATM, vending_machine, laundromat, kiosk, other)
- manufacturer
- model_number
- serial_number
- year_manufactured
- condition (new, good, needs_repair)
- location_address
- location_type (mall, gas_station, school, etc.)
- latitude
- longitude
- created_at
- updated_at

---

## 4. Asset_Performance
- **performance_id** (PK)
- asset_id (FK → Assets.asset_id)
- monthly_cash_flow
- avg_transactions_per_month
- uptime_percentage
- maintenance_cost_per_month
- performance_start_date
- performance_end_date
- supporting_docs_url (bank statements, receipts)

---

## 5. Listings
- **listing_id** (PK)
- asset_id (FK → Assets.asset_id)
- seller_id (FK → Users.user_id)
- title
- description
- price_type (auction, fixed_price)
- buy_now_price
- reserve_price (auction only)
- start_date
- end_date
- status (active, sold, cancelled, expired)
- created_at
- updated_at

---

## 6. Bids
- **bid_id** (PK)
- listing_id (FK → Listings.listing_id)
- buyer_id (FK → Users.user_id)
- bid_amount
- bid_time
- status (active, outbid, won, withdrawn)

---

## 7. Transactions
- **transaction_id** (PK)
- listing_id (FK → Listings.listing_id)
- buyer_id (FK → Users.user_id)
- seller_id (FK → Users.user_id)
- agreed_price
- status (initiated, escrow_pending, escrow_released, completed, disputed, cancelled)
- created_at
- updated_at

---

## 8. Escrow
- **escrow_id** (PK)
- transaction_id (FK → Transactions.transaction_id)
- escrow_agent_id (FK → Users.user_id, role=escrow_agent)
- amount_held
- release_conditions
- status (holding, released, refunded, disputed)
- created_at
- updated_at

---

## 9. Payments
- **payment_id** (PK)
- transaction_id (FK → Transactions.transaction_id)
- payer_id (FK → Users.user_id)
- payee_id (FK → Users.user_id)
- amount
- payment_method (ACH, wire, crypto, card)
- status (pending, completed, failed, refunded)
- processed_at

---

## 10. Verification
- **verification_id** (PK)
- asset_id (FK → Assets.asset_id)
- verifier_id (FK → Users.user_id, role=admin/third_party)
- type (ownership, performance, condition)
- result (passed, failed, pending)
- verification_docs_url
- verified_at

---

## 11. Messaging
- **message_id** (PK)
- sender_id (FK → Users.user_id)
- receiver_id (FK → Users.user_id)
- listing_id (FK → Listings.listing_id, optional)
- message_text
- attachments_url
- sent_at
- read_status (read, unread)

---

## 12. Reviews
- **review_id** (PK)
- reviewer_id (FK → Users.user_id)
- reviewee_id (FK → Users.user_id)
- transaction_id (FK → Transactions.transaction_id)
- rating (1–5)
- review_text
- created_at

---

## 13. Notifications
- **notification_id** (PK)
- user_id (FK → Users.user_id)
- type (new_bid, bid_outbid, sale_completed, escrow_update, system_alert)
- message
- is_read (true/false)
- created_at

---

## 14. Audit_Log
- **log_id** (PK)
- user_id (FK → Users.user_id, nullable)
- entity_type (user, asset, listing, transaction, escrow, etc.)
- entity_id
- action (create, update, delete, verify, dispute)
- timestamp
- metadata (JSON)

---

## Relationships (Summary)
- **Users** ↔ **User_Profile**: 1-to-1  
- **Users** ↔ **Assets**: 1-to-many (seller owns multiple assets)  
- **Assets** ↔ **Asset_Performance**: 1-to-many (monthly data)  
- **Assets** ↔ **Listings**: 1-to-1 (an asset can be listed once at a time)  
- **Listings** ↔ **Bids**: 1-to-many  
- **Listings** ↔ **Transactions**: 1-to-1 (once sold)  
- **Transactions** ↔ **Escrow**: 1-to-1  
- **Transactions** ↔ **Payments**: 1-to-many (installments or fees)  
- **Users** ↔ **Messages**: many-to-many (through Messaging table)  
- **Users** ↔ **Reviews**: many-to-many (buyer ↔ seller)  
- **Notifications** linked to **Users**  
- **Audit_Log** linked to all entities

