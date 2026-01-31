# 🚀 Node.js Express Microservices Project Structure

> **PM2 bilan professional monorepo arxitekturasi**

---

## 📁 To'liq Folder Strukturasi

```
microservices-project/
│
├── services/                          # 🎯 Barcha microservislar
│   │
│   ├── api-gateway/                   # 🚪 API Gateway (Port: 3000)
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── index.js          # Asosiy config (PORT, ENV, etc)
│   │   │   │   └── services.js       # Servislarning URL manzillari
│   │   │   │
│   │   │   ├── middlewares/
│   │   │   │   ├── auth.js           # JWT token tekshirish
│   │   │   │   ├── rateLimiter.js    # Rate limiting
│   │   │   │   ├── requestLogger.js  # Request logging
│   │   │   │   ├── cors.js           # CORS sozlamalari
│   │   │   │   └── errorHandler.js   # Global error handler
│   │   │   │
│   │   │   ├── routes/
│   │   │   │   ├── index.js          # Barcha routelarni yig'ish
│   │   │   │   ├── userRoutes.js     # /api/users → user-service
│   │   │   │   ├── productRoutes.js  # /api/products → product-service
│   │   │   │   ├── orderRoutes.js    # /api/orders → order-service
│   │   │   │   ├── paymentRoutes.js  # /api/payments → payment-service
│   │   │   │   └── healthRoutes.js   # Health check endpoint
│   │   │   │
│   │   │   ├── utils/
│   │   │   │   ├── httpClient.js     # Axios instance (servisga murojaat)
│   │   │   │   ├── responseHandler.js # Standart response format
│   │   │   │   ├── logger.js         # Winston logger
│   │   │   │   └── validator.js      # Request validation
│   │   │   │
│   │   │   ├── app.js                # Express app configuration
│   │   │   └── server.js             # Server ishga tushirish
│   │   │
│   │   ├── tests/
│   │   │   ├── unit/
│   │   │   └── integration/
│   │   │
│   │   ├── .env
│   │   ├── .env.example
│   │   ├── .gitignore
│   │   ├── package.json
│   │   └── README.md
│   │
│   ├── user-service/                  # 👤 User Service (Port: 3001)
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── index.js          # Environment config
│   │   │   │   ├── database.js       # MongoDB/PostgreSQL connection
│   │   │   │   └── redis.js          # Redis cache (ixtiyoriy)
│   │   │   │
│   │   │   ├── controllers/
│   │   │   │   ├── authController.js # register, login, logout, refresh
│   │   │   │   └── userController.js # getProfile, updateProfile, deleteUser
│   │   │   │
│   │   │   ├── models/
│   │   │   │   └── User.js           # User schema (Mongoose/Sequelize)
│   │   │   │
│   │   │   ├── routes/
│   │   │   │   ├── index.js
│   │   │   │   ├── authRoutes.js     # POST /auth/register, /auth/login
│   │   │   │   └── userRoutes.js     # GET/PUT/DELETE /users/:id
│   │   │   │
│   │   │   ├── services/
│   │   │   │   ├── authService.js    # Business logic (register, login)
│   │   │   │   └── userService.js    # Business logic (CRUD)
│   │   │   │
│   │   │   ├── middlewares/
│   │   │   │   ├── auth.js           # Protect routes
│   │   │   │   ├── validate.js       # Request validation (Joi/Yup)
│   │   │   │   └── errorHandler.js   # Error handling
│   │   │   │
│   │   │   ├── utils/
│   │   │   │   ├── jwt.js            # generateToken, verifyToken
│   │   │   │   ├── bcrypt.js         # hashPassword, comparePassword
│   │   │   │   ├── validators.js     # Validation schemas
│   │   │   │   ├── logger.js         # Winston logger
│   │   │   │   └── sendEmail.js      # Email utility (ixtiyoriy)
│   │   │   │
│   │   │   ├── events/               # Event-driven (ixtiyoriy)
│   │   │   │   ├── publishers/
│   │   │   │   │   └── userPublisher.js  # USER_CREATED event
│   │   │   │   └── subscribers/
│   │   │   │       └── userSubscriber.js # Listen to events
│   │   │   │
│   │   │   ├── app.js
│   │   │   └── server.js
│   │   │
│   │   ├── tests/
│   │   │   ├── unit/
│   │   │   │   ├── authService.test.js
│   │   │   │   └── userService.test.js
│   │   │   └── integration/
│   │   │       ├── auth.test.js
│   │   │       └── user.test.js
│   │   │
│   │   ├── .env
│   │   ├── .env.example
│   │   ├── .gitignore
│   │   ├── package.json
│   │   └── README.md
│   │
│   ├── product-service/               # 📦 Product Service (Port: 3002)
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── index.js
│   │   │   │   └── database.js
│   │   │   │
│   │   │   ├── controllers/
│   │   │   │   ├── productController.js  # getAllProducts, getProduct, createProduct, updateProduct, deleteProduct
│   │   │   │   └── categoryController.js # Category CRUD
│   │   │   │
│   │   │   ├── models/
│   │   │   │   ├── Product.js        # name, price, description, stock, category
│   │   │   │   └── Category.js       # name, description
│   │   │   │
│   │   │   ├── routes/
│   │   │   │   ├── index.js
│   │   │   │   ├── productRoutes.js  # GET/POST/PUT/DELETE /products
│   │   │   │   └── categoryRoutes.js # GET/POST/PUT/DELETE /categories
│   │   │   │
│   │   │   ├── services/
│   │   │   │   ├── productService.js # Business logic
│   │   │   │   └── categoryService.js
│   │   │   │
│   │   │   ├── middlewares/
│   │   │   │   ├── auth.js           # Verify JWT
│   │   │   │   ├── validate.js
│   │   │   │   └── errorHandler.js
│   │   │   │
│   │   │   ├── utils/
│   │   │   │   ├── validators.js
│   │   │   │   ├── logger.js
│   │   │   │   └── imageUpload.js    # Multer/Cloudinary (ixtiyoriy)
│   │   │   │
│   │   │   ├── events/
│   │   │   │   ├── publishers/
│   │   │   │   │   └── productPublisher.js  # PRODUCT_CREATED
│   │   │   │   └── subscribers/
│   │   │   │
│   │   │   ├── app.js
│   │   │   └── server.js
│   │   │
│   │   ├── tests/
│   │   ├── .env
│   │   ├── package.json
│   │   └── README.md
│   │
│   ├── order-service/                 # 🛒 Order Service (Port: 3003)
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── index.js
│   │   │   │   └── database.js
│   │   │   │
│   │   │   ├── controllers/
│   │   │   │   └── orderController.js # createOrder, getOrders, getOrder, updateOrderStatus, cancelOrder
│   │   │   │
│   │   │   ├── models/
│   │   │   │   └── Order.js          # userId, products[], totalPrice, status, paymentStatus
│   │   │   │
│   │   │   ├── routes/
│   │   │   │   ├── index.js
│   │   │   │   └── orderRoutes.js    # GET/POST /orders
│   │   │   │
│   │   │   ├── services/
│   │   │   │   └── orderService.js   # Business logic + call product/payment service
│   │   │   │
│   │   │   ├── middlewares/
│   │   │   │   ├── auth.js
│   │   │   │   ├── validate.js
│   │   │   │   └── errorHandler.js
│   │   │   │
│   │   │   ├── utils/
│   │   │   │   ├── httpClient.js     # Call other services
│   │   │   │   ├── validators.js
│   │   │   │   └── logger.js
│   │   │   │
│   │   │   ├── events/
│   │   │   │   ├── publishers/
│   │   │   │   │   └── orderPublisher.js  # ORDER_CREATED, ORDER_COMPLETED
│   │   │   │   └── subscribers/
│   │   │   │       └── paymentSubscriber.js # Listen PAYMENT_SUCCESS
│   │   │   │
│   │   │   ├── app.js
│   │   │   └── server.js
│   │   │
│   │   ├── tests/
│   │   ├── .env
│   │   ├── package.json
│   │   └── README.md
│   │
│   ├── payment-service/               # 💳 Payment Service (Port: 3004)
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── index.js
│   │   │   │   ├── database.js
│   │   │   │   └── stripe.js         # Stripe/PayPal config
│   │   │   │
│   │   │   ├── controllers/
│   │   │   │   └── paymentController.js # processPayment, getPaymentStatus, refund
│   │   │   │
│   │   │   ├── models/
│   │   │   │   └── Payment.js        # orderId, amount, status, method, transactionId
│   │   │   │
│   │   │   ├── routes/
│   │   │   │   ├── index.js
│   │   │   │   └── paymentRoutes.js  # POST /payments, GET /payments/:id
│   │   │   │
│   │   │   ├── services/
│   │   │   │   ├── paymentService.js # Business logic
│   │   │   │   └── stripeService.js  # Stripe integration
│   │   │   │
│   │   │   ├── middlewares/
│   │   │   │   ├── auth.js
│   │   │   │   ├── validate.js
│   │   │   │   └── errorHandler.js
│   │   │   │
│   │   │   ├── utils/
│   │   │   │   ├── validators.js
│   │   │   │   └── logger.js
│   │   │   │
│   │   │   ├── events/
│   │   │   │   ├── publishers/
│   │   │   │   │   └── paymentPublisher.js  # PAYMENT_SUCCESS, PAYMENT_FAILED
│   │   │   │   └── subscribers/
│   │   │   │       └── orderSubscriber.js   # Listen ORDER_CREATED
│   │   │   │
│   │   │   ├── app.js
│   │   │   └── server.js
│   │   │
│   │   ├── tests/
│   │   ├── .env
│   │   ├── package.json
│   │   └── README.md
│   │
│   └── notification-service/          # 📧 Notification Service (Port: 3005)
│       ├── src/
│       │   ├── config/
│       │   │   ├── index.js
│       │   │   ├── email.js          # Nodemailer config
│       │   │   └── sms.js            # Twilio config (ixtiyoriy)
│       │   │
│       │   ├── controllers/
│       │   │   └── notificationController.js # sendEmail, sendSMS
│       │   │
│       │   ├── services/
│       │   │   ├── emailService.js   # Send email logic
│       │   │   └── smsService.js     # Send SMS logic
│       │   │
│       │   ├── routes/
│       │   │   ├── index.js
│       │   │   └── notificationRoutes.js
│       │   │
│       │   ├── middlewares/
│       │   │   └── errorHandler.js
│       │   │
│       │   ├── utils/
│       │   │   ├── templates/        # Email HTML templates
│       │   │   │   ├── welcome.html
│       │   │   │   ├── orderConfirmation.html
│       │   │   │   └── resetPassword.html
│       │   │   └── logger.js
│       │   │
│       │   ├── events/
│       │   │   └── subscribers/      # Faqat listen qiladi
│       │   │       ├── userSubscriber.js      # USER_CREATED → send welcome email
│       │   │       ├── orderSubscriber.js     # ORDER_CREATED → send confirmation
│       │   │       └── paymentSubscriber.js   # PAYMENT_SUCCESS → send receipt
│       │   │
│       │   ├── app.js
│       │   └── server.js
│       │
│       ├── tests/
│       ├── .env
│       ├── package.json
│       └── README.md
│
├── shared/                            # 🔄 Umumiy kod (barcha servislar ishlatadi)
│   ├── middlewares/
│   │   ├── errorHandler.js           # Global error handler
│   │   ├── logger.js                 # Winston logger middleware
│   │   ├── asyncHandler.js           # Async error wrapper
│   │   └── validateRequest.js        # Request validation
│   │
│   ├── utils/
│   │   ├── jwt.js                    # JWT generate/verify
│   │   ├── response.js               # Standart response format
│   │   ├── AppError.js               # Custom Error class
│   │   ├── logger.js                 # Winston logger config
│   │   └── redis.js                  # Redis client (ixtiyoriy)
│   │
│   ├── constants/
│   │   ├── httpStatus.js             # 200, 201, 400, 401, 404, 500...
│   │   ├── errorMessages.js          # Standart error messages
│   │   ├── events.js                 # Event names (USER_CREATED, ORDER_CREATED...)
│   │   └── roles.js                  # USER, ADMIN, SELLER...
│   │
│   ├── config/
│   │   ├── messageQueue.js           # RabbitMQ/Redis Pub/Sub config
│   │   └── database.js               # Database utilities
│   │
│   └── package.json                  # Shared dependencies
│
├── config/                            # ⚙️ Global konfiguratsiya
│   ├── pm2/
│   │   └── ecosystem.config.js       # PM2 config (barcha servislar)
│   │
│   └── nginx/                         # Nginx reverse proxy (ixtiyoriy)
│       └── nginx.conf                # Load balancing config
│
├── scripts/                           # 🔧 Automation scripts
│   ├── setup.sh                      # Initial setup (npm install hamma joyda)
│   ├── start-all.sh                  # pm2 start ecosystem.config.js
│   ├── stop-all.sh                   # pm2 stop all
│   ├── restart-all.sh                # pm2 restart all
│   ├── logs.sh                       # pm2 logs
│   ├── delete-all.sh                 # pm2 delete all
│   ├── seed-db.sh                    # Database seed data
│   └── db-migrate.sh                 # Run migrations
│
├── logs/                              # 📝 PM2 logs (auto-generated)
│   ├── api-gateway/
│   │   ├── error.log
│   │   └── out.log
│   ├── user-service/
│   ├── product-service/
│   ├── order-service/
│   ├── payment-service/
│   └── notification-service/
│
├── docs/                              # 📚 Dokumentatsiya
│   ├── API.md                        # API endpoints documentation
│   ├── ARCHITECTURE.md               # Arxitektura tushuntirish
│   ├── SETUP.md                      # Setup instructions
│   ├── DEPLOYMENT.md                 # Deployment guide
│   ├── CONTRIBUTING.md               # Contribution guidelines
│   └── diagrams/                     # Architecture diagrams
│       ├── microservices-flow.png
│       └── database-schema.png
│
├── .gitignore                         # Git ignore file
├── .env.example                       # Environment variables example
├── .prettierrc                        # Code formatting
├── .eslintrc.js                       # Linting rules
├── package.json                       # Root package.json (scripts)
├── lerna.json                         # Lerna config (ixtiyoriy)
├── README.md                          # Main README
└── LICENSE                            # License file
```

