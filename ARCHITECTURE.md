# Voice Restaurant Project Architecture

## Overview
A voice-powered restaurant ordering system using Ultravox AI for real-time voice interactions, MCP (Model Context Protocol) server for AI coordination, ERPNext (navigo.qastco.com) for backend APIs and database, and React frontend.

## System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           Voice Restaurant System                        │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐      │
│  │                 │    │                 │    │                 │      │
│  │  React Frontend │◄──►│  MCP Server     │◄──►│   ERPNext       │      │
│  │                 │    │   (Node.js)     │    │ navigo.qastco.com│     │
│  │ • Voice UI      │    │                 │    │                 │      │
│  │ • Menu Display  │    │ • AI Orchestr.  │    │ • Restaurant    │      │
│  │ • Order Mgmt    │    │ • Voice Process │    │   Management    │      │
│  │ • Ultravox SDK  │    │ • ERPNext API   │    │ • Menu Items    │      │
│  │ • Real-time UI  │    │   Integration   │    │ • Orders        │      │
│  │                 │    │ • Context Mgmt  │    │ • Customers     │      │
│  └─────────────────┘    └─────────────────┘    │ • Inventory     │      │
│           │                       │            │ • Payments      │      │
│           │                       │            │ • Reports       │      │
│           ▼                       ▼            │                 │      │
│  ┌─────────────────┐    ┌─────────────────┐    └─────────────────┘      │
│  │                 │    │                 │                            │
│  │  Ultravox AI    │    │  MariaDB/MySQL  │                            │
│  │                 │    │   (ERPNext DB)  │                            │
│  │ • Voice STT/TTS │    │                 │                            │
│  │ • NLU/NLG       │    │ • Items         │                            │
│  │ • Real-time     │    │ • Sales Orders  │                            │
│  │   Processing    │    │ • Customers     │                            │
│  │ • Conversation  │    │ • Payments      │                            │
│  │   Context       │    │ • Custom Fields │                            │
│  │                 │    │                 │                            │
│  └─────────────────┘    └─────────────────┘                            │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## Technical Stack

### Frontend (React)
- **Framework**: React with TypeScript
- **State Management**: Redux Toolkit or Zustand
- **UI Components**: Material-UI or Tailwind CSS
- **Voice Integration**: Ultravox Web SDK
- **ERPNext Integration**: Frappe REST API client
- **Real-time**: WebSocket for live updates

### MCP Server (Node.js)
- **Runtime**: Node.js with Express.js
- **Language**: TypeScript
- **Role**: Voice AI orchestration and ERPNext integration
- **Features**:
  - Voice command processing via Ultravox
  - ERPNext API integration
  - Order context management
  - Real-time communication bridge

### ERPNext Backend (navigo.qastco.com)
- **Framework**: Frappe Framework
- **Database**: MariaDB/MySQL
- **API**: REST API with authentication
- **Features**:
  - Restaurant management doctypes
  - Menu items (Items doctype)
  - Orders (Sales Order doctype)
  - Customer management
  - Payment integration
  - Inventory management
  - Custom fields for voice attributes

### Voice AI (Ultravox)
- **Speech-to-Text**: Real-time voice recognition
- **Natural Language**: Order intent processing
- **Text-to-Speech**: Voice responses
- **Integration**: Direct connection to MCP server
- **Features**: Multimodal voice + visual menu interaction

## Implementation Plan

### Phase 1: ERPNext & Foundation Setup (Week 1-2)
1. **Project Structure**
   ```
   voice-restaurant/
   ├── frontend/          # React app
   ├── mcp-server/        # MCP server (Node.js)
   ├── erpnext-config/    # ERPNext custom doctypes/fields
   ├── shared/            # Shared types & utilities
   └── docs/              # Documentation
   ```

2. **ERPNext Configuration (navigo.qastco.com)**
   - Create custom doctypes for restaurant operations
   - Configure menu items using Items doctype
   - Set up Sales Order for voice orders
   - Add custom fields for voice attributes
   - Configure API access and authentication

3. **Basic Infrastructure**
   - Set up MCP server with Express.js
   - Create React frontend with TypeScript
   - Configure ERPNext API integration
   - Set up development environment

4. **Ultravox Integration**
   - Register for Ultravox API access
   - Install Ultravox Web SDK in React
   - Configure voice input/output in MCP server

### Phase 2: ERPNext Integration & Core Features (Week 3-4)
1. **ERPNext Restaurant Setup**
   - Create Restaurant doctype
   - Configure menu categories and items
   - Set up customer management
   - Configure payment methods

2. **Voice Ordering System**
   - MCP server integration with ERPNext APIs
   - Voice command processing via Ultravox
   - Natural language to ERPNext Sales Order mapping
   - Order validation using ERPNext business rules

