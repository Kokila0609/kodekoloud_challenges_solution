# PostgreSQL Setup: Create User and Database with Permissions

## Step 1: Login to PostgreSQL

Open a terminal and switch to the `postgres` user:

```bash
sudo -i -u postgres
````

Access the PostgreSQL shell:

```bash
psql
```

---

## Step 2: Create a New Database User

Create a user named `kodekloud_roy` with the password `8FmzjvFU6S`:

```sql
CREATE USER kodekloud_roy WITH PASSWORD '8FmzjvFU6S';
```

---

## Step 3: Create a New Database

Create a new database named `kodekloud_db10`:

```sql
CREATE DATABASE kodekloud_db10;
```

---

## Step 4: Grant Permissions

Grant full privileges on the newly created database to user `kodekloud_roy`:

```sql
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db10 TO kodekloud_roy;
```

---

## Step 5: Verify the Database Exists

To list all databases and confirm that `kodekloud_db10` exists, run:

```sql
\l
```

You should see `kodekloud_db10` in the output list.

---
