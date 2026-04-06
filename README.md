# BricoLink Service Booking Platform - System Documentation

## 1. Project Overview
This repository contains the documentation and source code for the BricoLink Service Booking Platform. The platform serves as a centralized hub connecting local artisans and repair professionals with clients. It is built as a monolithic web application utilizing a RESTful architecture, preparing for future expansion into mobile clients.

### Architecture Summary
- **Backend & API**: Laravel (PHP) handles the core business logic, routing, and access control. 
- **Frontend**: Blade templating engine combined with custom CSS provides the user interface. We consciously avoided heavy CSS frameworks for the initial phase to maintain granular control over the design and ensure a deep understanding of the styling architecture.
- **Database**: Currently utilizing SQLite for rapid prototyping and local development convenience. The production target is MySQL. Eloquent Models and Schema Migrations are used to ensure a smooth transition between database drivers.

## 2. Development Report (Latest Sprint)
During the last two days of development, the team focused on establishing the core infrastructure and implementing the primary booking pipeline. Below is a detailed technical summary of the completed features.

### 2.1 Core Infrastructure Setup
We successfully scaffolded the Laravel application and established the Model-View-Controller (MVC) structure. Routing is configured for web functionality, and the basic directory structure is populated.

### 2.2 Database Configuration Strategy
For this phase of development, we implemented **SQLite**. 
- **Rationale**: SQLite is file-based and requires no separate server processes. This allows all team members to pull the repository and immediately run the project without spending time diagnosing local database server conflicts. 
- **Future Migration**: Since we have strictly adhered to using Laravel's database migrations and Eloquent ORM rather than raw SQL queries, our codebase is largely database-agnostic. We plan to transition to **MySQL** in a future sprint to support better concurrent database connections and overall production readiness. 

### 2.3 The Dual-Approval Booking Pipeline
A significant portion of the sprint was dedicated to the booking negotiation system. This logic was designed to protect both the client and the artisan through a mutual agreement protocol:
1. **Initiation**: Clients browse artisan profiles and submit a service request, detailing the problem, location, and desired date.
2. **State Management**: The request defaults to a `pending` state. Anti-spam logic prevents a client from submitting duplicate requests to the same artisan.
3. **Artisan Review & Bidding**: The artisan views the request on their dashboard. They have the option to reject it or accept it. If accepting, the artisan must define the "Final Terms", which explicitly sets the agreed price and any conditional requirements for the job.
4. **Client Confirmation**: The client reviews the final terms proposed by the artisan. The booking is only confirmed and moved to an active state if the client explicitly approves these terms.

### 2.4 Artisan Dashboard & Job Management
We implemented a status-based management interface for artisans to track their workload:
- **Pending Requests**: Inbound quotes requiring attention.
- **Active Jobs**: Bookings that have been mutually agreed upon and are currently in progress.
- **Completed**: Historical record of finalized work.
This acts similarly to a Kanban board, organizing tasks purely by their database status column, specifically in the `artisan.dashboard` view.

### 2.5 Portfolios and Rating System
- **Portfolio Images**: Created a mechanism for artisans to upload local files representing their previous work. These images are linked to the artisan's profile via the `PortfolioImage` model and stored in the local storage directory.
- **Post-Completion Ratings**: Implemented a feedback loop where clients can rate artisans out of 5 stars once a job is marked completed. This data is aggregated to calculate the artisan's overall platform score.

## 3. Local Environment Setup Guide
To ensure consistency across the team, follow these instructions to configure your local development environment.

### Prerequisites
- PHP 8.1 or higher
- Composer
- Node.js and NPM
- Git

### Installation Steps

1. **Repository Access**
   Clone the repository to your local workspace and navigate into the backend directory.
   ```bash
   git clone <repository-url>
   cd bricolink-laravel
   ```

2. **Dependency Installation**
   Install the necessary PHP and JavaScript packages.
   ```bash
   composer install
   npm install
   ```

3. **Environment Configuration**
   Copy the example environment file.
   ```bash
   cp .env.example .env
   ```
   Open the `.env` file and modify the database variables to use SQLite. Ensure the configuration looks exactly like this:
   ```env
   APP_NAME=BricoLinkPlatform
   DB_CONNECTION=sqlite
   ```
   *(Important: Remove or comment out DB_HOST, DB_PORT, DB_DATABASE, DB_USERNAME, and DB_PASSWORD)*

4. **Database Initialization**
   Create the physical SQLite file and run the migrations to construct the schema based on our Laravel migration files.
   ```bash
   touch database/database.sqlite
   php artisan migrate
   ```
   *(Windows CMD users: use `echo > database\database.sqlite` instead of `touch`)*

5. **Storage Linkage**
   To make user-uploaded portfolio images securely accessible from the web directory, create a symbolic link:
   ```bash
   php artisan storage:link
   ```
6. **Generate the Application Key**
   Once your .env file is in place, you need to tell Laravel to generate a unique random key and automatically insert it into the file.
   ```bash
   php artisan key:generate
   ```
   *You should see a message saying "Application key set successfully."*
8. **Application Execution**
   You must run two separate server instances concurrently to handle the backend processing and frontend asset serving.
   
   **Terminal 1 (PHP Development Server):**
   ```bash
   php artisan serve
   ```
   
   **Terminal 2 (Frontend Asset Compilation):**
   ```bash
   npm run dev
   ```
   
   Access the application at `http://localhost:8000`.

## 4. Git Collaboration Workflow
To prevent integration conflicts, the team must adhere to the following version control protocols:
1. The `main` branch is protected. Direct commits are strictly prohibited.
2. Development must occur on distinct feature branches (e.g., `feature/booking-system`, `fix/login-routing`).
3. Code is merged into `main` exclusively via Pull Requests, which require at least one peer review before integration.
