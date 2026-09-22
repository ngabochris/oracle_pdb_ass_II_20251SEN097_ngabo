# Oracle Pluggable Databases (PDB) Management

**Name:** NGABO MEDDY Chriss

**Student ID:** 20251SEN097

**Group:** C

**DBMS Used:** Oracle Database 21c Express Edition (Docker / `gvenzl/oracle-xe:21-full`)

**SQL Client:** Oracle SQL Developer

## Submission Details

| Field | Value | 
 | ----- | ----- | 
| **Repo Link** | `https://github.com/ngabochris/oracle_pdb_ass_II_20251SEN097_ngabo` | 
| **PDB Name** | `ng_pdb_20251SEN097` | 
| **Issue Encountered** | Yes *(Resolved - details in Challenges section)* | 

## 1. Overview of Tasks

This assignment covers key Oracle Multitenant Database administration tasks using SQL Developer and Docker container environments:

1. **Creation of a Pluggable Database (PDB):** Provisioning a new PDB from `PDB$SEED` with standard tablespaces and admin user configuration.

2. **PDB Lifecycle Management (Create & Drop/Delete):** Testing PDB creation, state modification (`READ WRITE`, `SAVE STATE`), and dropping a target PDB including datafiles.

3. **Oracle Enterprise Manager (OEM Express) Setup:** Exposing management ports, enabling XML DB HTTP/HTTPS services, and accessing OEM Express for database monitoring.

## 2. Oracle Environment Used

* **Host OS:** Linux (Ubuntu/Debian-based)

* **Containerization:** Docker (`gvenzl/oracle-xe:21-full`)

* **Database Architecture:** Oracle Multitenant Architecture (CDB/PDB)

* **Port Mappings:**

  * `1521` $\rightarrow$ Oracle TNS Listener

  * `8000:8080` $\rightarrow$ APEX / ORDS

  * `5500` $\rightarrow$ Oracle Enterprise Manager (OEM) Express (HTTPS)

* **Tooling:** Oracle SQL Developer, SQL\*Plus, Docker CLI

## 3. Task Explanations & SQL Scripts

### Task 1: Create a New Pluggable Database (PDB)

A new PDB named `ng_pdb_20251SEN097` was created from `PDB$SEED` with a dedicated local admin user and storage file conversion settings.

-- Connect as SYSDBA to CDB$ROOT
-- 1. Create PDB from Seed

```sql
CREATE PLUGGABLE DATABASE ng_pdb_20251SEN097
ADMIN USER ngabo_plsqlauca_20251SEN097 IDENTIFIED BY "1234"
FILE_NAME_CONVERT = (
    '/opt/oracle/oradata/XE/pdbseed/', 
    '/opt/oracle/oradata/XE/ng_pdb_20251SEN097/'
);

-- 2. Open PDB in Read/Write Mode and Save State
ALTER PLUGGABLE DATABASE ng_pdb_20251SEN097 OPEN READ WRITE;
ALTER PLUGGABLE DATABASE ng_pdb_20251SEN097 SAVE STATE;

-- 3. Verify PDB Status
SELECT name, open_mode FROM v$pdbs WHERE name = 'ng_pdb_20251SEN097';

```

### Task 2: Create and Delete (Drop) a PDB

To verify database lifecycle management, a temporary PDB was created, closed, and completely removed along with its datafiles.

-- Create temporary PDB

```sql
CREATE PLUGGABLE DATABASE ng_to_delete_pdb_20251SEN097
ADMIN USER ngabo_plsqlauca_20251SEN097 IDENTIFIED BY "1234"
FILE_NAME_CONVERT = (
    '/opt/oracle/oradata/XE/pdbseed/', 
    '/opt/oracle/oradata/XE/ng_to_delete_pdb_20251SEN097/'
);

-- Open and verify
ALTER PLUGGABLE DATABASE ng_to_delete_pdb_20251SEN097 OPEN READ WRITE;

-- Close PDB prior to deletion
ALTER PLUGGABLE DATABASE ng_to_delete_pdb_20251SEN097 CLOSE IMMEDIATE;

-- Drop PDB including datafiles
DROP PLUGGABLE DATABASE ng_to_delete_pdb_20251SEN097 INCLUDING DATAFILES;

```

