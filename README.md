<div align="center">

# 🛒 Hardware Point POS — Enterprise MERN Retail ERP

### Production-Ready, Decoupled Point of Sale with Clean Architecture, Domain-Driven Layering & Offline IndexedDB Resiliency

<p>
  <img src="https://img.shields.io/badge/React-18.2.0-61DAFB?style=for-the-badge&logo=react&logoColor=white" />
  <img src="https://img.shields.io/badge/Vite-8.2.0-646CFF?style=for-the-badge&logo=vite&logoColor=white" />
  <img src="https://img.shields.io/badge/Node.js-18.x%20%7C%2020.x-339933?style=for-the-badge&logo=node.js&logoColor=white" />
  <img src="https://img.shields.io/badge/Express-5.2.1-000000?style=for-the-badge&logo=express&logoColor=white" />
  <img src="https://img.shields.io/badge/MongoDB-Atlas%208.x-47A248?style=for-the-badge&logo=mongodb&logoColor=white" />
  <img src="https://img.shields.io/badge/Mongoose-8.24.4-880000?style=for-the-badge&logo=mongoose&logoColor=white" />
  <img src="https://img.shields.io/badge/Dexie.js-4.4.0-379392?style=for-the-badge&logo=dexie&logoColor=white" />
  <img src="https://img.shields.io/badge/Ant%20Design-5.12.6-0170FE?style=for-the-badge&logo=ant-design&logoColor=white" />
  <img src="https://img.shields.io/badge/License-Permission--Required-red?style=for-the-badge" />
</p>

**A production-grade Point of Sale and Inventory Management System engineered for retail hardware shops, sanitary merchants, and electrical distributors. Features Clean Architecture, Domain-Driven Design (DDD), Dexie.js offline-first local checkout, reactive cache invalidation, hardware barcode scanner support, and 80mm thermal receipt printing.**

