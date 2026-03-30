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

| Volume         | Description                          |
|----------------|--------------------------------------|
| `wordpress`    | 📝 WordPress files (`/var/www/html`) |
| `db_data`      | 🗄️ MySQL data (`/var/lib/mysql`)     |
| `caddy_data`   | 🔒 Caddy TLS certificates and data  |
| `caddy_config` | ⚙️ Caddy configuration               |

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

## 📄 License

This project is licensed under the [MIT License](LICENSE).
