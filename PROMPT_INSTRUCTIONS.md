# Voice Restaurant Project - Step-by-Step Implementation Guide

## Overview
Complete implementation guide for the voice-powered restaurant ordering system with 3 separate projects, deployed on Contavo VPS with ERPNext integration.

## Implementation Order & Prompts

### Phase 1: ERPNext Custom App Setup (Week 1)

#### Step 1.1: Create ERPNext Custom App Structure
**Prompt:**
```
I need to create a custom ERPNext app called "restaurant_voice" for my voice restaurant project.

Please help me:
1. Create the complete directory structure for the custom app
2. Generate all necessary files including __init__.py, hooks.py, modules.txt
3. Create setup.py and requirements.txt
4. Set up the basic app configuration

The app should be ready to install on ERPNext instance at https://navigo.qastco.com/
```

#### Step 1.2: Create Custom Doctypes
**Prompt:**
```
I need to create 5 custom doctypes for my restaurant voice ordering system:

1. Voice Session - to track voice ordering sessions
2. Restaurant Table - for table management with QR codes
3. Voice Order - for processing voice commands into orders
4. Restaurant Menu Category - for organizing menu items
5. COD Settings - for cash on delivery configuration (single doctype)

Please create all the JSON files with complete field definitions, permissions, and proper relationships between doctypes. Use the specifications from my PROJECT_STRUCTURE.md file.
```

#### Step 1.3: Add Custom Fields to Existing Doctypes
**Prompt:**
```
I need to add custom fields to existing ERPNext doctypes for voice restaurant functionality:

1. Item doctype - Add voice aliases, prep time, dietary info, spice level, menu category
2. Sales Order doctype - Add voice session ID, payment method, COD fields, guest info
3. Customer doctype - Add voice preferences, dietary restrictions, favorite items

Please create the custom field definitions and the Python code to add them programmatically during app installation.
```

#### Step 1.4: Create API Methods for Voice Integration
**Prompt:**
```
I need to create Python API methods in my ERPNext custom app for voice ordering integration:

1. Authentication APIs - login, register, forgot password, guest sessions
2. Menu APIs - get items, search, popular items, voice aliases
3. Voice APIs - create sessions, process commands, manage conversations
4. Order APIs - create orders, update status, order history
5. Payment APIs - process payments, COD settings, payment methods

Please create all the Python files in the api/ directory with proper error handling, validation, and ERPNext framework integration.
```

### Phase 2: MCP Server Development (Week 2)

#### Step 2.1: Create MCP Server Project Structure
**Prompt:**
```
I need to create a Node.js MCP server that acts as an API proxy between React frontend and ERPNext.

Important: This server should NOT have any database connections - it only calls ERPNext APIs.

Please create:
1. Complete TypeScript project structure
2. Package.json with dependencies (NO database drivers)
3. Basic Express.js setup with middleware
4. Configuration files for ERPNext API integration
5. Environment variable setup

The server should run on port 3001 and proxy all data operations to https://navigo.qastco.com/api/
```

#### Step 2.2: Implement ERPNext API Client
**Prompt:**
```
I need an ERPNext API client service for my MCP server that handles all communication with my ERPNext instance at https://navigo.qastco.com/

Please create:
1. ERPNext API client class with authentication (API key/secret and username/password methods)
2. Methods for all CRUD operations on doctypes
3. Error handling and retry logic
4. Request/response logging
5. TypeScript interfaces for all ERPNext responses

The client should handle authentication, rate limiting, and provide typed responses for menu items, orders, customers, etc.
```

#### Step 2.3: Implement Ultravox Voice Integration
**Prompt:**
```
I need to integrate Ultravox AI for voice processing in my MCP server.

Please create:
1. Ultravox service class for voice session management
2. Voice command processing and natural language understanding
3. Real-time voice streaming support
4. WebSocket handlers for voice communication
5. Voice session state management (using Redis cache only)

The service should convert voice commands like "I want two pizzas" into structured data for ERPNext orders.
```

#### Step 2.4: Create API Routes and Controllers
**Prompt:**
```
I need to create all API routes and controllers for my MCP server that proxy requests to ERPNext:

1. Authentication routes (/api/auth/*) - login, register, guest sessions
2. Menu routes (/api/menu/*) - get items, search, categories
3. Voice routes (/api/voice/*) - process commands, manage sessions
4. Order routes (/api/order/*) - create, track, history
5. Payment routes (/api/payment/*) - process payments, COD settings

Each controller should validate requests, call ERPNext APIs, handle errors, and return properly formatted responses. Include WebSocket support for real-time updates.
```

### Phase 3: React Frontend Development (Week 3)

#### Step 3.1: Create React Project Structure
**Prompt:**
```
I need to create a React TypeScript frontend for voice restaurant ordering with comprehensive user experience.

Please create:
1. Complete React project structure with TypeScript
2. Redux Toolkit setup for state management
3. React Router for navigation
4. Component structure for auth, voice, menu, orders, payments
5. Service layer for API communication with MCP server
6. TypeScript interfaces for all data types

The app should support both guest users and registered users with full authentication features.
```

#### Step 3.2: Implement Authentication System
**Prompt:**
```
I need a complete authentication system for my React app that works with both guest and registered users:

1. Login/Register forms with validation
2. Forgot password and reset password functionality
3. Guest checkout system (no registration required)
4. Protected routes and authentication guards
5. User profile management
6. Session persistence and auto-refresh

The authentication should integrate with my MCP server APIs and support voice ordering for both user types.
```