3. **API Integration Layer**
   - ERPNext REST API client in MCP server
   - Authentication handling (API keys/tokens)
   - Error handling and retry logic
   - Real-time data synchronization

### Phase 3: Advanced Features (Week 5-6)
1. **Real-time Updates**
   - Order status tracking via ERPNext webhooks
   - Kitchen notifications through ERPNext
   - Customer updates via voice/text using ERPNext Communication

2. **Payment Integration**
   - ERPNext Payment Entry integration
   - Voice-initiated payment processing
   - Receipt generation via ERPNext Print Formats

3. **Analytics & Insights**
   - ERPNext Report Builder for order analytics
   - Popular items tracking via ERPNext Dashboard
   - Voice interaction metrics in custom doctype
   - Integration with existing ERPNext reporting

### Phase 4: Enhancement & Deployment (Week 7-8)
1. **Performance Optimization**
   - Voice latency optimization
   - ERPNext API caching in MCP server
   - Optimized database queries in ERPNext

2. **Testing & Quality**
   - Unit tests for MCP server components
   - Integration tests with ERPNext APIs
   - E2E testing with voice scenarios
   - ERPNext custom app testing

3. **Deployment**
   - Docker containerization for MCP server and React app
   - CI/CD pipeline for custom ERPNext apps
   - Production deployment strategy
   - ERPNext backup and monitoring

## Key Features

### Voice Ordering Flow
```
Customer Interaction Flow:
1. "Hi, I'd like to place an order"
2. System: "Welcome! What would you like today?"
3. "I'll have a large pepperoni pizza and a Coke"
4. System: "Got it! Large pepperoni pizza and a Coke. Total is $15.99. Confirm?"
5. "Yes, that's correct"
6. System: "Perfect! Your order is confirmed. ETA 25 minutes."
```

### Core Functionalities
- **Voice Menu Navigation**: Browse menu using voice commands
- **Smart Ordering**: Natural language order processing
- **Order Customization**: Voice-based item modifications
- **Payment Processing**: Voice-initiated secure payments
- **Order Tracking**: Real-time status updates
- **Multi-language**: Support for multiple languages
- **Accessibility**: Full voice-driven experience

## API Integration

### ERPNext API Endpoints (navigo.qastco.com)
```
Base URL: https://navigo.qastco.com/api/
Authentication: API Key + Secret or JWT Token
```

#### Menu Management
- `GET /api/resource/Item` - Get menu items
- `GET /api/resource/Item/{item_code}` - Get specific menu item
- `POST /api/resource/Item` - Create menu item
- `PUT /api/resource/Item/{item_code}` - Update menu item

#### Order Management
- `GET /api/resource/Sales%20Order` - Get orders
- `POST /api/resource/Sales%20Order` - Create voice order
- `PUT /api/resource/Sales%20Order/{name}` - Update order
- `GET /api/resource/Sales%20Order/{name}` - Get order details

#### Customer Management
- `GET /api/resource/Customer` - Get customers
- `POST /api/resource/Customer` - Create customer
- `GET /api/resource/Customer/{name}` - Get customer details

### MCP Server Endpoints
```
Base URL: http://localhost:3001/api/
```

#### Voice Processing
- `POST /api/voice/process` - Process voice command via Ultravox
- `GET /api/voice/session/:id` - Get voice session context
- `WebSocket /ws/voice` - Real-time voice connection
- `POST /api/voice/order` - Convert voice to ERPNext order

#### ERPNext Proxy
- `GET /api/erpnext/menu` - Cached menu items from ERPNext
- `POST /api/erpnext/order` - Create order in ERPNext
- `GET /api/erpnext/order/:id` - Get order status from ERPNext

## ERPNext Schema Configuration

### Core Doctypes (Existing)
```javascript
// Items (Menu Items)
{
  "item_code": "PIZZA_001",
  "item_name": "Margherita Pizza",
  "description": "Classic tomato, mozzarella, basil",
  "item_group": "Food",
  "stock_uom": "Nos",
  "is_sales_item": 1,
  "standard_rate": 12.99,
  "custom_voice_aliases": ["margherita", "classic pizza", "cheese pizza"],
  "custom_prep_time": 20,
  "custom_dietary_info": "Vegetarian"
}

// Sales Order (Voice Orders)
{
  "doctype": "Sales Order",
  "customer": "WALK-IN-001",
  "order_type": "Sales",
  "custom_voice_session_id": "vs_123456",
  "custom_order_source": "Voice AI",
  "items": [
    {
      "item_code": "PIZZA_001",
      "qty": 2,
      "rate": 12.99
    }
  ]
}

// Customer (Voice Customers)
{
  "customer_name": "Voice Customer 001",
  "customer_type": "Individual",
  "custom_phone_number": "+1234567890",
  "custom_preferred_name": "John",
  "custom_voice_preferences": {"language": "en", "speed": "normal"}
}
```

