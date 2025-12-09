# ArchiMate Model: Hotel Services Platform

## Purpose and scope
Design outlines a service platform that supports hotel operations and guest-facing digital services. It focuses on how business services map to applications and supporting technology.

## Motivation view
- **Drivers**: Increase occupancy, reduce operational costs, improve guest satisfaction, comply with payment/security standards.
- **Assessment**: Legacy PMS silos and manual housekeeping coordination create delays and overbooking risks.
- **Goals**: Unified inventory control, real-time availability, frictionless check-in/out, data-driven upsell.

## Business layer
- **Actors/roles**: Guest, Corporate Travel Partner, Front Desk Agent, Housekeeping Staff, Revenue Manager, Hotel Manager.
- **Business services**: Room Booking Service, Check-in/Check-out Service, Housekeeping Service, Payment & Billing Service, Loyalty & Offers Service, Reporting Service.
- **Processes**:
  - *Booking Management*: search availability → select offer → reserve → confirm payment.
  - *Arrival & Stay*: online check-in → digital key issuance → room ready notification → service requests.
  - *Housekeeping Cycle*: auto room assignment → cleaning execution → status update to PMS.
  - *Revenue & Reporting*: rate management → channel distribution → dashboards.
- **Value exchanges**: Guests receive confirmations, invoices, and loyalty points; Corporate partners receive negotiated rates and booking confirmations.

## Application layer
- **Customer Web/Mobile App**: browsing, booking, self check-in/out, payments, loyalty wallet.
- **Property Management System (PMS)**: source of truth for reservations, folios, room status, guest profiles.
- **Channel Manager**: synchronizes rates/availability with OTAs and corporate portals.
- **Payment Gateway Adapter**: tokenized payments, refunds, PCI-DSS compliance.
- **Loyalty & CRM**: profiles, tiering, targeted offers, consent management.
- **Housekeeping App**: task lists, room-status updates, maintenance tickets.
- **BI & Reporting**: occupancy, ADR/RevPAR dashboards, operational KPIs.
- **Integration backbone**: API Gateway with event bus for reservation events and housekeeping status changes.

### Application collaboration and interfaces
- Booking portal uses PMS APIs for availability, creates reservations, and invokes Payment Gateway Adapter for preauthorizations/captures.
- Channel Manager publishes availability updates to OTAs and consumes bookings, normalizing into PMS.
- Housekeeping App subscribes to reservation check-out events to trigger cleaning tasks; posts status updates back to PMS and notifies Front Desk Agent.
- Loyalty & CRM listens to stay-completed events, updates points, and pushes targeted offers to Customer App.
- BI & Reporting consumes event bus streams and PMS exports for analytics.

## Technology layer
- **API Gateway** fronting REST/GraphQL services; **Event Bus** (e.g., Kafka) for decoupling.
- **Application Runtime**: container platform (orchestration with autoscaling) hosting booking, CRM, and housekeeping services.
- **Data**: PMS relational database; document store for profiles/offers; blob storage for invoices and digital keys; secrets manager for credentials.
- **Security**: IAM for staff SSO and guest authentication; WAF and rate limiting at the gateway; device attestation for digital keys.
- **Network/Edge**: CDN for static assets; VPN/SD-WAN connectivity to on-prem door-lock controllers.

## Cross-layer mappings
- Room Booking, Check-in/Check-out, and Loyalty services are realized by Customer App + PMS + Payment Gateway Adapter + Loyalty & CRM.
- Housekeeping Service is realized by PMS task APIs and the Housekeeping App, triggered by Event Bus messages from check-out events.
- Reporting Service is realized by BI & Reporting consuming PMS databases and event streams.

## Implementation and roadmap hints
- Start with API Gateway + Event Bus to decouple PMS integration from web/mobile experiences.
- Prioritize real-time room-status loop (PMS ↔ Housekeeping App) to prevent overbooking and speed turnovers.
- Phase channel manager integration to avoid inventory conflicts; follow with loyalty/CRM enrichment for upsell.
