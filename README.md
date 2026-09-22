## Student : GISINGIZO Gloria
## ID: 20252SEN385

###PLUGGABLE DATABASES (PDBS) MANAGEMENT

## Task 1: Pluggable Database (PDB) & Local User Creation

### 1. Database Configuration & OMF Verification
Checked the status of Oracle Managed Files (OMF) to verify if the file system automatically handles PDB generation:
```sql
SHOW PARAMETER db_create_file_dest;
```
* **Status:** OMF was disabled (parameter value was blank).
* **Resolution:** Enabled OMF permanently to handle database files automatically:
```sql
ALTER SYSTEM SET db_create_file_dest = 'FOLDER PATH' SCOPE=BOTH;
```

### 2. PDB Creation Command
Executed the standard administrative command from `CDB$ROOT` to create a fresh pluggable database from the seed template:
```sql
CREATE PLUGGABLE DATABASE Gl_pdb_20252SEN385 
ADMIN USER Gloria_plsqlaucaa_20252SEN385 IDENTIFIED BY "Gloria123";
```

### 3. PDB Open State
Opened the database and ran the verification query to confirm that the PDB successfully transitioned into the active `READ WRITE` state:
```sql
ALTER PLUGGABLE DATABASE Gl_pdb_20252SEN385 OPEN;

SELECT name, open_mode FROM v\$pdbs WHERE name = 'GL_PDB_20252SEN385';
```

### 4. Local User Creation (Username Clearly Visible)
Switched the terminal session container into the isolated PDB environment to provision the local user account required for future class work:
```sql
ALTER SESSION SET CONTAINER = Gl_pdb_20252SEN385;

CREATE USER "Admin_Gloria" IDENTIFIED BY "admin_gloria123";
GRANT DBA TO "Admin_Gloria";
ALTER USER "Admin_Gloria" QUOTA UNLIMITED ON users;
```
Verified that the user account was successfully registered and remains visible inside the database view:
```sql
SELECT username, account_status FROM dba_users;
```

---

## Task 2: PDB Lifecycle Management (Creation and Complete Deletion)

### 1. PDB Creation
Executed the administrative command from the root container (`CDB$ROOT`) to create a temporary test pluggable database using the established OMF configuration:
```sql
CREATE PLUGGABLE DATABASE Gl_to_delete_pdb_20252SEN385 
ADMIN USER temp_admin IDENTIFIED BY "gloria123";
```

### 2. Existence Verification
Queried the data dictionary to confirm that the new PDB was successfully registered in the system container registry:
```sql
SELECT name, open_mode FROM v\$pdbs WHERE name = 'GL_TO_DELETE_PDB_20252SEN385';
```

### 3. Complete PDB Deletion
To safely remove the PDB and free up storage, the database instance was closed immediately, followed by a complete drop command to purge its metadata and physical data files from disk:
```sql
ALTER PLUGGABLE DATABASE Gl_to_delete_pdb_20252SEN385 CLOSE IMMEDIATE;

DROP PLUGGABLE DATABASE Gl_to_delete_pdb_20252SEN385 INCLUDING DATAFILES;
```

### 4. Removal Confirmation
Ran a final system check to confirm that the PDB registry record and all associated files no longer exist:
```sql
SELECT name, open_mode FROM v\$pdbs WHERE name = 'GL_TO_DELETE_PDB_20252SEN385';
```
* **Result:** No rows selected (Successful confirmation of total removal).
