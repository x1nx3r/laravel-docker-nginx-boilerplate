# Laravel Docker Nginx Boilerplate

A Docker setup for Laravel because apparently running PHP directly on a server like it's 2010 is too simple for modern web development. This isn't a "cloud-native enterprise-grade solution" - it's a boilerplate for shoving your Laravel app into containers and yeeting it onto whatever $3/month VPS you found advertised on a sketchy forum.

Perfect for when you need to get something online quickly without pretending you understand Kubernetes. Drop your Laravel code in the `app/` folder, run `docker-compose up`, pray to whatever deity handles container orchestration, and you've got a working web application. Deploy it to DigitalOcean, Linode, or that "high-performance cloud hosting" provider you've never heard of but offers suspiciously cheap prices.

## What This Actually Is (Spoiler: It's Simple)

This is a straightforward Docker setup that runs Laravel with Nginx and MariaDB because apparently we need three separate processes to serve a web page now. No fancy orchestration, no enterprise patterns designed by people who've never deployed actual applications, no microservices architecture that turns your monolith into a distributed monolith with network latency.

Just three containers that hopefully talk to each other and serve your web application to the seven people who will actually use it.

It includes:
- **PHP Application Container**: Runs your Laravel code with PHP-FPM because mod_php is apparently dead
- **Nginx Web Server**: Serves static files and proxies PHP requests, with ModSecurity because security through obscurity feels better than no security
- **MariaDB Database**: Stores your data and hopefully doesn't corrupt it when you inevitably run out of disk space

## Repository Structure (Shocking in Its Simplicity)

```
laravel-docker-nginx-boilerplate/
├── app/                     # Drop your Laravel application here (revolutionary)
├── docker/
│   ├── nginx/
│   │   └── custom-laravel.conf  # Nginx config that took 6 hours to debug
│   └── php/                 # PHP container with extensions Laravel actually needs
├── docker-compose.yml       # 50 lines that replace 3 weeks of server configuration
└── README.md                # This manifesto of modern web development
```

## How to Use This Thing (If You Dare)

### Step 1: Acquire a Laravel Application
Put your existing Laravel application in the `app/` directory. If you don't have one yet, create another todo app because the world definitely needs more of those:

```bash
git clone https://github.com/yourusername/laravel-docker-nginx-boilerplate.git
cd laravel-docker-nginx-boilerplate
docker-compose run --rm app composer create-project laravel/laravel .
# watch Composer download 400MB of dependencies for a framework that serves HTML
```

### Step 2: Configure Your Environment (The Fun Part)
Copy your Laravel `.env` file into the `app/` directory and update the database settings to point at your containerized database because localhost is too mainstream:

```env
DB_CONNECTION=mysql
DB_HOST=mariadb          # not localhost, because containers
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=laravel
DB_PASSWORD=laravelpassword  # definitely change this before deployment
```

### Step 3: Start Everything and Cross Your Fingers
```bash
docker-compose up -d
# watch Docker download 2GB of base images
# contemplate the entropy of modern software development
# question whether serving a website should require this much infrastructure
```

### Step 4: The Laravel Permission Dance
```bash
docker-compose exec app chown -R www-data:www-data storage bootstrap/cache
docker-compose exec app chmod -R 775 storage bootstrap/cache
# the ritual that every Laravel developer knows by heart
# because file permissions are apparently still unsolved in 2024
```

### Step 5: Run Your Migrations (Pray for Success)
```bash
docker-compose exec app php artisan migrate
# watch Eloquent create tables with names like "password_reset_tokens"
# wonder why every Laravel app needs the same 5 migration files
```

### Step 6: Marvel at Your Achievement
Open http://localhost:8000 and behold your containerized Laravel application displaying the default welcome page you forgot to replace.

## Deployment (The Reason This Exists)

This setup is designed to be deployed anywhere that can run Docker, which is everywhere except the server that would actually be perfect for your use case.

### On Your Bargain-Basement VPS
```bash
ssh root@your-sketchy-vps-provider.net
git clone https://github.com/yourusername/your-laravel-masterpiece.git
cd your-laravel-masterpiece
docker-compose up -d
# cross fingers that the VPS has enough RAM to run three containers
```

That's it. Your application is now running on the internet, accessible to anyone who can guess your domain name.

### For Production Deployment (Bold Assumptions)
Change these things before unleashing this on real users:

1. **Database Passwords**: Update the MariaDB passwords in `docker-compose.yml` to something more creative than "laravelpassword"
2. **Laravel Environment**: Set `APP_ENV=production` and `APP_DEBUG=false` unless you want to leak your database credentials to every 500 error
3. **Application Key**: Generate a new key with `php artisan key:generate` because sharing encryption keys is caring
4. **Port Mapping**: Change port 8000 to 80 if you want people to access your site without typing port numbers like it's 1995

```yaml
# In docker-compose.yml, change this amateur hour setup:
ports:
  - "8000:80"
# To this professional configuration:
ports:
  - "80:80"
```

## Configuration Details (The Boring But Important Stuff)

### Database Settings (Creative Naming Conventions)
The MariaDB container uses these imaginative default credentials:
- **Database**: laravel (groundbreaking)
- **Username**: laravel (see above)
- **Password**: laravelpassword (security through predictability)
- **Root Password**: rootpassword (because creativity is hard)

Change these in `docker-compose.yml` before deployment unless you enjoy unauthorized database access.

