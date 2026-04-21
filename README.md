# 🐳 Docker Compose: WordPress 6.9 + MySQL + Caddy

A quick and easy way to spin up a WordPress site with MySQL and Caddy for automatic HTTPS. 🚀

## 📦 Stack

| Service   | Version | Description                                      |
|-----------|---------|--------------------------------------------------|
| WordPress | 6.9     | 📝 Latest stable release (6.9.4)                 |
| MySQL     | 8.4     | 🗄️ Latest LTS version with improved defaults     |
| Caddy     | 2       | 🔒 Automatic HTTPS reverse proxy with Let's Encrypt |
| SFTP      | atmoz   | 📂 SFTP access to wp-content (themes, plugins)   |

## ✅ Prerequisites

- [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/) installed
- 🌐 A domain name pointing to your server's IP address (required for automatic HTTPS)

### 🐧 Linux Users — File Permissions

On macOS, Docker Desktop handles file ownership transparently. On **Linux**, the
MySQL container runs as UID `999` (the `mysql` user inside the container), so the
`db_data-*` directory will be owned by that UID on your host. To avoid permission
issues, export your user's GID before starting the stack:

```bash
# Add to your .bashrc / .zshrc for convenience
export DOCKER_GID=$(id -g)
```

Or prepend it to the start command:

```bash
GID=$(id -g) docker compose -p wp69 --env-file .env.wp-6.9 up -d
```

> **Note:** The `wordpress-69/` directory is written by Apache (UID `33` /
> `www-data`). If you need to edit WordPress files as your host user, run:
> `sudo chown -R $(id -u):$(id -g) wordpress-69/`

## ⚡ Quick Start

1. Clone the repository:

   ```bash
   git clone https://github.com/ronaldpringadi/docker-compose-wordpress-mysql-caddy.git
   cd docker-compose-wordpress-mysql-caddy
   ```

2. Copy the example environment file and configure it:

   ```bash
   cp .env.example .env.wp-6.9
   ```

3. ✏️ Edit `.env.wp-6.9` with your values:

   ```bash
   DOMAIN=example.com
   MYSQL_ROOT_PASSWORD=change-me-root
   MYSQL_DATABASE=wordpress
   MYSQL_USER=wordpress
   MYSQL_PASSWORD=change-me
   SFTP_USER=wpadmin
   SFTP_PASSWORD=change-me-sftp
   ```

4. 🏁 Start the stack:

   ```bash
   docker compose -p wp69 --env-file .env.wp-6.9 up -d
   ```

   > **Why `--env-file`?** The `env_file:` directive in `docker-compose.yml` only
   > injects variables into containers at runtime. It does **not** make them
   > available for `${VAR}` substitution in the compose file itself. The `--env-file`
   > CLI flag is required for that. See
   > [Docker docs — Variable interpolation](https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/).

   > **💡 Tip:** Use `docker compose config` to verify that all variables are
   > resolved correctly before starting the stack:
   >
   > ```bash
   > # Should show fully resolved values (no ${VAR} placeholders)
   > docker compose --env-file .env.wp-6.9 config
   >
   > # Without --env-file, you'll see warnings about unset variables
   > docker compose config
   > ```

5. 🎉 Visit `https://your-domain.com` and complete the WordPress installation wizard.

## 🗂️ Project Structure

```
.
├── .env.example        # 🔑 Environment variable template
├── .env.wp-6.9         # 🔑 Actual env file for this branch (gitignored)
├── Caddyfile           # 🌐 Caddy reverse proxy configuration
├── docker-compose.yml  # 🐳 Docker Compose service definitions
├── wordpress-69/       # 📝 WordPress files (created on first run)
├── db_data-69/         # 🗄️ MySQL data (created on first run)
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
| `SFTP_USER`           | 📂 SFTP username                   | wpadmin     |
| `SFTP_PASSWORD`       | 🔐 SFTP password                   | —           |

### 💾 Volumes

| Volume           | Type        | Description                          |
|------------------|-------------|--------------------------------------|
| `./wordpress-69` | Local folder | 📝 WordPress files (`/var/www/html`) |
| `./db_data-69`   | Local folder | 🗄️ MySQL data (`/var/lib/mysql`)     |
| `caddy_data`     | Named volume | 🔒 Caddy TLS certificates and data  |
| `caddy_config`   | Named volume | ⚙️ Caddy configuration               |

## 🛠️ Useful Commands

```bash
# ▶️ Start the stack
docker compose -p wp69 --env-file .env.wp-6.9 up -d

# 📋 View logs
docker compose -p wp69 logs -f

# ⏹️ Stop the stack
docker compose -p wp69 down

# 💥 Stop and remove volumes (destroys all data)
docker compose -p wp69 down -v

# 📂 Connect via SFTP
sftp -P 2222 wpadmin@localhost
```

## 🔀 Running v6.9 and v5.9 side-by-side

Each version lives on its own git branch with separate ports, network, domain,
and env file so the two stacks don't collide.

| | Branch `wordpress-6.9` (v6.9) | Branch `wordpress-5.9` (v5.9) |
|---|---|---|
| Ports | 80 / 443 | 8080 / 8443 |
| SFTP port | 2222 | 2223 |
| Domain | wptesterx.com | wptesterx59.com |
| Env file | `.env.wp-6.9` | `.env.wp-5.9` |
| Network | wp-wordpress-69 | wp-wordpress-59 |

**Step 1** — Add both domains to `/etc/hosts` (each on its own line):
```
127.0.0.1  wptesterx.com
127.0.0.1  wptesterx59.com
```

**Step 2** — Start v6.9 first (uses standard HTTP/HTTPS ports):
```bash
git checkout wordpress-6.9
docker compose -p wp69 --env-file .env.wp-6.9 up -d
```

**Step 3** — Start v5.9 (uses alternate ports):
```bash
git checkout wordpress-5.9
docker compose -p wp59 --env-file .env.wp-5.9 up -d
```

**Access:**
- v6.9 → `https://wptesterx.com`
- v5.9 → `https://wptesterx59.com:8443`

**Tear down:**
```bash
docker compose -p wp69 down
docker compose -p wp59 down
```

## 📎 Appendix

### Trusting Caddy's Local CA (remove browser certificate warning)

When using a local domain (e.g. via `/etc/hosts`), the Caddyfile uses `tls internal` which generates a self-signed certificate. Browsers will show a warning because the cert isn't signed by a public CA.

To permanently suppress the warning, trust Caddy's local root CA on your machine:

**macOS:**
```bash
# Extract Caddy's root CA cert
docker compose -p wp69 cp caddy:/data/caddy/pki/authorities/local/root.crt ./caddy_local_ca.crt

# Trust it system-wide
sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain ./caddy_local_ca.crt
```

Restart your browser after running the commands. The warning will not appear again for any site using `tls internal` on this machine.

> This is only needed for local development. On a real server with a public domain, remove `tls internal` from the Caddyfile and Caddy will obtain a trusted certificate from Let's Encrypt automatically.

## 📄 License

This project is licensed under the [MIT License](LICENSE).
