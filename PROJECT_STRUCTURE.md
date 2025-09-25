# Voice Restaurant Project Structure & Deployment Guide

## Overview
Complete voice-powered restaurant ordering system with 3 separate projects deployed on Contavo VPS with Nginx.

## Project Architecture - API-DRIVEN DESIGN

```
Voice Restaurant System (100% API-Based)
├── Project 1: voice-restaurant-frontend (React)
├── Project 2: voice-restaurant-mcp (MCP Server) - NO DATABASE ACCESS
├── Project 3: restaurant-voice-erpnext (ERPNext Custom App)
└── Deployment: Nginx + PM2 on Contavo VPS

Data Flow:
Frontend → MCP Server (API Proxy) → ERPNext APIs → Database (MariaDB on ERPNext)

IMPORTANT: MCP Server has ZERO direct database connections!
```

## MCP Server Architecture - API PROXY ONLY

### Core Principle
The MCP Server acts as a **Smart API Proxy** that:
- ✅ Receives requests from React frontend
- ✅ Processes voice commands via Ultravox
- ✅ Forwards ALL data operations to ERPNext APIs
- ✅ Caches responses in Redis for performance
- ✅ Manages WebSocket connections for real-time updates
- ❌ **NEVER** connects directly to any database
- ❌ **NO** database drivers (mysql, postgres, etc.)
- ❌ **NO** ORM (Prisma, TypeORM, Sequelize)

### What MCP Server Does:
```typescript
// CORRECT: API calls to ERPNext
const response = await axios.post('https://navigo.qastco.com/api/resource/Sales%20Order', orderData);

// WRONG: Direct database queries (DON'T DO THIS)
// const order = await db.query('INSERT INTO tabSales Order...');
```

## Project 1: Voice Restaurant Frontend (React)

### Repository Structure
```
voice-restaurant-frontend/
├── public/
│   ├── index.html
│   ├── favicon.ico
│   └── manifest.json
├── src/
│   ├── components/
│   │   ├── auth/
│   │   │   ├── LoginForm.tsx
│   │   │   ├── RegisterForm.tsx
│   │   │   ├── ForgotPassword.tsx
│   │   │   ├── ResetPassword.tsx
│   │   │   └── GuestCheckout.tsx
│   │   ├── voice/
│   │   │   ├── VoiceRecorder.tsx
│   │   │   ├── VoicePlayer.tsx
│   │   │   ├── VoiceStatus.tsx
│   │   │   └── VoiceOrdering.tsx
│   │   ├── menu/
│   │   │   ├── MenuDisplay.tsx
│   │   │   ├── MenuItem.tsx
│   │   │   ├── MenuCategory.tsx
│   │   │   └── MenuSearch.tsx
│   │   ├── order/
│   │   │   ├── OrderSummary.tsx
│   │   │   ├── OrderHistory.tsx
│   │   │   ├── OrderTracking.tsx
│   │   │   └── OrderConfirmation.tsx
│   │   ├── payment/
│   │   │   ├── PaymentForm.tsx
│   │   │   ├── PaymentMethods.tsx
│   │   │   └── PaymentSuccess.tsx
│   │   ├── profile/
│   │   │   ├── UserProfile.tsx
│   │   │   ├── VoiceSettings.tsx
│   │   │   └── OrderPreferences.tsx
│   │   └── common/
│   │       ├── Header.tsx
│   │       ├── Footer.tsx
│   │       ├── Loading.tsx
│   │       ├── ErrorBoundary.tsx
│   │       └── ProtectedRoute.tsx
│   ├── hooks/
│   │   ├── useAuth.ts
│   │   ├── useVoice.ts
│   │   ├── useOrder.ts
│   │   ├── useMenu.ts
│   │   └── usePayment.ts
│   ├── services/
│   │   ├── api/
│   │   │   ├── auth.ts
│   │   │   ├── menu.ts
│   │   │   ├── order.ts
│   │   │   ├── payment.ts
│   │   │   └── voice.ts
│   │   ├── ultravox/
│   │   │   ├── ultravoxClient.ts
│   │   │   └── voiceHandler.ts
│   │   └── utils/
│   │       ├── storage.ts
│   │       ├── validation.ts
│   │       └── helpers.ts
│   ├── store/
│   │   ├── slices/
│   │   │   ├── authSlice.ts
│   │   │   ├── menuSlice.ts
│   │   │   ├── orderSlice.ts
│   │   │   ├── voiceSlice.ts
│   │   │   └── uiSlice.ts
│   │   └── index.ts
│   ├── types/
│   │   ├── auth.ts
│   │   ├── menu.ts
│   │   ├── order.ts
│   │   ├── voice.ts
│   │   └── api.ts
│   ├── pages/
│   │   ├── Home.tsx
│   │   ├── Menu.tsx
│   │   ├── VoiceOrder.tsx
│   │   ├── Cart.tsx
│   │   ├── Checkout.tsx
│   │   ├── Profile.tsx
│   │   ├── OrderHistory.tsx
│   │   ├── Login.tsx
│   │   ├── Register.tsx
│   │   └── NotFound.tsx
│   ├── styles/
│   │   ├── globals.css
│   │   ├── components.css
│   │   └── voice.css
│   ├── App.tsx
│   ├── index.tsx
│   └── setupTests.ts
├── package.json
├── tsconfig.json
├── tailwind.config.js
├── .env.example
├── .env.local
├── .gitignore
├── README.md
└── Dockerfile
```