### Custom Doctypes (To Create)
```javascript
// Voice Session (Custom Doctype)
{
  "doctype": "Voice Session",
  "session_id": "vs_123456",
  "customer": "WALK-IN-001",
  "status": "Active", // Active, Completed, Abandoned
  "conversation_log": "JSON string of conversation",
  "current_order": "SO-2024-001",
  "created_at": "2024-01-01 10:00:00",
  "updated_at": "2024-01-01 10:15:00"
}

// Restaurant Table (Custom Doctype)
{
  "doctype": "Restaurant Table",
  "table_number": "T01",
  "seating_capacity": 4,
  "status": "Available", // Available, Occupied, Reserved
  "current_session": "vs_123456",
  "qr_code": "Generated QR for voice ordering"
}
```

### Custom Fields to Add
```javascript
// Item doctype custom fields
- custom_voice_aliases: Small Text
- custom_prep_time: Int (minutes)
- custom_dietary_info: Data
- custom_spice_level: Select (Mild, Medium, Hot)
- custom_available_for_voice: Check

// Sales Order custom fields
- custom_voice_session_id: Data
- custom_order_source: Select (Voice AI, Web, Mobile)
- custom_special_instructions: Text
- custom_estimated_delivery: Datetime

// Customer custom fields
- custom_voice_preferences: JSON
- custom_dietary_restrictions: Text
- custom_preferred_payment: Select
```

## Environment Variables
```env
# MCP Server
NODE_ENV=development
MCP_SERVER_PORT=3001
MCP_SERVER_HOST=localhost

# ERPNext API
ERPNEXT_BASE_URL=https://navigo.qastco.com
ERPNEXT_API_KEY=your-api-key
ERPNEXT_API_SECRET=your-api-secret
# OR
ERPNEXT_USERNAME=your-username
ERPNEXT_PASSWORD=your-password

# Ultravox AI
ULTRAVOX_API_KEY=your-ultravox-key
ULTRAVOX_APP_ID=your-app-id
ULTRAVOX_WEBHOOK_SECRET=your-webhook-secret

# React Frontend
REACT_APP_MCP_SERVER_URL=http://localhost:3001
REACT_APP_ULTRAVOX_PUBLIC_KEY=pk_...
REACT_APP_ERPNEXT_BASE_URL=https://navigo.qastco.com

# Optional: Redis for caching
REDIS_URL=redis://localhost:6379

# Optional: WebSocket
WS_PORT=3002
```

## Development Commands
```bash
# Start all services
npm run dev

# Individual services
npm run dev:frontend    # React dev server (port 3000)
npm run dev:mcp         # MCP server (port 3001)
npm run dev:ws          # WebSocket server (port 3002)

# ERPNext Development
bench --site navigo.qastco.com migrate    # Run migrations for custom doctypes
bench --site navigo.qastco.com console    # ERPNext console for testing
bench --site navigo.qastco.com restart    # Restart ERPNext after changes

# Testing
npm test                # Run all tests
npm run test:voice      # Voice interaction tests
npm run test:erpnext    # ERPNext API integration tests
npm run test:e2e        # End-to-end voice ordering tests

# Production
npm run build           # Build React frontend and MCP server
npm run start           # Start production servers
```

## ERPNext Custom App Development
```bash
# Create custom app for restaurant features
bench new-app restaurant_voice
bench get-app restaurant_voice
bench --site navigo.qastco.com install-app restaurant_voice

# Development workflow
bench --site navigo.qastco.com migrate
bench --site navigo.qastco.com restart
bench build
```

## Security Considerations
- ERPNext API key/secret management
- Voice data encryption in transit
- ERPNext role-based permissions for voice orders
- Rate limiting on voice endpoints
- Input validation before ERPNext API calls
- Secure webhook handling for real-time updates
- ERPNext audit trail for voice orders

## Integration with Existing Voice AI Project
Based on your voiceai-inbound-bot project, you can leverage:
- Existing MCP server architecture patterns
- Voice processing pipeline from inbound bot
- ERPNext integration patterns you've already developed
- Authentication and session management
- Real-time communication infrastructure

## Scalability Plan
- MCP server horizontal scaling with load balancer
- ERPNext database optimization and indexing
- Redis caching for frequently accessed menu items
- CDN for React frontend static assets
- Voice processing load balancing via Ultravox
- ERPNext background jobs for order processing
- Webhook queue management for real-time updates