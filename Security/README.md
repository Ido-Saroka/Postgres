# PostgreSQL Security Checklist

## 1. **User & Role Management**

- **Avoid Using the Default Superuser**: Create a new superuser instead of using the default `postgres` user. Disable or restrict `postgres` where possible.
- **Principle of Least Privilege**: Assign the minimal required privileges to each user.
- **Role-Based Access Control (RBAC)**: Use roles to group users and assign privileges accordingly.
- **Restrict Role Inheritance**: Ensure users only inherit necessary privileges.

## 2. **Authentication & Connection Security**

- **Use Secure Authentication Methods**:
  - Avoid `trust`, `peer`, `ident`, and `password` authentication.
  - Use `scram-sha-256`, `md5`, `LDAP`, or `RADIUS` authentication, even for local connections.
- **Limit Database Access**:
  - Use SSH or VPN to connect to the database from a dedicated IP range.
  - Define allowed IPs in `pg_hba.conf` instead of allowing all (`0.0.0.0`).
- **Change the Default Port**:
  - Modify the default port (5432) to a non-standard port to reduce the risk of automated scans.
- **Restrict Listening Interfaces**:
  - Configure `postgresql.conf` to listen only on required IP addresses, not `0.0.0.0`.
- **Enable SSL Encryption**:
  - Use Let's Encrypt or another CA for SSL certificates.
  - Mask domain registration details to prevent phishing attempts.

## 3. **Database Hardening**

- **Restrict Template Databases**:
  - Disable connections to `template1` and `postgres` to prevent unauthorized database creation.
- **Ensure Proper File Permissions**:
  - PostgreSQL configuration files should be readable/writable only by the PostgreSQL user.
- **Brute Force Protection**:
  - Use tools like `fail2ban` to monitor and block repeated failed login attempts.
  - Implement rate limiting and logging for authentication attempts.
- **Enable Logging & Auditing**:
  - Configure logging to track login attempts, query execution, and privilege changes.
  - Regularly review logs for suspicious activity.

## 4. **Schema & Object Security**

- **Use Separate Schemas for Different Privileges**:
  - Store end-user tables/functions in a dedicated schemas (not `public`).
- **Define Default Search Paths**:
  - Set the default search path to include only the necessary schemas.
- **Restrict Function Access**:
  - Grant `EXECUTE` privileges only to authorized roles.
  - Add `TRY/CATCH` blocks to user functions to return generic error messages and prevent information leakage.
- **Ensure Proper Ownership**:
  - Avoid granting object ownership to regular users to prevent privilege escalation.

- **Revoke Public Schema Access**:
  - Ensure `public` has no default access to schemas, tables, or functions. 
  - Remove `CREATE` and `USAGE` privileges from the `public` schema to prevent unauthorized object creation (`CVE-2018-1058`) and table viewing.
  - Revoke the ability to connect to the database unless explicitly required.
  - Restrict access to system catalogs like `pg_proc` and `pg_get_functiondef()`.

## 5. **Access Control & Privilege Management**

- **Grant Only Necessary Privileges**:
  - Create roles with minimal privileges (`CONNECT`, `SELECT`, etc.).
  - Ensure users do not own objects unless explicitly required.
- **Implement Role-Based Login Restrictions**:
  - Use roles without login capabilities for privilege management ("basic_user")
  - Create login-enabled roles that inherit necessary permissions.
- **Revoke Unnecessary Privileges**:
  - Remove privileges that allow users to see other roles (`pg_roles`).

## 6. **Ongoing Maintenance & Compliance**

- **Regularly Update PostgreSQL**:
  - Stay up to date with security patches and major version upgrades.
- **Backup & Disaster Recovery**:
  - Automate daily backups and store them securely.
  - Regularly test recovery procedures.
- **Monitor & Review Security Settings**:
  - Use PostgreSQL extensions like `pgAudit` for enhanced auditing.
  - Conduct periodic security reviews and compliance checks.

By following this checklist, organizations can significantly enhance the security of their PostgreSQL databases, reducing the risk of unauthorized access, data breaches, and compliance violations.