---

## 📦 Root package.json

```json
{
  "name": "microservices-project",
  "version": "1.0.0",
  "description": "Node.js Express Microservices Architecture with PM2",
  "main": "index.js",
  "scripts": {
    "start": "pm2 start config/pm2/ecosystem.config.js",
    "start:prod": "pm2 start config/pm2/ecosystem.config.js --env production",
    "stop": "pm2 stop all",
    "restart": "pm2 restart all",
    "delete": "pm2 delete all",
    "logs": "pm2 logs",
    "monit": "pm2 monit",
    "setup": "bash scripts/setup.sh",
    "dev:gateway": "cd services/api-gateway && npm run dev",
    "dev:user": "cd services/user-service && npm run dev",
    "dev:product": "cd services/product-service && npm run dev",
    "dev:order": "cd services/order-service && npm run dev",
    "dev:payment": "cd services/payment-service && npm run dev",
    "dev:notification": "cd services/notification-service && npm run dev",
    "test": "npm run test --workspaces",
    "test:unit": "npm run test:unit --workspaces",
    "test:integration": "npm run test:integration --workspaces",
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check ."
  },
  "workspaces": ["services/*", "shared"],
  "keywords": ["microservices", "nodejs", "express", "pm2", "monorepo"],
  "author": "Your Name",
  "license": "MIT",
  "devDependencies": {
    "eslint": "^8.57.0",
    "eslint-config-airbnb-base": "^15.0.0",
    "eslint-plugin-import": "^2.29.1",
    "prettier": "^3.2.5",
    "pm2": "^5.3.1",
    "lerna": "^8.1.2"
  }
}
```