### Key Features
- **Guest Ordering**: Complete ordering without registration
- **User Authentication**: Login, register, forgot/reset password
- **Voice Ordering**: Integration with Ultravox AI
- **Menu Management**: Dynamic menu display
- **Order Management**: Cart, checkout, tracking
- **Payment Integration**: Multiple payment methods
- **User Profile**: Preferences, order history
- **Responsive Design**: Mobile-first approach

### Dependencies
```json
{
  "dependencies": {
    "@reduxjs/toolkit": "^1.9.7",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-redux": "^8.1.3",
    "react-router-dom": "^6.16.0",
    "@tanstack/react-query": "^4.35.3",
    "axios": "^1.5.1",
    "ultravox-client": "latest",
    "@headlessui/react": "^1.7.17",
    "@heroicons/react": "^2.0.18",
    "react-hook-form": "^7.47.0",
    "yup": "^1.3.3",
    "@hookform/resolvers": "^3.3.1",
    "react-hot-toast": "^2.4.1",
    "socket.io-client": "^4.7.2",
    "tailwindcss": "^3.3.5",
    "typescript": "^4.9.5"
  }
}
```

## Project 2: Voice Restaurant MCP Server (Node.js)

### Repository Structure
```
voice-restaurant-mcp/
├── src/
│   ├── controllers/
│   │   ├── voiceController.ts
│   │   ├── orderController.ts
│   │   ├── menuController.ts
│   │   ├── authController.ts
│   │   └── webhookController.ts
│   ├── services/
│   │   ├── ultravoxService.ts
│   │   ├── erpnextService.ts
│   │   ├── voiceProcessor.ts
│   │   ├── orderService.ts
│   │   ├── authService.ts
│   │   └── cacheService.ts
│   ├── middleware/
│   │   ├── auth.ts
│   │   ├── validation.ts
│   │   ├── rateLimit.ts
│   │   ├── cors.ts
│   │   └── errorHandler.ts
│   ├── routes/
│   │   ├── voice.ts
│   │   ├── order.ts
│   │   ├── menu.ts
│   │   ├── auth.ts
│   │   └── webhook.ts
│   ├── types/
│   │   ├── erpnext.ts      # ERPNext API response types
│   │   ├── voice.ts        # Voice processing types
│   │   ├── order.ts        # Order processing types
│   │   └── ultravox.ts     # Ultravox SDK types
│   ├── utils/
│   │   ├── erpnextClient.ts # ERPNext API client (NO DATABASE)
│   │   ├── logger.ts
│   │   ├── validator.ts
│   │   ├── cache.ts        # Redis cache only
│   │   └── helpers.ts
│   ├── websocket/
│   │   ├── socketHandler.ts
│   │   ├── voiceSocket.ts
│   │   └── orderSocket.ts
│   ├── config/
│   │   ├── redis.ts        # Redis for caching only
│   │   ├── ultravox.ts     # Ultravox configuration
│   │   └── erpnext.ts      # ERPNext API configuration (NO DATABASE)
│   └── app.ts
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── package.json
├── tsconfig.json
├── .env.example
├── .env
├── .gitignore
├── README.md
├── Dockerfile
└── ecosystem.config.js (PM2)
```

### Key Features - 100% API-DRIVEN (NO DATABASE ACCESS)
- **MCP Protocol Implementation**: AI orchestration layer
- **Ultravox Integration**: Voice processing and real-time communication
- **ERPNext API Integration**: ALL data operations via navigo.qastco.com APIs
- **WebSocket Support**: Real-time updates for voice sessions
- **Redis Caching**: ONLY for temporary session data and API response caching
- **API Proxy Layer**: Routes all requests to ERPNext APIs
- **Authentication**: Proxies auth through ERPNext + session management
- **Rate Limiting**: API protection and throttling
- **Webhook Handling**: ERPNext webhook processing

### Data Flow Architecture
```
React Frontend → MCP Server → ERPNext APIs (navigo.qastco.com) → MariaDB
     ↑                ↓
     └── WebSocket ←──┘

NO DIRECT DATABASE ACCESS IN MCP SERVER
ALL DATA OPERATIONS VIA ERPNEXT REST APIS
```

