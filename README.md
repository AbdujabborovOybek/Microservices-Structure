# 🚀 Node.js Express Microservices Monorepo (PM2 + Docker + DB Examples)

> **PM2 + Docker Compose bilan productionga yaqin microservice monorepo.**  
> Ushbu hujjat: **strukturani**, **PM2 ecosystem**, **Docker/Docker Compose**, hamda **MongoDB / PostgreSQL / MySQL (mysql2)** bilan ishlash **namunalarini** va **ishga tushirish jarayonini** batafsil ko‘rsatadi.

---

## ✅ 0) Talablar (Prerequisites)

Quyidagilar o‘rnatilgan bo‘lsin:

- **Node.js** (LTS tavsiya)
- **Docker** va **Docker Compose**
- (Ixtiyoriy) **PM2** global: `npm i -g pm2`
- (Ixtiyoriy) **pnpm** yoki **npm** (workspaces)

---

## 📁 1) Minimal Project Strukturasi (monorepo)

> Siz bergan strukturani saqlagan holda, Docker/PM2 uchun kerakli qo‘shimcha fayllar ham kiritildi.

```
microservices-project/
├─ services/
│  ├─ api-gateway/
│  ├─ user-service/
│  ├─ product-service/
│  ├─ order-service/
│  ├─ payment-service/
│  └─ notification-service/
├─ shared/
├─ config/
│  ├─ pm2/ecosystem.config.js
│  └─ nginx/nginx.conf              # ixtiyoriy
├─ scripts/
├─ logs/
├─ docker/
│  ├─ docker-compose.dev.yml        # development (local PM2 yoki nodemon)
│  ├─ docker-compose.prod.yml       # production (docker + pm2)
│  └─ .env.docker.example
├─ .env.example
├─ package.json
└─ README.md
```

---

## 🧠 2) Workspaces (Root package.json) tavsiya

> Sizning root `package.json`ingiz yaxshi. Workspaces ishlatilsa, **bitta komandada** hamma servis dependency o‘rnatiladi.

**Root `package.json` (qisqa):**

```json
{
  "name": "microservices-project",
  "private": true,
  "workspaces": ["services/*", "shared"],
  "scripts": {
    "setup": "bash scripts/setup.sh",
    "dev": "node -e \"console.log('Dev: har bir service alohida')\"",
    "pm2:start": "pm2 start config/pm2/ecosystem.config.js",
    "pm2:stop": "pm2 stop all",
    "pm2:restart": "pm2 restart all",
    "pm2:logs": "pm2 logs",
    "docker:dev": "docker compose -f docker/docker-compose.dev.yml --env-file docker/.env.docker up -d --build",
    "docker:down": "docker compose -f docker/docker-compose.dev.yml --env-file docker/.env.docker down",
    "docker:prod": "docker compose -f docker/docker-compose.prod.yml --env-file docker/.env.docker up -d --build"
  }
}
```

---

## ⚙️ 3) PM2 Ecosystem (Monorepo) — best practice

Siz bergan `ecosystem.config.js` to‘g‘ri. Quyida **healthcheck**, **graceful shutdown**, **log path** kabi kichik tavsiyalar qo‘shilgan.

**File:** `config/pm2/ecosystem.config.js`

