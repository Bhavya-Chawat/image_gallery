
# Image Gallery - Laravel + Vue + Inertia + Docker Setup

![Laravel](https://img.shields.io/badge/Laravel-11-red?logo=laravel)
![Vue.js](https://img.shields.io/badge/Vue.js-3-green?logo=vue.js)
![Docker](https://img.shields.io/badge/Docker-Compose-blue?logo=docker)
![Inertia.js](https://img.shields.io/badge/Inertia.js-SPA-purple)

## 📋 Project Overview

This project is a modern web application built with:

**Backend Stack:**
- **Laravel 11** - PHP framework
- **PostgreSQL** - Primary database
- **Redis** - Caching, sessions, job queues
- **MinIO** - S3-compatible object storage
- **Mailpit** - Email testing

**Frontend Stack:**
- **Vue 3** - JavaScript framework with Composition API
- **Inertia.js** - Bridges Laravel and Vue (SPA-like experience)
- **Tailwind CSS** - Utility-first CSS framework
- **Vite** - Fast build tool with hot module replacement

**Development Environment:**
- **Docker (Laravel Sail)** - Containerized development
- **WSL2 Optimized** - Enhanced performance on Windows

---

## 🔧 Prerequisites

### For Windows Users:
- [x] **WSL2** installed and configured
- [x] **Docker Desktop** for Windows with WSL2 integration enabled
- [x] **Web browser** (Chrome, Firefox, etc.)

### For Linux/Mac Users:
- [x] **Docker** and **Docker Compose** installed
- [x] **Web browser**

---

## 🚀 Installation Guide

### Step 1: Open Terminal
- **Windows:** Open WSL2 terminal (Ubuntu/Debian)
- **Linux/Mac:** Open terminal

### Step 2: Navigate to Projects Directory
```
cd ~
mkdir -p projects
cd projects
```

### Step 3: Create Laravel Project with Docker Services
```
# Create Laravel project with PostgreSQL, Redis, MinIO, and Mailpit
curl -s "https://laravel.build/image_gallery?with=pgsql,redis,minio,mailpit" | bash
```

> **Note:** This command downloads and sets up a complete Laravel project with all necessary Docker services.

### Step 4: Navigate to Project Directory
```
cd image_gallery
```

### Step 5: Install Laravel Breeze with Vue + Inertia
```
# Install Breeze package
./vendor/bin/sail composer require laravel/breeze --dev

# Install Breeze with Vue + Inertia (includes Tailwind CSS)
./vendor/bin/sail artisan breeze:install vue
```

**When prompted:**
- Choose **"No"** for Inertia SSR (unless specifically needed)
- Choose **"No"** for TypeScript (unless specifically needed)  
- Choose testing framework preference (**PHPUnit recommended**)

### Step 6: Install Frontend Dependencies
```
# Install npm packages (Vue, Inertia, Vite, Tailwind CSS)
./vendor/bin/sail npm install --legacy-peer-deps
```

### Step 7: Configure Environment
```
# Generate application key
./vendor/bin/sail artisan key:generate
```

**Update your `.env` file** with these Docker-optimized settings:

```
APP_NAME="Image Gallery" 
APP_ENV=local
APP_URL=http://localhost

# Database - PostgreSQL Container
DB_CONNECTION=pgsql
DB_HOST=pgsql
DB_PORT=5432
DB_DATABASE=image_gallery
DB_USERNAME=sail
DB_PASSWORD=password

# Cache & Sessions - Redis Container
CACHE_STORE=redis
SESSION_DRIVER=redis
QUEUE_CONNECTION=redis
REDIS_HOST=redis
REDIS_PORT=6379

# File Storage - MinIO Container
FILESYSTEM_DISK=s3
AWS_ACCESS_KEY_ID=sail
AWS_SECRET_ACCESS_KEY=password
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=local
AWS_USE_PATH_STYLE_ENDPOINT=true
AWS_ENDPOINT=http://minio:9000
AWS_URL=http://localhost:9000/local

# Email Testing - Mailpit Container
MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_FROM_ADDRESS="hello@image-gallery.local"
```

### Step 8: Start All Services
```
# Start Docker containers (Laravel, PostgreSQL, Redis, MinIO, Mailpit)
./vendor/bin/sail up -d

# Run database migrations (creates authentication tables)
./vendor/bin/sail artisan migrate

# Build frontend assets
./vendor/bin/sail npm run build

# Start development server with hot reload (run in separate terminal)
./vendor/bin/sail npm run dev
```

---

## ✅ Verification & Testing

### 1. Access the Application
- **Main Application:** http://localhost
- **Authentication:** Try register/login functionality

### 2. Test Services
Create this test route in `routes/web.php`:

```
Route::get('/test-services', function () {
    return response()->json([
        'database' => DB::connection()->getPdo() ? '✅ PostgreSQL Connected' : '❌ Failed',
        'redis' => Redis::connection()->ping() ? '✅ Redis Connected' : '❌ Failed',
        'cache' => Cache::put('test', 'works') ? '✅ Cache Working' : '❌ Failed',
        'storage' => Storage::disk('s3')->exists('') ? '✅ MinIO Working' : '❌ Failed'
    ]);
});
```

Visit: http://localhost/test-services

### 3. Admin Panels
- **MinIO Console:** http://localhost:8900
  - Username: `sail`
  - Password: `password`
- **Email Testing:** http://localhost:8025

---

## 🔄 Development Commands

### Starting the Application
```
# Start all services
./vendor/bin/sail up -d

# Start frontend with hot reload (separate terminal)
./vendor/bin/sail npm run dev
```

### Common Laravel Commands
```
# Run migrations
./vendor/bin/sail artisan migrate

# Create new migration
./vendor/bin/sail artisan make:migration create_posts_table

# Create controller
./vendor/bin/sail artisan make:controller PostController

# Clear cache
./vendor/bin/sail artisan cache:clear
```

### Frontend Commands
```
# Install new npm package
./vendor/bin/sail npm install package-name

# Build for production
./vendor/bin/sail npm run build

# Run development server
./vendor/bin/sail npm run dev
```

### Database Management
```
# Access PostgreSQL
./vendor/bin/sail exec pgsql psql -U sail -d image_gallery

# Run database seeder
./vendor/bin/sail artisan db:seed
```

---

## 📁 Project Structure

```
image_gallery/
├── app/                    # Laravel backend code
│   ├── Http/Controllers/   # Controllers
│   ├── Models/            # Eloquent models
│   └── ...
├── resources/js/
│   ├── Pages/             # Vue components (Inertia pages)
│   │   ├── Auth/          # Login, Register pages
│   │   ├── Dashboard.vue  # Dashboard page
│   │   └── Welcome.vue    # Welcome page
│   ├── Components/        # Reusable Vue components
│   ├── Layouts/          # Vue layouts (AuthenticatedLayout, etc.)
│   └── app.js            # Inertia + Vue setup
├── resources/css/         # Tailwind CSS
├── routes/web.php         # Web routes
├── database/migrations/   # Database migrations
├── docker-compose.yml     # Docker services configuration
├── vite.config.js        # Vite configuration
├── tailwind.config.js    # Tailwind CSS configuration
└── package.json          # Frontend dependencies
```

---

## 🛠️ Technology Stack Details

| Category | Technology | Purpose |
|----------|-----------|---------|
| **Backend** | Laravel 11 | PHP framework |
| **Database** | PostgreSQL | Primary database |
| **Cache** | Redis | Caching, sessions, queues |
| **Storage** | MinIO | S3-compatible object storage |
| **Email** | Mailpit | Email testing |
| **Frontend** | Vue 3 | JavaScript framework |
| **SPA** | Inertia.js | Laravel-Vue bridge |
| **CSS** | Tailwind CSS | Utility-first styling |
| **Build** | Vite | Fast build tool |
| **Auth** | Laravel Breeze | Authentication scaffolding |

### Features:
- ✅ Complete authentication system (register, login, password reset)
- ✅ Responsive design with Tailwind CSS
- ✅ Hot reload during development
- ✅ Optimized for performance
- ✅ Docker containerization
- ✅ Production-ready setup

---

## 🔧 Troubleshooting

### Port Conflicts
```
# Check running containers
./vendor/bin/sail ps

# Stop services
./vendor/bin/sail down

# Start again
./vendor/bin/sail up -d
```

### Frontend Issues
```
# Clear Vite cache
./vendor/bin/sail npm run build

# Reinstall dependencies
rm -rf node_modules package-lock.json
./vendor/bin/sail npm install --legacy-peer-deps
```

### Database Issues
```
# Reset database
./vendor/bin/sail artisan migrate:fresh --seed
```

### Common Error: "Vite manifest not found"
```
# Build assets first
./vendor/bin/sail npm run build
# OR start dev server
./vendor/bin/sail npm run dev
```

---

## ⚡ Performance Notes

- **WSL2 Optimization:** Project is located in WSL2 filesystem (`~/projects/`) for optimal performance
- **Container-based:** All PHP, Node.js, and database operations run in containers
- **Hot Reload:** Vite provides instant feedback during development
- **Production Ready:** Built assets are optimized for production deployment

---

## 🆘 Support

For any issues during setup:

1. **Ensure Docker Desktop is running**
2. **Verify WSL2 integration is enabled**
3. **Check that all ports are available:**
   - `80` - Laravel application
   - `5432` - PostgreSQL
   - `6379` - Redis
   - `9000` - MinIO API
   - `8900` - MinIO Console
   - `1025` - Mailpit SMTP
   - `8025` - Mailpit Web UI
4. **Ensure `./vendor/bin/sail` commands are run from the project directory**

---

## 📝 Additional Notes

> **Important:** This setup provides a complete, modern web development environment with Laravel, Vue.js, and Docker, optimized for both development and production use.

**Development Workflow:**
1. Start containers: `./vendor/bin/sail up -d`
2. Start frontend dev server: `./vendor/bin/sail npm run dev`
3. Make changes to Vue components in `resources/js/Pages/`
4. Changes auto-reload in browser
5. Build for production: `./vendor/bin/sail npm run build`

---

*This installation guide ensures a consistent, reproducible development environment for all team members and judges.*
```