### Dependencies (API-Focused)
```json
{
  "dependencies": {
    "express": "^4.18.2",
    "socket.io": "^4.7.2",
    "axios": "^1.5.1",           // For ERPNext API calls
    "jsonwebtoken": "^9.0.2",    // Session management only
    "redis": "^4.6.8",           // Cache layer only (NO persistence)
    "joi": "^17.10.2",           // Request validation
    "cors": "^2.8.5",
    "helmet": "^7.0.0",
    "express-rate-limit": "^6.10.0",
    "winston": "^3.10.0",        // Logging
    "ultravox-sdk": "latest",    // Voice processing
    "node-cron": "^3.0.2",       // Cache cleanup tasks
    "typescript": "^4.9.5",
    "@types/node": "^18.17.15"
  }
}
```

## Project 3: Restaurant Voice ERPNext App

### Repository Structure
```
restaurant_voice/
├── restaurant_voice/
│   ├── __init__.py
│   ├── hooks.py
│   ├── modules.txt
│   ├── patches.txt
│   └── restaurant_voice/
│       ├── doctype/
│       │   ├── voice_session/
│       │   │   ├── __init__.py
│       │   │   ├── voice_session.json
│       │   │   ├── voice_session.py
│       │   │   ├── voice_session.js
│       │   │   └── test_voice_session.py
│       │   ├── restaurant_table/
│       │   │   ├── __init__.py
│       │   │   ├── restaurant_table.json
│       │   │   ├── restaurant_table.py
│       │   │   ├── restaurant_table.js
│       │   │   └── test_restaurant_table.py
│       │   ├── voice_order/
│       │   │   ├── __init__.py
│       │   │   ├── voice_order.json
│       │   │   ├── voice_order.py
│       │   │   ├── voice_order.js
│       │   │   └── test_voice_order.py
│       │   └── restaurant_menu_category/
│       │       ├── __init__.py
│       │       ├── restaurant_menu_category.json
│       │       ├── restaurant_menu_category.py
│       │       └── restaurant_menu_category.js
│       ├── api/
│       │   ├── __init__.py
│       │   ├── voice_api.py
│       │   ├── menu_api.py
│       │   ├── order_api.py
│       │   └── auth_api.py
│       ├── custom_fields/
│       │   ├── __init__.py
│       │   ├── item_fields.py
│       │   ├── sales_order_fields.py
│       │   └── customer_fields.py
│       ├── fixtures/
│       │   ├── __init__.py
│       │   └── sample_data.json
│       ├── public/
│       │   ├── css/
│       │   ├── js/
│       │   └── images/
│       ├── templates/
│       │   ├── voice_order.html
│       │   └── menu_display.html
│       └── utils/
│           ├── __init__.py
│           ├── voice_utils.py
│           ├── order_utils.py
│           └── menu_utils.py
├── setup.py
├── requirements.txt
├── README.md
└── license.txt
```

## ERPNext Doctypes & Custom Fields

### Custom Doctypes

#### 1. Voice Session
```json
{
  "doctype": "Voice Session",
  "module": "Restaurant Voice",
  "fields": [
    {"fieldname": "session_id", "fieldtype": "Data", "unique": 1, "reqd": 1},
    {"fieldname": "customer", "fieldtype": "Link", "options": "Customer"},
    {"fieldname": "table_number", "fieldtype": "Link", "options": "Restaurant Table"},
    {"fieldname": "status", "fieldtype": "Select", "options": "Active\nCompleted\nAbandoned\nPaused"},
    {"fieldname": "language", "fieldtype": "Data", "default": "en"},
    {"fieldname": "conversation_log", "fieldtype": "Long Text"},
    {"fieldname": "current_order", "fieldtype": "Link", "options": "Sales Order"},
    {"fieldname": "voice_preferences", "fieldtype": "JSON"},
    {"fieldname": "session_start", "fieldtype": "Datetime"},
    {"fieldname": "session_end", "fieldtype": "Datetime"},
    {"fieldname": "total_duration", "fieldtype": "Int"},
    {"fieldname": "order_total", "fieldtype": "Currency"}
  ],
  "permissions": [
    {"role": "Restaurant Manager", "read": 1, "write": 1, "create": 1}
  ]
}
```

#### 2. Restaurant Table
```json
{
  "doctype": "Restaurant Table",
  "module": "Restaurant Voice",
  "fields": [
    {"fieldname": "table_number", "fieldtype": "Data", "unique": 1, "reqd": 1},
    {"fieldname": "table_name", "fieldtype": "Data"},
    {"fieldname": "seating_capacity", "fieldtype": "Int", "default": 4},
    {"fieldname": "status", "fieldtype": "Select", "options": "Available\nOccupied\nReserved\nMaintenance"},
    {"fieldname": "qr_code", "fieldtype": "Data"},
    {"fieldname": "current_session", "fieldtype": "Link", "options": "Voice Session"},
    {"fieldname": "location", "fieldtype": "Data"},
    {"fieldname": "is_voice_enabled", "fieldtype": "Check", "default": 1}
  ],
  "permissions": [
    {"role": "Restaurant Manager", "read": 1, "write": 1, "create": 1}
  ]
}
```

