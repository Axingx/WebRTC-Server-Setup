# Security Policy

## Supported Versions

Currently, we provide security guidance for the deployment stack documented in this repository.

## Reporting a Vulnerability

If you discover a security vulnerability, please report it responsibly by opening an issue with the `security` label or contacting the repository maintainers. Do not disclose vulnerabilities publicly until they have been addressed.

## Deployment Security Checklist

### 1. Secure CoTurn Credential Configuration
- Use strong, randomly generated passwords for all TURN users.
- Regularly rotate the `static-auth-secret` in `turnserver.conf`.
- Generate user credentials using `turnadmin -k` and store them securely.

### 2. Avoid Default or Weak Passwords
- Never use default credentials (e.g., `hechao:123456`) in production.
- Do not hardcode secrets in configuration files or commit them to version control. Use environment variables or secure secret management tools.

### 3. Recommended Firewall Rules
- Restrict inbound traffic to only the following ports:
  - `80` / `443` (HTTP/HTTPS for signaling and room server)
  - `3478` (UDP/TCP for STUN/TURN)
  - `5349` (TLS for TURN over TCP)
  - `59000-65000` (UDP range for TURN relay traffic)
- Block all other ports using `iptables` or cloud provider security groups.
- Enable `no-loopback-peers` and `no-multicast-peers` in CoTurn to prevent unauthorized relay usage.

### 4. TLS Certificate Requirements
- All signaling and room server endpoints **must** use HTTPS/WSS. Browsers block media capture on insecure origins.
- Use valid certificates from a trusted CA (e.g., Let's Encrypt) for production.
- Configure CoTurn with `cert` and `pkey` pointing to valid TLS certificates. Avoid self-signed certificates in production environments.

### 5. Limiting Public Exposure
- Do not expose the room or signaling servers directly to the internet without authentication or reverse proxy protection.
- Use a reverse proxy (e.g., Nginx) to enforce HTTPS, apply rate limiting, and restrict access to trusted IPs if needed.
- Monitor logs regularly for suspicious relay usage or unauthorized connection attempts.

### 6. General Best Practices
- Keep CoTurn, Node.js, Go, and Nginx updated with the latest security patches.
- Run services under non-root users where possible.
- Audit `turnserver.conf`, `constants.py`, and Nginx configs regularly for hardcoded secrets or misconfigurations.
