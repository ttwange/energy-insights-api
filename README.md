## ⚡ Energy Insights API

Python: https://img.shields.io/badge/python-3.10%2B-blue.svg
Flash: https://img.shields.io/badge/flask-3.x-lightgrey.svg
PostgreSQL: https://img.shields.io/badge/postgresql-15.x-blue.svg
Docker: https://img.shields.io/badge/docker-compose-blue.svg

A containerized **Flask + PostgreSQL** data API that fetches and stores live **electricity demand** and **CO₂ emission data** from the [Danish Energy Data Service](https://www.energydataservice.dk/).  
This project is part of a broader DevOps & Data Engineering learning roadmap.
```
```
## Project Overview

| Component | Purpose |
|------------|----------|
| * Flask API** | Fetches live Danish energy and emission data |
| * PostgreSQL** | Stores demand and emissions data persistently |
| * Docker Compose** | Orchestrates Flask and Postgres containers |
| * Nginx (Next Step)** | Reverse proxy + SSL termination |
| * GitHub Actions (Later)** | CI/CD deployment to Contabo VPS |

### Architecture (Current)


            Danish Energy Data Service
                        ↓
               Flask (API Layer)
                        ↓
               PostgreSQL (Storage)
                        ↓
                 Docker Compose
```

```

You can access endpoints locally on:


Demand: http://localhost:5050/v1/energy/dk/demand;
Emissions: http://localhost:5050/v1/energy/dk/emissions;
Test health: http://localhost:5050/v1/health;
````
## ⚙️ Setup & Run

### 1️⃣ Clone the repo
```bash
git clone https://github.com/<your-username>/energy-insights-api.git
cd energy-insights-api
````

### 2️⃣ Build and start the stack

```bash
docker-compose up --build
```

Flask runs on port **5050**, PostgreSQL on **5432**.

### 3️⃣ Access the API

```bash
GET http://localhost:5050/v1/energy/dk/demand
GET http://localhost:5050/v1/energy/dk/emissions
GET http://localhost:5050/v1/health
```

Each call:

* Fetches live data from the Danish Energy API
* Inserts it into PostgreSQL (`ON CONFLICT DO NOTHING` prevents duplicates)
* Returns the latest 10 records as JSON

---

## 🧩 Project Structure

```
energy-insights-api/
│
├── app/
│   ├── main.py                # Flask routes + logic
│   ├── db.py                  # Database connection + table creation
│   ├── requirements.txt       # Flask + SQLAlchemy dependencies
│
├── docker-compose.yml         # Multi-container setup (Flask + Postgres)
├── Dockerfile                 # Flask container build file
└── README.md                  # Documentation
```

---

## 💾 PostgreSQL Schema

### `energy_demand`

| Column        | Type               | Description             |
| ------------- | ------------------ | ----------------------- |
| id            | SERIAL             | Primary key             |
| timestamp     | TIMESTAMP (UNIQUE) | Measurement timestamp   |
| total_load_mw | FLOAT              | Electricity demand (MW) |

### `energy_emissions`

| Column                 | Type               | Description           |
| ---------------------- | ------------------ | --------------------- |
| id                     | SERIAL             | Primary key           |
| timestamp              | TIMESTAMP (UNIQUE) | Measurement timestamp |
| co2_emission_g_per_kwh | FLOAT              | CO₂ emissions (g/kWh) |

---

## 🧮 PostgreSQL Cheat Sheet

Open Postgres inside Docker Desktop or terminal:

```bash
docker-compose exec db psql -U energy_user -d energy_db
```

| Action                | Command                                |
| --------------------- | -------------------------------------- |
| List databases        | `\l`                                   |
| Connect to a DB       | `\c energy_db`                         |
| List tables           | `\dt`                                  |
| Describe table schema | `\d energy_demand`                     |
| Show first 5 rows     | `SELECT * FROM energy_demand LIMIT 5;` |
| Count rows            | `SELECT COUNT(*) FROM energy_demand;`  |
| Empty a table         | `TRUNCATE TABLE energy_demand;`        |
| Drop a table          | `DROP TABLE energy_demand;`            |
| Exit PostgreSQL       | `\q`                                   |

---

## 🧠 Developer Notes

### 🔹 Flask Auto-Reload

The app auto-reloads whenever you edit files in `./app` because of:

```yaml
volumes:
  - ./app:/app
```

No need to rebuild — just save your changes and Flask restarts instantly.

### 🔹 Persistent Database

Data persists between restarts using a named Docker volume:

```yaml
volumes:
  pgdata:/var/lib/postgresql/data
```

To reset the database completely:

```bash
docker-compose down -v
```

### 🔹 Access Postgres from GUI (optional)

Use pgAdmin, DBeaver, or TablePlus:

```
Host: localhost
Port: 5432
User: energy_user
Password: energy_pass
Database: energy_db
```

---

## 🐳 Common Docker Commands

| Action             | Command                                                   |
| ------------------ | --------------------------------------------------------- |
| Start containers   | `docker-compose up -d`                                    |
| View logs          | `docker-compose logs -f`                                  |
| Stop containers    | `docker-compose down`                                     |
| Rebuild            | `docker-compose up --build`                               |
| Remove all volumes | `docker-compose down -v`                                  |
| Access Flask shell | `docker exec -it energy-api /bin/bash`                    |
| Access Postgres    | `docker-compose exec db psql -U energy_user -d energy_db` |

---

## ✅ Next Phase: Nginx Reverse Proxy + SSL

Next, you will:

1. Add an **Nginx** service to Docker Compose
2. Proxy traffic to Flask internally
3. Configure **HTTPS** with **Let’s Encrypt (Certbot)**
4. Deploy the stack on your **Contabo VPS**

---

## 🧰 Tech Stack

* **Python 3.10 / Flask**
* **PostgreSQL 15**
* **SQLAlchemy**
* **Docker Compose**
* *(Coming Soon)* Nginx + GitHub Actions + SSL

---

## 🧩 Author

Developed as part of a **Data Engineering & DevOps learning roadmap**, integrating open energy data, containerized infrastructure, and cloud deployment workflows.

---

## 🚀 Roadmap

| Phase   | Description                          | Status      |
| ------- | ------------------------------------ | ----------- |
| **1**   | VPS Hardening (non-root + UFW)       | ✅ Done      |
| **2**   | Flask API + Docker Setup             | ✅ Done      |
| **3**   | PostgreSQL Integration               | ✅ Done      |
| **3.5** | Auto-Reload Development Mode         | ✅ Done      |
| **4**   | Nginx Reverse Proxy + SSL            | 🔜 Next     |
| **5**   | CI/CD (GitHub Actions → Contabo VPS) | 🔜 Upcoming |
| **6**   | Visualization (Grafana / Superset)   | 🔜 Future   |

---

🟢 **Next Step → [Phase 4: Nginx Reverse Proxy Setup](#)**
Expose your API publicly via:

```
http://<your-vps-ip>/v1/energy/dk/demand
```

and later:

```
https://<your-domain>/v1/energy/dk/demand
```

```

---

Would you like me to also create a **minimal “docs/quick-commands.md”** file that contains only the Postgres and Docker cheat-sheet for easier reference during development?
```