---

## 🔧 PM2 Ecosystem Config

**File:** `config/pm2/ecosystem.config.js`

```javascript
module.exports = {
  apps: [
    {
      name: "api-gateway",
      script: "./services/api-gateway/src/server.js",
      instances: 2,
      exec_mode: "cluster",
      watch: false,
      env: {
        NODE_ENV: "development",
        PORT: 3000,
      },
      env_production: {
        NODE_ENV: "production",
        PORT: 3000,
      },
      error_file: "./logs/api-gateway/error.log",
      out_file: "./logs/api-gateway/out.log",
      log_date_format: "YYYY-MM-DD HH:mm:ss Z",
      merge_logs: true,
      autorestart: true,
      max_memory_restart: "500M",
      min_uptime: "10s",
      max_restarts: 10,
    },
    {
      name: "user-service",
      script: "./services/user-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      watch: false,
      env: {
        NODE_ENV: "development",
        PORT: 3001,
        DB_NAME: "user_db",
      },
      env_production: {
        NODE_ENV: "production",
        PORT: 3001,
        DB_NAME: "user_db",
      },
      error_file: "./logs/user-service/error.log",
      out_file: "./logs/user-service/out.log",
      log_date_format: "YYYY-MM-DD HH:mm:ss Z",
      autorestart: true,
      max_memory_restart: "300M",
    },
    {
      name: "product-service",
      script: "./services/product-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      watch: false,
      env: {
        NODE_ENV: "development",
        PORT: 3002,
        DB_NAME: "product_db",
      },
      env_production: {
        NODE_ENV: "production",
        PORT: 3002,
        DB_NAME: "product_db",
      },
      error_file: "./logs/product-service/error.log",
      out_file: "./logs/product-service/out.log",
      autorestart: true,
      max_memory_restart: "300M",
    },
    {
      name: "order-service",
      script: "./services/order-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      env: {
        NODE_ENV: "development",
        PORT: 3003,
        DB_NAME: "order_db",
      },
      env_production: {
        NODE_ENV: "production",
        PORT: 3003,
        DB_NAME: "order_db",
      },
      error_file: "./logs/order-service/error.log",
      out_file: "./logs/order-service/out.log",
      autorestart: true,
    },
    {
      name: "payment-service",
      script: "./services/payment-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      env: {
        NODE_ENV: "development",
        PORT: 3004,
        DB_NAME: "payment_db",
      },
      env_production: {
        NODE_ENV: "production",
        PORT: 3004,
        DB_NAME: "payment_db",
      },
      error_file: "./logs/payment-service/error.log",
      out_file: "./logs/payment-service/out.log",
      autorestart: true,
    },
    {
      name: "notification-service",
      script: "./services/notification-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      env: {
        NODE_ENV: "development",
        PORT: 3005,
      },
      env_production: {
        NODE_ENV: "production",
        PORT: 3005,
      },
      error_file: "./logs/notification-service/error.log",
      out_file: "./logs/notification-service/out.log",
      autorestart: true,
    },
  ],
};
```

