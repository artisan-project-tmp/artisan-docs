# BricoLink Service Booking Platform - System Documentation

## 1. Project Overview
BricoLink is a modern, authentic platform connecting local artisans and master craftsmen with clients. The system features a robust negotiation engine, real-time communication, and a professional dual-approval booking pipeline.

### Architecture Summary
- **Backend & API**: Laravel 11 (PHP 8.2) handles core business logic and state management.
- **Authentication**: Powered by **Laravel Breeze (Blade Stack)**, customized with our unique "Guild & Gilded" aesthetic.
- **Frontend**: Blade templating integrated with **Tailwind CSS** and Vite for a premium, responsive experience.
- **Database**: **PostgreSQL** is utilized for both development and production, ensuring high performance and seamless deployment on Render.
- **Deployment**: Fully containerized using **Docker**, optimized for cloud environments like Render.

## 2. Development Report (Latest Sprint)
The recent development phase successfully transitioned the platform from a prototype to a production-ready system.

### 2.1 Database & Deployment Transition
- **SQLite to PostgreSQL**: Migrated the entire database schema to PostgreSQL to support production-level concurrency and reliability.
- **Dockerization**: Implemented a `Dockerfile` to handle the environment setup, asset compilation (NPM), and dependency management (Composer) in a single containerized unit.
- **Render Integration**: Configured the application to use dynamic `DATABASE_URL` and `$PORT` environment variables for zero-config deployment.

### 2.2 Advanced Booking & Negotiation Engine
The booking pipeline has been enhanced into a "Deal Room" architecture:
1. **Rich Inquiry**: Clients submit detailed requests including a project title, description, budget range, location, and up to 3 reference images.
2. **Deal Room**: A dedicated space for each booking where clients and artisans can negotiate terms.
3. **Real-Time Chat**: Integrated a messaging system within the Deal Room for direct, transparent communication.
4. **Final Terms**: Artisans propose structured "Final Terms" (Price + Scope), which the client must approve to confirm the booking.

### 2.3 Dashboard & UI Enhancements
- **Status-Based Organization**: Dashboards now categorize jobs into Pending, In Discussion, Booked, and Archived for better workflow management.
- **Profile Customization**: Users can now upload profile pictures (limited to 1MB) and artisans can manage a portfolio of up to 5 high-quality images.
- **Availability Toggle**: Artisans can toggle their "Available For Hire" status, which controls their visibility in the Discovery Feed.

### 2.4 Authentication & Security
- **Breeze Integration**: Integrated Laravel Breeze for secure login, registration, and password management.
- **Custom Registration**: Extended the registration flow to handle dual roles (Client/Artisan) and custom profile initialization.

## 3. Local Environment Setup Guide

### Prerequisites
- Docker (Recommended) OR PHP 8.2+, Composer, Node.js, and PostgreSQL.

### Installation Steps (Local Development)

1. **Repository Access**
   ```bash
   git clone <repository-url>
   cd bricolink-laravel
   ```

2. **Environment Configuration**
   ```bash
   cp .env.example .env
   ```
   Update the `.env` with your local PostgreSQL credentials:
   ```env
   DB_CONNECTION=pgsql
   DB_HOST=127.0.0.1
   DB_PORT=5432
   DB_DATABASE=artisan_app
   DB_USERNAME=your_username
   DB_PASSWORD=your_password
   ```

3. **Dependency Installation**
   ```bash
   composer install
   npm install && npm run build
   ```

4. **Database Initialization**
   ```bash
   php artisan key:generate
   php artisan migrate --seed
   php artisan storage:link
   ```
   *(The seeder populates essential categories like Plomberie, Électricité, etc.)*

5. **Application Execution**
   ```bash
   php artisan serve
   ```

## 4. Production Deployment (Render)
To deploy this application on Render:
1. Create a new **Web Service** on Render.
2. Select the **Docker** runtime.
3. Add the following Environment Variables:
   - `APP_KEY`: Your generated Laravel key.
   - `DATABASE_URL`: Your PostgreSQL connection string.
   - `APP_ENV`: `production`
   - `APP_DEBUG`: `false`
4. Render will use the root `Dockerfile` to build and serve the application.

## 5. Git Collaboration Workflow
1. The `main` branch is protected. 
2. Feature development occurs on dedicated branches.
3. Merges are handled via reviewed Pull Requests to ensure code quality and architectural integrity.