#### 3. Voice Order
```json
{
  "doctype": "Voice Order",
  "module": "Restaurant Voice",
  "fields": [
    {"fieldname": "voice_session", "fieldtype": "Link", "options": "Voice Session", "reqd": 1},
    {"fieldname": "sales_order", "fieldtype": "Link", "options": "Sales Order"},
    {"fieldname": "order_type", "fieldtype": "Select", "options": "Dine In\nTakeaway\nDelivery"},
    {"fieldname": "voice_command", "fieldtype": "Long Text"},
    {"fieldname": "processed_items", "fieldtype": "Table", "options": "Voice Order Item"},
    {"fieldname": "special_instructions", "fieldtype": "Text"},
    {"fieldname": "confidence_score", "fieldtype": "Percent"},
    {"fieldname": "processing_time", "fieldtype": "Float"},
    {"fieldname": "requires_confirmation", "fieldtype": "Check"}
  ]
}
```

#### 4. Restaurant Menu Category
```json
{
  "doctype": "Restaurant Menu Category",
  "module": "Restaurant Voice",
  "fields": [
    {"fieldname": "category_name", "fieldtype": "Data", "reqd": 1},
    {"fieldname": "description", "fieldtype": "Text"},
    {"fieldname": "display_order", "fieldtype": "Int"},
    {"fieldname": "is_active", "fieldtype": "Check", "default": 1},
    {"fieldname": "voice_aliases", "fieldtype": "Small Text"},
    {"fieldname": "image", "fieldtype": "Attach Image"},
    {"fieldname": "availability_time", "fieldtype": "Table", "options": "Menu Availability"}
  ]
}
```

### Custom Fields for Existing Doctypes

#### Item (Menu Items)
```python
custom_fields = {
    "Item": [
        {
            "fieldname": "restaurant_section",
            "label": "Restaurant Section",
            "fieldtype": "Section Break"
        },
        {
            "fieldname": "is_menu_item",
            "label": "Is Menu Item",
            "fieldtype": "Check"
        },
        {
            "fieldname": "voice_aliases",
            "label": "Voice Aliases (comma separated)",
            "fieldtype": "Small Text",
            "description": "Alternative names for voice recognition"
        },
        {
            "fieldname": "prep_time",
            "label": "Preparation Time (minutes)",
            "fieldtype": "Int"
        },
        {
            "fieldname": "dietary_info",
            "label": "Dietary Information",
            "fieldtype": "Select",
            "options": "\nVegetarian\nVegan\nGluten Free\nDairy Free\nNut Free\nHalal\nKosher"
        },
        {
            "fieldname": "spice_level",
            "label": "Spice Level",
            "fieldtype": "Select",
            "options": "\nMild\nMedium\nHot\nExtra Hot"
        },
        {
            "fieldname": "available_for_voice",
            "label": "Available for Voice Ordering",
            "fieldtype": "Check",
            "default": 1
        },
        {
            "fieldname": "menu_category",
            "label": "Menu Category",
            "fieldtype": "Link",
            "options": "Restaurant Menu Category"
        },
        {
            "fieldname": "popularity_score",
            "label": "Popularity Score",
            "fieldtype": "Int",
            "default": 0
        }
    ]
}
```

#### Sales Order (Voice Orders)
```python
custom_fields = {
    "Sales Order": [
        {
            "fieldname": "voice_order_section",
            "label": "Voice Order Details",
            "fieldtype": "Section Break"
        },
        {
            "fieldname": "voice_session_id",
            "label": "Voice Session ID",
            "fieldtype": "Data",
            "read_only": 1
        },
        {
            "fieldname": "order_source",
            "label": "Order Source",
            "fieldtype": "Select",
            "options": "\nVoice AI\nWeb\nMobile\nPhone\nWalk-in"
        },
        {
            "fieldname": "table_number",
            "label": "Table Number",
            "fieldtype": "Link",
            "options": "Restaurant Table"
        },
        {
            "fieldname": "special_instructions",
            "label": "Special Instructions",
            "fieldtype": "Text"
        },
        {
            "fieldname": "estimated_delivery_time",
            "label": "Estimated Delivery Time",
            "fieldtype": "Datetime"
        },
        {
            "fieldname": "voice_confidence_score",
            "label": "Voice Recognition Confidence",
            "fieldtype": "Percent"
        },
        {
            "fieldname": "guest_order",
            "label": "Guest Order",
            "fieldtype": "Check"
        },
        {
            "fieldname": "guest_phone",
            "label": "Guest Phone",
            "fieldtype": "Data"
        },
        {
            "fieldname": "guest_name",
            "label": "Guest Name",
            "fieldtype": "Data"
        }
    ]
}
```