---

## 🚀 PM2 Komandalar

### Asosiy komandalar

```bash
# Barcha servislarni ishga tushirish
pm2 start config/pm2/ecosystem.config.js

# Production mode
pm2 start config/pm2/ecosystem.config.js --env production

# Bitta servisni ishga tushirish
pm2 start config/pm2/ecosystem.config.js --only user-service

# Status ko'rish
pm2 status
pm2 list

# Loglarni ko'rish
pm2 logs                    # Barcha servislar
pm2 logs user-service       # Bitta servis
pm2 logs --lines 100        # Oxirgi 100 qator

# Restart
pm2 restart all
pm2 restart user-service
pm2 restart config/pm2/ecosystem.config.js

# Stop
pm2 stop all
pm2 stop user-service

# Delete
pm2 delete all
pm2 delete user-service

# Monitoring
pm2 monit                   # Real-time monitoring

# Auto restart on boot (server restart bo'lganda)
pm2 startup
pm2 save

# Flush logs
pm2 flush

# Reload (0 downtime restart - cluster mode uchun)
pm2 reload all
```

---

## 📋 Har bir Servisning package.json namunasi

**File:** `services/user-service/package.json`

```json
{
  "name": "user-service",
  "version": "1.0.0",
  "description": "User management microservice",
  "main": "src/server.js",
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js",
    "test": "jest",
    "test:unit": "jest --testPathPattern=tests/unit",
    "test:integration": "jest --testPathPattern=tests/integration",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage"
  },
  "dependencies": {
    "express": "^4.18.2",
    "mongoose": "^8.1.1",
    "bcryptjs": "^2.4.3",
    "jsonwebtoken": "^9.0.2",
    "dotenv": "^16.4.1",
    "cors": "^2.8.5",
    "helmet": "^7.1.0",
    "express-rate-limit": "^7.1.5",
    "joi": "^17.12.0",
    "winston": "^3.11.0",
    "nodemailer": "^6.9.8"
  },
  "devDependencies": {
    "nodemon": "^3.0.3",
    "jest": "^29.7.0",
    "supertest": "^6.3.4",
    "@types/jest": "^29.5.11"
  }
}
```

