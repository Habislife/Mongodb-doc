# Security and Authentication

Security is a critical aspect of any database system, and MongoDB provides robust features to ensure both data confidentiality and access control. In this module, we’ll cover advanced security and authentication concepts in MongoDB through a blend of theory and hands-on practice.

## :closed_lock_with_key: Authentication and Authorization

### MongoDB Authentication Mechanisms

MongoDB supports several authentication mechanisms:

- ***SCRAM*** (Salted Challenge Response Authentication Mechanism) – Default in MongoDB. Supports SHA-1 and SHA-256.
- ***LDAP*** – Allows MongoDB to authenticate users using a centralized LDAP directory.
- Other mechanisms (Enterprise only): x.509, Kerberos.

### Authorization with Roles

MongoDB uses Role-Based Access Control (RBAC):

- Roles define actions (e.g. `find`, `insert`, `drop`) on specific resources.
- Built-in roles: `read`, `readWrite`, `dbAdmin`, `userAdmin`, etc.
- Custom roles can be created for fine-grained control.

### :hammer_and_wrench: Practical: Enabling and Configuring Access Control

#### Enable Access Control

Edit `mongod.conf`:

```javascript
security:
  authorization: enabled
```

Restart MongoDB.

#### Create Admin User

```javascript
use admin
db.createUser({
  user: "admin",
  pwd: "adminPass123",
  roles: ["userAdminAnyDatabase", "dbAdminAnyDatabase", "readWriteAnyDatabase"]
})
```

#### Connect as Admin

```javascript
mongosh -u admin -p adminPass123 --authenticationDatabase admin
```

#### Create Application User

```javascript
use app_db
db.createUser({
  user: "appUser",
  pwd: "appPass",
  roles: [{ role: "readWrite", db: "app_db" }]
})
```

#### Create a Custom Role

```javascript 
use admin

db.createRole({
  role: "readInsertRole",
  privileges: [
    {
      resource: { db: "app_db", collection: "" },
      actions: ["find", "insert"]
    }
  ],
  roles: []
})

db.createUser({
  user: "customUser",
  pwd: "customPass",
  roles: [{ role: "readInsertRole", db: "admin" }]
})
```

## :closed_lock_with_key: Encryption in MongoDB

### Encryption at Rest

- Encrypts data stored on disk.
- Available in MongoDB Enterprise.
- Uses a key file or integrates with KMIP.

### Encryption in Transit

- Protects data in motion using TLS/SSL.
-Prevents eavesdropping and MITM attacks.

### :hammer_and_wrench: Practical: Enabling Encryption

#### Encryption at Rest(Practical)

Create a key file:

```javascript
openssl rand -base64 756 > keyfile
chmod 600 keyfile
```

Edit `mongod.conf`:

```javascript
security:
  enableEncryption: true
  encryptionKeyFile: /path/to/keyfile
```

Restart MongoDB.

#### Encryption in Transit (TLS/SSL)

Generate certificate:

```javascript
openssl req -newkey rsa:2048 -new -x509 -days 365 -nodes \
  -out mongodb-cert.crt -keyout mongodb-cert.key
cat mongodb-cert.key mongodb-cert.crt > mongodb.pem
```

Edit `mongod.conf`:

```javascript
net:
  ssl:
    mode: requireSSL
    PEMKeyFile: /path/to/mongodb.pem
```

Python Example:

```python
from pymongo import MongoClient
MongoClient("mongodb://localhost:27017/?ssl=true", ssl_certfile='/path/to/client.pem')
```

Spring Boot Configuration:

```xml
spring.data.mongodb.uri=mongodb://localhost:27017/app_db?ssl=true
```

## Auditing and Monitoring

### Auditing

- Tracks user actions (logins, read/write events).
- Useful for compliance and forensic investigations.

### Monitoring

- Provides visibility into performance, operations, and potential issues.
- Tools: MongoDB Atlas (cloud), Ops Manager (on-prem).

### :hammer_and_wrench: Practical: Setting Up Auditing and Monitoring

#### Enable Auditing (Enterprise Only)

Edit `mongod.conf`:

```javascript
auditLog:
  destination: file
  format: JSON
  path: /var/log/mongodb/auditLog.json
```

#### Monitor with MongoDB Atlas

1. Create a free cluster at [MongoDB Atlas](https://www.mongodb.com/products/platform/cloud).
2. Set up alerts for performance metrics.
3. View dashboards for CPU, memory, IOPS, query execution, etc.

#### Log Analysis Tools

- `mongotop`: Real-time stats on collection-level operations.
- `mongostat`: Per-second metrics of DB activity.
- Analyze `mongod.log` and `auditLog.json` for error tracing and policy breaches.

## :calendar: Summary

MongoDB offers enterprise-grade security features:

- Robust authentication mechanisms (SCRAM, LDAP).
- Fine-grained access control using built-in and custom roles.
- Encryption at rest and in transit.
- Advanced auditing and monitoring tools.

By applying these techniques, you can secure your MongoDB deployments for both cloud and on-prem environments.
