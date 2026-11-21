# Implementation of a Three-Node Replicated Distributed SQL Database Using CockroachDB

A distributed SQL database system demonstrating multi-node clustering, Multi-Raft consensus replication, horizontal data distribution, fault tolerance with quorum, and full-stack CRUD monitoring.

---

## Quick Start (3 Steps)

### Step 1: Start the 3-Node CockroachDB Cluster
```powershell
docker compose up -d
```

### Step 2: Initialize Database and Schema (Optional - Auto-initialized by server)
```powershell
docker exec -i roach1 ./cockroach sql --insecure < init-cluster.sql
```

### Step 3: Start the Monitoring Dashboard & Backend
```powershell
npm start
```

Open your browser to: **[http://localhost:3000](http://localhost:3000)**

---

## Project Structure

```
d:\DBMS PROJECT\
├── docker-compose.yml          # 3 CockroachDB nodes (roach1, roach2, roach3) + cluster-init container
├── package.json               # Node.js project configuration (Express, pg, cors)
├── server.js                  # Multi-node Express backend with 3 distinct connection pools
├── init-cluster.sql           # SQL script creating distributed_store and 3x replicated products table
├── test-queries.sql           # SQL verification queries for replication, DistSQL, and transactions
├── public/                    # Frontend Web Dashboard
│   ├── index.html             # Responsive monitoring interface & simulation controls
│   ├── style.css              # Modern slate/dark UI styling
│   └── app.js                 # Real-time health polling, CRUD routing, and consistency tester
├── docs/                      # Comprehensive Academic Project Deliverables
│   ├── ARCHITECTURE.md        # System architecture, Multi-Raft consensus, and leaseholder specs
│   ├── PROJECT_REPORT.md      # Formal academic DBMS project report
│   └── TESTING_GUIDE.md       # Step-by-step demonstration walkthrough for viva & evaluation
└── README.md                  # This file
```

---

## Key Features

1. **True 3-Node Cluster**: Configured using Docker Compose with dedicated storage volumes and internal bridge network (`roachnet`).
2. **Replication Factor 3**: Configured via zone configuration (`ALTER TABLE products CONFIGURE ZONE USING num_replicas = 3`).
3. **Multi-Node Read/Write**: Frontend allows routing queries through **Node 1 (26257)**, **Node 2 (26258)**, or **Node 3 (26259)** to empirically prove instant cross-node data consistency.
4. **Live Raft Range Inspector**: Reads real-time metadata from `SHOW RANGES FROM TABLE products` (Range ID, replica node IDs, and active leaseholder).
5. **Fault Tolerance with Quorum**:
   - Survives 1 node failure ($Q = 2$ of 3 replicas online).
   - Serves continuous reads and writes during failure.
   - Automatically catches up and restores full health when the failed node restarts.
6. **Distributed SQL (DistSQL)**: Demonstrates parallel distributed query execution graphs (`EXPLAIN DISTSQL`).
7. **Distributed ACID Transactions**: Atomic stock transfer across products executed under `SERIALIZABLE` isolation.