---

## 🔐 .env.example

**File:** `.env.example` (root level)

```env
# Node Environment
NODE_ENV=development

# API Gateway
GATEWAY_PORT=3000

# User Service
USER_SERVICE_PORT=3001
USER_DB_URI=mongodb://localhost:27017/user_db
USER_JWT_SECRET=your-super-secret-jwt-key-change-this
USER_JWT_EXPIRE=7d

# Product Service
PRODUCT_SERVICE_PORT=3002
PRODUCT_DB_URI=mongodb://localhost:27017/product_db

# Order Service
ORDER_SERVICE_PORT=3003
ORDER_DB_URI=mongodb://localhost:27017/order_db

# Payment Service
PAYMENT_SERVICE_PORT=3004
PAYMENT_DB_URI=mongodb://localhost:27017/payment_db
STRIPE_SECRET_KEY=sk_test_your_stripe_secret_key
STRIPE_WEBHOOK_SECRET=whsec_your_webhook_secret

# Notification Service
NOTIFICATION_SERVICE_PORT=3005
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-app-password

# Redis (ixtiyoriy - caching uchun)
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=

# RabbitMQ (ixtiyoriy - message queue uchun)
RABBITMQ_URL=amqp://localhost:5672

# Logging
LOG_LEVEL=debug
```

