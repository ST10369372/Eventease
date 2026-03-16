# Eventease
event management web app built with ASP.NET Core MVC, deployed on Azure App Service Supports public customers, booking specialists, and admins using role-based authentication via ASP.NET Core Identity Includes full CRUD function 

# EventEase — Venue Booking System

> A venue booking and event management platform built with ASP.NET Core MVC (.NET 8), deployed to Microsoft Azure.

🌐 **Live Demo:** (https://STxxxxx.azurewebsites.net](https://easyeasephathu-gdcrgqbrauh5hrd3.southafricanorth-01.azurewebsites.net/Venues))


---

## What It Does

EventEase is a web-based booking platform that allows event coordinators to manage venues, events, and client bookings from a single dashboard. It was built to replace a manual process that was causing double bookings, last-minute cancellations, and a lack of visibility into venue availability.

- **Customers** can browse upcoming events on the public landing page — no login required
- **Booking Specialists** can log in to browse available venues, view events, and create or manage bookings on behalf of clients
- **Admins** have full control — creating, editing, and deleting venues, events, and bookings

---

## How It Works

```
Customer (public)
    └── Views upcoming events on the landing page

Booking Specialist (login required)
    ├── Browses available venues
    ├── Views event details
    ├── Creates bookings for clients
    └── Edits or updates existing bookings

Admin (login required)
    ├── All specialist permissions +
    ├── Creates / edits / deletes venues
    ├── Creates / edits / deletes events
    └── Deletes bookings
```

**Double-booking protection** — when a specialist creates a booking, the system checks whether the selected venue already has an overlapping event. If a conflict is detected, the booking is blocked and an error is shown.

**Deletion protection** — venues and events that have existing bookings attached cannot be deleted. The admin must remove or cancel the associated bookings first.

**Pre-assignment support** — an event can be created without a venue assigned (shown as "TBC"), allowing event coordinators to load events into the system before a venue becomes available.

All data is stored in **Azure SQL Database** with automatic backups and referential integrity enforced at the database level.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | ASP.NET Core MVC (.NET 8) |
| ORM | Entity Framework Core 8 |
| Authentication | ASP.NET Core Identity |
| Database | Azure SQL Database |
| Hosting | Azure App Service |
| Frontend | Razor Views, CSS, JavaScript |

---

## Running the Project Locally

### Prerequisites
- [Visual Studio 2022](https://visualstudio.microsoft.com/) (with ASP.NET and web development workload)
- [.NET 8 SDK](https://dotnet.microsoft.com/download)
- SQL Server LocalDB *(included with Visual Studio)*

### Steps

**1. Clone the repository**
```bash
git clone (https://github.com/ST10369372/Eventease.git)
cd EventEase
```

**2. Open in Visual Studio**

Open `EventEase.csproj` in Visual Studio 2022.

**3. Restore NuGet packages**

Visual Studio restores packages automatically. If not, run in the Package Manager Console:
```powershell
Update-Package -reinstall
```

**4. Apply database migrations**

In **Tools → NuGet Package Manager → Package Manager Console**:
```powershell
Add-Migration InitialCreate
Update-Database
```

This creates the local database and seeds it with sample venues, events, bookings, and user accounts.

**5. Run the application**

Press **F5** or click the green ▶ button. The app opens at `(https://easyeasephathu-gdcrgqbrauh5hrd3.southafricanorth-01.azurewebsites.net/Venues)`.

---

## Login Credentials

| Role | Email | Password |
|---|---|---|
| Admin | `Admin@EventEase.com` | `EventEase@123` |
| Booking Specialist | `bookingspecialists@EventEase.com` | `BookingS@123` |

The public landing page is accessible without logging in.

---

## Project Structure

```
EventEase/
├── Controllers/        ← HomeController, AccountController, VenueController,
│                         EventController, BookingController
├── Models/             ← Venue.cs, Event.cs, Booking.cs
├── Data/               ← ApplicationDbContext.cs (EF Core + Identity + seeding)
├── Views/              ← Razor views for all controllers
│   ├── Shared/         ← _Layout.cshtml (shared navigation & footer)
│   ├── Home/           ← Public landing page
│   ├── Venue/          ← CRUD views
│   ├── Event/          ← CRUD views
│   ├── Booking/        ← CRUD views
│   └── Account/        ← Login, AccessDenied
├── wwwroot/css/        ← site.css (luxury dark theme)
├── appsettings.json    ← Local connection string
└── Program.cs          ← App startup, DI configuration
```

---

## Database Schema

```
Venues          Events              Bookings
────────        ────────────        ─────────────────
VenueId (PK)    EventId (PK)        BookingId (PK)
VenueName       EventName           BookingReference (UQ)
Location        EventDate           BookingDate
Capacity        EndDate             EventId (FK → Events)
ImageUrl        Description         VenueId (FK → Venues)
Description     ImageUrl            ClientName
                VenueId (FK)        ClientEmail
                                    Status (int)
                                    
```

**Relationships:**
- One venue → many events (`SET NULL` on venue delete)
- One event → many bookings (`RESTRICT` — cannot delete event with bookings)
- One venue → many bookings (`RESTRICT` — cannot delete venue with bookings)

---

## Deploying to Azure

1. **Azure Web App** — right-click the project in Visual Studio → Publish → Azure → Azure App Service (Windows)
2. **Azure SQL Database** — create a SQL Database in the Azure Portal, copy the connection string into App Service → Configuration → Connection strings as `DefaultConnection`
3. Run `Update-Database` pointed at the Azure SQL connection string to apply migrations

Live URL format: (https://easyeasephathu-gdcrgqbrauh5hrd3.southafricanorth-01.azurewebsites.net/Venues)
