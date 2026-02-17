# RDS, Aurora & ElastiCache — AWS SAA Notes

## Section Overview
This section covers AWS managed database services — when to use what, how they provide high availability, and how to secure them. These are HEAVILY tested on the SAA exam.

---

## 1. Amazon RDS Overview

### What is RDS?
RDS (Relational Database Service) is a managed database service. AWS handles the boring stuff — provisioning, patching, backups, scaling, OS maintenance — so you focus on your data.

### Supported Engines
- PostgreSQL
- MySQL
- MariaDB
- Oracle
- Microsoft SQL Server
- IBM Db2
- **Aurora** (AWS's own engine)

### What "Managed" Means — What AWS Does For You
- Automated provisioning and OS patching
- Continuous automated backups (point-in-time restore)
- Monitoring dashboards
- Multi-AZ deployment for disaster recovery
- Scaling (vertical and horizontal)
- Storage backed by EBS (gp2 or io1)

### What You CANNOT Do
- SSH into the RDS instance
- Direct access to the underlying OS
- This is a tradeoff — you lose control but gain automation

### Storage Auto Scaling
- RDS can automatically increase storage when it's running low
- You set a maximum storage threshold
- Useful for unpredictable workloads
- Avoids manually scaling storage

---

## 2. RDS Read Replicas vs Multi-AZ

### This is one of the MOST tested topics on the SAA exam

### Read Replicas — For Performance

```
                    ┌─────────────────┐
     Writes ──────► │   Main (Primary) │
                    │   RDS Instance   │
                    └────────┬────────┘
                             │ Async Replication
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
        ┌──────────┐  ┌──────────┐  ┌──────────┐
        │ Read     │  │ Read     │  │ Read     │
        │ Replica 1│  │ Replica 2│  │ Replica 3│
        └──────────┘  └──────────┘  └──────────┘
              ▲              ▲              ▲
              │              │              │
         App reads      App reads      App reads
```

**Key Points:**
- Up to **15 read replicas**
- Replication is **ASYNC** (eventually consistent)
- Replicas can be within AZ, cross-AZ, or **cross-Region**
- Read replicas can be **promoted to their own standalone database**
- Applications must update connection string to use read replicas
- **Use case:** Reporting, analytics — heavy read workloads that shouldn't burden the main DB

**Network Cost:**
- Same Region, different AZ → **FREE** replication
- Cross-Region → **$$ network charges apply**

### Multi-AZ — For Disaster Recovery (High Availability)

```
        Availability Zone A         Availability Zone B
        ┌─────────────────┐        ┌─────────────────┐
        │   Main (Primary) │  SYNC  │   Standby       │
        │   RDS Instance   │ ─────► │   RDS Instance   │
        │   (Read/Write)   │        │   (No access)    │
        └─────────────────┘        └─────────────────┘
                │                          │
                │    If Primary fails ──► Automatic
                │                         Failover
                ▼                          │
           Applications               Applications now
           connect here               connect to standby
                                      (same DNS endpoint)
```

**Key Points:**
- Replication is **SYNC** (every write goes to both)
- Standby is **NOT accessible** — you can't read from it
- **One DNS name** — automatic failover, app doesn't need code changes
- Used for **disaster recovery**, NOT performance
- Failover happens automatically in case of: loss of AZ, loss of network, instance failure, storage failure
- **No downtime** needed to enable Multi-AZ (just click "modify")

### The Critical Difference (Exam Favorite)

| Feature | Read Replicas | Multi-AZ |
|---------|:------------:|:--------:|
| Purpose | **Performance** (scale reads) | **Availability** (disaster recovery) |
| Replication | Async | Sync |
| Accessible? | Yes (read-only) | No (standby only) |
| Can be cross-Region? | Yes | No (same Region only) |
| Max count | 15 | 1 standby |
| Automatic failover? | No (manual promotion) | Yes (automatic) |
| Use case | Reporting, analytics, read-heavy apps | Production DB that can't go down |

### Can You Have Both?
**YES.** You can set up Read Replicas OF Multi-AZ databases. This gives you both performance AND high availability.

---

## 3. RDS Custom

### What is it?
- Available ONLY for **Oracle** and **Microsoft SQL Server**
- Gives you access to the underlying OS and database
- You CAN SSH, install patches, configure settings
- Basically: managed RDS + some manual control

### When to use?
- When you need OS-level or database-level customization
- Legacy Oracle/SQL Server apps with specific configuration needs

**Exam tip:** If question mentions needing OS access to a database → RDS Custom

---

## 4. Amazon Aurora

### What is Aurora?
Aurora is AWS's own cloud-optimized relational database engine. It's compatible with **MySQL** and **PostgreSQL** but built from the ground up for the cloud.

### Why Aurora Over Regular RDS?
- **5x faster** than MySQL on RDS
- **3x faster** than PostgreSQL on RDS
- Storage automatically grows from **10GB to 128TB**
- Up to **15 read replicas** (vs 5 for MySQL RDS)
- **Instant failover** — faster than Multi-AZ RDS
- Costs about **20% more** than regular RDS but way more efficient

### Aurora Storage Architecture

```
┌─────────────────────────────────────────────────┐
│              Aurora Cluster                       │
│                                                   │
│   ┌──────────┐     ┌──────────┐                  │
│   │  Writer   │     │  Reader   │ (up to 15)     │
│   │ Instance  │     │ Instance  │                 │
│   └─────┬────┘     └─────┬────┘                  │
│         │                │                        │
│         ▼                ▼                        │
│   ┌─────────────────────────────────────────┐    │
│   │     Shared Storage Volume                │    │
│   │     (Auto-scales 10GB → 128TB)          │    │
│   │                                          │    │
│   │   6 copies across 3 Availability Zones   │    │
│   │   ┌───┐ ┌───┐ ┌───┐ ┌───┐ ┌───┐ ┌───┐ │    │
│   │   │ 1 │ │ 2 │ │ 3 │ │ 4 │ │ 5 │ │ 6 │ │    │
│   │   └───┘ └───┘ └───┘ └───┘ └───┘ └───┘ │    │
│   │   AZ-1    AZ-1   AZ-2   AZ-2  AZ-3  AZ-3│    │
│   └─────────────────────────────────────────┘    │
└─────────────────────────────────────────────────┘
```

**Storage Key Points:**
- 6 copies of data across 3 AZs
- Needs only 4/6 copies for writes (can lose one AZ)
- Needs only 3/6 copies for reads
- Self-healing with peer-to-peer replication
- Storage is striped across 100s of volumes

### Aurora Endpoints

```
Writer Endpoint ──► Always points to the writer instance
                    (for INSERT, UPDATE, DELETE)

Reader Endpoint ──► Load balances across all read replicas
                    (for SELECT queries)
```

**Exam tip:** Applications use the Reader Endpoint for reads — it automatically load balances. No need to manage individual replica connections.

---

## 5. Aurora Advanced Concepts

### Aurora Replicas — Auto Scaling
- Read replicas can auto-scale based on CPU/connections
- More traffic → more replicas added automatically

### Aurora Custom Endpoints
- Create custom endpoints pointing to specific replicas
- Use case: Point analytics queries to larger instances, regular queries to smaller ones

### Aurora Serverless
- Automated database instantiation and auto-scaling
- No capacity planning needed
- Pay per second
- **Use case:** Infrequent, intermittent, or unpredictable workloads

### Aurora Global Database
- **1 primary Region** (read/write)
- Up to **5 secondary Regions** (read-only)
- Replication lag < 1 second
- Promoting a secondary region takes < 1 minute
- **Use case:** Disaster recovery across regions, low-latency global reads

### Aurora Machine Learning
- Integrates with SageMaker and Comprehend
- Add ML predictions to SQL queries
- **Use case:** Fraud detection, sentiment analysis, product recommendations

---

## 6. RDS & Aurora Backup and Monitoring

### Automated Backups (RDS)
- Daily full backup during maintenance window
- Transaction logs backed up every 5 minutes
- **Retention: 1-35 days** (set to 0 to disable)
- Point-in-time restore to any time within retention period

### Automated Backups (Aurora)
- Same concept but **cannot be disabled**
- Retention: 1-35 days

### Manual Snapshots (Both)
- Manually triggered by user
- **Retained as long as you want** (even after deleting the DB)
- **Cost-saving trick:** If you use a DB for 2 hours/day, snapshot it → delete DB → restore from snapshot when needed. Cheaper than keeping it running.

### Restore
- Restoring a backup or snapshot creates a **NEW database instance**
- You cannot restore into an existing instance

### Aurora Database Cloning
- Creates a new Aurora cluster from an existing one
- Uses **copy-on-write protocol** — fast and storage efficient
- **Use case:** Create a staging environment from production data without impacting production

---

## 7. RDS Security

### Encryption
- **At-rest encryption:**
  - Uses AWS KMS (AES-256)
  - Must be defined at launch time
  - If master is not encrypted, read replicas cannot be encrypted
  - To encrypt an unencrypted DB: snapshot → copy snapshot with encryption → restore from encrypted snapshot

- **In-transit encryption:**
  - SSL/TLS certificates
  - Use SSL option in connection string

### Network Security
- Deploy RDS in **private subnet** (not public)
- Use **Security Groups** to control access
- Same concept as EC2 security groups

### Access Management
- **IAM policies** control who can manage RDS (create, delete, etc.)
- **Username/password** for logging INTO the database
- **IAM Authentication** available for MySQL and PostgreSQL
  - Use IAM role/token instead of password
  - Token valid for 15 minutes
  - Network traffic encrypted with SSL

---

## 8. RDS Proxy

### What is it?
A fully managed database proxy that sits between your app and RDS.

```
Without Proxy:
┌─────┐ ┌─────┐ ┌─────┐
│App 1│ │App 2│ │App 3│   100 connections each = 300 total
└──┬──┘ └──┬──┘ └──┬──┘   Database overwhelmed!
   │       │       │
   ▼       ▼       ▼
┌─────────────────────┐
│     RDS Database     │
│   (connection limit) │
└─────────────────────┘

With Proxy:
┌─────┐ ┌─────┐ ┌─────┐
│App 1│ │App 2│ │App 3│   100 connections each
└──┬──┘ └──┬──┘ └──┬──┘
   │       │       │
   ▼       ▼       ▼
┌─────────────────────┐
│     RDS Proxy        │   Pools and shares connections
│  (connection pooling)│   Database sees only 30 connections
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│     RDS Database     │
│    (much less load)  │
└─────────────────────┘
```

**Key Points:**
- Reduces database connections through **connection pooling**
- Reduces failover time by up to **66%**
- Enforces IAM authentication
- **Never publicly accessible** (must be accessed from within VPC)
- Supports RDS (MySQL, PostgreSQL, MariaDB, SQL Server) and Aurora

**Exam tip:** If question mentions Lambda + RDS → RDS Proxy is the answer. Lambda can create hundreds of connections quickly, overwhelming the DB. Proxy solves this.

---

## 9. ElastiCache Overview

### What is it?
Managed in-memory database service. Supports **Redis** and **Memcached**. Data is stored in RAM, making it extremely fast (microsecond latency).

### Why Use a Cache?
- Reduce load on databases for read-heavy workloads
- Make application stateless by storing session data externally

### How Caching Works

```
Application Query Flow:

1. App checks cache first
   ┌─────┐         ┌──────────────┐
   │ App │ ──────► │ ElastiCache   │  Cache HIT → return data (fast!)
   └─────┘         └──────────────┘
      │
      │ Cache MISS (data not in cache)
      ▼
   ┌──────────────┐
   │   RDS Database │  Get from DB (slower)
   └──────┬───────┘
          │
          │ Write to cache for next time
          ▼
   ┌──────────────┐
   │ ElastiCache   │  Next request will be a cache HIT
   └──────────────┘
```

### Redis vs Memcached

| Feature | Redis | Memcached |
|---------|:-----:|:---------:|
| Multi-AZ with failover | ✅ | ❌ |
| Read Replicas | ✅ | ❌ |
| Data persistence | ✅ | ❌ |
| Backup and restore | ✅ | ❌ |
| Sets and sorted sets | ✅ | ❌ |
| Multi-threaded | ❌ | ✅ |
| Sharding (partitioning) | ✅ | ✅ |

**Exam shortcut:**
- Need **high availability, backups, persistence** → **Redis**
- Need **simple, multi-threaded, pure caching** → **Memcached**
- **When in doubt, pick Redis** — it's the answer 90% of the time on the exam

---

## 10. ElastiCache for Solution Architects

### Cache Security
- **Redis:**
  - Supports IAM authentication (Redis 6+)
  - SSL in-transit encryption
  - Redis AUTH (password/token)
- **Memcached:**
  - Supports SASL-based authentication

### Caching Patterns

**Lazy Loading (Cache-Aside):**
- Read from cache first
- If miss → read from DB → write to cache
- Pros: Only caches what's needed
- Cons: Cache miss = 3 trips (cache check + DB read + cache write)

**Write-Through:**
- Write to cache AND DB at the same time
- Pros: Cache is always up to date
- Cons: Writes are slower, caches data that may never be read

**Session Store:**
- Store user session data in ElastiCache
- Makes application **stateless** — any server can handle any user
- User logs in → session stored in Redis → any server reads the session

```
User logs in to Server A → Session saved to Redis
User's next request goes to Server B → Server B reads session from Redis
User is still logged in! No need to re-authenticate.
```

**Exam tip:** Question says "make the application stateless" → ElastiCache for session storage

---

## 11. Important Port Numbers

| Service | Port |
|---------|:----:|
| PostgreSQL | 5432 |
| MySQL / MariaDB | 3306 |
| Oracle | 1521 |
| Microsoft SQL Server | 1433 |
| Aurora | Same as MySQL (3306) or PostgreSQL (5432) depending on engine |
| Redis | 6379 |
| Memcached | 11211 |

**Why this matters:** You need to set correct ports in Security Groups to allow traffic to your databases.

---

## Exam Cheat Sheet — What to Pick When

| Scenario | Answer |
|----------|--------|
| Need to scale reads | Read Replicas |
| Need high availability / disaster recovery | Multi-AZ |
| Need OS/DB level access for Oracle | RDS Custom |
| Need fastest relational DB on AWS | Aurora |
| Need unpredictable/intermittent DB workloads | Aurora Serverless |
| Need global disaster recovery for DB | Aurora Global Database |
| Need to reduce DB connection overload | RDS Proxy |
| Lambda connecting to RDS | RDS Proxy |
| Need microsecond latency reads | ElastiCache |
| Need to make app stateless | ElastiCache (session store) |
| Need cache with high availability | Redis |
| Need simple multi-threaded cache | Memcached |
| Encrypt unencrypted RDS DB | Snapshot → Copy with encryption → Restore |
| Question says "managed" + "relational" | RDS or Aurora |
| Question says "in-memory" or "caching" | ElastiCache |