---

## 📜 Scripts

### setup.sh

```bash
#!/bin/bash

echo "🚀 Microservices Setup Starting..."

# Root dependencies
echo "📦 Installing root dependencies..."
npm install

# Install dependencies for all services
echo "📦 Installing service dependencies..."

services=("api-gateway" "user-service" "product-service" "order-service" "payment-service" "notification-service")

for service in "${services[@]}"
do
  echo "📦 Installing dependencies for $service..."
  cd services/$service
  npm install
  cd ../..
done

# Install shared dependencies
echo "📦 Installing shared dependencies..."
cd shared
npm install
cd ..

# Create logs directories
echo "📁 Creating logs directories..."
mkdir -p logs/api-gateway
mkdir -p logs/user-service
mkdir -p logs/product-service
mkdir -p logs/order-service
mkdir -p logs/payment-service
mkdir -p logs/notification-service

# Copy .env.example to .env if not exists
if [ ! -f .env ]; then
  echo "📝 Creating .env file..."
  cp .env.example .env
  echo "⚠️  Please update .env file with your credentials"
fi

echo "✅ Setup completed successfully!"
echo "🚀 Run 'npm start' to start all services"
```

### start-all.sh

```bash
#!/bin/bash

echo "🚀 Starting all microservices with PM2..."

pm2 start config/pm2/ecosystem.config.js

echo "✅ All services started!"
echo "📊 Run 'pm2 status' to check services"
echo "📝 Run 'pm2 logs' to see logs"
```

