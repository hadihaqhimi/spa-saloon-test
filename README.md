# Nur Jannah Salon & Spa Muslimah

A MERN booking application based on the supplied HTML sketch. The customer site is in Malay and includes services, packages, account registration, live slot availability, bookings, and booking history. Staff can view appointments and register walk-ins; admins can manage services and accounts and view completed-session sales.

## What is implemented

- React + Vite frontend, Express API, MongoDB with Mongoose.
- Password hashes with bcrypt, HTTP-only session cookie, server-enforced customer/staff/admin roles, origin checks for writes, and login rate limiting.
- Bookings store the service name and price at booking time. Fifteen-minute unique reservations in a MongoDB transaction stop overlapping treatments for the same therapist, including concurrent requests. Appointments are in Malaysia time. Slots: 10:00, 11:30, 14:00, 15:30, 17:00; sessions must finish by 19:00.
- Staff status flow: pending → confirmed → in progress → settled. Customers may cancel pending/confirmed future bookings. Sales count settled sessions only, at their recorded booking price. No payment processing is implied.
- The public landing page is the customer page. Booking and booking history require a customer login; staff and admin dashboards require their own accounts. Public sign-up can create customer accounts only.
- To reduce speculative bookings, a customer can have one active booking per day, at most two pending future requests, at most three new requests per Malaysia calendar day, and at most eight booking attempts per hour. New online bookings remain pending until staff confirm them. These controls limit abuse but do not establish that the submitted phone number belongs to the customer.
- Admin “delete” archives services and deactivates users, preserving historical booking and sales records.

## Run locally

Requires Node.js 20+, npm and a **MongoDB replica set** (MongoDB Atlas also works). Transactions do not work on a standalone MongoDB server.

1. `npm install`
2. Copy `server/.env.example` to `server/.env` and set `MONGODB_URI`, a random `JWT_SECRET` (32+ characters), `ADMIN_EMAIL`, and a unique `ADMIN_PASSWORD` (12+ characters). Use a replica-set connection string.
3. `npm run seed -w server` to create the starter menu and first admin account. This is safe to rerun and does not overwrite existing services or the admin password.
4. `npm run dev` and open `http://localhost:5173`.
5. Log in as admin and add at least one active staff profile before customers can book.

`npm test` runs booking-rule tests; `npm run build` creates the production frontend. Never commit `.env` files or share account credentials in chat.

## Hosting

Deploy this repository as **one Node web service** connected to MongoDB Atlas. The Express server serves the built React site and `/api` on the same domain, so session cookies work without cross-domain setup.

Suggested service settings:

- Root directory: repository root (`nur-jannah-mern` if kept in a larger repository)
- Build command: `npm ci && npm run build`
- Start command: `npm run seed -w server && npm start`
- Environment: `NODE_ENV=production`, `MONGODB_URI`, `JWT_SECRET`, `ADMIN_EMAIL`, `ADMIN_PASSWORD`. The host supplies `PORT`.
- Give the service network access to the Atlas cluster; configure the Atlas database user and network access in your own account. Use HTTPS and keep secrets in the host's environment settings.

The first seed creates the admin and menu. Log in to add staff and confirm salon details before sharing the public URL. A custom domain can be attached after the service is live.

## Before launch

The sketch contains placeholder salon contact details and no confirmed address, business hours or payment policy. The site deliberately does not publish those as facts. Confirm the business name spelling, branch address, operating days, contact/WhatsApp number, cancellation policy and whether deposits or online payments are required. Staff availability is presently the fixed slots above; individual schedules, notifications, deposits, and financial reconciliation can be added in a later phase.

For stronger protection against fake bookings, add phone verification with one-time codes before first booking. If no-shows remain costly, consider a small deposit credited against the treatment, with the amount, refund window and payment provider agreed by the salon first. Do not label a booking “confirmed” until the verification/payment rule and staff workflow are fulfilled.