```js
module.exports = {
  apps: [
    {
      name: "api-gateway",
      script: "./services/api-gateway/src/server.js",
      instances: 2,
      exec_mode: "cluster",

      // logs
      error_file: "./logs/api-gateway/error.log",
      out_file: "./logs/api-gateway/out.log",
      merge_logs: true,
      log_date_format: "YYYY-MM-DD HH:mm:ss Z",

      // restart policy
      autorestart: true,
      max_memory_restart: "500M",
      min_uptime: "10s",
      max_restarts: 10,

      env: { NODE_ENV: "development", PORT: 3000 },
      env_production: { NODE_ENV: "production", PORT: 3000 },
    },

    {
      name: "user-service",
      script: "./services/user-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      error_file: "./logs/user-service/error.log",
      out_file: "./logs/user-service/out.log",
      merge_logs: true,
      autorestart: true,
      max_memory_restart: "300M",
      env: { NODE_ENV: "development", PORT: 3001 },
      env_production: { NODE_ENV: "production", PORT: 3001 },
    },

    {
      name: "product-service",
      script: "./services/product-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      error_file: "./logs/product-service/error.log",
      out_file: "./logs/product-service/out.log",
      merge_logs: true,
      autorestart: true,
      max_memory_restart: "300M",
      env: { NODE_ENV: "development", PORT: 3002 },
      env_production: { NODE_ENV: "production", PORT: 3002 },
    },

    {
      name: "order-service",
      script: "./services/order-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      error_file: "./logs/order-service/error.log",
      out_file: "./logs/order-service/out.log",
      merge_logs: true,
      autorestart: true,
      env: { NODE_ENV: "development", PORT: 3003 },
      env_production: { NODE_ENV: "production", PORT: 3003 },
    },

    {
      name: "payment-service",
      script: "./services/payment-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      error_file: "./logs/payment-service/error.log",
      out_file: "./logs/payment-service/out.log",
      merge_logs: true,
      autorestart: true,
      env: { NODE_ENV: "development", PORT: 3004 },
      env_production: { NODE_ENV: "production", PORT: 3004 },
    },

    {
      name: "notification-service",
      script: "./services/notification-service/src/server.js",
      instances: 1,
      exec_mode: "fork",
      error_file: "./logs/notification-service/error.log",
      out_file: "./logs/notification-service/out.log",
      merge_logs: true,
      autorestart: true,
      env: { NODE_ENV: "development", PORT: 3005 },
      env_production: { NODE_ENV: "production", PORT: 3005 },
    },
  ],
};
```

**PM2 asosiy komandalar:**

```bash
pm2 start config/pm2/ecosystem.config.js
pm2 start config/pm2/ecosystem.config.js --env production
pm2 status
pm2 logs
pm2 restart all
pm2 stop all
pm2 delete all
pm2 save
pm2 startup
```

---

## 🐳 4) Docker: Dev va Prod uchun “toza” yondashuv

### 4.1) Qachon Docker kerak?

- **Local dev**: DBlarni Dockerda (Mongo/Postgres/MySQL) ko‘tarib, node servislari lokalda ishlashi (eng tez).
- **Prod/Stage**: hamma servislari + DB + reverse-proxy docker-compose’da.

Quyida ikkita compose:

- `docker-compose.dev.yml` — **DBlar dockerda**, servislar lokalda (PM2 yoki nodemon).
- `docker-compose.prod.yml` — **servislar ham dockerda**, ichida PM2.

---

## 🧩 5) Docker Compose (DEV) — faqat DBlar

**File:** `docker/docker-compose.dev.yml`

> Bu usulda siz Node servislarni kompyuteringizda PM2/nodemon bilan yuritasiz, DBlar esa dockerda.

```yaml
version: "3.9"

services:
  mongo:
    image: mongo:7
    container_name: ms_mongo
    ports:
      - "${MONGO_PORT:-27017}:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: "${MONGO_ROOT_USER:-root}"
      MONGO_INITDB_ROOT_PASSWORD: "${MONGO_ROOT_PASS:-rootpass}"
    volumes:
      - mongo_data:/data/db

  postgres:
    image: postgres:16
    container_name: ms_postgres
    ports:
      - "${POSTGRES_PORT:-5432}:5432"
    environment:
      POSTGRES_USER: "${POSTGRES_USER:-postgres}"
      POSTGRES_PASSWORD: "${POSTGRES_PASSWORD:-postgres}"
      POSTGRES_DB: "${POSTGRES_DB:-app_db}"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  mysql:
    image: mysql:8
    container_name: ms_mysql
    ports:
      - "${MYSQL_PORT:-3306}:3306"
    environment:
      MYSQL_ROOT_PASSWORD: "${MYSQL_ROOT_PASSWORD:-rootpass}"
      MYSQL_DATABASE: "${MYSQL_DATABASE:-app_db}"
      MYSQL_USER: "${MYSQL_USER:-app}"
      MYSQL_PASSWORD: "${MYSQL_PASSWORD:-apppass}"
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mongo_data:
  postgres_data:
  mysql_data:
```

