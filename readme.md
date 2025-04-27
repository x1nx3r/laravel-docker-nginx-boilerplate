# Laravel Docker Boilerplate

A streamlined Docker environment for Laravel application development with Nginx, PHP, and MariaDB.

## 📋 Overview

This boilerplate provides a ready-to-use Docker environment for Laravel development with:

- Nginx web server
- PHP-FPM application server
- MariaDB database
- Pre-configured networking
- Volume persistence

## 🚀 Quick Start

### Prerequisites

- Docker and Docker Compose installed on your system
- Git (for cloning this repository)

### Setup Instructions

1. **Clone this repository**
   ```bash
   git clone https://github.com/yourusername/laravel-docker-app.git
   cd laravel-docker-app
   ```

2. **Create a new Laravel project in the app directory**
   ```bash
   composer create-project laravel/laravel app
   ```

3. **Configure Laravel's database connection**

   Edit `app/.env` file with these settings:
   ```
   DB_CONNECTION=mysql
   DB_HOST=db
   DB_PORT=3306
   DB_DATABASE=laravel
   DB_USERNAME=laravel
   DB_PASSWORD=laravelpassword
   ```

4. **Start the Docker environment**
   ```bash
   docker-compose up -d
   ```

5. **Access your application**
   - Website: http://localhost:8000
   - Database: localhost:3307 (username: laravel, password: laravelpassword)

## 🏗️ Project Structure

```
laravel-docker-app/
├── app/                    # Laravel application (created during setup)
├── docker/
│   ├── nginx/              # Nginx configuration
│   │   └── default.conf    # Server block configuration
│   └── php/                # PHP configuration and Dockerfile
├── docker-compose.yml      # Docker services configuration
└── README.md               # This file
```

## ⚙️ Configuration Details

### Web Server (Nginx)
- External Port: 8000
- Configuration File: `docker/nginx/default.conf`

### PHP Application
- Built from Dockerfile in `docker/php/`
- Laravel app mounted at `/var/www/html`

### Database (MariaDB)
- External Port: 3307
- Internal Port: 3306
- Root Password: rootpassword
- Database: laravel
- User: laravel
- Password: laravelpassword
- Persistent Storage: Docker volume `dbdata`

## 🧰 Common Tasks

### Running Artisan Commands
```bash
docker-compose exec app php artisan <command>
```

### Accessing the Database CLI
```bash
docker-compose exec db mysql -u laravel -p
```

### Installing PHP Dependencies
```bash
docker-compose exec app composer install

## 🔍 Troubleshooting

### Database Connection Issues
- Ensure DB_HOST is set to `db` in your `.env` file
- Make sure to use port 3306 (internal container port) in your Laravel config
- Check if the database service is running: `docker-compose ps`

### Permission Problems
If you encounter permission issues:
```bash
docker-compose exec app chown -R www-data:www-data /var/www/html/storage
```

### Container Access
To access a container's shell:
```bash
docker-compose exec app bash
docker-compose exec db bash
docker-compose exec webserver sh
```

## 🛠️ Customization

### Adding PHP Extensions
Modify `docker/php/Dockerfile` to install additional PHP extensions.

### Changing Database Settings
Modify the environment variables in the `docker-compose.yml` file.

### Modifying Nginx Configuration
Edit `docker/nginx/default.conf` and rebuild the containers.

## 📄 License

This boilerplate is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).

---

*For contributions, issues, or feature requests, please submit them to the GitHub repository.*