### Task 3: Oracle Enterprise Manager (OEM Express) Setup

Configured the XML DB HTTP/HTTPS listener settings to enable web access to Enterprise Manager Express.

-- Executed as SYSDBA in CDB$ROOT

```sql
EXEC DBMS_XDB_CONFIG.SETHTTPSPORT(5500);
EXEC DBMS_XDB_CONFIG.SETGLOBALPORTENABLED(TRUE);

```

**Access URL:** `https://localhost:5500/em`

**User:** `SYS` (Connected as `SYSDBA`)

**Password** `1234`

**Container:** `CDB$ROOT`

## 4. Challenges Faced & Resolutions

1. **HTTP 400 "Bad Request" on EM Port 8000:**

   * *Issue:* Attempting to access EM Express over plain HTTP on port 8000 returned a `Bad Request` error because port 8080/8000 hosts ORDS/APEX, whereas EM Express requires HTTPS on port 5500.

   * *Resolution:* Re-created the Docker container with `-p 5500:5500` exposed and enabled HTTPS using `DBMS_XDB_CONFIG.SETHTTPSPORT(5500)`.

2. **Container Exit/Crash on Restart (`Exited 1`):**

   * *Issue:* Re-running `docker run` without environment parameters caused initialization failure.

   * *Resolution:* Re-created the container with explicit environment flags (`-e ORACLE_PASSWORD=...`) and monitored initialization using `docker logs -f oracle21c` until `DATABASE IS READY TO USE!` was displayed.

3. **New PDB Remaining in `MOUNTED` State:**

   * *Issue:* By default, newly created PDBs stay in `MOUNTED` state after container/service restarts.

   * *Resolution:* Executed `ALTER PLUGGABLE DATABASE <pdb_name> SAVE STATE;` to retain `READ WRITE` status across restarts.

## 5. Results & Screenshots

![Screenshot from 2026-09-22 12-55-31](screenshots/Screenshot%20from%202026-09-22%2012-55-31.png)

![Screenshot from 2026-09-22 13-06-29](screenshots/Screenshot%20from%202026-09-22%2013-06-29.png)

![Screenshot from 2026-09-22 13-34-45](screenshots/Screenshot%20from%202026-09-22%2013-34-45.png)

![Screenshot from 2026-09-22 13-35-15](screenshots/Screenshot%20from%202026-09-22%2013-35-15.png)

![Screenshot from 2026-09-22 13-36-03](screenshots/Screenshot%20from%202026-09-22%2013-36-03.png)

![Screenshot from 2026-09-22 13-37-25](screenshots/Screenshot%20from%202026-09-22%2013-37-25.png)

![Screenshot from 2026-09-22 13-40-55](screenshots/Screenshot%20from%202026-09-22%2013-40-55.png)

![Screenshot from 2026-09-22 13-49-40](screenshots/Screenshot%20from%202026-09-22%2013-49-40.png)

![Screenshot from 2026-09-22 13-49-54](screenshots/Screenshot%20from%202026-09-22%2013-49-54.png)

![Screenshot from 2026-09-22 14-07-37](screenshots/Screenshot%20from%202026-09-22%2014-07-37.png)

![Screenshot from 2026-09-22 14-17-43](screenshots/Screenshot%20from%202026-09-22%2014-17-43.png)

### Task 1: PDB Creation & Verification

### Task 2: PDB Deletion & Lifecycle Management

### Task 3: Oracle Enterprise Manager (OEM) Express Access

## 6. Integrity Statement

I hereby declare that this assignment represents my own original work conducted in accordance with academic integrity standards. All database operations, configurations, and scripts presented in this report were executed by me in my personal development environment.

**Signature:** NGABO MEDDY Chriss

**Date:** September 22, 2026