#### Step 3.3: Implement Voice Ordering Interface
**Prompt:**
```
I need to implement voice ordering functionality in my React app using Ultravox SDK:

1. Voice recorder component with real-time audio visualization
2. Voice session management and WebSocket integration
3. Voice command processing and response display
4. Menu navigation using voice commands
5. Order building through voice interaction
6. Voice feedback and confirmation system

The interface should work seamlessly on mobile and desktop with proper error handling and accessibility features.
```

#### Step 3.4: Create Menu and Ordering Components
**Prompt:**
```
I need menu display and ordering components for my React app:

1. Menu category navigation and item display
2. Search functionality with voice search support
3. Item customization (size, toppings, special instructions)
4. Shopping cart with add/remove/modify items
5. Order summary and checkout process
6. Real-time order tracking

Components should support both voice interaction and traditional touch/click interface with responsive design.
```

#### Step 3.5: Implement Payment System
**Prompt:**
```
I need a comprehensive payment system supporting multiple payment methods including COD:

1. Payment method selection (Card, Cash, COD, Online)
2. Credit card payment form with validation
3. Cash on Delivery flow with change calculation
4. Payment confirmation and receipt generation
5. Failed payment handling and retry logic
6. Payment history and refund support

The payment system should integrate with my MCP server and handle all payment scenarios securely.
```

### Phase 4: Deployment Setup (Week 4)

#### Step 4.1: Prepare Contavo VPS Server
**Prompt:**
```
I need to set up my Contavo VPS server for deploying the voice restaurant system:

1. Update Ubuntu system and install required packages (Node.js, Nginx, Redis, PM2)
2. Create directory structure for the projects
3. Set up firewall and security configurations
4. Install and configure Redis for caching
5. Create system users and permissions

Provide complete server setup commands and security best practices.
```

#### Step 4.2: Deploy ERPNext Custom App
**Prompt:**
```
I need to deploy my restaurant_voice custom app to my ERPNext instance at https://navigo.qastco.com/:

1. Guide me through installing the custom app on ERPNext
2. Run migrations to create custom doctypes
3. Set up custom fields on existing doctypes
4. Configure permissions and roles
5. Test API endpoints and functionality

Provide step-by-step commands and troubleshooting tips for ERPNext deployment.
```

#### Step 4.3: Deploy MCP Server with PM2
**Prompt:**
```
I need to deploy my MCP server to the Contavo VPS using PM2:

1. Build and deploy the TypeScript MCP server
2. Configure environment variables for production
3. Set up PM2 ecosystem configuration
4. Configure log rotation and monitoring
5. Set up auto-restart and cluster mode

Provide complete deployment scripts and PM2 configuration for production environment.
```

#### Step 4.4: Deploy React Frontend with Nginx
**Prompt:**
```
I need to deploy my React frontend and configure Nginx:

1. Build React app for production
2. Configure Nginx as reverse proxy for MCP server
3. Set up SSL certificates with Let's Encrypt
4. Configure WebSocket support for voice features
5. Set up static file serving and caching

Provide complete Nginx configuration and deployment steps for the frontend.
```

#### Step 4.5: Configure SSL and Final Testing
**Prompt:**
```
I need to finalize the deployment with SSL and comprehensive testing:

1. Set up SSL certificates for domain
2. Configure HTTPS redirects and security headers
3. Test all functionality: voice ordering, payments, COD, guest/user flows
4. Set up monitoring and logging
5. Performance optimization and caching

Provide final configuration steps and testing checklist to ensure everything works correctly.
```

## Additional Implementation Prompts

### Database and API Integration
**Prompt for ERPNext Data Setup:**
```
I need to set up sample data in my ERPNext instance for testing the voice restaurant system:

1. Create sample restaurant menu items with voice aliases
2. Set up restaurant tables with QR codes
3. Configure payment methods and COD settings
4. Create test customer accounts
5. Set up menu categories and item groups

Provide Python scripts or manual steps to create this test data in ERPNext.
```

### Voice AI Training and Configuration
**Prompt for Ultravox Setup:**
```
I need to configure Ultravox AI for optimal restaurant voice ordering:

1. Set up voice models for restaurant terminology
2. Configure menu item recognition and aliases
3. Train the system for order modifications and special requests
4. Set up multi-language support
5. Configure voice response templates

Provide configuration files and training data for restaurant-specific voice recognition.
```

### Performance Optimization
**Prompt for Optimization:**
```
I need to optimize performance for my voice restaurant system:

1. Implement Redis caching for menu items and frequently accessed data
2. Optimize API response times between MCP server and ERPNext
3. Minimize voice processing latency
4. Set up CDN for static assets
5. Implement lazy loading and code splitting in React

Provide specific optimization strategies and implementation code.
```

### Testing and Quality Assurance
**Prompt for Testing:**
```
I need comprehensive testing for my voice restaurant system:

1. Unit tests for MCP server API functions
2. Integration tests for ERPNext API calls
3. End-to-end tests for voice ordering workflows
4. Performance tests for concurrent voice sessions
5. Security tests for authentication and payment processing

Provide test suites and testing strategies for all components.
```

## Usage Instructions

### How to Use These Prompts:

1. **Sequential Implementation**: Use prompts in the order listed for systematic development
2. **Customization**: Modify prompts based on your specific requirements
3. **Iteration**: After each implementation step, test and refine before moving to the next
4. **Documentation**: Keep track of changes and configurations for each step

### Before Starting Each Phase:

1. Review the PROJECT_STRUCTURE.md for detailed specifications
2. Ensure previous phase is completed and tested
3. Set up development environment for the specific technology
4. Have access credentials ready (ERPNext API keys, Ultravox keys, etc.)

### After Each Implementation Step:

1. Test the implemented functionality
2. Update documentation with any changes
3. Commit code to version control
4. Prepare environment for next step

This systematic approach ensures a complete, tested, and production-ready voice restaurant ordering system with full integration between all components.