#### Customer (Voice Customers)
```python
custom_fields = {
    "Customer": [
        {
            "fieldname": "voice_preferences_section",
            "label": "Voice Preferences",
            "fieldtype": "Section Break"
        },
        {
            "fieldname": "preferred_name",
            "label": "Preferred Name for Voice",
            "fieldtype": "Data"
        },
        {
            "fieldname": "voice_language",
            "label": "Preferred Voice Language",
            "fieldtype": "Select",
            "options": "en\nes\nfr\nde\nit\npt\nru\nzh\nja\nko"
        },
        {
            "fieldname": "dietary_restrictions",
            "label": "Dietary Restrictions",
            "fieldtype": "Text"
        },
        {
            "fieldname": "favorite_items",
            "label": "Favorite Items",
            "fieldtype": "Table",
            "options": "Customer Favorite Item"
        },
        {
            "fieldname": "voice_ordering_enabled",
            "label": "Voice Ordering Enabled",
            "fieldtype": "Check",
            "default": 1
        },
        {
            "fieldname": "total_voice_orders",
            "label": "Total Voice Orders",
            "fieldtype": "Int",
            "read_only": 1
        }
    ]
}
```

## API Endpoints Documentation

### Authentication APIs

#### ERPNext Authentication
```python
# /api/method/restaurant_voice.api.auth_api.login
POST /api/method/restaurant_voice.api.auth_api.login
{
    "usr": "user@example.com",
    "pwd": "password"
}

# /api/method/restaurant_voice.api.auth_api.register
POST /api/method/restaurant_voice.api.auth_api.register
{
    "email": "user@example.com",
    "first_name": "John",
    "last_name": "Doe",
    "phone": "+1234567890",
    "password": "password123"
}

# /api/method/restaurant_voice.api.auth_api.forgot_password
POST /api/method/restaurant_voice.api.auth_api.forgot_password
{
    "email": "user@example.com"
}

# /api/method/restaurant_voice.api.auth_api.reset_password
POST /api/method/restaurant_voice.api.auth_api.reset_password
{
    "key": "reset_key",
    "new_password": "newpassword123"
}

# /api/method/restaurant_voice.api.auth_api.verify_token
GET /api/method/restaurant_voice.api.auth_api.verify_token
Headers: Authorization: Bearer <token>
```

#### MCP Server Authentication
```javascript
// POST /api/auth/login
{
    "email": "user@example.com",
    "password": "password"
}

// POST /api/auth/register
{
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "phone": "+1234567890",
    "password": "password123"
}

// POST /api/auth/guest-session
{
    "guestName": "John",
    "guestPhone": "+1234567890"
}

// POST /api/auth/forgot-password
{
    "email": "user@example.com"
}

// POST /api/auth/reset-password
{
    "token": "reset_token",
    "password": "newpassword123"
}

// GET /api/auth/verify
Headers: Authorization: Bearer <token>
```

### Menu APIs

#### ERPNext Menu APIs
```python
# Get menu items
GET /api/resource/Item?filters=[["is_menu_item", "=", 1], ["available_for_voice", "=", 1]]

# Get menu categories
GET /api/resource/Restaurant%20Menu%20Category?filters=[["is_active", "=", 1]]

# Get item details
GET /api/resource/Item/{item_code}

# Search menu items
GET /api/method/restaurant_voice.api.menu_api.search_items?query=pizza

# Get popular items
GET /api/method/restaurant_voice.api.menu_api.get_popular_items
```

#### MCP Server Menu APIs
```javascript
// GET /api/menu/categories
// GET /api/menu/items?category=appetizers
// GET /api/menu/items/{itemCode}
// GET /api/menu/search?q=pizza
// GET /api/menu/popular
// GET /api/menu/voice-aliases/{alias}
```

### Voice APIs

#### MCP Server Voice APIs
```javascript
// POST /api/voice/start-session
{
    "tableNumber": "T01",
    "customerId": "optional",
    "language": "en"
}

// POST /api/voice/process-command
{
    "sessionId": "vs_123456",
    "voiceData": "base64_audio",
    "command": "I want two margherita pizzas"
}

// GET /api/voice/session/{sessionId}

// POST /api/voice/confirm-order
{
    "sessionId": "vs_123456",
    "confirmed": true
}

// WebSocket /ws/voice/{sessionId}
```

#### ERPNext Voice APIs
```python
# Create voice session
POST /api/method/restaurant_voice.api.voice_api.create_session
{
    "table_number": "T01",
    "customer": "optional"
}

# Process voice command
POST /api/method/restaurant_voice.api.voice_api.process_command
{
    "session_id": "vs_123456",
    "command": "I want two margherita pizzas",
    "confidence": 0.95
}

# Get session details
GET /api/method/restaurant_voice.api.voice_api.get_session?session_id=vs_123456
```

