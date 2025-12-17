# MySQL Master-Replica Example 🐬

This project demonstrates a **MySQL 8 master-replica setup using Docker Compose**, with separate users for application, replication, and read-only reporting.  

The setup is ready for **development and testing**, and can be adapted for production reporting replicas.

---

## Features ✅

- **Master (\`mysql-master\`)**  
  - App database: \`relvora\`  
  - App user: \`relvora\` (full access to \`relvora\` database)  
  - Replication user: \`repl\`  

- **Replica (\`mysql-replica\`)**  
  - Read-only replica of master database  
  - Reporting user: \`report\` (read-only access to all databases)  

- Automatic replication via GTID (Global Transaction IDs)  
- \`read-only\` mode on replica ensures safety  
- Fully scripted with Docker Compose + SQL init scripts  

---

## Project Structure 📁

```
master-replica-example/
│
├─ docker-compose.yml         # Docker Compose setup for master and replica
├─ master-init/
│   └─ master-init.sql        # Master initialization: app user, replication user, reporting user
├─ replica-init/
│   └─ replica-init.sql       # Replica initialization: configure replication from master
└─ README.md                  # Project documentation
```

---

## Setup Instructions 🛠️

### 1. Clone the repository

bash
git clone https://github.com/tariqulgithub/master-replica-example.git
cd master-replica-example

### 2. Start the containers

> **Warning:** For the first run, remove old volumes to allow init scripts to execute properly.

bash
docker compose down -v
docker compose up -d


### 3. Verify the setup

**Check master databases:**

bash
docker exec -it mysql-master mysql -urelvora -prelvorapassword -e "SHOW DATABASES;"

**Check replica databases (read-only):**

bash
docker exec -it mysql-replica mysql -ureport -preportpassword -e "SHOW DATABASES;"

**Check replication status:**

bash
docker exec -it mysql-replica mysql -uroot -prootpassword -e "SHOW REPLICA STATUS\G"

---

## Users & Permissions 🔑

| User     | Host | Permissions                               | Usage |
|----------|------|-------------------------------------------|-------|
| \`root\`   | \`%\`  | Full access (master), limited on replica | Admin |
| \`relvora\`| \`%\`  | All privileges on \`relvora\` database     | App   |
| \`repl\`   | \`%\`  | Replication privileges                    | Replication threads |
| \`report\` | \`%\`  | \`SELECT\` on all databases                 | Read-only reporting on replica |

---

## Notes ⚠️

- The replica is **read-only**, so no writes are allowed except through replication.  
- For production, consider adding \`--super-read-only=ON\` on the replica for stricter protection.  
- Always remove volumes for the first run to ensure init scripts execute.  

---

## License

This project is **open source** and available under the MIT License. 