### 5.1) docker env (DEV)

**File:** `docker/.env.docker` (namuna)

```env
MONGO_PORT=27017
MONGO_ROOT_USER=root
MONGO_ROOT_PASS=rootpass

POSTGRES_PORT=5432
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=app_db

MYSQL_PORT=3306
MYSQL_ROOT_PASSWORD=rootpass
MYSQL_DATABASE=app_db
MYSQL_USER=app
MYSQL_PASSWORD=apppass
```

### 5.2) DEV compose ishga tushirish

```bash
# rootda
cp docker/.env.docker.example docker/.env.docker 2>/dev/null || true
docker compose -f docker/docker-compose.dev.yml --env-file docker/.env.docker up -d

# tekshirish
docker ps
```

---

## 🧱 6) Docker Compose (PROD) — hamma servislar dockerda + PM2

Bu yondashuvda:

- Har bir servisga Dockerfile beriladi
- Container ichida **PM2 runtime** bilan node process yuradi
- `depends_on` bilan DBlar bog‘lanadi

### 6.1) Har bir servis uchun Dockerfile (universal)

**Masalan:** `services/user-service/Dockerfile`

```dockerfile
FROM node:20-alpine

WORKDIR /app

# faqat dependency uchun
COPY package*.json ./
RUN npm ci --omit=dev

# app source
COPY . .

# PM2 runtime
RUN npm i -g pm2

ENV NODE_ENV=production
EXPOSE 3001

CMD ["pm2-runtime", "src/server.js", "--name", "user-service"]
```

> **Eslatma:** monorepo bo‘lgani uchun `npm ci` jarayoni servis ichida ishlaydi. Katta monorepoda optimizatsiya qilish mumkin (root install + pruning), lekin bu hujjat **tushunarli** va **ishga tayyor** variant.

### 6.2) docker-compose.prod.yml (namuna)

**File:** `docker/docker-compose.prod.yml`

