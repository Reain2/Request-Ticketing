# Getting Started with FacilityTicketing

This guide covers everything you need to get the project running locally from scratch, including how to configure it for your own environment.

---

## Requirements

Make sure the following are installed on your machine before proceeding.

| Requirement | Minimum Version | Check Command |
|---|---|---|
| PHP | 8.2 | `php -v` |
| Composer | 2.x | `composer -V` |
| Node.js | 18.x | `node -v` |
| npm | 9.x | `npm -v` |
| MySQL | 8.0 | `mysql --version` |
| Git | any | `git --version` |

---

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/your-username/FacilityTicketing.git
cd FacilityTicketing
```

### 2. Install PHP dependencies

```bash
composer install
```

### 3. Install Node dependencies

```bash
npm install
```

### 4. Copy the environment file

```bash
cp .env.example .env
```

### 5. Generate the application key

```bash
php artisan key:generate
```

---

## Database Setup

### 1. Create the database

Log into MySQL and create a new database:

```sql
CREATE DATABASE facility_ticketing;
```

Or via the command line:

```bash
mysql -u root -p -e "CREATE DATABASE facility_ticketing;"
```

### 2. Update your .env file

Open `.env` and fill in your database credentials:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=facility_ticketing
DB_USERNAME=root
DB_PASSWORD=your_password_here
```

Replace `root` and `your_password_here` with your actual MySQL username and password.

### 3. Run the migrations

```bash
php artisan migrate
```

### 4. Seed the database

```bash
php artisan db:seed
```

This creates the following accounts automatically:

| Role | Email | Password |
|---|---|---|
| Admin | admin@example.com | password |
| User | (3 users via factory) | password |

To see the generated user emails, run:

```bash
php artisan tinker
> App\Models\User::where('is_admin', false)->pluck('email')
```

---

## File Storage Setup

Run the storage symlink command so uploaded images are publicly accessible:

```bash
php artisan storage:link
```

This links `storage/app/public` to `public/storage`. Without this step, ticket photos will not display.

---

## Building Assets

For production:

```bash
npm run build
```

For local development with hot reload:

```bash
npm run dev
```

Keep the `npm run dev` process running in a separate terminal while developing.

---

## Running the Application

```bash
php artisan serve
```

Visit `http://localhost:8000` in your browser.

Log in with:
- Admin: `admin@example.com` / `password`
- User: any seeded email / `password`

---

## Configuration Reference

These are the key values in `.env` you may need to change:

```env
# Application
APP_NAME=FacilityTicketing        # Name shown in the browser tab
APP_URL=http://localhost:8000     # Change if running on a different port or domain

# Database
DB_DATABASE=facility_ticketing    # Your database name
DB_USERNAME=root                  # Your MySQL username
DB_PASSWORD=                      # Your MySQL password

# Filesystem
FILESYSTEM_DISK=public            # Leave as "public" for local storage

# Mail (optional — currently set to log driver)
MAIL_MAILER=log                   # Change to smtp if you want real emails
```

---

## Customization

### Changing the admin account

The default admin is created in `database/seeders/DatabaseSeeder.php`. Edit this before seeding:

```php
User::create([
    'name'     => 'Your Name',
    'email'    => 'your@email.com',
    'password' => Hash::make('your_secure_password'),
    'is_admin' => true,
]);
```

Then re-run the seeder:

```bash
php artisan migrate:fresh --seed
```

Note: `migrate:fresh` drops and recreates all tables. Do not run this in production.

### Promoting an existing user to admin

Using Tinker:

```bash
php artisan tinker
> App\Models\User::where('email', 'user@example.com')->update(['is_admin' => true])
```

### Changing file upload limits

Open `app/Http/Controllers/TicketController.php` and find the validation rules:

```php
'photos'   => ['nullable', 'array', 'max:5'],      // max 5 files
'photos.*' => ['image', 'mimes:jpg,jpeg,png', 'max:5120'],  // 5MB per file
```

Change `max:5` to allow more files, or `max:5120` (in kilobytes) to allow larger files. Apply the same change in `AdminTicketController.php` for fix images.

### Changing ticket statuses

The allowed statuses are defined as a constant in `app/Models/Ticket.php`:

```php
public const STATUSES = [
    'open'        => 'Open',
    'in_progress' => 'In Progress',
    'resolved'    => 'Resolved',
    'closed'      => 'Closed',
];
```

If you add a new status, also update the `enum` in the tickets migration and re-run migrations. The status badge colors are defined in `STATUS_COLORS` in the same file.

### Changing the app name and branding

Update `APP_NAME` in `.env` and change the wordmark in `resources/views/layouts/app.blade.php`:

```blade
Facility<span style="color: var(--accent);">Ticketing</span>
```

---

## Resetting Everything

To wipe the database and start fresh with seed data:

```bash
php artisan migrate:fresh --seed
php artisan storage:link
```

---

## Common Issues

**Images not showing after upload**
Run `php artisan storage:link`. If the symlink already exists, delete `public/storage` and run it again.

**Class not found errors after pulling changes**
```bash
composer dump-autoload
php artisan optimize:clear
```

**Vite assets not loading**
Make sure `npm run dev` is running, or run `npm run build` for compiled assets.

**Database connection refused**
Confirm MySQL is running and your `.env` credentials are correct. On some systems the host needs to be `127.0.0.1` rather than `localhost`.

**Permission denied on storage**
```bash
chmod -R 775 storage bootstrap/cache
```

---

## Tech Stack Summary

- **Laravel 12** — PHP framework
- **Laravel Breeze** — authentication scaffolding
- **Tailwind CSS** — utility-first CSS framework
- **Vite** — asset bundling
- **MySQL** — relational database
- **barryvdh/laravel-dompdf** — PDF generation