[Architecture & Design Patterns](#-architecture--software-design-patterns) • [Mermaid Visual Models](#-mermaid-visual-models) • [Folder Structure](#-complete-folder-structure) • [Database Design](#-database-design) • [API Reference](#-api-reference) • [Getting Started](#-getting-started) • [Contributing](#-contributing) • [License](#-license)

---

</div>

> [!IMPORTANT]
> **License Notice**: This software is licensed under the **Hardware Point POS Permission-Based Non-Commercial License**. It is non-paid for approved personal and educational inspection, but **requires prior written permission before usage, deployment, or modification**. Commercial use or resale without written consent is strictly prohibited. See [LICENSE](file:///d:/mern-pos/LICENSE) for terms.

---

## 🎯 Release Highlights & Version Matrix

| Version | Status | Key Highlights |
|:---:|:---:|---|
| **`v2.5.0`** | **Production (Current)** | • **Offline-First Resilience**: Dexie.js 4.4 IndexedDB client database with auto-reconciling background sync.<br>• **Design System Modernization**: Ant Design 5 `<ConfigProvider>` tokens, high-contrast vibrant badges, top-level reactive progress bar.<br>• **Backend Modernization**: Express 5.2.1, Mongoose 8.24, Jose JWT signing, Unit of Work transactional checkout, and EventEmitter pub/sub.<br>• **Hardware Scanner & Thermal Engine**: 80mm ESC/POS roll printing, `F2` barcode focus, and `F4` payment hotkeys.<br>• **Monorepo DX**: Unified root scripts for concurrent client/server dev and database seeding. |
| **`v2.4.0`** | Superseded | 4-Tier Frontend Clean Architecture and Domain-Driven Backend split. |
| **`v2.0.0`** | Superseded | Introduction of JWT Bearer Authentication, Admin User Management panel, and PrivateRoute guards. |
| **`v1.0.0`** | Deprecated | Legacy prototype with monolithic controllers, plain-text credentials, and manual page refreshes. |

---

## 🏛️ Architecture & Software Design Patterns

Hardware Point POS decouples responsibilities across both client and server according to industry-standard patterns:

```
┌──────────────────────────────────────────────────────────────────────────────────┐
│                            CLIENT SPA (REACT 18 & VITE)                          │
│  ┌────────────────────────────────────────────────────────────────────────────┐  │
│  │ Tier 1: Presentation Layer (Ant Design 5 UI, High-Contrast Badges, Layout) │  │
│  └──────────────────────────────────────┬─────────────────────────────────────┘  │
│                                         │ delegates actions & calculations       │
│  ┌──────────────────────────────────────┴─────────────────────────────────────┐  │
│  │ Tier 2: Action Handlers (src/handlers/) & Centralized Error Interception   │  │
│  │ Tier 3: Pure Calculators (src/calculaters/ - Math, COGS, Margins, Formats) │  │
│  └──────────────────────────────────────┬─────────────────────────────────────┘  │
│                                         │ fetches / mutates / syncs              │
│  ┌──────────────────────────────────────┴─────────────────────────────────────┐  │
│  │ Tier 4: Service Layer + Dexie 4 IndexedDB Store + TanStack Query Cache     │  │
│  └──────────────────────────────────────┬─────────────────────────────────────┘  │
└─────────────────────────────────────────┼────────────────────────────────────────┘
                                          │ HTTP / REST (Jose JWT Bearer Auth)
┌─────────────────────────────────────────▼────────────────────────────────────────┐
│                        BACKEND SERVER (EXPRESS 5 & MONGOOSE 8)                   │
│  ┌────────────────────────────────────────────────────────────────────────────┐  │
│  │ Presentation Layer: Modular Controllers, Express 5 Routers, Auth Guards    │  │
│  └──────────────────────────────────────┬─────────────────────────────────────┘  │
│                                         │ calls domain services                  │
│  ┌──────────────────────────────────────┴─────────────────────────────────────┐  │
│  │ Application & Domain: Unit of Work, EventEmitter Pub/Sub, Strategy Pattern │  │
│  └──────────────────────────────────────┬─────────────────────────────────────┘  │
│                                         │ implements repository contracts        │
│  ┌──────────────────────────────────────┴─────────────────────────────────────┐  │
│  │ Infrastructure: Mongoose Repositories, Soft Delete Plugin, Jose Signer     │  │
│  └──────────────────────────────────────┬─────────────────────────────────────┘  │
└─────────────────────────────────────────┼────────────────────────────────────────┘
                                          │ TLS / Replica Set Connection Pool
                               ┌──────────▼──────────┐
                               │    MONGODB ATLAS    │
                               │  Primary & Replicas │
                               └─────────────────────┘
```

### Key Architectural Patterns Implemented

1. **Offline-First & Eventual Consistency**:
   - The client embeds a complete local IndexedDB database powered by **Dexie.js 4.4**. Cashiers can process sales even during internet outages; transactions queue locally and reconcile via `syncEngine.js` once connection returns.
2. **Transactional Unit of Work (ACID Guarantee)**:
   - On the backend, stock decrements and invoice creations are executed inside an atomic `unitOfWork.js` transaction. If an item stock check fails, the entire transaction is aborted.
3. **Pluggable Strategy Patterns**:
   - **Receipt Strategies**: Pluggable formatting for thermal 80mm roll receipts vs. standard A4 tax invoices (`strategies/receipts/`).
   - **Tax Strategies**: Zero-rated, flat percentage, and VAT tax calculation engines (`strategies/tax/`).
4. **Domain Pub/Sub Event Bus**:
   - `AppEvents` (built on Node.js `EventEmitter`) decouples core checkout from secondary concerns like low-stock alerts, audit logging, and webhook broadcasts.
5. **Pure Mathematical Calculators**:
   - Financial algorithms (gross revenue, COGS, net margins, category valuations, tax, tender change) are isolated into pure functions inside `client/src/calculaters/`. They have zero side effects and are 100% testable.

---

## 📊 Mermaid Visual Models

### 1. High-Level Enterprise Topology

```mermaid
graph TB
    subgraph Terminals["Store Cashier Terminals & Hardware"]
        Desktop["🖥️ POS Cashier Terminal (Chrome Desktop)"]
        Tablet["📱 Manager Tablet (Safari / Chrome Mobile)"]
        Scanner["🔫 USB / Bluetooth Hardware Barcode Scanner"]
        Printer["🖨️ 80mm ESC/POS Thermal Receipt Printer"]
        Scanner -.->|Keystroke Emulation| Desktop
        Desktop -.->|Window Print Driver| Printer
    end

    subgraph ClientApp["Frontend Client (Port 3000 / Vite)"]
        ReactRouter["React Router v6 Navigation"]
        DexieDB["Dexie.js 4.4 IndexedDB Store\n(Offline Fallback & Local Queue)"]
        SyncEngine["Background Sync Engine\n(Auto-Reconciles Pending Invoices)"]
        ReactQuery["TanStack React Query Cache\n(Reactive Invalidation)"]
        Services["Domain Services Layer\n(product · bill · dealer · charge · user)"]
        Axios["Centralized Axios Client\n(Jose JWT Bearer Interceptor)"]
        
        Desktop --> ReactRouter
        Tablet --> ReactRouter
        ReactRouter --> DexieDB
        DexieDB --> SyncEngine
        ReactRouter --> ReactQuery
        ReactQuery --> Services
        Services --> Axios
        SyncEngine --> Axios
    end

    subgraph BackendApp["Backend Server (Port 8080 / Express 5)"]
        ExpressApp["Express 5 REST API Router"]
        AuthMid["Jose JWT authMiddleware\n(Bearer Token & RBAC Guard)"]
        Controllers["Domain Feature Controllers"]
        UOW["Transactional Unit of Work\n(ACID MongoDB Sessions)"]
        EventBus["EventEmitter Domain Event Bus\n(Pub/Sub Notifications)"]
        MongoModels["Mongoose 8 Schemas & Soft-Delete"]

        Axios -->|HTTP / JSON| ExpressApp
        ExpressApp --> AuthMid
        AuthMid --> Controllers
        Controllers --> UOW
        UOW --> MongoModels
        Controllers --> EventBus
    end

    subgraph DatabaseCluster["MongoDB Atlas Cloud"]
        MongoCluster[("MongoDB Replica Set\npos-mern Database\nitems · bills · dealers · charges · users")]
        MongoModels -->|TLS Encrypted Connection Pool| MongoCluster
    end
```

---

### 2. POS Transaction & Offline Fallback Sequence

```mermaid
sequenceDiagram
    autonumber
    actor Cashier
    participant POS as POS Client
    participant Service as billService.js
    participant Dexie as posDatabase.js (IndexedDB)
    participant API as Server /api/bill/add-bill
    participant DB as MongoDB Atlas

    Cashier->>POS: Adds items & presses F4 (Proceed to Pay)
    POS->>Service: createBill(billPayload)
    alt Terminal is Online
        Service->>API: POST /api/bill/add-bill
        API->>DB: Atomic Unit of Work (Decrement Stock + Save Bill)
        DB-->>API: Saved Bill
        API-->>Service: 201 Created
        Service->>Dexie: Update local product stock
        Service-->>POS: Show Confetti & Print Receipt
    else Network Lost (Offline Mode)
        Service->>Dexie: posDb.saveOfflineBill(billPayload)
        Dexie->>Dexie: Store bill in offline_bills queue
        Dexie->>Dexie: Decrement local stock in products table
        Dexie-->>Service: Offline invoice snapshot
        Service-->>POS: Show Confetti & Print Receipt (Zero Downtime!)
        Note over POS: Live Badge switches to Amber "Sync Pending"
    end
```

---

### 3. Multi-Role RBAC Authorization Flow

```mermaid
flowchart TD
    A["👤 User Access Request"] --> B{"Has Valid JWT Token\nin localStorage.auth?"}
    B -->|"❌ No"| C["Redirect to /login"]
    B -->|"✅ Yes"| D["Decode Jose JWT & Inspect Role"]
    D --> E{"Role == 'admin'?"}
    E -->|"✅ Yes"| F["Grant Full Access\nPOS · Invoices · Catalog · Stock BI · Dealers · Charges · User Management"]
    E -->|"❌ No (Cashier)"| G{"Requested Route Protected\nfor Admin Only?"}
    G -->|"Yes (/users, /stock, /charges)"| H["🚫 403 Forbidden / Redirect to POS"]
    G -->|"No (/, /cart, /bills)"| I["✅ Render Front-Desk Operational Interface"]
```

---

## 🗄️ Database Design

### Comprehensive Entity Relationship Diagram (ERD)

```mermaid
erDiagram
    USERS ||--o{ BILLS : "registers / bills"
    BILLS ||--|{ CART_ITEMS : "records"
    ITEMS ||--o{ CART_ITEMS : "references"
    DEALERS ||--o{ ITEMS : "supplies"
    CHARGES }o--|| USERS : "logged by"

    USERS {
        ObjectId _id PK "Unique user identifier"
        String name "Full staff name (e.g. Haider Ali)"
        String userId UK "Login identifier (e.g. admin, 1001)"
        String password "Cryptographically hashed via Bcrypt"
        String role "admin, manager, or cashier"
        Boolean active "Status flag (true = active, false = suspended)"
        Boolean isDeleted "Soft delete flag"
        Date createdAt "Timestamp"
        Date updatedAt "Timestamp"
    }

    ITEMS {
        ObjectId _id PK "Unique inventory item identifier"
        String name "Product display name"
        Number purchasePrice "Wholesale cost price (PKR)"
        Number salePrice "Retail sale price (PKR)"
        Number stock "Available inventory on-hand"
        Number reorderLevel "Threshold alert level (Default: 5)"
        String category "Classification (Shower, Pipe, Tools, etc.)"
        String barcode "EAN-13 / UPC barcode string"
        String sku "Internal SKU reference code"
        String image "Product thumbnail URL"
        Boolean isDeleted "Soft delete flag"
        Date createdAt "Timestamp"
        Date updatedAt "Timestamp"
    }

    BILLS {
        ObjectId _id PK "Invoice record unique identifier"
        String costumerName "Customer billing name"
        String costumerNumber "Customer phone number"
        Number totalAmount "Gross invoice payable (PKR)"
        Number paidAmount "Cash amount received (PKR)"
        String paymentMethod "cash, card, or borrow"
        Array cartItems "Snapshot of purchased line items"
        Date date "Sale timestamp"
        Boolean isVoided "Voided transaction flag"
        Date createdAt "Timestamp"
        Date updatedAt "Timestamp"
    }

    CART_ITEMS {
        ObjectId _id "Referenced Product ID"
        String name "Product name at sale time"
        Number salePrice "Unit sale price at sale time"
        Number purchasePrice "Unit cost price at sale time"
        Number quantity "Units purchased"
    }

    DEALERS {
        ObjectId _id PK "Vendor record identifier"
        String dealerName "Wholesale company name"
        String contactName "Vendor representative"
        String shopName "Shop branch or warehouse name"
        String address "Physical address"
        String products "Supplied product lines"
        Date createdAt "Timestamp"
        Date updatedAt "Timestamp"
    }

    CHARGES {
        ObjectId _id PK "Expense entry identifier"
        String description "Expense purpose (Electricity, Wages, Rent)"
        Number amount "Expense amount (PKR)"
        Date date "Expense timestamp"
        Date createdAt "Timestamp"
        Date updatedAt "Timestamp"
    }
```

---

## 📂 Complete Folder Structure

```
📂 mern-pos/                                      # Monorepo Workspace Root
├── 📄 .env.example                               # Global environment template
├── 📄 CONTRIBUTING.md                            # Contributor guidelines
├── 📄 LICENSE                                    # Custom Permission-Based License
├── 📄 package.json                               # Monorepo scripts (dev:server, dev:client, seed)
├── 📄 README.md                                  # Root architectural blueprint
│
├── 📁 server/                                    # Express 5 & Mongoose 8 Backend
│   ├── 📄 .env.example                           # Server environment blueprint
│   ├── 📄 package.json                           # Dependencies & scripts
│   ├── 📄 app.js                                 # Express 5 application factory
│   ├── 📄 server.js                              # HTTP server lifecycle & DNS resolver
│   ├── 📄 seeders.js                             # Multi-tenant catalog & user seeder
│   └── 📁 src/                                   # Clean Modular Architecture
│       ├── 📁 core/                              # Cross-cutting concerns
│       │   ├── 📁 database/                      # connection.js, tenantManager.js, unitOfWork.js
│       │   ├── 📁 errors/                        # AppError.js & centralized errorHandler.js
│       │   ├── 📁 events/                        # eventEmitter.js & eventTypes.js pub/sub
│       │   ├── 📁 middlewares/                   # authenticate.js, requirePermission.js
│       │   ├── 📁 models/                        # Item.js, Bill.js, Dealer.js, Charge.js, User.js
│       │   └── 📁 security/                      # JoseTokenSigner.js
│       ├── 📁 modules/                           # Feature modules
│       │   ├── 📁 auth/                          # auth.controller.js, auth.routes.js, auth.service.js
│       │   ├── 📁 billing/                       # billing.controller.js, billing.routes.js, billing.service.js
│       │   ├── 📁 inventory/                     # inventory.controller.js, inventory.routes.js
│       │   ├── 📁 expenses/                      # charges & dealers controllers, routes, services
│       │   └── 📁 tenant-admin/                  # tenant & user management controllers, routes
│       └── 📁 strategies/                        # Strategy patterns (receipts/ & tax/)
│
└── 📁 client/                                    # React 18 & Vite Frontend
    ├── 📄 .env.example                           # Frontend environment blueprint
    ├── 📄 package.json                           # Vite 8, React 18, AntD 5, Dexie 4 dependencies
    ├── 📄 vite.config.js                         # Vite build & proxy configuration
    ├── 📄 README.md                              # Dedicated frontend architecture docs
    └── 📁 src/                                   # 4-Tier Clean Frontend Architecture
        ├── 📄 App.jsx                            # ConfigProvider theme tokens & routes
        ├── 📄 index.jsx                          # Root render, TanStack QueryClientProvider
        ├── 📄 index.css                          # Modern typography reset & print rules
        ├── 📁 api/                               # Axios HTTP client with Bearer token interceptor
        ├── 📁 db/                                # Dexie.js 4.4 IndexedDB database (posDatabase.js)
        ├── 📁 services/                          # productService.js, billService.js, syncEngine.js
        ├── 📁 handlers/                          # posHandlers.js, billsHandlers.js, cartHandlers.js
        ├── 📁 calculaters/                       # Pure math: stockCalculations.js, billCalculations.js
        ├── 📁 hooks/                             # usePosQueries.js, useBarcodeScanner.js, usePosShortcuts.js
        ├── 📁 redux/                             # Redux store & persistent cart slice
        ├── 📁 pages/                             # Homepage, Cartpage, Billspage, Itempage, Stockpage, Users
        ├── 📁 components/                        # Defaultlayouts.jsx, PrivateRoute.jsx, ItemsList.jsx
        └── 📁 styles/                            # Pos.css, Defaultlayouts.css
```

---

## 📡 REST API Reference

**Base URL:** `http://localhost:8080/api`

| Module | Method | Endpoint | Access Level | Description |
|---|---|---|:---:|---|
| **Auth** | `POST` | `/users/login` | Public | Authenticate operator, returns Jose JWT & user profile |
| **Auth** | `POST` | `/users/register` | Public | Operator self-registration |
| **Auth** | `POST` | `/users/reset-password` | Public | Credential reset with verification |
| **Users** | `GET` | `/users/get-users` | `admin` | Retrieve all staff accounts |
| **Users** | `POST` | `/users/admin-create` | `admin` | Provision a new operator with role |
| **Users** | `PATCH` | `/users/toggle-status` | `admin` | Suspend or reactivate staff account |
| **Users** | `PATCH` | `/users/update-role` | `admin` | Promote or demote operator (`cashier` / `manager` / `admin`) |
| **Users** | `DELETE` | `/users/delete/:userId` | `admin` | Delete operator account |
| **Catalog** | `GET` | `/items/get-item` | Public | Fetch product catalog with live stock counts |
| **Catalog** | `POST` | `/items/add-item` | Authenticated | Create a new inventory record |
| **Catalog** | `PUT` | `/items/edit-item` | Authenticated | Update product prices, stock, or reorder threshold |
| **Catalog** | `POST` | `/items/delete-item` | Authenticated | Soft-delete inventory item |
| **Billing** | `GET` | `/bill/get-bill` | Authenticated | Retrieve customer invoices and transaction records |
| **Billing** | `POST` | `/bill/add-bill` | Authenticated | Complete checkout with atomic stock decrement |
| **Billing** | `POST` | `/bill/void-bill/:id` | Manager/Admin | Void transaction & restore stock |
| **Dealers** | `GET` | `/dealers/get-dealers` | Authenticated | List all wholesale suppliers |
| **Charges** | `GET` | `/charges/get-charges` | Authenticated | Retrieve store operating expenses |

---

## 🚀 Getting Started

### 1. Prerequisites
- **Node.js**: `v18.x` or `v20.x`
- **npm**: `v9.x` or later
- **MongoDB**: MongoDB Atlas URI or local replica set

### 2. Quick Setup & Installation

From the monorepo root:

```bash
# 1. Install all dependencies across root, client, and server
npm run install:all

# 2. Configure environment files
cp server/.env.example server/.env
cp client/.env.example client/.env

# 3. Seed initial products, store settings, and default admin user
npm run seed
```

### 3. Launch Full-Stack Development

```bash
npm run dev
```

- **Frontend Client**: `http://localhost:3000`
- **Backend API**: `http://localhost:8080`
- **Health Check**: `http://localhost:8080/health`

Default credentials:
- **Admin**: User ID `admin` · Password `test123`
- **Cashier**: User ID `1001` · Password `test123`

---

## 📄 License

This software is governed by the **Hardware Point POS Permission-Based Non-Commercial License**.

- **Permission Required**: You must request and receive prior written consent from author [Haider Ali](https://github.com/Haiderali445) before running, modifying, or distributing this software.
- **Non-Paid**: Free of monetary subscription charges for approved personal, research, and non-commercial educational use.
- **No Commercial Resale**: Commercial redistribution, sale, or SaaS deployment is strictly prohibited without an executed commercial license.

See the full [LICENSE](file:///d:/mern-pos/LICENSE) file for complete terms.

Copyright © 2024–2026 [Haider Ali](https://github.com/Haiderali445). All rights reserved.