```yaml
version: "3.9"

services:
  mongo:
    image: mongo:7
    container_name: ms_mongo
    restart: always
    ports:
      - "${MONGO_PORT:-27017}:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: "${MONGO_ROOT_USER:-root}"
      MONGO_INITDB_ROOT_PASSWORD: "${MONGO_ROOT_PASS:-rootpass}"
    volumes:
      - mongo_data:/data/db

  postgres:
    image: postgres:16
    container_name: ms_postgres
    restart: always
    ports:
      - "${POSTGRES_PORT:-5432}:5432"
    environment:
      POSTGRES_USER: "${POSTGRES_USER:-postgres}"
      POSTGRES_PASSWORD: "${POSTGRES_PASSWORD:-postgres}"
      POSTGRES_DB: "${POSTGRES_DB:-app_db}"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  mysql:
    image: mysql:8
    container_name: ms_mysql
    restart: always
    ports:
      - "${MYSQL_PORT:-3306}:3306"
    environment:
      MYSQL_ROOT_PASSWORD: "${MYSQL_ROOT_PASSWORD:-rootpass}"
      MYSQL_DATABASE: "${MYSQL_DATABASE:-app_db}"
      MYSQL_USER: "${MYSQL_USER:-app}"
      MYSQL_PASSWORD: "${MYSQL_PASSWORD:-apppass}"
    volumes:
      - mysql_data:/var/lib/mysql

  api-gateway:
    build:
      context: ../services/api-gateway
    container_name: ms_api_gateway
    restart: always
    ports:
      - "3000:3000"
    environment:
      NODE_ENV: production
      PORT: 3000
      USER_SERVICE_URL: "http://user-service:3001"
      PRODUCT_SERVICE_URL: "http://product-service:3002"
      ORDER_SERVICE_URL: "http://order-service:3003"
      PAYMENT_SERVICE_URL: "http://payment-service:3004"
    depends_on:
      - user-service
      - product-service
      - order-service
      - payment-service

  user-service:
    build:
      context: ../services/user-service
    container_name: ms_user_service
    restart: always
    environment:
      NODE_ENV: production
      PORT: 3001
      # Mongo example:
      MONGO_URI: "mongodb://${MONGO_ROOT_USER:-root}:${MONGO_ROOT_PASS:-rootpass}@mongo:27017/user_db?authSource=admin"
      # Postgres example (agar user-service Postgres ishlatsa):
      POSTGRES_URL: "postgresql://${POSTGRES_USER:-postgres}:${POSTGRES_PASSWORD:-postgres}@postgres:5432/user_db"
    depends_on:
      - mongo
      - postgres

  product-service:
    build:
      context: ../services/product-service
    container_name: ms_product_service
    restart: always
    environment:
      NODE_ENV: production
      PORT: 3002
      MYSQL_URL: "mysql://${MYSQL_USER:-app}:${MYSQL_PASSWORD:-apppass}@mysql:3306/product_db"
    depends_on:
      - mysql

  order-service:
    build:
      context: ../services/order-service
    container_name: ms_order_service
    restart: always
    environment:
      NODE_ENV: production
      PORT: 3003
      POSTGRES_URL: "postgresql://${POSTGRES_USER:-postgres}:${POSTGRES_PASSWORD:-postgres}@postgres:5432/order_db"
    depends_on:
      - postgres

  payment-service:
    build:
      context: ../services/payment-service
    container_name: ms_payment_service
    restart: always
    environment:
      NODE_ENV: production
      PORT: 3004
      POSTGRES_URL: "postgresql://${POSTGRES_USER:-postgres}:${POSTGRES_PASSWORD:-postgres}@postgres:5432/payment_db"
    depends_on:
      - postgres

  notification-service:
    build:
      context: ../services/notification-service
    container_name: ms_notification_service
    restart: always
    environment:
      NODE_ENV: production
      PORT: 3005

volumes:
  mongo_data:
  postgres_data:
  mysql_data:
```

### 6.3) PROD compose ishga tushirish

```bash
# rootda
docker compose -f docker/docker-compose.prod.yml --env-file docker/.env.docker up -d --build

# loglar
docker logs -f ms_api_gateway
```

---

## 🗄️ 7) DB bilan ishlash NAMUNALARI (MongoDB, PostgreSQL, MySQL2)

Quyida **minimal, productionga mos** connection + CRUD misollar berilgan.

> **Tavsiya:** Har bir service o‘z DBsiga ega bo‘lsin (decentralized data). Misol:
>
> - user-service: MongoDB yoki Postgres
> - product-service: MySQL
> - order-service: Postgres

---

# 7.A) MongoDB (Mongoose) — user-service uchun

### 7.A.1) Install

`services/user-service` ichida:

```bash
npm i mongoose
```

### 7.A.2) Config: `src/config/database.mongo.js`

```js
const mongoose = require("mongoose");

async function connectMongo(uri) {
  mongoose.set("strictQuery", true);

  await mongoose.connect(uri, {
    autoIndex: false,
    serverSelectionTimeoutMS: 5000,
  });

  console.log("✅ MongoDB connected");
}

async function disconnectMongo() {
  await mongoose.disconnect();
  console.log("🛑 MongoDB disconnected");
}

module.exports = { connectMongo, disconnectMongo };
```

### 7.A.3) Model: `src/models/User.mongo.js`

```js
const { Schema, model } = require("mongoose");

const userSchema = new Schema(
  {
    email: { type: String, required: true, unique: true, index: true },
    passwordHash: { type: String, required: true },
    fullName: { type: String, required: true },
  },
  { timestamps: true },
);

module.exports = model("User", userSchema);
```

