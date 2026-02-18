# FacilityTicketing

A web-based facility maintenance ticketing system built for organizations that need a structured, accountable way to report and resolve physical infrastructure issues — broken equipment, faulty lighting, plumbing problems, and anything in between.

---

## What It Does

FacilityTicketing gives staff a simple interface to report maintenance issues and gives the facilities team a centralized place to manage, prioritize, and close those reports. Every ticket has a clear owner, a traceable status history, and an optional photo record on both ends — from the initial report to the proof of resolution.

The system operates on two distinct roles. Regular users submit tickets describing the problem, attach photos if needed, and can follow the progress of their own requests. Administrators have full visibility over all tickets across the organization, can update statuses, communicate through comments, upload proof-of-fix photos, and export report data for record-keeping or review meetings.

---

## Core Features

**For reporters**
- Submit a ticket with a title, detailed description, and up to five photos
- Track the real-time status of every ticket they have submitted
- Read admin comments and resolution notes on their tickets
- View proof-of-fix photos uploaded by the facilities team

**For administrators**
- View and search all tickets across all reporters
- Filter by status or keyword to prioritize the queue
- Update ticket status across four stages: open, in progress, resolved, and closed
- Upload fix proof images and write resolution notes
- Add comments to communicate directly on a ticket
- Close tickets with an automatic timestamp for audit purposes
- Generate reports filtered by date range and status
- Export data as CSV for spreadsheet analysis or PDF for formal documentation

---

## How It Works

When a user submits a ticket, the system records their identity, timestamps the submission, sets the initial status to open, and stores any uploaded images on the server's local disk. The ticket immediately appears in the admin queue.

An admin works the ticket through its lifecycle — moving it to in progress when work begins, to resolved when the fix is complete, and finally closing it when the case is fully concluded. Each status change is intentional and manual, giving the facilities team control over the workflow without rigid automation.

Authorization is enforced at two levels. The middleware layer blocks non-administrators from accessing admin routes entirely. The policy layer ensures that even within the user-facing routes, a reporter can only view their own tickets — attempting to access another user's ticket results in a 403 response.

---

## Technical Stack

- **Framework**: Laravel 12 on PHP 8.2
- **Frontend**: Blade templates with Tailwind CSS, served via Vite
- **Authentication**: Laravel Breeze (email and password)
- **Database**: MySQL with Eloquent ORM
- **File storage**: Laravel's public disk with a symbolic link
- **PDF export**: barryvdh/laravel-dompdf
- **Authorization**: Laravel Policies and custom middleware

---

## Data Model

The system is built around five database tables. The `users` table carries an `is_admin` boolean that determines role. The `tickets` table is the core entity, holding the status, description, resolution note, and closed timestamp. Two separate image tables — `ticket_images` and `fix_images` — keep reporter-submitted evidence and admin-submitted proof cleanly separated. A `comments` table allows threaded notes attached to any ticket, authored by either role.

All child records use cascading deletes, so removing a ticket automatically cleans up its images and comments.

---

## Intended Use

FacilityTicketing is designed for small to medium organizations — office buildings, schools, hotels, hospitals, or any environment where a team is responsible for maintaining physical spaces and needs a lightweight system that is faster than email threads and simpler than a full ITSM platform.

---

## Project Type

Full-stack web application — solo developer project demonstrating role-based access control, file upload handling, resource management, report generation, and clean MVC architecture in a real-world Laravel context.
