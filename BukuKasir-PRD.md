# BukuKasir - Product Requirements Document (PRD)

**Product Name:** BukuKasir
**Version:** 0.3  
**Date:** March 2026
**Status:** Prototype

## Table of contents

- [Executive Summary](#executive-summary)
- [Product Vision](#product-vision)
- [Target Market](#target-market)
- [System Architecture](#system-architecture)
- [User Management & Multi-Tenancy](#user-management--multi-tenancy)
- [Core Features](#core-features)
  - [Menu Management](#menu-management)
  - [Table Management](#table-management)
  - [Order Management (Cashier App)](#order-management-cashier-app)
  - [Transaction Customization](#transaction-customization)
  - [Payment Management](#payment-management)
  - [Staff Management](#staff-management)
  - [Reporting & Analytics](#reporting--analytics)
- [Cashier App (Android Tablet)](#cashier-app-android-tablet)
- [Back Office (Web)](#back-office-web)
- [Technical Requirements](#technical-requirements)
- [Integration Requirements](#integration-requirements)
- [Data Model (High-Level)](#data-model-high-level)
- [Security Requirements](#security-requirements)
- [Performance Requirements](#performance-requirements)
- [Compliance & Legal](#compliance--legal)
- [User Experience (UX) Requirements](#user-experience-ux-requirements)
  - [UX Acceptance Criteria](#ux-acceptance-criteria)
- [Success Metrics (KPIs)](#success-metrics-kpis)
- [Roadmap](#roadmap)
- [Risks & Mitigation](#risks--mitigation)
- [Appendix](#appendix)

## Executive Summary

BukuKasir is a comprehensive Point-of-Sale (POS) system designed specifically for medium to upscale Food & Beverage (F&B) merchants and restaurants with multiple staff members. The system operates as part of the Buku ecosystem, sharing user authentication and database infrastructure with BukuPay.

## Product Vision

To provide F&B businesses with an intuitive, reliable, and feature-rich POS solution that streamlines operations, enhances staff productivity, and delivers actionable business insights through comprehensive reporting.

**Key Differentiators**

- Native Android tablet experience optimized for cashier operations
- Seamless integration with BukuPay user ecosystem
- Multi-business and multi-staff architecture
- Comprehensive modifier and menu management with optional product thumbnails and AI generation
- Open Table functionality for continuous ordering and multiple order sessions
- Full transaction customization including discounts, additional fees, and custom receipts
- Flexible payment method management
- Real-time synchronization between cashier and back office

## Target Market

**Primary Audience**

- Business Type: Medium to upscale F&B establishments (restaurants, cafes, bars)
- Team Size: Minimum 2 staff members
- Location: Indonesia (initial launch)
- Tech Maturity: Comfortable with digital payment systems

**User Personas**

1. Owner/Manager - Uses web back office for management and reporting
2. Cashier Staff - Uses Android tablet for order processing and payment
3. Kitchen Staff - Receives orders (future enhancement: KDS integration)

## System Architecture

**Multi-Platform Approach**

```
                    BUKU ECOSYSTEM
  +--------------+  +--------------+  +--------------+
  |   BukuPay    |  |  BukuKasir   |  |  Future Apps |
  +------+-------+  +------+-------+  +------+-------+
         |                 |                 |
         +-----------------+-----------------+
                           |
                    +------+------+
                    | Shared User |
                    |  Database   |
                    +-------------+
```

Buku Ecosystem contains BukuPay, BukuKasir, and future apps all connected to a shared user database.

**Application Components**


| Component   | Platform                | Primary Users   | Key Functions                                                                                                                  |
| ----------- | ----------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| Cashier App | Native Android (Tablet) | Cashier Staff   | Order taking, payment processing, table assignment, transaction customization                                                  |
| Back Office | Web (Responsive)        | Owners/Managers | Menu management with thumbnails and AI generation, reporting, staff management, payment method customization, receipt settings |
| API Backend | Cloud-based             | Both            | Data synchronization, business logic, user authentication                                                                      |


## User Management & Multi-Tenancy

**User Hierarchy**

```
User Account (BukuPay/BukuKasir)
|
+-- Business 1 (Restaurant A)
|   |
|   +-- Owner Role
|   +-- Manager Role
|   +-- Cashier Role
|   +-- Kitchen Role
|
+-- Business 2 (Cafe B)
|   |
|   +-- Owner Role
|   +-- Cashier Role
|   +-- Waiter Role
|
+-- Business 3 (Bar C)
    |
    +-- Owner Role
    +-- Manager Role
```

**User Authentication**

- Single Sign-On (SSO) with BukuPay
- Phone number/Email-based authentication
- OTP verification
- Biometric authentication (optional for Cashier App)

**User Roles & Permissions**


| Role    | Permissions                                                                                                                                                                                                                   |
| ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Owner   | Full access to all features, can create businesses, manage all staff, customize payment methods, configure receipt templates, view all reports                                                                                |
| Manager | Menu management with thumbnail uploads and AI generation, staff management (except owner), customize payment methods, configure receipt templates, view reports, cannot delete business                                       |
| Cashier | Process orders, manage open tables (add orders, partial payments), apply discounts and additional fees, process payments with method selection, print custom receipts, view assigned tables, view limited reports (own shift) |
| Kitchen | View incoming orders, update order status (if KDS enabled)                                                                                                                                                                    |
| Waiter  | Create orders, view tables, add to open tables, cannot process payments                                                                                                                                                       |


## Core Features

### Menu Management

**Menu Structure**

```
Menu
|
+-- Category: Appetizers
|   |
|   +-- Menu Item: Nasi Goreng
|   |   |
|   |   +-- Thumbnail Image (optional, AI generatable)
|   |   +-- Base Price: Rp 45,000
|   |   +-- Modifiers
|   |   |   +-- Extra Egg (+Rp 5,000)
|   |   |   +-- Extra Spicy (free)
|   |   |   +-- No MSG (free)
|   |   +-- Variants
|   |   |   +-- Regular (Rp 45,000)
|   |   |   +-- Large (Rp 60,000)
|   |   +-- Options
|   |       +-- Rice: White/Brown
|   |       +-- Sauce: Sweet/Spicy
|   |
|   +-- Menu Item 2
|
+-- Category: Main Course
+-- Category: Beverages
```

**Features**

- Categories: Organize menu items (unlimited categories)
- Menu Items: Name, description, price, optional thumbnail image, SKU/code
- Product Thumbnails: Optional image upload for each menu item (JPEG/PNG, max 2MB), displayed as grid in cashier app for quick visual identification
- AI Thumbnail Generation: Generate product images from text descriptions using AI (integrated image generation service), customizable style and composition
- Modifiers:
  - Add-on modifiers (affects price)
  - Note modifiers (no price change)
  - Required vs optional modifiers
  - Modifier groups (e.g., Choose your side)
- Variants: Size options, portion options with different pricing
- Availability Toggle: Mark items as available/unavailable
- Printer Routing: Assign items to specific kitchen printers
- Tax Configuration: Inclusive/exclusive tax per item

### Table Management

**Table Structure**

```
Outlet Floor Plan
|
+-- Floor: Ground Floor
|   |
|   +-- Area: Indoor Dining
|   |   |
|   |   +-- Table T1 (4 seats, Regular)
|   |   |   +-- Status: Available
|   |   |   +-- Current Order: None
|   |   |
|   |   +-- Table T2 (6 seats, VIP)
|   |   |   +-- Status: Occupied
|   |   |   +-- Current Order: #1023 (Rp 450K)
|   |   |   +-- Open Table Session: Active
|   |   |
|   |   +-- Table T3 (2 seats, Bar Counter)
|   |       +-- Status: Reserved
|   |
|   +-- Area: Outdoor Terrace
|       |
|       +-- Table T4 (4 seats, Regular)
|       +-- Table T5 (8 seats, Large Party)
|
+-- Floor: 2nd Floor
    |
    +-- Area: Private Dining
        |
        +-- Table T6 (10 seats, VIP)
        +-- Table T7 (4 seats, Regular)

Table Status Lifecycle:
Available --> Occupied --> [Open Table] --> Payment --> Available
    |            |              |
    v            v              v
Reserved    Merged Tables   Partial Payment
    |            |              |
    v            v              v
Cleaning   Split Bills    Transfer Table
```

**Table Features**

- Floor Plan Editor: Visual layout creation (drag-and-drop)
- Table Types: Regular, bar counter, outdoor, VIP, etc.
- Table Status:
  - Available (Green)
  - Occupied (Red) - with order details
  - Reserved (Yellow)
  - Cleaning (Orange)
- Open Table: Keep table orders active to continuously add items throughout customer stay
- Merge Tables: Combine multiple tables for large parties
- Split Bills: Divide single table order into multiple payments
- Table QR Codes: For self-ordering (future enhancement)

**Open Table Feature**

- Continuous Ordering: Add multiple orders to the same table throughout customer's dining session
- Order Accumulation: All orders consolidated into single bill or tracked separately by order session
- Running Tab: View total accumulated amount in real-time
- Partial Payment: Accept partial payments while keeping table open
- Hold and Resume: Place table on hold, return later to add more items
- Order History per Table: View all orders placed at a table with timestamps and staff names
- Visual Indicator: Tables with open orders show running total and item count
- Auto-Close: Configurable auto-close after payment or manual close by cashier
- Transfer Open Table: Move open table and all its orders to different table
- Split Open Table: Split accumulated orders between multiple bills while keeping some orders open

**Floor Management**

- Multiple floors/areas support
- Custom table shapes and sizes
- Table capacity indication
- Quick table selection from list view

### Order Management (Cashier App)

**Order Flow**

```
START
  |
  v
+----------------------------------+
| Select Table or Takeaway         |
+----------------------------------+
  |
  v
+----------------------------------+
| Add Items from Menu              |
| - Search/browse thumbnails       |
| - Apply modifiers                |
+----------------------------------+
  |
  v
+----------------------------------+
| Review Order                     |
| - Edit quantities                |
| - Add special instructions       |
+----------------------------------+
  |
  v
+----------------------------------+
| Apply Discounts?                 |
| - % or fixed amount              |
| - Item or order level            |
| - Enter reason                   |
+----------------------------------+
  |
  v
+----------------------------------+
| Add Additional Fees?             |
| - Service charge                 |
| - Packaging fee                  |
| - Custom fees                    |
+----------------------------------+
  |
  v
+----------------------------------+
| Send to Kitchen                  |
+----------------------------------+
  |
  v
+----------------------------------+
| Select Payment Method            |
| - Cash/Card/QRIS                 |
| - E-wallet/BukuPay               |
| - Split payment                  |
+----------------------------------+
  |
  v
+----------------------------------+
| Process Payment                  |
+----------------------------------+
  |
  v
+----------------------------------+
| Print Custom Receipt             |
| - Header with logo               |
| - Itemized order                 |
| - Discounts & fees               |
| - Custom footer                  |
+----------------------------------+
  |
  v
END
```

**Order Types**

- Dine-in (with table assignment)
  - Standard Order: Single order, pay and close
  - Open Table: Continuous ordering, keep adding items over time
- Takeaway
- Delivery (future: integration with delivery platforms Grabfood, Gofood)

**Order Operations**

- Hold/Park orders
- Void items (with reason)
- Void entire order (manager approval required)
- Reprint receipts
- Order history lookup
- Open Table Management: Add to existing table, view running total, partial payments

**Open Table Workflow**

```
CUSTOMER ARRIVES
       |
       v
+--------------------------+
| Assign Table T5          |
| Status: Occupied         |
+--------------------------+
       |
       v
+--------------------------+
| First Order Session      |
| - Add items              |
| - Send to kitchen        |
| - Order Total: Rp 150K   |
+--------------------------+
       |
       +---> KITCHEN PREPARING
       |
       v
+--------------------------+
| Keep Table OPEN          |
| (Don't close yet)        |
+--------------------------+
       |
       v
CUSTOMER REQUESTS MORE
       |
       v
+--------------------------+
| Second Order Session     |
| - Tap Table T5 (occupied)|
| - "Add More Items"       |
| - Add new items          |
| - Running Total: Rp 320K |
+--------------------------+
       |
       +---> KITCHEN PREPARING
       |
       v
+--------------------------+
| Still Keep Table OPEN    |
| (Multiple sessions OK)   |
+--------------------------+
       |
       v
CUSTOMER READY TO PAY
       |
       v
+--------------------------+
| View All Orders:         |
| - Session 1: Rp 150K     |
| - Session 2: Rp 170K     |
| - Total: Rp 320K         |
+--------------------------+
       |
       +---> OPTION A: Pay Full Amount
       |            |
       |            v
       |    +------------------+
       |    | Close Table      |
       |    | Print Receipt    |
       |    +------------------+
       |
       +---> OPTION B: Partial Payment
                    |
                    v
            +------------------+
            | Pay Rp 200K      |
            | Balance: Rp 120K |
            | Keep Table OPEN  |
            +------------------+
                    |
                    v
            CUSTOMER CAN ADD
            MORE ITEMS OR PAY
            REMAINING BALANCE
```

**Open Table Interactions**

1. Tap Occupied Table: View current orders and running total
2. Add More Items: Add new order session to existing table
3. View Order History: See all order sessions with timestamps
4. Partial Payment: Accept payment without closing table
5. Close Table: Final payment, print receipt, reset table

### Transaction Customization

**Transaction Structure**

```
Order Transaction
|
+-- Subtotal (before adjustments)
|   +-- Item 1: Rp 45,000
|   +-- Item 2: Rp 60,000
|   +-- Item 3: Rp 35,000
|   = Rp 140,000
|
+-- Discounts
|   |
|   +-- Item-Level Discounts
|   |   +-- Item 1: -Rp 5,000 (10%)
|   |
|   +-- Order-Level Discounts
|   |   +-- Subtotal Discount: -Rp 10,000 (10%)
|   |   +-- Reason: Member Promo
|   |
|   = Total Discount: -Rp 15,000
|
+-- Additional Fees
|   |
|   +-- Service Charge (PB1): +Rp 6,250 (5% after discount)
|   +-- Packaging Fee: +Rp 3,000
|   +-- Custom Fee (Delivery): +Rp 10,000
|   |
|   = Total Fees: +Rp 19,250
|
+-- Tax (PPN 10%)
|   +-- Calculated on: (Subtotal - Discounts + Fees)
|   +-- Tax Amount: +Rp 14,425
|
+-- GRAND TOTAL: Rp 158,675
|
+-- Payment Split
    |
    +-- Payment 1: Cash Rp 100,000
    +-- Payment 2: QRIS Rp 58,675
    = Fully Paid
```

**Discounts**

- Quick discount buttons: 5%, 10%, 15%, 20%
- Custom percentage input
- Custom fixed amount input
- Item-level discount: Apply to specific items only
- Order-level discount: Apply to entire order subtotal
- Discount reasons: Required field for tracking
- Maximum discount limit: Configurable by admin
- Manager approval: Required for discounts above threshold

**Additional Fees**

- Service Charge: Configurable percentage (default 5-10%) or fixed amount
- Packaging Fee: Per item or per order
- Delivery Fee: Distance-based or fixed
- Custom Fees: Admin can create custom fee types with names
- Fee application: Before or after tax (configurable)
- Fee visibility: Show as separate line items on receipt

**Receipt Structure**

```
Printed Receipt (80mm/58mm Thermal)
|
+-- HEADER (Customizable)
|   +-- Logo Image (optional)
|   +-- Business Name: Warung Makan Sari
|   +-- Address: Jl. Sudirman No. 123, Jakarta
|   +-- Phone: +62 812-3456-7890
|   +-- Custom Text: "Terima Kasih Atas Kunjungan Anda"
|
+-- ORDER DETAILS
|   +-- Order #: 1024 | Table: T5
|   +-- Staff: Budi | Date: 28 Mar 2026 14:30
|   +-- --------------------------------
|   +-- 2x Nasi Goreng     Rp 90,000
|   +--    + Extra Egg     Rp 10,000
|   +-- 1x Es Teh Manis    Rp 15,000
|   +-- --------------------------------
|
+-- FINANCIAL BREAKDOWN
|   +-- Subtotal:          Rp 115,000
|   +-- Discount (10%):     -Rp 11,500
|   +-- Service Charge:    +Rp 5,175
|   +-- Tax (PPN 10%):     +Rp 10,868
|   +-- --------------------------------
|   +-- TOTAL:             Rp 119,543
|
+-- PAYMENT INFO
|   +-- Method: Cash
|   +-- Paid:              Rp 120,000
|   +-- Change:            Rp 457
|
+-- FOOTER (Customizable)
    +-- Thank You Message
    +-- Return Policy: "No returns after 24 hours"
    +-- QR Code: [Feedback Survey]
    +-- Social Media: @warungmakan.sari
```

**Receipt Customization**

- Custom Header:
  - Business logo upload
  - Business name and address
  - Contact information (phone, email)
  - Social media handles
  - Custom text/greeting
  - Multiple line support
- Custom Footer:
  - Thank you message
  - Return/refund policy
  - Terms and conditions
  - Promotional messages
  - QR code for feedback/survey
  - Custom text (unlimited lines)
- Receipt Settings:
  - Paper size (58mm or 80mm thermal printer)
  - Font size options
  - Show/hide fields (tax breakdown, staff name, order time)
  - Duplicate receipt option
  - Email receipt option

### Payment Management

**Payment Methods Hierarchy**

```
Payment Methods (Admin Configurable)
|
+-- Standard Methods
|   |
|   +-- Cash
|   |   +-- Enable/Disable: ON
|   |   +-- Change Calculation: Auto
|   |   +-- Round to nearest: Rp 100
|   |   +-- Permission: Cashier+
|   |
|   +-- Card (EDC)
|   |   +-- Enable/Disable: ON
|   |   +-- Require Last 4 Digits: YES
|   |   +-- Supported: Visa, Mastercard, JCB
|   |   +-- Permission: Cashier+
|   |
|   +-- QRIS (Unified QR)
|   |   +-- Enable/Disable: ON
|   |   +-- Providers: GoPay, OVO, DANA, LinkAja
|   |   +-- Auto-check payment status: YES
|   |   +-- Permission: Cashier+
|
+-- Digital Wallets
|   |
|   +-- BukuPay (Integrated)
|   |   +-- Enable/Disable: ON
|   |   +-- Show Balance: YES
|   |   +-- Direct Deduction: YES
|   |   +-- Permission: Cashier+
|   |
|   +-- External E-Wallets (Deep Link)
|       +-- GoPay: ON
|       +-- OVO: ON
|       +-- DANA: ON
|       +-- LinkAja: ON
|
+-- Custom Methods (Created by Admin)
|   |
|   +-- Voucher
|   |   +-- Icon: [Voucher Icon]
|   |   +-- Require Reference: YES
|   |   +-- Permission: Manager+
|   |
|   +-- Employee Meal
|   |   +-- Icon: [Employee Icon]
|   |   +-- Require PIN: YES
|   |   +-- Permission: Manager+
|   |
|   +-- House Account (Credit)
|       +-- Icon: [Account Icon]
|       +-- Require Approval: YES
|       +-- Permission: Owner only
|
+-- Split Payment
    +-- Enable Multiple Methods: YES
    +-- Max Split: 3 methods
    +-- Partial Payment: Supported

Payment Method Priority (Display Order):
1. Cash
2. QRIS
3. BukuPay
4. Card
5. Voucher
```

**Payment Method Customization (Admin)**

- Enable/Disable Methods: Toggle payment methods on/off
- Custom Payment Methods: Create custom methods (e.g., Voucher, Employee Meal, House Account)
- Payment Method Settings:
  - Display name customization
  - Icon/image upload
  - Default payment method
  - Require reference number (for tracking)
  - Cashier permission level required
- Payment Method Ordering: Drag to reorder display priority
- Method-Specific Settings:
  - Cash: Enable/disable change calculation
  - Card: Require last 4 digits, support multiple card types
  - E-wallet: Deep linking to apps
  - BukuPay: Direct integration with wallet balance check

**Payment Methods Available**

- Cash: With automatic change calculation
- Card: Credit/Debit card (EDC integration)
- QRIS: Unified QR code payment
- E-wallets: GoPay, OVO, DANA, LinkAja
- BukuPay: Direct wallet deduction with balance display
- Custom Methods: Vouchers, complimentary, employee meals
- Split Payment: Multiple methods for single transaction

**Payment Features**

- Custom tipping option
- Round up/down for cash payments
- Partial payment tracking
- Refund processing with reason
- Payment reconciliation
- Real-time payment status updates

### Staff Management

**Staff Hierarchy & Access Control**

```
Business: Warung Makan Sari
|
+-- Outlet: Main Branch (Jakarta)
|   |
|   +-- Staff Directory
|   |   |
|   |   +-- Owner (1)
|   |   |   +-- Name: Pak Sari
|   |   |   +-- Permissions: FULL ACCESS
|   |   |   +-- Can: Manage all, delete business
|   |   |   +-- Access: All outlets
|   |   |
|   |   +-- Manager (1)
|   |   |   +-- Name: Ibu Dewi
|   |   |   +-- Permissions: MENU, STAFF (non-owner), REPORTS, SETTINGS
|   |   |   +-- Cannot: Delete business, manage owners
|   |   |   +-- Access: Assigned outlets only
|   |   |
|   |   +-- Cashier (2)
|   |   |   +-- Budi
|   |   |   |   +-- Shift: Day (08:00-16:00)
|   |   |   |   +-- PIN: ****
|   |   |   |   +-- Permissions: Orders, Payments, Open Tables
|   |   |   |   +-- Today Sales: Rp 2.4M (45 orders)
|   |   |   |
|   |   |   +-- Ani
|   |   |       +-- Shift: Night (16:00-00:00)
|   |   |       +-- PIN: ****
|   |   |       +-- Permissions: Orders, Payments, Open Tables
|   |   |       +-- Today Sales: Rp 1.8M (32 orders)
|   |   |
|   |   +-- Waiter (3)
|   |   |   +-- Rudi, Siti, Joko
|   |   |   +-- Permissions: Create orders, View tables
|   |   |   +-- Cannot: Process payments
|   |   |
|   |   +-- Kitchen (2)
|   |       +-- Chef Ahmad, Chef Maya
|   |       +-- Permissions: View orders, Update status
|   |       +-- KDS Access: Enabled
|   |
|   +-- Shift Schedule (Today)
|       +-- Morning: Budi, Rudi, Siti, Chef Ahmad
|       +-- Evening: Ani, Joko, Chef Maya
|
+-- Outlet: Branch (Bandung)
    +-- Manager: Pak Tono
    +-- Cashier: 1 staff
    +-- Waiter: 2 staff

Staff Activity Audit Trail:
[Timestamp] [Staff] [Action] [Order/Table] [Details]
14:32:15    Budi    Order Created   T5      Rp 150K
14:45:22    Ani     Discount Applied T3     10% - Manager Approved
15:10:05    Budi    Payment Received T5      Cash Rp 150K
```

**Staff Features**

- Staff Profiles: Name, role, contact, PIN code
- Shift Management: Clock in/out, shift reports
- Performance Tracking: Sales by staff, orders processed
- Access Control: Role-based permissions

**Staff Operations**

- Add/edit/delete staff (manager/owner only)
- Assign staff to specific outlets
- View staff activity logs
- Reset PIN codes

### Reporting & Analytics

**Reports Structure**

```
Reporting Dashboard
|
+-- Sales Performance
|   |
|   +-- Daily Sales Summary
|   |   +-- Revenue: Rp 25.5M (Today)
|   |   +-- Orders: 487
|   |   +-- AOV: Rp 52,360
|   |   +-- vs Yesterday: +12%
|   |
|   +-- Hourly Sales Heatmap
|   |   +-- Peak: 12:00-13:00 (Rp 4.2M)
|   |   +-- Low: 15:00-16:00 (Rp 800K)
|   |
|   +-- Category Performance
|   |   +-- Food: 65% (Rp 16.6M)
|   |   +-- Beverages: 25% (Rp 6.4M)
|   |   +-- Desserts: 10% (Rp 2.5M)
|   |
|   +-- Staff Performance
|       +-- Top: Budi - Rp 8.2M (156 orders)
|       +-- Avg: Rp 6.4M per cashier
|
+-- Financial Analysis
|   |
|   +-- Revenue Breakdown
|   |   +-- Gross Revenue: Rp 25.5M
|   |   +-- Discounts: -Rp 1.8M (7%)
|   |   +-- Net Revenue: Rp 23.7M
|   |
|   +-- Payment Method Mix
|   |   +-- Cash: 35% (Rp 8.9M)
|   |   +-- QRIS: 40% (Rp 10.2M)
|   |   +-- Card: 15% (Rp 3.8M)
|   |   +-- BukuPay: 10% (Rp 2.6M)
|   |
|   +-- Tax Reporting (PPN)
|       +-- Taxable Revenue: Rp 21.5M
|       +-- Tax Collected: Rp 2.15M
|       +-- Service Charge (PB1): Rp 1.2M
|
+-- Discounts & Fees Analysis
|   |
|   +-- Discounts Given
|   |   +-- Total: Rp 1.8M across 124 orders
|   |   +-- By Type: Member 60%, Promo 30%, Other 10%
|   |   +-- By Staff: Budi Rp 800K, Ani Rp 600K, Manager Rp 400K
|   |   +-- Top Reasons: Member Benefit, Happy Hour, Complaint Resolution
|   |
|   +-- Additional Fees
|       +-- Service Charge: Rp 1.2M (all dine-in)
|       +-- Packaging: Rp 450K (takeaway orders)
|       +-- Delivery: Rp 320K (delivery orders)
|
+-- Menu Analytics
|   |
|   +-- Top Selling Items
|   |   +-- 1. Nasi Goreng Special - 156 sold
|   |   +-- 2. Es Teh Manis - 142 sold
|   |   +-- 3. Ayam Bakar - 98 sold
|   |
|   +-- Slow Moving Items
|   |   +-- 1. Salad Caesar - 3 sold (flagged)
|   |   +-- 2. Soup Tomato - 5 sold (review needed)
|   |
|   +-- Modifier Analysis
|       +-- Extra Egg: 45% of applicable orders
|       +-- Extra Spicy: 30% of applicable orders
|       +-- No MSG: 15% of applicable orders
|
+-- Open Table Metrics
|   |
|   +-- Active Tables (Now)
|   |   +-- 8 tables occupied
|   |   +-- Running totals: Rp 1.2M combined
|   |   +-- Avg session time: 45 minutes
|   |
|   +-- Historical Analysis
|       +-- Avg orders per table: 2.3 sessions
|       +-- Partial payment rate: 12% of open tables
|       +-- Table turnover rate: 6.2 per day

Report Filters Available:
[Date Range] [Outlet] [Staff] [Payment Method] [Open Table Status] [Export: PDF/Excel]
```

**Sales Reports**

- Daily Sales Summary: Total revenue, order count, average order value
- Payment Method Breakdown: Cash vs non-cash ratio
- Hourly Sales: Peak hours identification
- Category Performance: Sales by menu category
- Staff Performance: Sales by cashier/server

**Discount & Fee Reports**

- Total Discounts Given: By type, by staff, by date range
- Additional Fees Collected: Service charge, packaging, delivery breakdown
- Discount Reasons: Analytics on why discounts applied

**Menu Analytics**

- Top Selling Items: Most popular menu items with thumbnail view
- Slow Moving Items: Items with low sales
- Menu Mix Analysis: Category contribution to revenue
- Modifier Analysis: Most chosen modifiers

**Financial Reports**

- Revenue Report: Gross and net revenue after discounts and fees
- Tax Report: Tax collected breakdown
- Profit Margin: By item and overall

**Open Table Reports**

- Active Open Tables: Currently occupied tables with running totals
- Open Table History: Closed sessions with duration and total orders
- Average Table Duration: How long customers stay
- Multiple Orders per Table: Tables with 2+ order sessions
- Partial Payment Tracking: Tables with partial payments
- Table Turnover Rate: Tables opened and closed per day
- Staff Performance on Open Tables: Which staff manage open tables best

**Report Filters**

- Date range (daily, weekly, monthly, custom)
- Outlet/location filter
- Staff filter
- Payment method filter
- Open table status filter (active/closed/all)
- Export to PDF/Excel

## Cashier App (Android Tablet)

**Design Principles**

- Touch-Optimized: Large buttons, minimal typing
- Offline Capability: Continue operations during internet outage
- Speed: Fast order entry (< 30 seconds per order)
- Visual Recognition: Optional product thumbnails for quick menu navigation (falls back to text view)
- Visibility: Clear order summary always visible

**Screen Layout**

```
+-------------------------------------------------------------+
|  HEADER: Business Name | Staff Name | Time | Battery/Network|
+-----------------+-------------------------------------------+
|                 |                                           |
|   TABLE GRID    |           MENU INTERFACE                  |
|                 |                                           |
|  [T1] [T2]      |  +-------------------------------------+  |
|  [T3] [T4]      |  | Categories: [All][Food][Drinks]     |  |
|  [T5] [T6]      |  +-------------------------------------+  |
|  [T7] [T8]      |                                           |
|                 |  +----------+ +----------+ +----------+  |
|                 |                                           |
| LEGEND:         |                                           |
| [T1] Available  |                                           |
| [T2] Occupied   |                                           |
| [T3] OPEN       |  <- Running Total: Rp 450K               |
|     (3 items)   |                                           |
| [T4] Reserved   |                                           |
|  [Takeaway]     |  | [IMAGE]  | | [IMAGE]  | | [IMAGE]  |  |
|  [Delivery]     |  |  Item 1  | |  Item 2  | |  Item 3  |  |
|                 |  |  Rp45K   | |  Rp35K   | |  Rp50K   |  |
|                 |  +----------+ +----------+ +----------+  |
|                 |                                           |
+-----------------+-------------------------------------------+
|                 |  CURRENT ORDER                            |
|  QUICK ACTIONS  |  +-------------------------------------+  |
|  [Void] [Hold]  |  | 2x Nasi Goreng      Rp 90,000      |  |
|  [Discount]     |  |    + Extra Egg      Rp 5,000       |  |
|  [Add Fee]      |  | 1x Es Teh Manis     Rp 8,000       |  |
|  [Print Bill]   |  +-------------------------------------+  |
|                 |  Subtotal:         Rp 103,000             |
|                 |  Discount (10%):   -Rp 10,300             |
|                 |  Service Charge:   +Rp 9,270              |
|                 |  Tax (10%):        Rp 10,227              |
|                 |  TOTAL:            Rp 112,197             |
|                 |                                           |
|                 |  [        PAY NOW        ]                |
+-----------------+-------------------------------------------+
```

**Key Interactions**

- Table Selection: Tap to view/occupy, long-press for options
- Open Table Management: Tap occupied table to add more items or view running total
- Menu Navigation: Swipe categories, tap items to add
- Product Selection: Visual thumbnail grid for quick identification (or text-only view if no thumbnails)
- Modifier Selection: Modal popup with options
- Order Editing: Swipe to delete, tap to edit quantity
- Discount Application: Dedicated button with quick options
- Additional Fee: Button to add service charge, packaging, or custom fees
- Payment: Full-screen payment interface with method selection
- Partial Payment: Pay partial amount while keeping table open
- Receipt Preview: Before printing with header/footer preview

**Open Table User Scenarios**

Scenario 1: Waiter Takes Multiple Orders

```
Waiter -> Sees Occupied Table T3 -> Taps Table
       -> Views Running Total: Rp 320K (2 orders)
       -> Taps "Add Order" 
       -> Adds new items
       -> Sends to Kitchen
       -> Table remains OPEN
       -> Customer can order again later
```

Scenario 2: Cashier Handles Payment

```
Cashier -> Sees Table T3 (Occupied - Rp 450K)
        -> Taps Table
        -> Views All Order Sessions:
           Order #1021: Rp 150K (10:30 AM)
           Order #1034: Rp 170K (11:15 AM)
           Order #1056: Rp 130K (12:00 PM)
        -> Customer pays Rp 300K
        -> Applies Partial Payment
        -> Balance: Rp 150K
        -> Table stays OPEN
        -> Customer continues dining
```

Scenario 3: Different Staff Continue Service

```
Staff A (10:00 AM) -> Opens Table T5 -> Takes Order 1
Staff B (11:30 AM) -> Sees T5 Active -> Takes Order 2  
Staff C (1:00 PM)  -> Sees T5 Active -> Processes Payment -> Closes Table
       
All staff can see table status and add orders
```

**Discount Workflow**

1. Cashier taps Discount button
2. Select discount type (percentage or fixed)
3. Choose from preset values or enter custom amount
4. Select application scope (entire order or specific items)
5. Enter reason for discount (required)
6. If exceeds threshold, request manager PIN
7. Applied discount displayed in order summary

**Additional Fee Workflow**

1. Cashier taps Additional Fee button
2. Select fee type (Service Charge, Packaging, Delivery, Custom)
3. Amount auto-filled from settings or enter custom
4. Fee applied and displayed in order summary
5. Multiple fees can be stacked

**Payment Workflow**

```
+----------------------------------+
| Tap PAY NOW                      |
+----------------------------------+
  |
  v
+----------------------------------+
| Display Payment Methods          |
| (Admin Customizable List)        |
+----------------------------------+
  |
  v
+----------------------------------+
| Select Payment Method            |
| [Cash] [Card] [QRIS]             |
| [GoPay] [OVO] [BukuPay]          |
| [Split Payment]                  |
+----------------------------------+
  |
  +---> Cash ----------------------> Enter Amount -> Calculate Change -> Confirm
  |
  +---> Card ----------------------> Enter Card Details -> Process -> Confirm
  |
  +---> QRIS/E-wallet -------------> Display QR Code -> Scan -> Confirm
  |
  +---> BukuPay -------------------> Check Balance -> Deduct -> Confirm
  |
  +---> Split Payment -------------> Select Method 1 (Amount) -> Select Method 2 (Amount) -> Confirm
  |
  v
+----------------------------------+
| Payment Successful               |
+----------------------------------+
  |
  v
+----------------------------------+
| Print Custom Receipt             |
+----------------------------------+
```

1. Tap Pay Now
2. Display available payment methods (customizable by admin)
3. Select primary method
4. Enter amount (auto-filled with total)
5. For split payment: Add secondary method and amount
6. Calculate change (for cash) or confirm (for digital)
7. Process payment
8. Auto-print receipt or prompt for print

**Receipt Printing**

1. Auto-print after successful payment (configurable)
2. Manual reprint option
3. Preview receipt with custom header/footer before printing
4. Print settings: Customer copy, kitchen copy, merchant copy
5. Receipt shows:
  - Custom header (logo, business info)
  - Order details with item thumbnails (optional)
  - Item prices, quantities, modifiers
  - Subtotal
  - Discounts applied (itemized)
  - Additional fees (itemized)
  - Tax breakdown
  - Total amount
  - Payment method(s) used
  - Change given (if cash)
  - Custom footer (thank you, policy, etc.)

**Offline Mode**

- Local data caching
- Queue orders for sync when online
- Local receipt printing
- Conflict resolution for concurrent edits

## Back Office (Web)

**Dashboard**

- Revenue overview (today, this week, this month)
- Active orders count
- Recent transactions with discount/fee breakdown
- Quick action buttons
- Sales trend chart

**Menu Configuration**

- Full menu editor with optional thumbnail uploads
- Drag-and-drop image upload for product thumbnails
- AI Thumbnail Generation:
  - Generate images from text descriptions
  - Multiple style options (food photography, illustration, minimalist)
  - Edit and refine generated images
  - One-click apply to menu item
  - Credit system for AI generation (free tier + paid tier)
- Image cropping and optimization
- Option to use menu without any thumbnails (text-only mode)
- Bulk import/export (CSV/Excel)
- Menu scheduling (breakfast/lunch/dinner menus)
- Menu item analytics with thumbnail view

**Payment Method Settings**

- Enable/disable standard payment methods
- Create custom payment methods
- Upload payment method icons
- Configure payment method display order
- Set permissions (which staff roles can use)
- Configure reference number requirements
- Set default payment method

**Receipt Configuration**

- Header Editor:
  - Logo upload (max resolution, auto-resize)
  - Business information fields
  - Custom text areas (up to 5 lines)
  - Preview mode
- Footer Editor:
  - Template messages
  - Custom text (up to 10 lines)
  - QR code generator for feedback/social media
  - Terms and policy text
- Receipt Settings:
  - Paper size selection (58mm/80mm)
  - Font size adjustment
  - Toggle fields on/off
  - Auto-print after payment toggle
  - Duplicate copy options

**Discount Settings**

- Preset discount values configuration
- Maximum discount limit per role
- Approval requirements by threshold
- Discount reason categories
- Disable discounts toggle

**Additional Fee Settings**

- Service charge configuration (default percentage)
- Packaging fee options
- Custom fee type creation
- Fee calculation rules (before/after tax)
- Fee display options

**Staff Administration**

- Staff directory
- Role assignment
- Permission customization
- Activity audit logs

**Settings**

- Business profile
- Outlet settings
- Tax configuration
- Printer configuration
- Integration settings

**Reports Center**

- Interactive charts and graphs
- Drill-down capabilities
- Discount and fee analytics
- Scheduled report emails
- Data export options

## Technical Requirements

**Cashier App (Android)**

- Minimum SDK: Android 8.0 (API 26)
- Target SDK: Android 14 (API 34)
- Architecture: MVVM or MVI
- Programming Language: Kotlin
- Database: Room (local), Firebase/SQLite
- Key Libraries:
  - Retrofit/OkHttp for API calls
  - Glide for image loading and thumbnail display
  - MPAndroidChart for charts
  - Firebase Cloud Messaging for notifications
  - Thermal printer SDK (ESC/POS)

**Back Office (Web)**

- Framework: React.js or Vue.js
- UI Library: Material-UI or Ant Design
- State Management: Redux or Vuex
- Charts: Chart.js or D3.js
- Image Upload: Image compression and optimization
- AI Image Generation: Integration with image generation API (DALL-E, Midjourney API, or Stable Diffusion)
- Build Tool: Vite or Webpack

**Backend API**

- Architecture: RESTful API or GraphQL
- Language: Node.js or Python
- Database: PostgreSQL
- Cache: Redis
- File Storage: AWS S3 or similar (for thumbnails and logos)
- Authentication: JWT tokens
- Real-time: WebSocket for live updates

**Hardware Requirements (Cashier)**

- Android tablet (minimum 10-inch, 1080p)
- 3GB RAM minimum
- 32GB storage minimum
- Bluetooth for printer connection
- USB-C for charging and peripherals
- Camera for QR code scanning

**Peripheral Support**

- Printers: Bluetooth thermal printers (58mm and 80mm)
- Card Readers: Bluetooth EDC devices
- Cash Drawers: USB or Bluetooth connected
- Barcode Scanners: Bluetooth or USB OTG
- Kitchen Display: Android tablet or dedicated KDS

## Integration Requirements

**BukuPay Integration**

- Shared user authentication
- Wallet balance display in payment screen
- Direct payment from BukuPay wallet
- Transaction history sync

**Payment Gateway Integrations**

- Midtrans/Xendit for card payments
- QRIS aggregator integration
- E-wallet deep linking

**Printer Integration**

- ESC/POS command support
- Auto-detection of printers
- Print queue management
- Template customization with header/footer
- Support for 58mm and 80mm paper widths

**Image Storage & AI Generation**

- Thumbnail image upload and optimization (optional, not required)
- AI Image Generation Service:
  - Integration with AI image generation APIs
  - Text-to-image conversion for menu items
  - Style presets for food photography
  - Generated image storage and management
  - User credit system for generation limits
- Logo upload for receipt headers
- Payment method icon uploads
- CDN delivery for fast image loading

**Future Integrations**

- Accounting software (Jurnal, QuickBooks)
- Delivery platforms (GoFood, GrabFood, ShopeeFood)
- Inventory management systems
- CRM and loyalty programs

## Data Model (High-Level)

**Entity Relationship Overview**

```
+--------+       +----------+       +--------+
|  User  |<>-----| Business |<>-----| Outlet |
+--------+       +----------+       +--------+
                      |                   |
                      |             +-----+-----+
                      |             |           |
                      v             v           v
                 +----------+   +-------+  +---------+
                 |  Staff   |   | Table |  |  Menu   |
                 +----------+   +-------+  +----+----+
                                               |
                                         +-----+-----+
                                         |           |
                                         v           v
                                    +----------+  +------------+
                                    | Category |  |  MenuItem  |
                                    +----------+  +------+-----+
                                                       |
                              +------------------------+--------+
                              |                                 |
                              v                                 v
                       +------------+                 +----------------+
                       |  Modifier  |                 | PaymentMethod  |
                       +------------+                 +----------------+

+--------+       +------------+       +----------+       +-----------+
| Outlet |<>-----|   Order    |<>-----| OrderItem|<>-----|   Menu    |
+--------+       +------------+       +----------+       |   Item    |
       |                 |                               +-----------+
       |                 |
       |                 +----------+
       |                            |
       v                            v
+--------------+           +---------------+
| PaymentMethod|           | Payment       |
+--------------+           +---------------+
       |                            |
       v                            v
+--------------+           +---------------+
| AdditionalFee|           |ReceiptTemplate|
+--------------+           +---------------+
```

**Core Entities**

User

- id, email, phone, name, created_at
- Businesses[] (many-to-many)

Business

- id, name, address, phone, tax_id, logo
- owner_id (User)
- receipt_header_text, receipt_footer_text
- receipt_logo_url
- Outlets[]
- Staff[] (Users with roles)
- PaymentMethods[] (customizable)

Outlet

- id, business_id, name, location
- service_charge_percentage
- enable_service_charge
- Tables[]
- Menus[]
- Settings{}

Table

- id, outlet_id, table_number, table_name
- table_type (regular/bar/outdoor/vip)
- capacity, position_x, position_y
- status (available/occupied/reserved/cleaning)
- current_open_session_id (nullable)
- running_total_amount (if open)
- is_open_table (boolean)
- qr_code_url
- created_at, updated_at

Menu

- id, outlet_id, name, description
- Categories[]
- Items[]

MenuItem

- id, menu_id, category_id
- name, description, price, cost
- thumbnail_url (optional), thumbnail_file
- ai_generated_thumbnail (boolean)
- ai_prompt (text, if generated)
- image_url (optional larger image)
- sku, is_available
- Modifiers[]
- Variants[]

PaymentMethod

- id, business_id, outlet_id
- name, type (cash/card/qris/ewallet/custom)
- is_enabled, display_order
- icon_url, require_reference
- min_role_required
- settings{}

Order

- id, outlet_id, table_id
- open_table_session_id (optional, for open table orders)
- staff_id, customer_name
- order_type (dine_in/takeaway/delivery)
- is_open_table_order (boolean)
- status, subtotal_amount
- discount_amount, discount_reason, discount_type
- additional_fees[], fee_total
- tax_amount, tax_rate
- total_amount, payment_status
- created_at, updated_at
- OrderItems[]

OrderItem

- id, order_id, menu_item_id
- quantity, unit_price
- discount_amount (item-level)
- modifiers[], variant
- subtotal

OpenTableSession

- id, table_id, outlet_id
- status (active/closed)
- opened_at, closed_at
- opened_by_staff_id
- total_orders_count
- running_total_amount
- amount_paid, remaining_balance
- is_partially_paid
- Orders[] (multiple orders per session)
- Payments[] (multiple payments for partial payments)

AdditionalFee

- id, order_id
- fee_type (service_charge/packaging/delivery/custom)
- fee_name, amount
- is_percentage, percentage_value

Payment

- id, order_id, amount
- payment_method_id, payment_method_name
- reference_number, status
- processed_by, processed_at
- change_amount (for cash)

ReceiptTemplate

- id, business_id, outlet_id
- header_lines[], logo_url
- footer_lines[], show_qr_code
- qr_code_url, qr_code_type
- paper_size, font_size
- show_tax_breakdown, show_staff_name
- auto_print, print_copies

Staff

- id, user_id, business_id, outlet_id
- role, pin_code, is_active
- permissions{}

## Security Requirements

**Authentication & Authorization**

- JWT-based authentication
- Role-based access control (RBAC)
- PIN protection for cashier app
- Session timeout (configurable)

**Data Protection**

- SSL/TLS encryption for all communications
- Sensitive data encryption at rest
- PCI DSS compliance for card data
- Regular security audits

**Audit Trail**

- Log all critical operations including discounts and fee applications
- Immutable transaction records
- Staff action tracking
- Data change history

## Performance Requirements


| Metric              | Target        |
| ------------------- | ------------- |
| App Launch Time     | < 3 seconds   |
| Menu Load Time      | < 2 seconds   |
| Thumbnail Load Time | < 1 second    |
| Order Processing    | < 5 seconds   |
| Payment Processing  | < 3 seconds   |
| Receipt Print       | < 2 seconds   |
| Report Generation   | < 10 seconds  |
| API Response Time   | < 500ms (p95) |
| Uptime SLA          | 99.9%         |


## Compliance & Legal

**Tax Compliance**

- Support for Indonesian tax regulations
- PPn (Value Added Tax) calculation
- PB1 (Service Charge) handling
- Tax invoice generation

**Data Privacy**

- PDPA (Personal Data Protection Act) compliance
- User consent management
- Data retention policies
- Right to be forgotten implementation

**Financial Regulations**

- Financial reporting standards
- Audit trail requirements
- Transaction record retention (5+ years)

## User Experience (UX) Requirements

**Cashier App UX**

- Minimal Clicks: Complete order in max 5 taps
- Visual Menu: Optional product thumbnails for quick recognition (AI-generatable)
- Open Table Visibility: Clear visual indicator of occupied tables with running totals
- Continuous Ordering: Easy "Add More" action on occupied tables (< 2 taps)
- Order History Access: Quick view of all order sessions for a table
- Partial Payment Clarity: Clear display of paid amount vs remaining balance
- Error Prevention: Confirm destructive actions
- Visual Feedback: Haptic feedback for successful actions
- Accessibility: Support for screen readers
- Internationalization: Indonesian and English support

**Back Office UX**

- Responsive Design: Works on desktop and tablet
- Intuitive Navigation: Clear menu hierarchy
- Data Visualization: Charts and graphs for reports
- Bulk Operations: Import/export capabilities
- Search & Filter: Powerful data filtering
- Image Management: Easy thumbnail and logo upload with preview

### UX Acceptance Criteria

Acceptance criteria below are **product-level**: design and engineering must be able to verify them in build reviews (they do not replace detailed UI specs).

**Onboarding & empty states**

- New business / new outlet: Guided setup covers business profile, tax defaults, first menu structure, first staff + roles, and printer pairing; each step has a clear skip/defer path where safe.
- Empty dashboard and empty menu views explain what to do next with a primary call to action (no dead ends).
- First login per role: Optional short tour or contextual hints for the three highest-frequency tasks for that role.

**Multi-business & multi-outlet context**

- Active business and active outlet are always visible in cashier and back office chrome; switching outlet requires explicit confirmation if open tables or unsaved drafts exist.
- After switching context, all lists and totals reflect the new scope within one screen transition (no stale data without a labeled “refreshing” state).

**Cashier: speed & power paths**

- Menu supports search with typo-tolerant matching; optional quick entry via SKU/barcode when hardware is connected.
- “Repeat last order” or equivalent one-action path exists for takeaway/recurring orders (configurable per outlet).
- Favorites / pinned items or “recently ordered” strip available for high-volume SKUs.

**Cashier: interrupt & recovery**

- Destructive actions (void order, void line, cancel payment in progress) require confirmation with plain-language consequence; irreversible actions show who will be recorded as actor after PIN/approval.
- Send-to-kitchen: If supported, customer-visible or staff-visible distinction between “saved draft” vs “sent/fired” is clear; recovery path when kitchen should not prepare is defined.
- Payment: If user navigates away or app backgrounds mid-flow, returning lands on a recoverable state or explicit “resume / cancel payment” choice.

**Hardware & connectivity**

- Printer unavailable: Blocking banner on payment/receipt steps with actionable steps (retry, select printer, print later if policy allows); never silent failure after “Payment successful.”
- EDC / QRIS timeout: Clear timeout message, retry, and alternate method suggestion without losing order totals.
- Bluetooth peripheral drop: User sees connection status and reconnect affordance from the screen where printing or card flow is triggered.
- Low battery / offline: Persistent indicator; when offline, queued actions and sync backlog are visible; staff can end shift only per policy (with warning if data not synced).

**Offline & sync (user-visible)**

- Per-order or per-action sync state (pending / failed / synced) is surfaced where operations can conflict; failed sync shows retry and support guidance.
- Conflict resolution prefers explicit user choice when two devices edit the same open table; automatic merge rules are documented and surfaced in release notes / admin help.

**Open table, waiter, handoff**

- Occupied open table shows running total, session count, and last activity time; “Add more” is reachable in ≤2 taps from floor view.
- When multiple staff act on one table, order timeline shows staff attribution per session or per send where technically feasible.
- Waiter-created orders and cashier payment: No duplicate bill risk—UX specifies whether waiter hands device, or cashier pulls table, and how unpaid orders appear to each role.

**Customer-facing moments (POS)**

- Before “Pay,” staff can show a read-only bill summary (line items, discounts, fees, tax wording, total) suitable for customer review; text size readable at arm’s length on tablet.
- Split bill: UX supports at least split-by-amount and split-by-line-item (or documented alternative), with running validation so allocated parts always equal total before payment is committed.
- Partial payment: Remaining balance and “table still open” state are shown to staff and reflected on printed interim bill if used.

**Receipts & PII**

- Email/SMS/digital receipt: Capture step includes consent language appropriate for PDPA; failure to send shows error and retry; success confirms destination masked (e.g., `a***@domain.com`).
- Receipt and order display of customer name/phone follow business-configurable fields and retention rules.

**Back office: configuration complexity**

- Payment methods, fees, discounts, and receipt templates: Live preview (receipt mock) before save; invalid combinations (e.g., fee before/after tax) blocked with inline explanation.
- Menu bulk import: Row-level validation report with downloadable error file; partial import allowed only when explicitly chosen and summarized before commit.

**Reporting & manager actions**

- At least one dashboard surface highlights anomalies (e.g., discount rate vs 7-day baseline, count of voids, open tables over duration threshold) with drill-down to transactions.
- Audit / activity views are human-readable (who, what, when, outlet) and exportable for investigations.

**Accessibility & inclusive design**

- Table and order status do not rely on color alone (icons, patterns, or labels accompany green/red/yellow states).
- System text scaling / large touch targets meet or exceed platform guidelines for primary actions.
- Screen reader labels for table grid, order lines, and payment method list are required acceptance checks on Android.

**AI thumbnails**

- Generation shows progress, cancel, and retry; failed generation does not block saving the menu item (fallback to text-only or manual upload).
- Staff can replace or remove AI image in one flow; generated images are visually distinct or labeled in editor if required for trust/compliance.

**Localization**

- Indonesian and English: UI strings, number/currency (IDR), and date/time formats follow locale; mixed-language menus do not break search.

**Training & support**

- In-app help entry point from cashier and back office; links or embedded content for printer setup and open-table workflow.
- Sandbox or training mode (optional MVP+): Safe practice without affecting live reports, if offered in roadmap phase.

## Success Metrics (KPIs)

**Business Metrics**

- Monthly Active Businesses
- Average Orders per Business per Day
- Average Transaction Value
- Average Discount Rate
- Service Charge Collection Rate
- Open Table Usage: Percentage of dine-in orders using open table feature
- Average Orders per Open Table: How many order sessions per open table
- Average Table Duration: Time from first order to final payment
- Table Turnover Rate: Number of tables served per day
- Partial Payment Adoption: Usage of partial payment feature
- Customer Retention Rate
- Revenue per User (ARPU)

**Technical Metrics**

- App Store Rating (Target: 4.5+)
- Crash Rate (Target: < 0.1%)
- API Error Rate (Target: < 0.5%)
- Image Load Success Rate (Target: > 99%)
- User Session Duration
- Feature Adoption Rate

**Operational Metrics**

- Average Order Processing Time
- Payment Success Rate
- Receipt Print Success Rate
- Staff Training Time (Target: < 30 minutes)
- Support Ticket Volume
- Net Promoter Score (NPS)

## Roadmap

**Phase 1 - MVP (Months 1-3)**

- Core order management
- Open Table functionality (continuous ordering, multiple sessions)
- Basic menu management with optional thumbnails
- AI thumbnail generation integration
- Cash and basic digital payments
- Custom receipt header/footer
- Simple reporting
- Single outlet support

**Phase 2 - Enhanced Features (Months 4-6)**

- Modifier and variant support
- Table management with floor plan
- Staff management and access control
- Discount and additional fee system
- Payment method customization
- Advanced reporting
- Multi-outlet support

**Phase 3 - Scale & Integrate (Months 7-9)**

- Inventory management
- BukuPay deep integration
- Third-party integrations
- Advanced analytics
- Loyalty program

**Phase 4 - Enterprise Features (Months 10-12)**

- Multi-location dashboard
- Franchise management
- Advanced permissions
- API for custom integrations
- White-label options

## Risks & Mitigation


| Risk                         | Impact | Mitigation                                 |
| ---------------------------- | ------ | ------------------------------------------ |
| Internet connectivity issues | High   | Offline mode implementation                |
| Hardware compatibility       | Medium | Certified hardware list, extensive testing |
| Data security breach         | High   | Encryption, regular audits, compliance     |
| User adoption resistance     | Medium | Intuitive UX, training materials, support  |
| Competition                  | Medium | Feature differentiation, pricing strategy  |
| Integration complexity       | Medium | Phased approach, robust API design         |
| Image storage costs          | Low    | Image optimization, CDN, compression       |


## Appendix

**Glossary**

- POS: Point of Sale
- KDS: Kitchen Display System
- SKU: Stock Keeping Unit
- EDC: Electronic Data Capture (card reader)
- QRIS: Quick Response Code Indonesian Standard
- MVP: Minimum Viable Product
- CDN: Content Delivery Network

**References**

- BukuPay API Documentation
- Indonesian Tax Regulations
- PCI DSS Compliance Guidelines
- Android Development Best Practices
- ESC/POS Printer Commands

**Document History**


| Version | Date       | Author       | Changes                                                        |
| ------- | ---------- | ------------ | -------------------------------------------------------------- |
| 0.3     | March 2026 | Product Team | Added visual ASCII tree charts for all Core Features sections  |
| 0.2     | March 2026 | Product Team | Added table of contents with section anchor links              |
| 1.1     | March 2026 | Product Team | Added UX Acceptance Criteria; deduplicated Report Filters list |
| 1.0     | March 2025 | Product Team | Initial PRD                                                    |


**Document Owner:** Product Management Team
**Review Cycle:** Monthly during development, Quarterly post-launch
**Next Review Date:** April 2026

*This document is confidential and proprietary to Buku. Distribution without written consent is prohibited.*