### 7.A.4) Service: `src/services/userService.mongo.js`

```js
const User = require("../models/User.mongo");

async function createUser({ email, passwordHash, fullName }) {
  return User.create({ email, passwordHash, fullName });
}

async function getUserById(id) {
  return User.findById(id).lean();
}

async function getUserByEmail(email) {
  return User.findOne({ email }).lean();
}

module.exports = { createUser, getUserById, getUserByEmail };
```

### 7.A.5) Server start: `src/server.js` (qisqa skeleton)

```js
const express = require("express");
require("dotenv").config();

const { connectMongo, disconnectMongo } = require("./config/database.mongo");

const app = express();
app.use(express.json());

app.get("/health", (req, res) =>
  res.json({ ok: true, service: "user-service" }),
);

const PORT = process.env.PORT || 3001;

async function bootstrap() {
  const mongoUri = process.env.MONGO_URI;
  if (!mongoUri) throw new Error("MONGO_URI is required");

  await connectMongo(mongoUri);

  const server = app.listen(PORT, () => {
    console.log(`✅ user-service listening on :${PORT}`);
  });

  // Graceful shutdown
  const shutdown = async () => {
    console.log("🛑 Shutting down...");
    server.close(async () => {
      await disconnectMongo();
      process.exit(0);
    });
  };

  process.on("SIGINT", shutdown);
  process.on("SIGTERM", shutdown);
}

bootstrap().catch((e) => {
  console.error("❌ bootstrap error:", e);
  process.exit(1);
});
```

---

# 7.B) PostgreSQL (node-postgres / pg) — order-service uchun

### 7.B.1) Install

`services/order-service` ichida:

```bash
npm i pg
```

### 7.B.2) Config: `src/config/database.pg.js`

```js
const { Pool } = require("pg");

let pool;

function getPgPool() {
  if (!pool) {
    const connectionString = process.env.POSTGRES_URL;
    if (!connectionString) throw new Error("POSTGRES_URL is required");

    pool = new Pool({
      connectionString,
      max: 10,
      idleTimeoutMillis: 30_000,
      connectionTimeoutMillis: 5_000,
    });
  }
  return pool;
}

async function pgQuery(text, params) {
  const p = getPgPool();
  return p.query(text, params);
}

async function closePg() {
  if (pool) await pool.end();
  pool = null;
}

module.exports = { pgQuery, closePg };
```

### 7.B.3) Minimal migration (SQL)

**File:** `services/order-service/sql/001_init.sql`

```sql
CREATE TABLE IF NOT EXISTS orders (
  id BIGSERIAL PRIMARY KEY,
  user_id BIGINT NOT NULL,
  status TEXT NOT NULL DEFAULT 'PENDING',
  total_price NUMERIC(12,2) NOT NULL DEFAULT 0,
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);
```

### 7.B.4) Migration run script (oddiy)

**File:** `services/order-service/scripts/migrate.js`

```js
const fs = require("fs");
const path = require("path");
require("dotenv").config();

const { pgQuery, closePg } = require("../src/config/database.pg");

async function run() {
  const sqlPath = path.join(__dirname, "..", "sql", "001_init.sql");
  const sql = fs.readFileSync(sqlPath, "utf8");
  await pgQuery(sql);
  console.log("✅ Migration applied:", sqlPath);
  await closePg();
}

run().catch((e) => {
  console.error("❌ migrate error:", e);
  process.exit(1);
});
```

### 7.B.5) Repository: `src/services/orderRepo.pg.js`

```js
const { pgQuery } = require("../config/database.pg");

async function createOrder({ userId, totalPrice }) {
  const q = `
    INSERT INTO orders (user_id, total_price)
    VALUES ($1, $2)
    RETURNING id, user_id, status, total_price, created_at
  `;
  const r = await pgQuery(q, [userId, totalPrice]);
  return r.rows[0];
}

async function listOrders() {
  const r = await pgQuery("SELECT * FROM orders ORDER BY id DESC LIMIT 50");
  return r.rows;
}

module.exports = { createOrder, listOrders };
```