### Ports (Probably Conflicts with Something)
- **Web Application**: Port 8000 (guaranteed to conflict with your development server)
- **Database**: Port 3330 (for when you need to peek at your data with a real database tool)

### Persistence (Until You Run `docker system prune`)
Database data is stored in a Docker volume named `mariadb_data` that persists between container restarts. Your data won't disappear unless you run `docker system prune` without thinking, which you will definitely do at some point.

## Security Considerations (Theater and Reality)

This setup includes OWASP ModSecurity which provides the illusion of protection by blocking legitimate requests that look suspicious while letting actual attacks through if they're properly formatted.

For production (if you're brave enough):
- Change all default passwords to different default passwords
- Use environment variables for sensitive configuration (then commit them to git anyway)
- Set up proper SSL/TLS termination with Let's Encrypt or pay for a certificate like it's 2010
- Keep your containers updated (manually, like a caveman)

## Common Commands (Your New Daily Routine)

Run Laravel commands inside the container because running PHP locally is apparently forbidden:

```bash
# Artisan commands that should be simple but aren't
docker-compose exec app php artisan migrate
docker-compose exec app php artisan cache:clear
docker-compose exec app php artisan config:cache
docker-compose exec app php artisan route:cache
docker-compose exec app php artisan view:cache
# cache everything, then clear everything, repeat until it works

# Composer commands for managing your 400MB vendor directory
docker-compose exec app composer install
docker-compose exec app composer update
docker-compose exec app composer dump-autoload

# Access container shell for debugging the inevitable problems
docker-compose exec app bash
```

## Troubleshooting (When Reality Sets In)

**Application shows "500 Internal Server Error":**
- Check Laravel logs: `docker-compose logs app` and wade through the symfony/error-handler stack traces
- Verify file permissions on storage directories (it's always file permissions)
- Make sure your `.env` file exists and has the correct database settings
- Clear all caches and pray: `docker-compose exec app php artisan optimize:clear`

**Database connection errors:**
- Wait 30 seconds for MariaDB to finish starting up (containers are slow)
- Check that your `.env` uses `mariadb` as the database host, not `localhost` like a reasonable person would expect
- Verify database credentials match between `.env` and `docker-compose.yml`
- Restart everything and hope the problem goes away

**Port conflicts (guaranteed to happen):**
- Change the port mapping in `docker-compose.yml` when port 8000 conflicts with your React development server
- Use `netstat -tulpn | grep :8000` to find what's stealing your port
- Pick a random port between 8001 and 9999 and hope for the best

**File permission issues (the Laravel special):**
- Run the chown/chmod commands again because file permissions are quantum and change when observed
- Make sure the web server can write to storage and cache directories
- Question why serving web pages requires this much permission management

## Limitations (Painful Truths)

This setup is designed for simplicity, not for impressing your DevOps team:

- Single server deployment only (clustering is for enterprises with budgets)
- No load balancing or high availability (your VPS either works or it doesn't)
- Basic security configuration (better than nothing, worse than everything)
- No automated backups (remember to dump your database manually)
- No monitoring or alerting (you'll find out it's broken when users complain)
- No CI/CD integration (deploy by SSH like your ancestors)
- No horizontal scaling (buy a bigger VPS when traffic increases)

If you need any of those enterprise features, you probably shouldn't be using a $5/month VPS and a Docker Compose file you found on GitHub.

## When to Use This (Honest Assessment)

This boilerplate is perfect for:
- Personal projects that three people will use
- Client websites that expect dozens of visitors per day
- Prototype applications you want to demo without explaining why it doesn't work
- Side projects you want online so you can add them to your portfolio
- Learning Docker while pretending to understand container orchestration
- Avoiding the complexity of "proper" deployment while feeling modern

Don't use this for:
- Applications that more than 100 people will use simultaneously
- Anything that handles money, personal data, or other lawsuit-worthy information
- Projects that need to stay online when your VPS provider has "scheduled maintenance"
- Enterprise applications with SLAs, compliance requirements, or adult supervision
- Anything your grandmother depends on

## License (Legal Disclaimer)

MIT License. Use it, modify it, deploy it, break it, curse at it. It becomes your problem the moment you run `docker-compose up`. I'm not responsible when your containerized application crashes and burns, your VPS gets compromised, or your database disappears into the digital void.

## Final Reality Check (Wake Up Call)

This is a straightforward Docker setup for Laravel applications that works well enough to fool people into thinking you know what you're doing. It's deployable, it serves HTTP requests, and it probably won't catch fire on the first day.

It's not fancy because fancy is complicated. It's not enterprise-grade because enterprise-grade costs money. It's not the most secure setup possible because security is hard and this is easy.

But sometimes you just need to get your Laravel app online without spending six months learning Kubernetes, Terraform, and whatever other tools the cool kids are using this week. This setup does that job adequately.

Put your code in the `app/` folder, change the passwords to something slightly less obvious, deploy it to whatever server you can afford, and call yourself a full-stack developer. Your application will be accessible to the internet, your data will persist between restarts (probably), and you can sleep knowing your side project actually exists somewhere other than localhost.

Just remember to backup your database before you break something, keep your containers vaguely updated, and don't blame me when it all goes wrong. You're running PHP in containers on a budget VPS - manage your expectations accordingly.

Welcome to modern web development, where everything is containerized, nothing is simple, and somehow we all convinced ourselves this is progress.
