# 🐳 Docker Compose: WordPress + MySQL + Caddy

A quick and easy way to spin up a WordPress site with MySQL and Caddy for automatic HTTPS. 🚀

## 📦 Stack

| Service   | Version | Description                                      |
|-----------|---------|--------------------------------------------------|
| WordPress | 6.9     | 📝 Latest stable release (6.9.4)                 |
| MySQL     | 8.4     | 🗄️ Latest LTS version with improved defaults     |
| Caddy     | 2       | 🔒 Automatic HTTPS reverse proxy with Let's Encrypt |

## ✅ Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/) installed
- 🌐 A domain name pointing to your server's IP address (required for automatic HTTPS)

## ⚡ Quick Start

1. Clone the repository:

   ```bash
   git clone https://github.com/ronaldpringadi/docker-compose-wordpress-mysql-caddy.git
   cd docker-compose-wordpress-mysql-caddy
   ```

2. Copy the example environment file and configure it:

   ```bash
   cp .env.example .env
   ```

3. ✏️ Edit `.env` with your values:

   ```bash
   DOMAIN=example.com
   MYSQL_ROOT_PASSWORD=change-me-root
   MYSQL_DATABASE=wordpress
   MYSQL_USER=wordpress
   MYSQL_PASSWORD=change-me
   ```

4. 🏁 Start the stack:

   ```bash
   docker compose up -d
   ```

5. 🎉 Visit `https://your-domain.com` and complete the WordPress installation wizard.

## 🗂️ Project Structure

```
.
├── .env.example        # 🔑 Environment variable template
├── Caddyfile           # 🌐 Caddy reverse proxy configuration
├── docker-compose.yml  # 🐳 Docker Compose service definitions
├── wordpress/          # 📝 WordPress files (created on first run)
├── db_data/            # 🗄️ MySQL data (created on first run)
└── README.md
```

## ⚙️ Configuration

### 🔑 Environment Variables

| Variable              | Description                        | Default     |
|-----------------------|------------------------------------|-------------|
| `DOMAIN`              | 🌐 Your domain name               | example.com |
| `MYSQL_ROOT_PASSWORD` | 🔐 MySQL root password             | —           |
| `MYSQL_DATABASE`      | 🗄️ WordPress database name         | wordpress   |
| `MYSQL_USER`          | 👤 WordPress database user          | wordpress   |
| `MYSQL_PASSWORD`      | 🔐 WordPress database password     | —           |

### 💾 Volumes

| Volume         | Type        | Description                          |
|----------------|-------------|--------------------------------------|
| `./wordpress`  | Local folder | 📝 WordPress files (`/var/www/html`) |
| `./db_data`    | Local folder | 🗄️ MySQL data (`/var/lib/mysql`)     |
| `caddy_data`   | Named volume | 🔒 Caddy TLS certificates and data  |
| `caddy_config` | Named volume | ⚙️ Caddy configuration               |

## 🛠️ Useful Commands

```bash
# ▶️ Start the stack
docker compose up -d

# 📋 View logs
docker compose logs -f

# ⏹️ Stop the stack
docker compose down

# 💥 Stop and remove volumes (destroys all data)
docker compose down -v
```

## 📎 Appendix

### Trusting Caddy's Local CA (remove browser certificate warning)

When using a local domain (e.g. via `/etc/hosts`), the Caddyfile uses `tls internal` which generates a self-signed certificate. Browsers will show a warning because the cert isn't signed by a public CA.

To permanently suppress the warning, trust Caddy's local root CA on your machine:

**macOS:**
```bash
# Extract Caddy's root CA cert
docker compose cp caddy:/data/caddy/pki/authorities/local/root.crt ./caddy_local_ca.crt

# Trust it system-wide
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ./caddy_local_ca.crt
```

Restart your browser after running the commands. The warning will not appear again for any site using `tls internal` on this machine.

> This is only needed for local development. On a real server with a public domain, remove `tls internal` from the Caddyfile and Caddy will obtain a trusted certificate from Let's Encrypt automatically.

## 📄 License

This project is licensed under the [MIT License](LICENSE).
