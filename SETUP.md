# Environment setup

## Recommended: local Odoo Community via Docker

Why local rather than the online trial: **the Odoo online trial does not give you PostgreSQL access**, so you cannot do the SQL verification section — and the trial expires. A local install gives you full database access and no time limit.

You do not need to write code for this. You need to run a few commands.

### 1. Install Docker Desktop
Download from docker.com and install. Start it.

### 2. Create a file named `docker-compose.yml`

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_DB: postgres
      POSTGRES_USER: odoo
      POSTGRES_PASSWORD: odoo
    volumes:
      - odoo-db:/var/lib/postgresql/data
    ports:
      - "5432:5432"

  odoo:
    image: odoo:latest
    depends_on:
      - db
    ports:
      - "8069:8069"
    environment:
      HOST: db
      USER: odoo
      PASSWORD: odoo
    volumes:
      - odoo-data:/var/lib/odoo

volumes:
  odoo-db:
  odoo-data:
```

### 3. Start it

```bash
docker compose up -d
```

Wait about a minute, then open <http://localhost:8069>.

### 4. Create the database
On first load Odoo asks you to create a database. **Tick "Load demonstration data"** — it gives you sample customers, products and stock so you are not starting from an empty system.

### 5. Install the modules under test
Apps → install **Sales**, **Inventory**, **Invoicing**.

### 6. Record your environment
Fill in the environment table in `01-test-plan/TEST-PLAN.md` — exact Odoo version (Settings → About), browser and version, OS. **Do this before you test anything.** A defect report without a version is not reproducible, and an experienced interviewer will notice immediately.

### Connecting to the database for the SQL section

```bash
docker compose exec db psql -U odoo -d <your-database-name>
```

List tables with `\dt`, quit with `\q`.

---

## Fallback: Odoo online trial

If Docker will not run on your machine, use the free trial at <https://www.odoo.com/trial>. Everything works except `06-sql-checks/` — in that case delete that folder rather than leaving it empty, and say in the README that testing was done on Odoo Online.
