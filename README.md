# 🚀 Node.js Express Microservices - To'liq Professional Guide

> **PM2 + Docker + MongoDB + PostgreSQL + MySQL2 - Mukammal Monorepo Arxitekturasi**

---

## 📑 Mundarija

1. [Loyiha Strukturasi](#-loyiha-strukturasi)
2. [Technology Stack](#-technology-stack)
3. [Database Konfiguratsiyalar](#-database-konfiguratsiyalar)
4. [Docker Setup](#-docker-setup)
5. [PM2 Setup](#-pm2-setup)
6. [Kod Misollari](#-kod-misollari)
7. [Ishga Tushirish](#-ishga-tushirish)
8. [Testing](#-testing)
9. [Deployment](#-deployment)
10. [Troubleshooting](#-troubleshooting)

---

## 📁 Loyiha Strukturasi

```
microservices-project/
│
├── services/                          # 🎯 Barcha microservislar
│   │
│   ├── api-gateway/                   # 🚪 API Gateway (Port: 3000)
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── index.js          # Asosiy config
│   │   │   │   └── services.js       # Servislarning URL manzillari
│   │   │   │
│   │   │   ├── middlewares/
│   │   │   │   ├── auth.js           # JWT verification
│   │   │   │   ├── rateLimiter.js    # Rate limiting (express-rate-limit)
│   │   │   │   ├── requestLogger.js  # Request logging
│   │   │   │   ├── cors.js           # CORS configuration
│   │   │   │   └── errorHandler.js   # Global error handler
│   │   │   │
│   │   │   ├── routes/
│   │   │   │   ├── index.js          # Routes aggregator
│   │   │   │   ├── userRoutes.js     # Proxy to user-service
│   │   │   │   ├── productRoutes.js  # Proxy to product-service
│   │   │   │   ├── orderRoutes.js    # Proxy to order-service
│   │   │   │   ├── paymentRoutes.js  # Proxy to payment-service
│   │   │   │   └── healthRoutes.js   # Health check
│   │   │   │
│   │   │   ├── utils/
│   │   │   │   ├── httpClient.js     # Axios instance
│   │   │   │   ├── responseHandler.js
│   │   │   │   ├── logger.js         # Winston logger
│   │   │   │   └── validator.js
│   │   │   │
│   │   │   ├── app.js
│   │   │   └── server.js
│   │   │
│   │   ├── tests/
│   │   │   ├── unit/
│   │   │   └── integration/
│   │   │
│   │   ├── .env
│   │   ├── .env.example
│   │   ├── .dockerignore
│   │   ├── Dockerfile
│   │   ├── package.json
│   │   └── README.md
│   │
│   ├── user-service/                  # 👤 User Service (MongoDB - Port: 3001)
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── index.js
│   │   │   │   ├── mongodb.js        # MongoDB connection (Mongoose)
│   │   │   │   └── redis.js          # Redis cache
│   │   │   │
│   │   │   ├── controllers/
│   │   │   │   ├── authController.js # register, login, logout, refresh
│   │   │   │   └── userController.js # CRUD operations
│   │   │   │
│   │   │   ├── models/
│   │   │   │   └── User.js           # Mongoose schema
│   │   │   │
│   │   │   ├── routes/
│   │   │   │   ├── index.js
│   │   │   │   ├── authRoutes.js
│   │   │   │   └── userRoutes.js
│   │   │   │
│   │   │   ├── services/
│   │   │   │   ├── authService.js
│   │   │   │   └── userService.js
│   │   │   │
│   │   │   ├── middlewares/
│   │   │   │   ├── auth.js
│   │   │   │   ├── validate.js
│   │   │   │   └── errorHandler.js
│   │   │   │
│   │   │   ├── utils/
│   │   │   │   ├── jwt.js
│   │   │   │   ├── bcrypt.js
│   │   │   │   ├── validators.js
│   │   │   │   ├── logger.js
│   │   │   │   └── sendEmail.js
│   │   │   │
│   │   │   ├── events/
│   │   │   │   ├── publishers/
│   │   │   │   │   └── userPublisher.js
│   │   │   │   └── subscribers/
│   │   │   │       └── userSubscriber.js
│   │   │   │
│   │   │   ├── app.js
│   │   │   └── server.js
│   │   │
│   │   ├── tests/
│   │   ├── .env
│   │   ├── Dockerfile
│   │   └── package.json
│   │
│   ├── product-service/               # 📦 Product Service (PostgreSQL - Port: 3002)
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── index.js
│   │   │   │   └── postgres.js       # PostgreSQL connection (Sequelize/pg)
│   │   │   │
│   │   │   ├── controllers/
│   │   │   │   ├── productController.js
│   │   │   │   └── categoryController.js
│   │   │   │
│   │   │   ├── models/
│   │   │   │   ├── Product.js        # Sequelize model
│   │   │   │   └── Category.js
│   │   │   │
│   │   │   ├── migrations/           # Database migrations
│   │   │   │   └── 001-create-products.js
│   │   │   │
│   │   │   ├── routes/
│   │   │   ├── services/
│   │   │   ├── middlewares/
│   │   │   ├── utils/
│   │   │   ├── events/
│   │   │   ├── app.js
│   │   │   └── server.js
│   │   │
│   │   ├── tests/
│   │   ├── .env
│   │   ├── Dockerfile
│   │   └── package.json
│   │
│   ├── order-service/                 # 🛒 Order Service (MySQL - Port: 3003)
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── index.js
│   │   │   │   └── mysql.js          # MySQL connection (mysql2/Sequelize)
│   │   │   │
│   │   │   ├── controllers/
│   │   │   │   └── orderController.js
│   │   │   │
│   │   │   ├── models/
│   │   │   │   └── Order.js
│   │   │   │
│   │   │   ├── routes/
│   │   │   ├── services/
│   │   │   ├── middlewares/
│   │   │   ├── utils/
│   │   │   ├── events/
│   │   │   ├── app.js
│   │   │   └── server.js
│   │   │
│   │   ├── tests/
│   │   ├── .env
│   │   ├── Dockerfile
│   │   └── package.json
│   │
│   ├── payment-service/               # 💳 Payment Service (MongoDB - Port: 3004)
│   │   ├── src/
│   │   │   ├── config/
│   │   │   │   ├── index.js
│   │   │   │   ├── mongodb.js
│   │   │   │   └── stripe.js
│   │   │   │
│   │   │   ├── controllers/
│   │   │   ├── models/
│   │   │   ├── routes/
│   │   │   ├── services/
│   │   │   ├── middlewares/
│   │   │   ├── utils/
│   │   │   ├── events/
│   │   │   ├── app.js
│   │   │   └── server.js
│   │   │
│   │   ├── tests/
│   │   ├── .env
│   │   ├── Dockerfile
│   │   └── package.json
│   │
│   └── notification-service/          # 📧 Notification Service (Port: 3005)
│       ├── src/
│       │   ├── config/
│       │   ├── controllers/
│       │   ├── services/
│       │   ├── routes/
│       │   ├── middlewares/
│       │   ├── utils/
│       │   ├── events/
│       │   ├── app.js
│       │   └── server.js
│       │
│       ├── tests/
│       ├── .env
│       ├── Dockerfile
│       └── package.json
│
├── shared/                            # 🔄 Shared libraries
│   ├── middlewares/
│   │   ├── errorHandler.js
│   │   ├── logger.js
│   │   ├── asyncHandler.js
│   │   └── validateRequest.js
│   │
│   ├── utils/
│   │   ├── jwt.js
│   │   ├── response.js
│   │   ├── AppError.js
│   │   ├── logger.js
│   │   └── redis.js
│   │
│   ├── constants/
│   │   ├── httpStatus.js
│   │   ├── errorMessages.js
│   │   ├── events.js
│   │   └── roles.js
│   │
│   ├── config/
│   │   ├── messageQueue.js
│   │   └── database.js
│   │
│   └── package.json
│
├── config/                            # ⚙️ Configuration
│   ├── pm2/
│   │   └── ecosystem.config.js       # PM2 configuration
│   │
│   ├── docker/
│   │   ├── docker-compose.yml        # Docker Compose
│   │   ├── docker-compose.dev.yml    # Development
│   │   └── docker-compose.prod.yml   # Production
│   │
│   └── nginx/
│       ├── nginx.conf
│       └── Dockerfile
│
├── scripts/                           # 🔧 Automation scripts
│   ├── setup.sh
│   ├── docker-setup.sh
│   ├── start-all.sh
│   ├── stop-all.sh
│   ├── restart-all.sh
│   ├── logs.sh
│   ├── db-setup.sh
│   ├── db-migrate.sh
│   └── db-seed.sh
│
├── logs/                              # 📝 Logs
│   ├── api-gateway/
│   ├── user-service/
│   ├── product-service/
│   ├── order-service/
│   ├── payment-service/
│   └── notification-service/
│
├── docs/                              # 📚 Documentation
│   ├── API.md
│   ├── ARCHITECTURE.md
│   ├── SETUP.md
│   ├── DEPLOYMENT.md
│   ├── DATABASE.md
│   └── diagrams/
│
├── .gitignore
├── .env.example
├── .prettierrc
├── .eslintrc.js
├── docker-compose.yml
├── package.json
├── README.md
└── LICENSE
```

---

## 🛠️ Technology Stack

### Backend

- **Runtime:** Node.js 20.x LTS
- **Framework:** Express.js 4.x
- **Process Manager:** PM2 5.x

### Databases

- **MongoDB:** 7.x (Mongoose)
- **PostgreSQL:** 16.x (Sequelize / pg)
- **MySQL:** 8.x (mysql2 / Sequelize)
- **Redis:** 7.x (Caching & Pub/Sub)

### Message Queue

- **RabbitMQ:** 3.x
- **Redis Pub/Sub**

### DevOps

- **Docker:** 24.x
- **Docker Compose:** 2.x
- **Nginx:** Latest (Load Balancer)

### Libraries

- **Authentication:** JWT (jsonwebtoken)
- **Validation:** Joi
- **Logging:** Winston
- **Testing:** Jest, Supertest
- **API Docs:** Swagger

---

## 🗄️ Database Konfiguratsiyalar

### 1️⃣ MongoDB Configuration (Mongoose)

**File:** `services/user-service/src/config/mongodb.js`

```javascript
const mongoose = require("mongoose");
const logger = require("../../../shared/utils/logger");

const connectMongoDB = async () => {
  try {
    const options = {
      useNewUrlParser: true,
      useUnifiedTopology: true,
      maxPoolSize: 10,
      serverSelectionTimeoutMS: 5000,
      socketTimeoutMS: 45000,
    };

    await mongoose.connect(process.env.MONGODB_URI, options);

    logger.info("✅ MongoDB connected successfully");

    // Connection events
    mongoose.connection.on("error", (err) => {
      logger.error("MongoDB connection error:", err);
    });

    mongoose.connection.on("disconnected", () => {
      logger.warn("MongoDB disconnected. Attempting to reconnect...");
    });

    mongoose.connection.on("reconnected", () => {
      logger.info("MongoDB reconnected");
    });

    // Graceful shutdown
    process.on("SIGINT", async () => {
      await mongoose.connection.close();
      logger.info("MongoDB connection closed through app termination");
      process.exit(0);
    });
  } catch (error) {
    logger.error("❌ MongoDB connection failed:", error);
    process.exit(1);
  }
};

module.exports = connectMongoDB;
```

**Mongoose Model Example:**

```javascript
// services/user-service/src/models/User.js
const mongoose = require("mongoose");
const bcrypt = require("bcryptjs");

const userSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: [true, "Name is required"],
      trim: true,
      maxlength: [50, "Name cannot be more than 50 characters"],
    },
    email: {
      type: String,
      required: [true, "Email is required"],
      unique: true,
      lowercase: true,
      match: [
        /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/,
        "Please provide a valid email",
      ],
    },
    password: {
      type: String,
      required: [true, "Password is required"],
      minlength: [6, "Password must be at least 6 characters"],
      select: false,
    },
    role: {
      type: String,
      enum: ["user", "admin", "seller"],
      default: "user",
    },
    isActive: {
      type: Boolean,
      default: true,
    },
    lastLogin: {
      type: Date,
    },
  },
  {
    timestamps: true,
    toJSON: { virtuals: true },
    toObject: { virtuals: true },
  },
);

// Indexes
userSchema.index({ email: 1 });
userSchema.index({ createdAt: -1 });

// Pre-save middleware
userSchema.pre("save", async function (next) {
  if (!this.isModified("password")) return next();

  const salt = await bcrypt.genSalt(10);
  this.password = await bcrypt.hash(this.password, salt);
  next();
});

// Instance method
userSchema.methods.comparePassword = async function (candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
};

// Static method
userSchema.statics.findByEmail = function (email) {
  return this.findOne({ email }).select("+password");
};

module.exports = mongoose.model("User", userSchema);
```

---

### 2️⃣ PostgreSQL Configuration (Sequelize)

**File:** `services/product-service/src/config/postgres.js`

```javascript
const { Sequelize } = require("sequelize");
const logger = require("../../../shared/utils/logger");

const sequelize = new Sequelize(
  process.env.POSTGRES_DB,
  process.env.POSTGRES_USER,
  process.env.POSTGRES_PASSWORD,
  {
    host: process.env.POSTGRES_HOST,
    port: process.env.POSTGRES_PORT || 5432,
    dialect: "postgres",
    logging: (msg) => logger.debug(msg),
    pool: {
      max: 10,
      min: 0,
      acquire: 30000,
      idle: 10000,
    },
    dialectOptions: {
      ssl:
        process.env.NODE_ENV === "production"
          ? {
              require: true,
              rejectUnauthorized: false,
            }
          : false,
    },
  },
);

const connectPostgreSQL = async () => {
  try {
    await sequelize.authenticate();
    logger.info("✅ PostgreSQL connected successfully");

    // Sync models (development only)
    if (process.env.NODE_ENV === "development") {
      await sequelize.sync({ alter: true });
      logger.info("Database synchronized");
    }

    // Graceful shutdown
    process.on("SIGINT", async () => {
      await sequelize.close();
      logger.info("PostgreSQL connection closed");
      process.exit(0);
    });
  } catch (error) {
    logger.error("❌ PostgreSQL connection failed:", error);
    process.exit(1);
  }
};

module.exports = { sequelize, connectPostgreSQL };
```

**Sequelize Model Example:**

```javascript
// services/product-service/src/models/Product.js
const { DataTypes } = require("sequelize");
const { sequelize } = require("../config/postgres");

const Product = sequelize.define(
  "Product",
  {
    id: {
      type: DataTypes.UUID,
      defaultValue: DataTypes.UUIDV4,
      primaryKey: true,
    },
    name: {
      type: DataTypes.STRING(255),
      allowNull: false,
      validate: {
        notEmpty: true,
        len: [3, 255],
      },
    },
    description: {
      type: DataTypes.TEXT,
      allowNull: true,
    },
    price: {
      type: DataTypes.DECIMAL(10, 2),
      allowNull: false,
      validate: {
        min: 0,
      },
    },
    stock: {
      type: DataTypes.INTEGER,
      allowNull: false,
      defaultValue: 0,
      validate: {
        min: 0,
      },
    },
    categoryId: {
      type: DataTypes.UUID,
      allowNull: true,
      references: {
        model: "Categories",
        key: "id",
      },
    },
    images: {
      type: DataTypes.ARRAY(DataTypes.STRING),
      defaultValue: [],
    },
    isActive: {
      type: DataTypes.BOOLEAN,
      defaultValue: true,
    },
  },
  {
    tableName: "products",
    timestamps: true,
    indexes: [
      { fields: ["name"] },
      { fields: ["categoryId"] },
      { fields: ["createdAt"] },
    ],
  },
);

// Associations
Product.associate = (models) => {
  Product.belongsTo(models.Category, {
    foreignKey: "categoryId",
    as: "category",
  });
};

module.exports = Product;
```

**Alternative: Raw pg (without Sequelize):**

```javascript
// services/product-service/src/config/postgres-raw.js
const { Pool } = require("pg");
const logger = require("../../../shared/utils/logger");

const pool = new Pool({
  host: process.env.POSTGRES_HOST,
  port: process.env.POSTGRES_PORT || 5432,
  database: process.env.POSTGRES_DB,
  user: process.env.POSTGRES_USER,
  password: process.env.POSTGRES_PASSWORD,
  max: 20,
  idleTimeoutMillis: 30000,
  connectionTimeoutMillis: 2000,
});

pool.on("connect", () => {
  logger.info("PostgreSQL client connected");
});

pool.on("error", (err) => {
  logger.error("Unexpected error on idle client", err);
  process.exit(-1);
});

const query = async (text, params) => {
  const start = Date.now();
  try {
    const res = await pool.query(text, params);
    const duration = Date.now() - start;
    logger.debug("Executed query", { text, duration, rows: res.rowCount });
    return res;
  } catch (error) {
    logger.error("Query error", { text, error: error.message });
    throw error;
  }
};

module.exports = { pool, query };
```

---

### 3️⃣ MySQL Configuration (mysql2)

**File:** `services/order-service/src/config/mysql.js`

```javascript
const mysql = require("mysql2/promise");
const logger = require("../../../shared/utils/logger");

let pool;

const connectMySQL = async () => {
  try {
    pool = mysql.createPool({
      host: process.env.MYSQL_HOST,
      port: process.env.MYSQL_PORT || 3306,
      user: process.env.MYSQL_USER,
      password: process.env.MYSQL_PASSWORD,
      database: process.env.MYSQL_DATABASE,
      waitForConnections: true,
      connectionLimit: 10,
      queueLimit: 0,
      enableKeepAlive: true,
      keepAliveInitialDelay: 0,
    });

    // Test connection
    const connection = await pool.getConnection();
    logger.info("✅ MySQL connected successfully");
    connection.release();

    // Graceful shutdown
    process.on("SIGINT", async () => {
      await pool.end();
      logger.info("MySQL connection closed");
      process.exit(0);
    });
  } catch (error) {
    logger.error("❌ MySQL connection failed:", error);
    process.exit(1);
  }
};

const query = async (sql, params) => {
  try {
    const [rows, fields] = await pool.execute(sql, params);
    return rows;
  } catch (error) {
    logger.error("MySQL query error:", error);
    throw error;
  }
};

const transaction = async (callback) => {
  const connection = await pool.getConnection();
  await connection.beginTransaction();

  try {
    const result = await callback(connection);
    await connection.commit();
    return result;
  } catch (error) {
    await connection.rollback();
    throw error;
  } finally {
    connection.release();
  }
};

module.exports = { connectMySQL, query, transaction, pool };
```

**MySQL with Sequelize Example:**

```javascript
// services/order-service/src/config/mysql-sequelize.js
const { Sequelize } = require("sequelize");
const logger = require("../../../shared/utils/logger");

const sequelize = new Sequelize(
  process.env.MYSQL_DATABASE,
  process.env.MYSQL_USER,
  process.env.MYSQL_PASSWORD,
  {
    host: process.env.MYSQL_HOST,
    port: process.env.MYSQL_PORT || 3306,
    dialect: "mysql",
    logging: (msg) => logger.debug(msg),
    pool: {
      max: 10,
      min: 0,
      acquire: 30000,
      idle: 10000,
    },
  },
);

const connectMySQL = async () => {
  try {
    await sequelize.authenticate();
    logger.info("✅ MySQL connected successfully");

    if (process.env.NODE_ENV === "development") {
      await sequelize.sync({ alter: true });
      logger.info("Database synchronized");
    }
  } catch (error) {
    logger.error("❌ MySQL connection failed:", error);
    process.exit(1);
  }
};

module.exports = { sequelize, connectMySQL };
```

**Order Model Example:**

```javascript
// services/order-service/src/models/Order.js
const { DataTypes } = require("sequelize");
const { sequelize } = require("../config/mysql-sequelize");

const Order = sequelize.define(
  "Order",
  {
    id: {
      type: DataTypes.UUID,
      defaultValue: DataTypes.UUIDV4,
      primaryKey: true,
    },
    userId: {
      type: DataTypes.UUID,
      allowNull: false,
    },
    products: {
      type: DataTypes.JSON,
      allowNull: false,
      defaultValue: [],
    },
    totalPrice: {
      type: DataTypes.DECIMAL(10, 2),
      allowNull: false,
    },
    status: {
      type: DataTypes.ENUM(
        "pending",
        "processing",
        "shipped",
        "delivered",
        "cancelled",
      ),
      defaultValue: "pending",
    },
    paymentStatus: {
      type: DataTypes.ENUM("pending", "paid", "failed", "refunded"),
      defaultValue: "pending",
    },
    shippingAddress: {
      type: DataTypes.JSON,
      allowNull: false,
    },
  },
  {
    tableName: "orders",
    timestamps: true,
    indexes: [
      { fields: ["userId"] },
      { fields: ["status"] },
      { fields: ["createdAt"] },
    ],
  },
);

module.exports = Order;
```

---

### 4️⃣ Redis Configuration

**File:** `shared/utils/redis.js`

```javascript
const Redis = require("ioredis");
const logger = require("./logger");

let redisClient;

const connectRedis = () => {
  try {
    redisClient = new Redis({
      host: process.env.REDIS_HOST || "localhost",
      port: process.env.REDIS_PORT || 6379,
      password: process.env.REDIS_PASSWORD || undefined,
      retryStrategy: (times) => {
        const delay = Math.min(times * 50, 2000);
        return delay;
      },
      maxRetriesPerRequest: 3,
    });

    redisClient.on("connect", () => {
      logger.info("✅ Redis connected");
    });

    redisClient.on("error", (err) => {
      logger.error("Redis error:", err);
    });

    redisClient.on("reconnecting", () => {
      logger.warn("Redis reconnecting...");
    });

    // Graceful shutdown
    process.on("SIGINT", async () => {
      await redisClient.quit();
      logger.info("Redis connection closed");
    });

    return redisClient;
  } catch (error) {
    logger.error("❌ Redis connection failed:", error);
    return null;
  }
};

// Cache helper functions
const cache = {
  get: async (key) => {
    try {
      const data = await redisClient.get(key);
      return data ? JSON.parse(data) : null;
    } catch (error) {
      logger.error("Redis get error:", error);
      return null;
    }
  },

  set: async (key, value, ttl = 3600) => {
    try {
      await redisClient.setex(key, ttl, JSON.stringify(value));
      return true;
    } catch (error) {
      logger.error("Redis set error:", error);
      return false;
    }
  },

  del: async (key) => {
    try {
      await redisClient.del(key);
      return true;
    } catch (error) {
      logger.error("Redis delete error:", error);
      return false;
    }
  },

  clear: async (pattern = "*") => {
    try {
      const keys = await redisClient.keys(pattern);
      if (keys.length > 0) {
        await redisClient.del(...keys);
      }
      return true;
    } catch (error) {
      logger.error("Redis clear error:", error);
      return false;
    }
  },
};

module.exports = { connectRedis, redisClient, cache };
```

---

## 🐳 Docker Setup

### Docker Compose (Development)

**File:** `docker-compose.yml`

```yaml
version: "3.8"

services:
  # ========== API Gateway ==========
  api-gateway:
    build:
      context: ./services/api-gateway
      dockerfile: Dockerfile
    container_name: api-gateway
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - PORT=3000
    volumes:
      - ./services/api-gateway:/app
      - /app/node_modules
    depends_on:
      - user-service
      - product-service
      - order-service
      - payment-service
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== User Service (MongoDB) ==========
  user-service:
    build:
      context: ./services/user-service
      dockerfile: Dockerfile
    container_name: user-service
    ports:
      - "3001:3001"
    environment:
      - NODE_ENV=development
      - PORT=3001
      - MONGODB_URI=mongodb://mongodb:27017/user_db
      - JWT_SECRET=your-super-secret-jwt-key
      - JWT_EXPIRE=7d
      - REDIS_HOST=redis
      - REDIS_PORT=6379
    volumes:
      - ./services/user-service:/app
      - /app/node_modules
    depends_on:
      - mongodb
      - redis
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== Product Service (PostgreSQL) ==========
  product-service:
    build:
      context: ./services/product-service
      dockerfile: Dockerfile
    container_name: product-service
    ports:
      - "3002:3002"
    environment:
      - NODE_ENV=development
      - PORT=3002
      - POSTGRES_HOST=postgres
      - POSTGRES_PORT=5432
      - POSTGRES_DB=product_db
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres123
    volumes:
      - ./services/product-service:/app
      - /app/node_modules
    depends_on:
      - postgres
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== Order Service (MySQL) ==========
  order-service:
    build:
      context: ./services/order-service
      dockerfile: Dockerfile
    container_name: order-service
    ports:
      - "3003:3003"
    environment:
      - NODE_ENV=development
      - PORT=3003
      - MYSQL_HOST=mysql
      - MYSQL_PORT=3306
      - MYSQL_DATABASE=order_db
      - MYSQL_USER=root
      - MYSQL_PASSWORD=mysql123
    volumes:
      - ./services/order-service:/app
      - /app/node_modules
    depends_on:
      - mysql
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== Payment Service (MongoDB) ==========
  payment-service:
    build:
      context: ./services/payment-service
      dockerfile: Dockerfile
    container_name: payment-service
    ports:
      - "3004:3004"
    environment:
      - NODE_ENV=development
      - PORT=3004
      - MONGODB_URI=mongodb://mongodb:27017/payment_db
      - STRIPE_SECRET_KEY=sk_test_your_key
    volumes:
      - ./services/payment-service:/app
      - /app/node_modules
    depends_on:
      - mongodb
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== Notification Service ==========
  notification-service:
    build:
      context: ./services/notification-service
      dockerfile: Dockerfile
    container_name: notification-service
    ports:
      - "3005:3005"
    environment:
      - NODE_ENV=development
      - PORT=3005
      - SMTP_HOST=smtp.gmail.com
      - SMTP_PORT=587
      - SMTP_USER=your-email@gmail.com
      - SMTP_PASS=your-app-password
    volumes:
      - ./services/notification-service:/app
      - /app/node_modules
    depends_on:
      - rabbitmq
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== MongoDB ==========
  mongodb:
    image: mongo:7
    container_name: mongodb
    ports:
      - "27017:27017"
    environment:
      - MONGO_INITDB_DATABASE=user_db
    volumes:
      - mongodb-data:/data/db
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== PostgreSQL ==========
  postgres:
    image: postgres:16
    container_name: postgres
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_DB=product_db
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres123
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== MySQL ==========
  mysql:
    image: mysql:8
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=mysql123
      - MYSQL_DATABASE=order_db
    volumes:
      - mysql-data:/var/lib/mysql
    command: --default-authentication-plugin=mysql_native_password
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== Redis ==========
  redis:
    image: redis:7-alpine
    container_name: redis
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== RabbitMQ ==========
  rabbitmq:
    image: rabbitmq:3-management-alpine
    container_name: rabbitmq
    ports:
      - "5672:5672" # AMQP port
      - "15672:15672" # Management UI
    environment:
      - RABBITMQ_DEFAULT_USER=admin
      - RABBITMQ_DEFAULT_PASS=admin123
    volumes:
      - rabbitmq-data:/var/lib/rabbitmq
    networks:
      - microservices-network
    restart: unless-stopped

  # ========== Nginx (Load Balancer) ==========
  nginx:
    build:
      context: ./config/nginx
      dockerfile: Dockerfile
    container_name: nginx
    ports:
      - "80:80"
    depends_on:
      - api-gateway
    networks:
      - microservices-network
    restart: unless-stopped

networks:
  microservices-network:
    driver: bridge

volumes:
  mongodb-data:
  postgres-data:
  mysql-data:
  redis-data:
  rabbitmq-data:
```

---

### Service Dockerfile Example

**File:** `services/user-service/Dockerfile`

```dockerfile
FROM node:20-alpine

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy source code
COPY . .

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001 && \
    chown -R nodejs:nodejs /app

# Switch to non-root user
USER nodejs

# Expose port
EXPOSE 3001

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=40s --retries=3 \
  CMD node -e "require('http').get('http://localhost:3001/health', (r) => {process.exit(r.statusCode === 200 ? 0 : 1)})"

# Start application
CMD ["node", "src/server.js"]
```

---

### .dockerignore

**File:** `services/user-service/.dockerignore`

```
node_modules
npm-debug.log
.env
.env.local
.git
.gitignore
README.md
tests
coverage
logs
*.log
.DS_Store
```

---

## ⚙️ PM2 Configuration

**File:** `config/pm2/ecosystem.config.js`

```javascript
module.exports = {
  apps: [
    // ========== API Gateway ==========
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
      kill_timeout: 5000,
      listen_timeout: 3000,
    },

    // ========== User Service (MongoDB) ==========
    {
      name: "user-service",
      script: "./services/user-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      watch: false,
      env: {
        NODE_ENV: "development",
        PORT: 3001,
        MONGODB_URI: "mongodb://localhost:27017/user_db",
        JWT_SECRET: "your-super-secret-jwt-key",
        JWT_EXPIRE: "7d",
        REDIS_HOST: "localhost",
        REDIS_PORT: 6379,
      },
      env_production: {
        NODE_ENV: "production",
        PORT: 3001,
      },
      error_file: "./logs/user-service/error.log",
      out_file: "./logs/user-service/out.log",
      log_date_format: "YYYY-MM-DD HH:mm:ss Z",
      autorestart: true,
      max_memory_restart: "400M",
    },

    // ========== Product Service (PostgreSQL) ==========
    {
      name: "product-service",
      script: "./services/product-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      watch: false,
      env: {
        NODE_ENV: "development",
        PORT: 3002,
        POSTGRES_HOST: "localhost",
        POSTGRES_PORT: 5432,
        POSTGRES_DB: "product_db",
        POSTGRES_USER: "postgres",
        POSTGRES_PASSWORD: "postgres123",
      },
      env_production: {
        NODE_ENV: "production",
        PORT: 3002,
      },
      error_file: "./logs/product-service/error.log",
      out_file: "./logs/product-service/out.log",
      autorestart: true,
      max_memory_restart: "400M",
    },

    // ========== Order Service (MySQL) ==========
    {
      name: "order-service",
      script: "./services/order-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      env: {
        NODE_ENV: "development",
        PORT: 3003,
        MYSQL_HOST: "localhost",
        MYSQL_PORT: 3306,
        MYSQL_DATABASE: "order_db",
        MYSQL_USER: "root",
        MYSQL_PASSWORD: "mysql123",
      },
      env_production: {
        NODE_ENV: "production",
        PORT: 3003,
      },
      error_file: "./logs/order-service/error.log",
      out_file: "./logs/order-service/out.log",
      autorestart: true,
    },

    // ========== Payment Service (MongoDB) ==========
    {
      name: "payment-service",
      script: "./services/payment-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      env: {
        NODE_ENV: "development",
        PORT: 3004,
        MONGODB_URI: "mongodb://localhost:27017/payment_db",
        STRIPE_SECRET_KEY: "sk_test_your_key",
      },
      env_production: {
        NODE_ENV: "production",
        PORT: 3004,
      },
      error_file: "./logs/payment-service/error.log",
      out_file: "./logs/payment-service/out.log",
      autorestart: true,
    },

    // ========== Notification Service ==========
    {
      name: "notification-service",
      script: "./services/notification-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      env: {
        NODE_ENV: "development",
        PORT: 3005,
        SMTP_HOST: "smtp.gmail.com",
        SMTP_PORT: 587,
        SMTP_USER: "your-email@gmail.com",
        SMTP_PASS: "your-app-password",
        RABBITMQ_URL: "amqp://localhost:5672",
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

  // Deployment configuration
  deploy: {
    production: {
      user: "ubuntu",
      host: ["your-server-ip"],
      ref: "origin/main",
      repo: "git@github.com:username/microservices-project.git",
      path: "/var/www/microservices-project",
      "post-deploy":
        "npm install && pm2 reload ecosystem.config.js --env production",
    },
  },
};
```

---

## 💻 Kod Misollari

### Complete Service Example: User Service

**File:** `services/user-service/src/server.js`

```javascript
const app = require("./app");
const connectMongoDB = require("./config/mongodb");
const { connectRedis } = require("../../../shared/utils/redis");
const logger = require("../../../shared/utils/logger");

const PORT = process.env.PORT || 3001;

const startServer = async () => {
  try {
    // Connect to MongoDB
    await connectMongoDB();

    // Connect to Redis (optional)
    if (process.env.REDIS_HOST) {
      connectRedis();
    }

    // Start server
    const server = app.listen(PORT, () => {
      logger.info(`🚀 User Service running on port ${PORT}`);
      logger.info(`Environment: ${process.env.NODE_ENV}`);
    });

    // Graceful shutdown
    const gracefulShutdown = async (signal) => {
      logger.info(`${signal} received. Starting graceful shutdown...`);

      server.close(async () => {
        logger.info("HTTP server closed");

        // Close database connections
        const mongoose = require("mongoose");
        await mongoose.connection.close();

        logger.info("Database connections closed");
        process.exit(0);
      });

      // Force shutdown after 30 seconds
      setTimeout(() => {
        logger.error("Forced shutdown after timeout");
        process.exit(1);
      }, 30000);
    };

    process.on("SIGTERM", () => gracefulShutdown("SIGTERM"));
    process.on("SIGINT", () => gracefulShutdown("SIGINT"));
  } catch (error) {
    logger.error("Failed to start server:", error);
    process.exit(1);
  }
};

startServer();
```

**File:** `services/user-service/src/app.js`

```javascript
const express = require("express");
const helmet = require("helmet");
const cors = require("cors");
const morgan = require("morgan");
const rateLimit = require("express-rate-limit");

const routes = require("./routes");
const errorHandler = require("../../../shared/middlewares/errorHandler");
const logger = require("../../../shared/utils/logger");

const app = express();

// Security middlewares
app.use(helmet());
app.use(
  cors({
    origin: process.env.ALLOWED_ORIGINS?.split(",") || "*",
    credentials: true,
  }),
);

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  message: "Too many requests from this IP, please try again later",
});
app.use("/api/", limiter);

// Body parser
app.use(express.json({ limit: "10mb" }));
app.use(express.urlencoded({ extended: true, limit: "10mb" }));

// Logging
const morganFormat = process.env.NODE_ENV === "production" ? "combined" : "dev";
app.use(
  morgan(morganFormat, {
    stream: {
      write: (message) => logger.http(message.trim()),
    },
  }),
);

// Health check
app.get("/health", (req, res) => {
  res.status(200).json({
    status: "healthy",
    service: "user-service",
    timestamp: new Date().toISOString(),
    uptime: process.uptime(),
  });
});

// API routes
app.use("/api/v1", routes);

// 404 handler
app.use("*", (req, res) => {
  res.status(404).json({
    success: false,
    message: "Route not found",
  });
});

// Error handler
app.use(errorHandler);

module.exports = app;
```

**File:** `services/user-service/src/routes/index.js`

```javascript
const express = require("express");
const authRoutes = require("./authRoutes");
const userRoutes = require("./userRoutes");

const router = express.Router();

router.use("/auth", authRoutes);
router.use("/users", userRoutes);

module.exports = router;
```

**File:** `services/user-service/src/routes/authRoutes.js`

```javascript
const express = require("express");
const {
  register,
  login,
  logout,
  refreshToken,
} = require("../controllers/authController");
const { validateRegister, validateLogin } = require("../middlewares/validate");

const router = express.Router();

router.post("/register", validateRegister, register);
router.post("/login", validateLogin, login);
router.post("/logout", logout);
router.post("/refresh-token", refreshToken);

module.exports = router;
```

**File:** `services/user-service/src/controllers/authController.js`

```javascript
const authService = require("../services/authService");
const asyncHandler = require("../../../../shared/utils/asyncHandler");
const { successResponse } = require("../../../../shared/utils/response");

// @desc    Register user
// @route   POST /api/v1/auth/register
// @access  Public
exports.register = asyncHandler(async (req, res) => {
  const { name, email, password } = req.body;

  const result = await authService.register({ name, email, password });

  successResponse(res, 201, "User registered successfully", result);
});

// @desc    Login user
// @route   POST /api/v1/auth/login
// @access  Public
exports.login = asyncHandler(async (req, res) => {
  const { email, password } = req.body;

  const result = await authService.login({ email, password });

  // Set refresh token in httpOnly cookie
  res.cookie("refreshToken", result.refreshToken, {
    httpOnly: true,
    secure: process.env.NODE_ENV === "production",
    sameSite: "strict",
    maxAge: 7 * 24 * 60 * 60 * 1000, // 7 days
  });

  successResponse(res, 200, "Login successful", {
    user: result.user,
    accessToken: result.accessToken,
  });
});

// @desc    Logout user
// @route   POST /api/v1/auth/logout
// @access  Private
exports.logout = asyncHandler(async (req, res) => {
  res.clearCookie("refreshToken");
  successResponse(res, 200, "Logout successful");
});

// @desc    Refresh access token
// @route   POST /api/v1/auth/refresh-token
// @access  Public
exports.refreshToken = asyncHandler(async (req, res) => {
  const { refreshToken } = req.cookies;

  const result = await authService.refreshToken(refreshToken);

  successResponse(res, 200, "Token refreshed", result);
});
```

**File:** `services/user-service/src/services/authService.js`

```javascript
const User = require("../models/User");
const { generateToken, verifyToken } = require("../../../../shared/utils/jwt");
const AppError = require("../../../../shared/utils/AppError");
const { cache } = require("../../../../shared/utils/redis");

class AuthService {
  async register({ name, email, password }) {
    // Check if user exists
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      throw new AppError("Email already registered", 400);
    }

    // Create user
    const user = await User.create({ name, email, password });

    // Generate tokens
    const accessToken = generateToken({ id: user._id }, "15m");
    const refreshToken = generateToken({ id: user._id }, "7d");

    // Cache user data
    await cache.set(`user:${user._id}`, user, 3600);

    return {
      user: {
        id: user._id,
        name: user.name,
        email: user.email,
        role: user.role,
      },
      accessToken,
      refreshToken,
    };
  }

  async login({ email, password }) {
    // Find user
    const user = await User.findByEmail(email);
    if (!user) {
      throw new AppError("Invalid credentials", 401);
    }

    // Check password
    const isPasswordValid = await user.comparePassword(password);
    if (!isPasswordValid) {
      throw new AppError("Invalid credentials", 401);
    }

    // Update last login
    user.lastLogin = new Date();
    await user.save();

    // Generate tokens
    const accessToken = generateToken({ id: user._id }, "15m");
    const refreshToken = generateToken({ id: user._id }, "7d");

    // Cache user
    await cache.set(`user:${user._id}`, user, 3600);

    return {
      user: {
        id: user._id,
        name: user.name,
        email: user.email,
        role: user.role,
      },
      accessToken,
      refreshToken,
    };
  }

  async refreshToken(token) {
    if (!token) {
      throw new AppError("Refresh token required", 401);
    }

    // Verify token
    const decoded = verifyToken(token);

    // Find user
    const user = await User.findById(decoded.id);
    if (!user) {
      throw new AppError("User not found", 404);
    }

    // Generate new access token
    const accessToken = generateToken({ id: user._id }, "15m");

    return { accessToken };
  }
}

module.exports = new AuthService();
```

---

### API Gateway Example

**File:** `services/api-gateway/src/routes/userRoutes.js`

```javascript
const express = require("express");
const axios = require("axios");
const asyncHandler = require("../../../shared/utils/asyncHandler");

const router = express.Router();

const USER_SERVICE_URL =
  process.env.USER_SERVICE_URL || "http://localhost:3001";

// Proxy all requests to user service
router.all(
  "*",
  asyncHandler(async (req, res) => {
    const url = `${USER_SERVICE_URL}${req.originalUrl}`;

    const response = await axios({
      method: req.method,
      url,
      data: req.body,
      headers: {
        ...req.headers,
        host: new URL(USER_SERVICE_URL).host,
      },
      params: req.query,
    });

    res.status(response.status).json(response.data);
  }),
);

module.exports = router;
```

---

### Shared Utils Examples

**File:** `shared/utils/jwt.js`

```javascript
const jwt = require("jsonwebtoken");
const AppError = require("./AppError");

exports.generateToken = (payload, expiresIn = "15m") => {
  return jwt.sign(payload, process.env.JWT_SECRET, { expiresIn });
};

exports.verifyToken = (token) => {
  try {
    return jwt.verify(token, process.env.JWT_SECRET);
  } catch (error) {
    throw new AppError("Invalid or expired token", 401);
  }
};
```

**File:** `shared/utils/response.js`

```javascript
exports.successResponse = (res, statusCode, message, data = null) => {
  const response = {
    success: true,
    message,
  };

  if (data) {
    response.data = data;
  }

  return res.status(statusCode).json(response);
};

exports.errorResponse = (res, statusCode, message, errors = null) => {
  const response = {
    success: false,
    message,
  };

  if (errors) {
    response.errors = errors;
  }

  return res.status(statusCode).json(response);
};
```

**File:** `shared/utils/AppError.js`

```javascript
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;

    Error.captureStackTrace(this, this.constructor);
  }
}

module.exports = AppError;
```

**File:** `shared/utils/asyncHandler.js`

```javascript
module.exports = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};
```

**File:** `shared/middlewares/errorHandler.js`

```javascript
const logger = require("../utils/logger");
const { errorResponse } = require("../utils/response");

module.exports = (err, req, res, next) => {
  let statusCode = err.statusCode || 500;
  let message = err.message || "Internal Server Error";

  // MongoDB duplicate key error
  if (err.code === 11000) {
    statusCode = 400;
    const field = Object.keys(err.keyValue)[0];
    message = `${field} already exists`;
  }

  // Mongoose validation error
  if (err.name === "ValidationError") {
    statusCode = 400;
    message = Object.values(err.errors)
      .map((e) => e.message)
      .join(", ");
  }

  // JWT errors
  if (err.name === "JsonWebTokenError") {
    statusCode = 401;
    message = "Invalid token";
  }

  if (err.name === "TokenExpiredError") {
    statusCode = 401;
    message = "Token expired";
  }

  // Log error
  if (statusCode >= 500) {
    logger.error(err);
  } else {
    logger.warn(err.message);
  }

  // Send response
  errorResponse(
    res,
    statusCode,
    message,
    process.env.NODE_ENV === "development" ? { stack: err.stack } : null,
  );
};
```

---

## 🚀 Ishga Tushirish

### 1️⃣ Local Development (PM2)

```bash
# 1. Clone repository
git clone https://github.com/username/microservices-project.git
cd microservices-project

# 2. Run setup script
chmod +x scripts/setup.sh
./scripts/setup.sh

# 3. Install databases (agar yo'q bo'lsa)
# MongoDB
brew install mongodb-community@7  # macOS
sudo apt install mongodb          # Ubuntu

# PostgreSQL
brew install postgresql@16        # macOS
sudo apt install postgresql       # Ubuntu

# MySQL
brew install mysql@8              # macOS
sudo apt install mysql-server     # Ubuntu

# Redis
brew install redis                # macOS
sudo apt install redis-server     # Ubuntu

# RabbitMQ
brew install rabbitmq             # macOS
sudo apt install rabbitmq-server  # Ubuntu

# 4. Start databases
# MongoDB
brew services start mongodb-community  # macOS
sudo systemctl start mongod            # Ubuntu

# PostgreSQL
brew services start postgresql         # macOS
sudo systemctl start postgresql        # Ubuntu

# MySQL
brew services start mysql              # macOS
sudo systemctl start mysql             # Ubuntu

# Redis
brew services start redis              # macOS
sudo systemctl start redis             # Ubuntu

# RabbitMQ
brew services start rabbitmq           # macOS
sudo systemctl start rabbitmq-server   # Ubuntu

# 5. Create databases
# MongoDB - automatically created
# PostgreSQL
psql -U postgres
CREATE DATABASE product_db;
\q

# MySQL
mysql -u root -p
CREATE DATABASE order_db;
exit;

# 6. Configure .env files
cp .env.example .env
# Edit .env with your credentials

# 7. Install PM2 globally
npm install -g pm2

# 8. Start all services
npm start
# or
pm2 start config/pm2/ecosystem.config.js

# 9. Check status
pm2 status

# 10. View logs
pm2 logs

# 11. Monitor
pm2 monit
```

---

### 2️⃣ Docker Development

```bash
# 1. Clone repository
git clone https://github.com/username/microservices-project.git
cd microservices-project

# 2. Install Docker & Docker Compose
# https://docs.docker.com/get-docker/

# 3. Build and start all services
docker-compose up --build

# Run in background
docker-compose up -d

# 4. Check status
docker-compose ps

# 5. View logs
docker-compose logs -f

# View specific service logs
docker-compose logs -f user-service

# 6. Stop all services
docker-compose down

# Stop and remove volumes
docker-compose down -v
```

---

### 3️⃣ Production Deployment

```bash
# 1. Build for production
npm run build

# 2. Start with PM2 in production mode
pm2 start config/pm2/ecosystem.config.js --env production

# 3. Save PM2 process list
pm2 save

# 4. Setup startup script (restart on reboot)
pm2 startup
# Follow the instructions

# 5. Monitor production
pm2 monit

# 6. Update application
git pull
npm install
pm2 reload all
```

---

## 📦 Package.json Examples

### Root package.json

```json
{
  "name": "microservices-project",
  "version": "2.0.0",
  "description": "Professional Node.js Express Microservices with PM2, Docker, MongoDB, PostgreSQL, MySQL",
  "author": "Oybek Abdujabbrov",
  "license": "MIT",
  "scripts": {
    "setup": "bash scripts/setup.sh",
    "start": "pm2 start config/pm2/ecosystem.config.js",
    "start:prod": "pm2 start config/pm2/ecosystem.config.js --env production",
    "stop": "pm2 stop all",
    "restart": "pm2 restart all",
    "delete": "pm2 delete all",
    "logs": "pm2 logs",
    "monit": "pm2 monit",
    "docker:up": "docker-compose up -d",
    "docker:down": "docker-compose down",
    "docker:logs": "docker-compose logs -f",
    "docker:build": "docker-compose up --build",
    "dev:gateway": "cd services/api-gateway && npm run dev",
    "dev:user": "cd services/user-service && npm run dev",
    "dev:product": "cd services/product-service && npm run dev",
    "dev:order": "cd services/order-service && npm run dev",
    "dev:payment": "cd services/payment-service && npm run dev",
    "dev:notification": "cd services/notification-service && npm run dev",
    "test": "npm run test --workspaces",
    "test:unit": "npm run test:unit --workspaces",
    "test:integration": "npm run test:integration --workspaces",
    "test:e2e": "npm run test:e2e --workspaces",
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check ."
  },
  "workspaces": ["services/*", "shared"],
  "keywords": [
    "microservices",
    "nodejs",
    "express",
    "pm2",
    "docker",
    "mongodb",
    "postgresql",
    "mysql",
    "redis",
    "rabbitmq",
    "monorepo"
  ],
  "devDependencies": {
    "eslint": "^8.57.0",
    "eslint-config-airbnb-base": "^15.0.0",
    "eslint-plugin-import": "^2.29.1",
    "prettier": "^3.2.5",
    "pm2": "^5.3.1"
  }
}
```

---

### User Service package.json

```json
{
  "name": "user-service",
  "version": "1.0.0",
  "description": "User management microservice with MongoDB",
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
    "ioredis": "^5.3.2",
    "amqplib": "^0.10.3",
    "nodemailer": "^6.9.8",
    "axios": "^1.6.7"
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

### Product Service package.json

```json
{
  "name": "product-service",
  "version": "1.0.0",
  "description": "Product catalog microservice with PostgreSQL",
  "main": "src/server.js",
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js",
    "migrate": "sequelize-cli db:migrate",
    "migrate:undo": "sequelize-cli db:migrate:undo",
    "seed": "sequelize-cli db:seed:all",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.2",
    "sequelize": "^6.36.0",
    "pg": "^8.11.3",
    "pg-hstore": "^2.3.4",
    "dotenv": "^16.4.1",
    "cors": "^2.8.5",
    "helmet": "^7.1.0",
    "joi": "^17.12.0",
    "winston": "^3.11.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.3",
    "sequelize-cli": "^6.6.2",
    "jest": "^29.7.0"
  }
}
```

---

### Order Service package.json

```json
{
  "name": "order-service",
  "version": "1.0.0",
  "description": "Order processing microservice with MySQL",
  "main": "src/server.js",
  "scripts": {
    "start": "node src/server.js",
    "dev": "nodemon src/server.js",
    "migrate": "sequelize-cli db:migrate",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.2",
    "mysql2": "^3.9.0",
    "sequelize": "^6.36.0",
    "dotenv": "^16.4.1",
    "cors": "^2.8.5",
    "helmet": "^7.1.0",
    "joi": "^17.12.0",
    "winston": "^3.11.0",
    "axios": "^1.6.7"
  },
  "devDependencies": {
    "nodemon": "^3.0.3",
    "sequelize-cli": "^6.6.2",
    "jest": "^29.7.0"
  }
}
```

---

## 🔐 Environment Variables

**File:** `.env.example`

```env
# ========== General ==========
NODE_ENV=development

# ========== API Gateway ==========
GATEWAY_PORT=3000
ALLOWED_ORIGINS=http://localhost:3000,http://localhost:3001

# ========== User Service ==========
USER_SERVICE_PORT=3001
USER_SERVICE_URL=http://localhost:3001

# MongoDB
MONGODB_URI=mongodb://localhost:27017/user_db

# JWT
JWT_SECRET=your-super-secret-jwt-key-change-this-in-production
JWT_EXPIRE=15m
JWT_REFRESH_EXPIRE=7d

# ========== Product Service ==========
PRODUCT_SERVICE_PORT=3002
PRODUCT_SERVICE_URL=http://localhost:3002

# PostgreSQL
POSTGRES_HOST=localhost
POSTGRES_PORT=5432
POSTGRES_DB=product_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres123

# ========== Order Service ==========
ORDER_SERVICE_PORT=3003
ORDER_SERVICE_URL=http://localhost:3003

# MySQL
MYSQL_HOST=localhost
MYSQL_PORT=3306
MYSQL_DATABASE=order_db
MYSQL_USER=root
MYSQL_PASSWORD=mysql123

# ========== Payment Service ==========
PAYMENT_SERVICE_PORT=3004
PAYMENT_SERVICE_URL=http://localhost:3004

# Payment Gateway
STRIPE_SECRET_KEY=sk_test_your_stripe_secret_key
STRIPE_WEBHOOK_SECRET=whsec_your_webhook_secret
PAYPAL_CLIENT_ID=your_paypal_client_id
PAYPAL_CLIENT_SECRET=your_paypal_client_secret

# ========== Notification Service ==========
NOTIFICATION_SERVICE_PORT=3005

# Email (SMTP)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-gmail-app-password

# SMS (Twilio)
TWILIO_ACCOUNT_SID=your_twilio_account_sid
TWILIO_AUTH_TOKEN=your_twilio_auth_token
TWILIO_PHONE_NUMBER=+1234567890

# ========== Redis ==========
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=
REDIS_DB=0

# ========== RabbitMQ ==========
RABBITMQ_URL=amqp://localhost:5672
RABBITMQ_USER=admin
RABBITMQ_PASS=admin123

# ========== Logging ==========
LOG_LEVEL=debug
LOG_FILE_ERROR=logs/error.log
LOG_FILE_COMBINED=logs/combined.log

# ========== Rate Limiting ==========
RATE_LIMIT_WINDOW_MS=900000
RATE_LIMIT_MAX_REQUESTS=100
```

---

## 📜 Scripts

### setup.sh

```bash
#!/bin/bash

echo "🚀 Microservices Setup Starting..."
echo "=================================="

# Colors
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
RED='\033[0;31m'
NC='\033[0m' # No Color

# Check Node.js
if ! command -v node &> /dev/null; then
    echo -e "${RED}❌ Node.js is not installed${NC}"
    echo "Please install Node.js from https://nodejs.org/"
    exit 1
fi

echo -e "${GREEN}✅ Node.js $(node -v) found${NC}"

# Check npm
if ! command -v npm &> /dev/null; then
    echo -e "${RED}❌ npm is not installed${NC}"
    exit 1
fi

echo -e "${GREEN}✅ npm $(npm -v) found${NC}"

# Install root dependencies
echo ""
echo "📦 Installing root dependencies..."
npm install

# Install service dependencies
echo ""
echo "📦 Installing service dependencies..."

services=("api-gateway" "user-service" "product-service" "order-service" "payment-service" "notification-service")

for service in "${services[@]}"
do
  echo -e "${YELLOW}📦 Installing dependencies for $service...${NC}"
  cd services/$service
  npm install
  cd ../..
done

# Install shared dependencies
echo ""
echo "📦 Installing shared dependencies..."
cd shared
npm install
cd ..

# Create logs directories
echo ""
echo "📁 Creating logs directories..."
mkdir -p logs/api-gateway
mkdir -p logs/user-service
mkdir -p logs/product-service
mkdir -p logs/order-service
mkdir -p logs/payment-service
mkdir -p logs/notification-service

# Copy .env.example to .env if not exists
if [ ! -f .env ]; then
  echo ""
  echo "📝 Creating .env file..."
  cp .env.example .env
  echo -e "${YELLOW}⚠️  Please update .env file with your credentials${NC}"
fi

# Check PM2
echo ""
if ! command -v pm2 &> /dev/null; then
    echo -e "${YELLOW}⚠️  PM2 is not installed${NC}"
    echo "Installing PM2 globally..."
    npm install -g pm2
fi

echo -e "${GREEN}✅ PM2 found${NC}"

echo ""
echo "=================================="
echo -e "${GREEN}✅ Setup completed successfully!${NC}"
echo ""
echo "Next steps:"
echo "1. Update .env file with your database credentials"
echo "2. Start databases (MongoDB, PostgreSQL, MySQL, Redis)"
echo "3. Run 'npm start' to start all services with PM2"
echo "   or 'docker-compose up' to use Docker"
echo ""
```

---

### start-all.sh

```bash
#!/bin/bash

echo "🚀 Starting all microservices with PM2..."

pm2 start config/pm2/ecosystem.config.js

echo ""
echo "✅ All services started!"
echo ""
echo "Useful commands:"
echo "  pm2 status       - Check service status"
echo "  pm2 logs         - View all logs"
echo "  pm2 monit        - Monitor services"
echo "  pm2 restart all  - Restart all services"
echo "  pm2 stop all     - Stop all services"
echo ""
```

---

### docker-setup.sh

```bash
#!/bin/bash

echo "🐳 Docker Setup Starting..."
echo "=================================="

# Check Docker
if ! command -v docker &> /dev/null; then
    echo "❌ Docker is not installed"
    echo "Please install Docker from https://docs.docker.com/get-docker/"
    exit 1
fi

echo "✅ Docker $(docker --version) found"

# Check Docker Compose
if ! command -v docker-compose &> /dev/null; then
    echo "❌ Docker Compose is not installed"
    exit 1
fi

echo "✅ Docker Compose $(docker-compose --version) found"

# Build and start containers
echo ""
echo "🔨 Building and starting containers..."
docker-compose up --build -d

echo ""
echo "⏳ Waiting for services to be healthy..."
sleep 10

# Check container status
echo ""
echo "📊 Container status:"
docker-compose ps

echo ""
echo "=================================="
echo "✅ Docker setup completed!"
echo ""
echo "Useful commands:"
echo "  docker-compose ps           - Check container status"
echo "  docker-compose logs -f      - View all logs"
echo "  docker-compose down         - Stop all containers"
echo "  docker-compose restart      - Restart all containers"
echo ""
```

---

## 🧪 Testing

### Jest Configuration

**File:** `services/user-service/jest.config.js`

```javascript
module.exports = {
  testEnvironment: "node",
  coverageDirectory: "coverage",
  collectCoverageFrom: ["src/**/*.js", "!src/server.js", "!src/config/**"],
  testMatch: ["**/tests/**/*.test.js"],
  setupFilesAfterEnv: ["<rootDir>/tests/setup.js"],
  verbose: true,
};
```

**File:** `services/user-service/tests/setup.js`

```javascript
const mongoose = require("mongoose");
const { MongoMemoryServer } = require("mongodb-memory-server");

let mongoServer;

beforeAll(async () => {
  mongoServer = await MongoMemoryServer.create();
  const mongoUri = mongoServer.getUri();
  await mongoose.connect(mongoUri);
});

afterAll(async () => {
  await mongoose.disconnect();
  await mongoServer.stop();
});

afterEach(async () => {
  const collections = mongoose.connection.collections;
  for (const key in collections) {
    await collections[key].deleteMany({});
  }
});
```

---

## 🔍 Troubleshooting

### Common Issues

**1. MongoDB Connection Failed**

```bash
# Check if MongoDB is running
brew services list  # macOS
sudo systemctl status mongod  # Ubuntu

# Start MongoDB
brew services start mongodb-community  # macOS
sudo systemctl start mongod  # Ubuntu
```

**2. PostgreSQL Connection Failed**

```bash
# Check if PostgreSQL is running
brew services list  # macOS
sudo systemctl status postgresql  # Ubuntu

# Create database
psql -U postgres
CREATE DATABASE product_db;
```

**3. MySQL Connection Failed**

```bash
# Check if MySQL is running
brew services list  # macOS
sudo systemctl status mysql  # Ubuntu

# Reset password
mysql -u root -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'mysql123';
```

**4. PM2 Not Found**

```bash
npm install -g pm2
```

**5. Port Already in Use**

```bash
# Find process using port
lsof -i :3000  # macOS/Linux
netstat -ano | findstr :3000  # Windows

# Kill process
kill -9 <PID>
```

---

## 📚 Resources

- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [PM2 Documentation](https://pm2.keymetrics.io/)
- [Express.js Guide](https://expressjs.com/)
- [MongoDB Manual](https://www.mongodb.com/docs/manual/)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [MySQL Documentation](https://dev.mysql.com/doc/)
- [Docker Documentation](https://docs.docker.com/)
- [Microservices Patterns](https://microservices.io/)

---

## 📞 Support

**Muallif:** Oybek Abdujabbrov  
**GitHub:** https://github.com/AbdujabborovOybek  

---

## 📄 License

MIT License - Free to use for personal and commercial projects

---

**🎉 Happy Coding! Omad tilaymiz! 🚀**

**⭐ Agar foydali bo'lsa, GitHub da star bosing!**