### Order APIs

#### ERPNext Order APIs
```python
# Create order
POST /api/resource/Sales%20Order
{
    "customer": "WALK-IN-001",
    "voice_session_id": "vs_123456",
    "order_source": "Voice AI",
    "guest_order": 1,
    "guest_name": "John",
    "guest_phone": "+1234567890",
    "items": [
        {
            "item_code": "PIZZA_001",
            "qty": 2,
            "rate": 12.99
        }
    ]
}

# Get order details
GET /api/resource/Sales%20Order/{order_name}

# Update order status
PUT /api/resource/Sales%20Order/{order_name}
{
    "status": "Confirmed"
}

# Get order history
GET /api/method/restaurant_voice.api.order_api.get_order_history?customer=CUST-001
```

#### MCP Server Order APIs
```javascript
// POST /api/order/create
{
    "sessionId": "vs_123456",
    "items": [
        {
            "itemCode": "PIZZA_001",
            "quantity": 2,
            "specialInstructions": "Extra cheese"
        }
    ],
    "orderType": "dine_in",
    "guestInfo": {
        "name": "John",
        "phone": "+1234567890"
    }
}

// GET /api/order/{orderId}
// PUT /api/order/{orderId}/status
// GET /api/order/history?customerId=123
// POST /api/order/guest-tracking
```

### Payment APIs

#### ERPNext Payment APIs
```python
# Create payment entry
POST /api/resource/Payment%20Entry
{
    "payment_type": "Receive",
    "party_type": "Customer",
    "party": "WALK-IN-001",
    "paid_amount": 25.98,
    "references": [
        {
            "reference_doctype": "Sales Order",
            "reference_name": "SO-2024-001",
            "allocated_amount": 25.98
        }
    ]
}

# Process card payment
POST /api/method/restaurant_voice.api.payment_api.process_payment
{
    "order_id": "SO-2024-001",
    "payment_method": "card",
    "amount": 25.98,
    "payment_details": {}
}
```

#### MCP Server Payment APIs
```javascript
// POST /api/payment/process
{
    "orderId": "SO-2024-001",
    "paymentMethod": "card",
    "amount": 25.98,
    "paymentToken": "stripe_token"
}

// GET /api/payment/methods
// POST /api/payment/refund
```

## Deployment Structure on Contavo VPS

### Server Setup
```bash
# Contavo VPS - Ubuntu 20.04/22.04
IP: your_server_ip
Domain: yourdomain.com (optional)

# Directory Structure
/var/www/
├── voice-restaurant-frontend/     # React build files
├── voice-restaurant-mcp/          # MCP Server
└── ssl/                          # SSL certificates
```

### Nginx Configuration
```nginx
# /etc/nginx/sites-available/voice-restaurant
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name yourdomain.com www.yourdomain.com;

    ssl_certificate /var/www/ssl/fullchain.pem;
    ssl_certificate_key /var/www/ssl/privkey.pem;

    # React Frontend
    location / {
        root /var/www/voice-restaurant-frontend/build;
        index index.html;
        try_files $uri $uri/ /index.html;
    }

    # MCP Server API
    location /api/ {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    # WebSocket for voice
    location /ws/ {
        proxy_pass http://localhost:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # ERPNext Proxy
    location /erpnext/ {
        proxy_pass https://navigo.qastco.com/;
        proxy_set_header Host navigo.qastco.com;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### PM2 Configuration
```javascript
// ecosystem.config.js
module.exports = {
  apps: [{
    name: 'voice-restaurant-mcp',
    script: './dist/app.js',
    cwd: '/var/www/voice-restaurant-mcp',
    instances: 2,
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production',
      PORT: 3001
    },
    error_file: '/var/log/pm2/voice-restaurant-mcp.error.log',
    out_file: '/var/log/pm2/voice-restaurant-mcp.out.log',
    log_file: '/var/log/pm2/voice-restaurant-mcp.log'
  }]
};
```

## Step-by-Step Deployment Guide

### Step 1: Server Preparation
```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install required packages
sudo apt install -y nginx nodejs npm git redis-server

# Install PM2
sudo npm install -g pm2

# Create directories
sudo mkdir -p /var/www/{voice-restaurant-frontend,voice-restaurant-mcp}
sudo chown -R $USER:$USER /var/www/
```

### Step 2: Deploy ERPNext Custom App
```bash
# On ERPNext server (navigo.qastco.com)
cd /home/frappe/frappe-bench

# Create and install custom app
bench new-app restaurant_voice
bench get-app restaurant_voice https://github.com/yourusername/restaurant-voice-erpnext.git
bench --site navigo.qastco.com install-app restaurant_voice

# Migrate custom doctypes
bench --site navigo.qastco.com migrate