---

# 7.C) MySQL (mysql2) — product-service uchun

### 7.C.1) Install

`services/product-service` ichida:

```bash
npm i mysql2
```

### 7.C.2) Config: `src/config/database.mysql.js`

```js
const mysql = require("mysql2/promise");

let pool;

function getMysqlPool() {
  if (!pool) {
    const url = process.env.MYSQL_URL;
    if (!url) throw new Error("MYSQL_URL is required");

    pool = mysql.createPool(url, {
      connectionLimit: 10,
      enableKeepAlive: true,
      keepAliveInitialDelay: 0,
    });
  }
  return pool;
}

async function mysqlQuery(sql, params = []) {
  const p = getMysqlPool();
  const [rows] = await p.execute(sql, params);
  return rows;
}

async function closeMysql() {
  if (pool) await pool.end();
  pool = null;
}

module.exports = { mysqlQuery, closeMysql };
```

### 7.C.3) Init SQL

**File:** `services/product-service/sql/001_init.sql`

```sql
CREATE TABLE IF NOT EXISTS products (
  id BIGINT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(255) NOT NULL,
  price DECIMAL(12,2) NOT NULL DEFAULT 0,
  created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);
```

### 7.C.4) Migration script

**File:** `services/product-service/scripts/migrate.js`

```js
const fs = require("fs");
const path = require("path");
require("dotenv").config();

const { mysqlQuery, closeMysql } = require("../src/config/database.mysql");

async function run() {
  const sqlPath = path.join(__dirname, "..", "sql", "001_init.sql");
  const sql = fs.readFileSync(sqlPath, "utf8");
  // mysql2: multi statement bo'lmasa, split qilish mumkin; bu misolda bitta statement
  await mysqlQuery(sql);
  console.log("✅ MySQL migration applied:", sqlPath);
  await closeMysql();
}

run().catch((e) => {
  console.error("❌ migrate error:", e);
  process.exit(1);
});
```

### 7.C.5) Repository: `src/services/productRepo.mysql.js`

```js
const { mysqlQuery } = require("../config/database.mysql");

async function createProduct({ name, price }) {
  const r = await mysqlQuery(
    "INSERT INTO products (name, price) VALUES (?, ?)",
    [name, price],
  );
  return { id: r.insertId, name, price };
}

async function listProducts() {
  return mysqlQuery("SELECT * FROM products ORDER BY id DESC LIMIT 50");
}

module.exports = { createProduct, listProducts };
```

---

## 🔁 8) API Gateway → Service call (axios) minimal

**Gateway install:**

```bash
cd services/api-gateway
npm i axios
```

**File:** `services/api-gateway/src/utils/httpClient.js`

```js
const axios = require("axios");

function createClient(baseURL) {
  return axios.create({
    baseURL,
    timeout: 5000,
  });
}

module.exports = { createClient };
```

**File:** `services/api-gateway/src/routes/userRoutes.js`

```js
const express = require("express");
const { createClient } = require("../utils/httpClient");

const router = express.Router();
const userClient = createClient(
  process.env.USER_SERVICE_URL || "http://localhost:3001",
);

router.get("/health", async (req, res) => {
  const r = await userClient.get("/health");
  res.json({ gateway: true, userService: r.data });
});

module.exports = router;
```

---

## 🧪 9) Healthcheck standartlari

Har bir service’da:

- `GET /health` — `200` qaytaradi
- `service name`, `uptime`, `version` kabi info bo‘lishi mumkin

Namuna:

```js
app.get("/health", (req, res) => {
  res.json({
    ok: true,
    service: "product-service",
    uptime: process.uptime(),
  });
});
```

---

## 🏁 10) Ishga tushirish: 3 xil yo‘l (eng ko‘p ishlatiladigan)

