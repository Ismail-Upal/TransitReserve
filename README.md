Project Summary: TransitReserve – Multi-Modal Transport Reservation & Billing Platform

1. Core Purpose
We are building a professional, production-grade reservation system that handles booking, real-time seat selection, live vehicle tracking, secure payments, and billing across all major fixed-route transit modes—buses, BRT, trolleybuses, light rail, metro, commuter rail, monorail, automated guideway transit, ferries, and later, planes. The system is also architected to eventually support demand-responsive transit (DRT) like micro-transit and paratransit, without requiring a full rewrite.

2. Business Perspective (As If You Own the Company)
Multi-operator, multi-mode – Different transport companies can manage their own fleets, routes, schedules, and seat maps under one umbrella platform.

Direct-to-consumer booking – Passengers search for trips, pick seats from a live layout, pay securely, and receive e-tickets.

Real-time experience – During the booking process, seats are temporarily locked so two people can’t buy the same seat. For active journeys, passengers can see the vehicle’s live GPS position.

Full billing & invoicing – Every booking generates an invoice with taxes, fees, and any discounts applied. Integrates with Stripe for real payments (sandbox first).

Scalable & extensible – Designed with clean architecture so you can add new transit modes, pricing models, or even a ride-hailing DRT module without breaking existing functionality.

3. Technical Blueprint
Architecture
Clean Architecture with four layers:

Domain – Core business rules, entities, enums, interfaces (no dependencies).

Application – Use cases, DTOs, service interfaces (depends only on Domain).

Infrastructure – EF Core, PostgreSQL, repositories, SignalR hubs, Stripe integration (implements Application interfaces).

API – ASP.NET Core Web API, controllers, middleware, startup configuration.

Backend
ASP.NET Core 8/9 Web API – Pure REST endpoints, Swagger documented.

Real-time – SignalR hubs for:

Broadcasting seat availability changes and temporary locks.

Streaming live location updates from driver app to passengers.

Authentication – ASP.NET Core Identity with JWT tokens, supporting roles (Passenger, Operator/Admin, Driver).

Database – PostgreSQL 16, managed via Entity Framework Core with code-first migrations.

Payment – Stripe Checkout (test mode) with webhooks for payment confirmation.

Concurrency – Optimistic concurrency (row version) to prevent double-booking; a SeatLock entity (or Redis cache) for short-lived seat blocking during checkout.

Domain Model Highlights
TransportMode enum – Bus, BRT, Trolleybus, LightRail, Metro, CommuterRail, Monorail, AGT, Ferry, Plane (extensible).

Vehicle – A generic transport unit (bus, train set, ferry, etc.) with registration, model, total seats, and transport mode.

Route & RouteStop – Defined with origin, destination, distance, transport mode, and an ordered list of intermediate stops (each with optional boarding/alighting restrictions).

Schedule – A specific vehicle assigned to a route at a specific departure/arrival time with a base price.

Seat & SeatClass – Each seat belongs to a vehicle, has a number (e.g., "A1"), class (Economy, Business, First, Sleeper), and optional deck (for double-deckers or trains).

Booking & Ticket – A booking groups one or more tickets (multi-passenger). Each ticket ties a passenger name and a seat to the booking, with the charged price at the time of booking. Booking statuses: Pending → Confirmed (after payment) or Cancelled. ScheduleId is nullable to later support demand-responsive trips without a fixed schedule.

Future-Ready Extensions Already Designed
Seat attributes (window, aisle, etc.) via a JSON column for any transport layout.

Company/Operator entity for multi-tenancy (planned for admin phase).

Dynamic pricing, taxes, and cancellation policies – hooks in the model.

DRT (on-demand) – Bookings without a schedule, matched later by a separate ride-hailing engine.

Notifications (email/SMS), audit logs, promo codes, loyalty points – all slated for later phases.