# Restart services
bench --site navigo.qastco.com restart
```

### Step 3: Deploy MCP Server
```bash
# Clone and setup
git clone https://github.com/yourusername/voice-restaurant-mcp.git /var/www/voice-restaurant-mcp
cd /var/www/voice-restaurant-mcp

# Install dependencies
npm install

# Build TypeScript
npm run build

# Setup environment
cp .env.example .env
# Edit .env with your configuration

# Start with PM2
pm2 start ecosystem.config.js
pm2 save
```

### Step 4: Deploy React Frontend
```bash
# Clone and build
git clone https://github.com/yourusername/voice-restaurant-frontend.git /tmp/frontend
cd /tmp/frontend

# Install and build
npm install
npm run build

# Copy build files
sudo cp -r build/* /var/www/voice-restaurant-frontend/
```

### Step 5: Configure Nginx
```bash
# Create Nginx config
sudo nano /etc/nginx/sites-available/voice-restaurant

# Enable site
sudo ln -s /etc/nginx/sites-available/voice-restaurant /etc/nginx/sites-enabled/

# Test and reload
sudo nginx -t
sudo systemctl reload nginx
```

### Step 6: SSL Setup (Let's Encrypt)
```bash
# Install certbot
sudo apt install certbot python3-certbot-nginx

# Get certificate
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

### Step 7: Final Configuration
```bash
# Setup firewall
sudo ufw allow 'Nginx Full'
sudo ufw allow 22
sudo ufw enable

# Setup logrotation
sudo nano /etc/logrotate.d/voice-restaurant

# Setup monitoring
pm2 startup
pm2 save
```

## Environment Variables

### React Frontend (.env.local)
```env
REACT_APP_API_URL=https://yourdomain.com/api
REACT_APP_WS_URL=wss://yourdomain.com/ws
REACT_APP_ULTRAVOX_PUBLIC_KEY=pk_your_ultravox_key
REACT_APP_STRIPE_PUBLIC_KEY=pk_test_your_stripe_key
```

### MCP Server (.env) - API-ONLY CONFIGURATION
```env
NODE_ENV=production
PORT=3001

# ERPNext API Configuration (NO DATABASE ACCESS)
ERPNEXT_BASE_URL=https://navigo.qastco.com
ERPNEXT_API_KEY=your_api_key
ERPNEXT_API_SECRET=your_api_secret
ERPNEXT_USERNAME=your_username  # Alternative auth method
ERPNEXT_PASSWORD=your_password  # Alternative auth method

# Voice AI
ULTRAVOX_API_KEY=your_ultravox_key
ULTRAVOX_APP_ID=your_app_id
ULTRAVOX_WEBHOOK_SECRET=your_webhook_secret

# Session & Cache (NO PERSISTENT DATABASE)
JWT_SECRET=your_jwt_secret_key
REDIS_URL=redis://localhost:6379  # ONLY for caching & sessions

# Server Configuration
CORS_ORIGIN=https://yourdomain.com
API_RATE_LIMIT=100  # requests per minute
SESSION_EXPIRE=24h
CACHE_TTL=300      # 5 minutes cache for API responses

# Logging
LOG_LEVEL=info
LOG_FILE=/var/log/voice-restaurant-mcp.log

# NO DATABASE VARIABLES NEEDED
# NO DATABASE_URL
# NO DB_HOST, DB_USER, DB_PASSWORD, etc.
```

## User Experience Features

### Guest User Experience
1. **No Registration Required**: Complete ordering without account
2. **Quick Voice Ordering**: Immediate access to voice interface
3. **Guest Checkout**: Phone number for order tracking
4. **Order Tracking**: SMS/email notifications
5. **Payment Options**: Card, cash, digital wallets

### Registered User Experience
1. **Full Authentication**: Login, register, forgot/reset password
2. **User Profile**: Preferences, dietary restrictions
3. **Order History**: Complete order tracking
4. **Favorite Items**: Quick reorder functionality
5. **Voice Preferences**: Language, speed settings
6. **Loyalty Integration**: Points and rewards (ERPNext)

### Additional Features
1. **Multi-language Support**: English, Spanish, French, etc.
2. **Accessibility**: Voice-first design for inclusive access
3. **Real-time Updates**: Order status via WebSocket
4. **Offline Support**: Service worker for basic functionality
5. **Mobile Responsive**: PWA capabilities
6. **Table-side Ordering**: QR code integration
7. **Kitchen Display**: Real-time order management
8. **Analytics Dashboard**: Voice interaction insights
9. **Payment Gateway**: Multiple payment methods
10. **Customer Support**: Voice-enabled help system

## Use Cases & Scenarios Covered

### 1. **Dine-In Restaurant Ordering**
- Customer sits at table, uses QR code to access voice ordering
- Voice commands: "I want a large pepperoni pizza and two Cokes"
- Table-side service with real-time kitchen notifications

### 2. **Office/Workplace Ordering**
- Employee opens website on mobile/desktop during lunch break
- Voice ordering: "Order 5 chicken sandwiches for pickup at 1 PM"
- Group ordering with individual payment splitting
- Scheduled/advance ordering for team meetings

### 3. **Mobile Delivery Ordering**
- Customer at home/office orders via mobile browser
- Voice navigation: "Show me vegetarian options under $15"
- Address-based delivery with GPS tracking
- Contactless delivery preferences

### 4. **Takeaway/Pickup Ordering**
- Quick voice ordering while driving: "I'll take my usual order"
- Hands-free ordering for accessibility
- Pickup time estimation and notifications
- Curbside pickup coordination

### 5. **Drive-Through Integration**
- Voice ordering at drive-through kiosk
- Car-based ordering via mobile hotspot
- Quick recognition of returning customers
- Menu recommendations based on time of day

### 6. **Catering & Bulk Orders**
- Office managers ordering for events/meetings
- Voice commands: "I need lunch for 20 people, mix of vegetarian and meat"
- Advanced scheduling and preparation time management
- Invoice generation for corporate accounts

### 7. **Accessibility & Special Needs**
- Visually impaired customers using voice navigation
- Elderly customers preferring voice over touch interfaces
- Multilingual support for diverse communities
- Simplified ordering for cognitive accessibility

### 8. **Multi-Location Chain Support**
- Customer voice ordering from different restaurant locations
- Location-based menu variations and pricing
- Loyalty points across multiple locations
- Franchise-specific customizations

### 9. **Integration Scenarios**
- **Smart Speakers**: "Alexa, order from [Restaurant Name]"
- **Car Integration**: Voice ordering via Android Auto/CarPlay
- **Wearable Devices**: Smart watch voice commands
- **IoT Integration**: Smart home voice assistants

### 10. **Business Intelligence Use Cases**
- Voice pattern analysis for menu optimization
- Popular item tracking via voice commands
- Customer preference learning from voice interactions
- Peak time identification through voice ordering data

## Supported Order Types

```javascript
// Order type configuration in ERPNext
const orderTypes = {
  "dine_in": {
    requiresTable: true,
    paymentTiming: "before_or_after",
    preparationType: "fresh_cooking"
  },
  "delivery": {
    requiresAddress: true,
    estimatedTime: "30-45 mins",
    paymentTiming: "before_delivery",
    preparationType: "fresh_cooking"
  },
  "pickup": {
    requiresPickupTime: true,
    paymentTiming: "at_pickup",
    preparationType: "ready_to_go"
  },
  "catering": {
    requiresAdvanceOrder: true,
    minimumQuantity: 10,
    paymentTiming: "advance_payment",
    preparationType: "bulk_preparation"
  }
}
```

## Location-Based Features

### GPS Integration
- **Auto-location Detection**: Determines nearest restaurant location
- **Delivery Radius**: Automatic delivery availability checking
- **Location-based Pricing**: Different pricing for different areas
- **Traffic-aware Delivery**: Dynamic delivery time estimation

### Multi-Restaurant Support
```javascript
// Restaurant location handling
const restaurantLocations = {
  "downtown": {
    address: "123 Main St, Downtown",
    serviceTypes: ["dine_in", "pickup", "delivery"],
    deliveryRadius: 5, // miles
    operatingHours: "9AM-11PM"
  },
  "suburb": {
    address: "456 Oak Ave, Suburbs",
    serviceTypes: ["dine_in", "pickup", "delivery"],
    deliveryRadius: 3,
    operatingHours: "10AM-10PM"
  }
}
```

## Advanced Voice Use Cases

### 1. **Contextual Ordering**
```
Customer: "I want my usual"
System: "Your usual large pepperoni pizza with extra cheese, estimated 25 minutes?"
Customer: "Yes, but make it two pizzas this time"
System: "Perfect! Two large pepperoni pizzas with extra cheese. Total $29.98"
```

### 2. **Dietary Restrictions & Preferences**
```
Customer: "I'm vegan and gluten-free, what do you recommend?"
System: "We have a great quinoa salad and our vegan pizza with gluten-free crust"
Customer: "I'll take the pizza"
System: "Vegan pizza on gluten-free crust, any additional toppings?"
```

### 3. **Group Ordering Management**
```
Customer: "I'm ordering for my office, we need 10 lunches"
System: "Great! Would you like our office combo deals or individual selections?"
Customer: "Mix it up - 5 chicken, 3 vegetarian, 2 vegan"
System: "Perfect! I'll prepare a variety pack. Delivery address?"
```

### 4. **Complex Modifications**
```
Customer: "Large pizza, half pepperoni half mushroom, thin crust, extra sauce on pepperoni side only"
System: "Large thin crust pizza: half pepperoni with extra sauce, half mushroom. Anything else?"
```

This comprehensive structure now covers everything from quick office lunch orders to complex catering scenarios, all accessible via voice commands on any device - mobile, desktop, or IoT integration!