### stop-all.sh

```bash
#!/bin/bash

echo "🛑 Stopping all microservices..."

pm2 stop all

echo "✅ All services stopped!"
```

### restart-all.sh

```bash
#!/bin/bash

echo "🔄 Restarting all microservices..."

pm2 restart all

echo "✅ All services restarted!"
```

### logs.sh

```bash
#!/bin/bash

pm2 logs
```

---

## 🗂️ .gitignore

```gitignore
# Dependencies
node_modules/
npm-debug.log*
yarn-debug.log*
yarn-error.log*

# Environment variables
.env
.env.local
.env.*.local

# Logs
logs/
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
lerna-debug.log*
.pnpm-debug.log*

# PM2
.pm2/
pids/
*.pid
*.seed
*.pid.lock

# Testing
coverage/
.nyc_output/

# Build
dist/
build/

# IDE
.vscode/
.idea/
*.swp
*.swo
*~

# OS
.DS_Store
Thumbs.db

# Temporary files
tmp/
temp/
```

---

## 🎯 Asosiy Prinsiplar

### 1. Single Responsibility

Har bir servis faqat bitta vazifani bajaradi

### 2. Independent Deployment

Servislar mustaqil ravishda deploy qilinadi

### 3. Decentralized Data

Har bir servis o'z database ga ega

### 4. Communication Patterns

- **Synchronous**: REST API (service-to-service)
- **Asynchronous**: Message Queue (RabbitMQ/Redis)

### 5. Fault Tolerance

- Health check endpoints
- Circuit breaker pattern
- Retry logic
- Graceful shutdown

---

## 📊 Port Configuration

| Service              | Port | Description             |
| -------------------- | ---- | ----------------------- |
| API Gateway          | 3000 | Main entry point        |
| User Service         | 3001 | User management         |
| Product Service      | 3002 | Product catalog         |
| Order Service        | 3003 | Order processing        |
| Payment Service      | 3004 | Payment handling        |
| Notification Service | 3005 | Email/SMS notifications |

---

## 🔄 Service Communication Flow

```
Client → API Gateway (3000)
           ↓
      ┌────┴────┐
      ↓         ↓
User Service  Product Service
  (3001)        (3002)
      ↓            ↓
      └────┬───────┘
           ↓
     Order Service (3003)
           ↓
     Payment Service (3004)
           ↓
  Notification Service (3005)
```

---

## 🛠️ Technology Stack

- **Runtime**: Node.js
- **Framework**: Express.js
- **Process Manager**: PM2
- **Database**: MongoDB / PostgreSQL
- **Cache**: Redis (ixtiyoriy)
- **Message Queue**: RabbitMQ / Redis Pub/Sub (ixtiyoriy)
- **Authentication**: JWT
- **Validation**: Joi / Yup
- **Logging**: Winston
- **Testing**: Jest, Supertest

---

## 📚 Next Steps

1. ✅ Clone qiling bu strukturani
2. ✅ `npm run setup` - dependencies o'rnatish
3. ✅ `.env` faylini sozlang
4. ✅ Database o'rnating (MongoDB/PostgreSQL)
5. ✅ `npm start` - barcha servislarni ishga tushiring
6. ✅ Postman orqali test qiling

---

## 📖 Additional Resources

- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [PM2 Documentation](https://pm2.keymetrics.io/)
- [Express.js Guide](https://expressjs.com/)
- [Microservices Patterns](https://microservices.io/patterns/index.html)

---

**Muallif**: Oybek Abdujabborov  
**Versiya**: 1.0.0  
**Sana**: 2026-010-01  
**License**: MIT

---

🎉 **Omad tilaymiz! Happy Coding!** 🚀
