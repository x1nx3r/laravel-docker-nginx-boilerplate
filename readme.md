# Laravel Docker Nginx Boilerplate

A modern, production-ready boilerplate for Laravel applications with Docker, Nginx with ModSecurity, and MariaDB.

## Overview

This boilerplate provides a complete development environment for Laravel applications using Docker containers. It includes:

- **PHP Application Container**: Configured specifically for Laravel
- **Nginx Web Server**: With OWASP ModSecurity for enhanced security
- **MariaDB Database**: Latest version with persistent storage

## Repository Structure

```
laravel-docker-nginx-boilerplate/
├── app/                     # Laravel application code
├── docker/
│   ├── nginx/
│   │   └── custom-laravel.conf  # Nginx configuration for Laravel
│   └── php/                 # PHP Dockerfile and configs
├── docker-compose.yml       # Docker Compose configuration
└── README.md                # This file
```

## Features

- **Security-focused**: Uses OWASP ModSecurity to protect against common web vulnerabilities
- **Development Ready**: Pre-configured for Laravel development
- **Production Capable**: Structured for easy deployment to production environments
- **Persistent Data**: Database data persists between container restarts
- **Isolated Network**: All services run on an isolated Docker network

## Requirements

- Docker Engine
- Docker Compose
- Git (for cloning the repository)

## Quick Start

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/laravel-docker-nginx-boilerplate.git
   cd laravel-docker-nginx-boilerplate
   ```

2. **Start the environment**
   ```bash
   docker-compose up -d
   ```

3. **Install Laravel (if starting a new project)**
   ```bash
   docker-compose exec app composer create-project laravel/laravel .
   ```

4. **Set permissions**
   ```bash
   docker-compose exec app chown -R www-data:www-data /var/www/html/storage
   docker-compose exec app chmod -R 775 /var/www/html/storage
   ```

5. **Access your application**
   - Web: http://localhost:8000
   - Database: localhost:3330 (Host), laravel (DB), laravel (User), laravelpassword (Password)

## Configuration

### Environment Variables

Database configuration (defined in docker-compose.yml):
- **MYSQL_ROOT_PASSWORD**: rootpassword
- **MYSQL_DATABASE**: laravel
- **MYSQL_USER**: laravel
- **MYSQL_PASSWORD**: laravelpassword

### Ports

- **Web Server**: 8000 (host) → 80 (container)
- **Database**: 3330 (host) → 3306 (container)

### Customizing

- **Nginx Configuration**: Modify `docker/nginx/custom-laravel.conf`
- **PHP Configuration**: Update Dockerfile and configs in `docker/php/`
- **Docker Compose**: Adjust services in `docker-compose.yml`

## Security Considerations

- This boilerplate includes OWASP ModSecurity for web application firewall capabilities
- Change all default passwords before deploying to production
- Consider using Docker secrets for sensitive information in production
- Review and customize ModSecurity rules for your specific application needs

## Development Workflow

1. Place your Laravel application code in the `app/` directory
2. The changes will be immediately reflected due to volume mounting
3. Run Laravel commands using `docker-compose exec app [command]`

## Production Deployment

For production deployment:

1. Modify environment variables for production settings
2. Use Docker Swarm or Kubernetes for orchestration
3. Set up proper TLS/SSL termination
4. Enable database backups
5. Configure proper logging and monitoring

## Contributing

Contributions to improve this boilerplate are welcome. Please feel free to submit a Pull Request.

## License

This boilerplate is open-sourced software licensed under the [MIT license](https://opensource.org/licenses/MIT).
