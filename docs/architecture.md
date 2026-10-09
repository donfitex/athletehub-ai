# AthleteHub AI architecture

## 1. Architecture overview

AthleteHub AI uses a modular monolith architecture centered on Django. This gives the project a strong foundation without prematurely splitting services.

The system is structured around shared business capabilities:

- identity and authorization
- athlete profile management
- organizations and memberships
- opportunities and applications
- bookings and scheduling
- conversations and notifications
- AI-assisted discovery
- payments and audit trails

The web and Android clients use the same backend interface for consistency.

## 2. Runtime components

### Frontend web client
- Next.js application
- TypeScript and React
- Tailwind CSS for responsive UI
- Browser requests hit the Django REST API
- Auth handled with secure cookies or token-based sessions depending on the chosen strategy

### Django backend
- business logic and API endpoints
- ORM models for relational data
- permission enforcement and validation
- Celery integration for async tasks
- search and recommendation orchestration

### PostgreSQL
- authoritative source of relational data
- users, organizations, applications, bookings, messages, media, and audit records
- transactional integrity for critical operations

### Redis
- cache layer
- job queue backing for Celery
- temporary sessions and rate-limiting support where needed

### Celery workers
- notification delivery
- indexing tasks
- recommendation generation
- retry logic for background jobs

### Android client
- Kotlin
- Jetpack Compose
- ViewModels, repositories, and Retrofit or equivalent HTTP library
- secure storage for session state and preference data

## 3. Data flow

1. User interacts with the web or Android client
2. Client sends authenticated requests to Django REST API
3. Django validates input and role-based permissions
4. Django reads or updates PostgreSQL
5. Background jobs may enqueue tasks in Redis/Celery
6. Search indexing, notifications, or recommendation generation runs asynchronously
7. Clients poll or refresh data from the API

## 4. Security model

Security is enforced primarily on the API server, not in the client.

- passwords are hashed with Django's secure auth mechanisms
- role checks and object-level access checks happen in the backend
- organization data is scoped by membership and permissions
- admin actions require authenticated and authorized backend validation
- file uploads are validated and stored securely
- secrets remain in environment variables and never in client code

## 5. Domain boundaries

The initial domain split is intentionally clear:

- identity: auth, users, roles, sessions
- profiles: athlete and organization profiles
- recruitment: opportunities, applications, shortlists
- scheduling: bookings, availability, conflict detection
- communication: messages, notifications, read status
- intelligence: search ranking, recommendation scoring, AI service abstractions
- payments: state machine and mock provider integration

This allows future extraction into separate services if the business justifies it.

## 6. API contract approach

The API should be versioned or clearly structured by domain, for example:

- /api/v1/auth/
- /api/v1/profiles/
- /api/v1/organizations/
- /api/v1/opportunities/
- /api/v1/bookings/
- /api/v1/messages/
- /api/v1/notifications/
- /api/v1/payments/

This keeps mobile and web clients aligned and reduces accidental coupling.

## 7. Authentication strategy

The recommended starting point is Django session-based auth for the web app, with a secure token or session-based API strategy for mobile clients. The exact scheme will be chosen after the backend foundation is implemented and after validating CSRF, XSS, and device security trade-offs.

Key security points:

- do not store tokens in plain Android preferences
- use secure encrypted storage for any sensitive mobile credentials
- validate every privileged action on the backend
- treat all frontend state as untrusted

## 8. Deployment boundaries

The local development footprint will be Docker Compose-driven and locally runnable. Production deployment will be considered only after the MVP is stable and well-tested.

## 9. Why this architecture

This design is chosen because it is:

- fast to build and reason about
- easy to teach and maintain
- secure enough for a real MVP
- compatible with both web and mobile clients
- extensible enough for future growth

It does not prematurely introduce microservices, service mesh complexity, or large infrastructure overhead.

## 10. Alternatives and trade-offs

### Alternative: full microservice split early
Pros:
- clearer service boundaries at scale

Cons:
- too much complexity for the MVP
- slower iteration during early development
- increased operational overhead

### Alternative: direct database access from frontend
Not acceptable because it breaks permission boundaries and exposes logic to the client.

### Alternative: Elasticsearch from day one
Possible later, but not necessary until search scale or complexity requires it.

## 11. Design principles

- one backend, two clients
- backend-enforced authorization
- normalized relational data for transactional correctness
- async background work for non-critical tasks
- local-first setup before cloud deployment
- testability before optimization