### Variant A) Eng tez DEV: DBlar Dockerda, Servislar lokalda (PM2 yoki nodemon)

1. DBlarni ko‘taring:

```bash
docker compose -f docker/docker-compose.dev.yml --env-file docker/.env.docker up -d
```

2. Root `.env` ni sozlang (local hostga ulangan):

```env
# localda service running bo'lsa
MONGO_URI=mongodb://root:rootpass@localhost:27017/user_db?authSource=admin
POSTGRES_URL=postgresql://postgres:postgres@localhost:5432/order_db
MYSQL_URL=mysql://app:apppass@localhost:3306/product_db
```

3. Dependency:

```bash
npm run setup
```

4. Migratsiyalar:

```bash
node services/order-service/scripts/migrate.js
node services/product-service/scripts/migrate.js
```

5. PM2 bilan ishga tushiring:

```bash
pm2 start config/pm2/ecosystem.config.js
pm2 logs
```

---

### Variant B) Full Docker (PRODga yaqin): hamma narsa containerlarda (PM2 runtime)

```bash
docker compose -f docker/docker-compose.prod.yml --env-file docker/.env.docker up -d --build
docker ps
```

---

### Variant C) Umuman Docker ishlatmasdan (faqat local DB)

Agar DBlar ham lokal (masalan native Postgres) bo‘lsa:

- `.env` — localhost
- `npm run setup`
- `pm2 start ...`

---

## 🧯 11) Troubleshooting (ko‘p uchraydigan muammolar)

### 11.1) “ECONNREFUSED” (service DBga ulana olmayapti)

- Dockerda DB port chiqyaptimi?
  ```bash
  docker ps
  docker logs ms_postgres
  ```
- `.env` to‘g‘ri yozilganmi?
  - Docker ichida `localhost` ishlamaydi. Containerlar o‘zaro **service nomi** bilan ulanadi: `postgres`, `mongo`, `mysql`.

### 11.2) Postgres “database does not exist”

- DB yaratilmagan bo‘lishi mumkin: `POSTGRES_DB` yoki migration.

### 11.3) MySQL “Access denied”

- `MYSQL_USER / MYSQL_PASSWORD` mos kelmayapti.
- URL misol:
  `mysql://app:apppass@mysql:3306/product_db`

### 11.4) PM2 loglar ko‘p bo‘lib ketdi

```bash
pm2 flush
```

---

## 🔐 12) Minimal .env.example (root)

```env
NODE_ENV=development

# Gateway
PORT=3000
USER_SERVICE_URL=http://localhost:3001
PRODUCT_SERVICE_URL=http://localhost:3002
ORDER_SERVICE_URL=http://localhost:3003
PAYMENT_SERVICE_URL=http://localhost:3004

# Mongo
MONGO_URI=mongodb://root:rootpass@localhost:27017/user_db?authSource=admin

# Postgres
POSTGRES_URL=postgresql://postgres:postgres@localhost:5432/order_db

# MySQL (mysql2)
MYSQL_URL=mysql://app:apppass@localhost:3306/product_db
```

---

## ✅ 13) “One command” ishga tushirish (tavsiya)

**DEV** (DBlar docker, servislar pm2):

```bash
# 1) DB
npm run docker:dev

# 2) install
npm run setup

# 3) migrate
node services/order-service/scripts/migrate.js
node services/product-service/scripts/migrate.js

# 4) pm2 start
npm run pm2:start
```

---

## 🎯 Yakuniy tavsiyalar (production mindset)

- Har servis uchun:
  - `SIGTERM` shutdown
  - `/health`
  - `.env.example`
  - loglar (json format) + request-id
- DB uchun:
  - migration tizimi (kamida scripts)
  - connection pool (pg/mysql2)
- Gateway uchun:
  - timeout, retry (ixtiyoriy)
  - rate limit, auth middleware

---

**Muallif**: Oybek Abdujabborov
**Versiya**: 1.1.0  
**Sana**: 2026  
**License**: MIT
