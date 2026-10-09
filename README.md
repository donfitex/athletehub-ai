# AthleteHub AI

AthleteHub AI is a sports talent marketplace that helps athletes showcase verified performance and opportunities, while helping clubs, academies, scouts, and organizations discover, evaluate, and recruit talented athletes using a shared platform.

## Product intent

The platform connects athletes with clubs, coaches, agencies, sponsors, and event organizers. Athletes can build professional profiles, apply to opportunities, and manage bookings. Organizations can publish recruitment opportunities, shortlist candidates, and manage communications and scheduling.

## Shared architecture

- Web frontend: Next.js + TypeScript + Tailwind CSS
- API layer: Django + Django REST Framework
- Database: PostgreSQL
- Background jobs: Redis + Celery
- Optional search layer: PostgreSQL first, Elasticsearch later if needed
- Native Android app: Kotlin + Jetpack Compose

## Documentation

- [docs/requirements.md](docs/requirements.md): product requirements, personas, milestones, and MVP boundaries
- [docs/architecture.md](docs/architecture.md): system architecture, security model, and data flow

## Local development

This repository is initialized for a local-first development workflow. We will use Docker Compose for the core services and keep cloud dependencies optional.

## License

Open-source project intended for local learning and commercial extension work. Third-party dependencies must be checked for license and maintenance status before adopting them.
