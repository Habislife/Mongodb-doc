# Security Policy

## Supported Versions

We actively maintain and apply security updates to the following versions:

| Version | Supported          |
| ------- | ------------------ |
| 0.1     | :white_check_mark: Supported |
| 0.2     | :white_check_mark: Supported |
| < 0.01  | :x: No longer supported |

## Reporting a Vulnerability

If you discover a security vulnerability in this MongoDB-based project, please report it responsibly.

- **Do not create a public GitHub issue.**
- Instead, send an email to: **<bishalf98@gmail.com>**
- Include the following:
  - Description of the vulnerability
  - Steps to reproduce
  - Potential impact
  - Suggested fix (if any)

We will acknowledge your report within 72 hours and strive to provide a resolution within 30 days. Once the vulnerability is resolved, a security advisory will be published.

## General Security Practices

To ensure your MongoDB deployment remains secure:

- **Never expose MongoDB to the public internet** without proper authentication and firewall rules.
- **Enable authentication and role-based access control** (RBAC).
- **Use TLS/SSL** to encrypt connections.
- **Use strong, unique passwords** and rotate them periodically.
- **Disable unused features** such as HTTP interface (`--rest`) or scripting if not needed.
- **Restrict network access** using firewalls or cloud security groups.
- **Backup regularly** and store backups securely.
- Keep MongoDB and all dependencies **updated** with the latest security patches.

## References

- [MongoDB Security Checklist](https://www.mongodb.com/docs/manual/administration/security-checklist/)
- [MongoDB Production Notes](https://www.mongodb.com/docs/manual/administration/production-notes/)
- [CVE Database for MongoDB](https://www.cvedetails.com/vendor/13106/Mongodb.html)
