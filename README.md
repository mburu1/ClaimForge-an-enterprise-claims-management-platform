# ClaimForge

Enterprise-grade Claims Management Platform for Insurance / InsurTech.

Built as a production-ready reference implementation of the skills and responsibilities typically required for senior Application Development roles involving React.js, Node.js, scalable APIs, multi-database design, and cloud-ready delivery.

---

## Overview

ClaimForge is a full-stack web application that supports the complete insurance claims lifecycle:

- Claim intake & triage
- Investigation & document management
- Settlement workflow
- Role-based dashboards & analytics
- Audit trails & compliance-ready logging

The architecture prioritizes scalability, maintainability, security, and performance — the same concerns called out in modern enterprise Application Development job descriptions.

### Tech Stack

| Layer              | Technology                          |
|--------------------|-------------------------------------|
| Frontend           | React 18 + TypeScript, Vite, Tailwind CSS, React Query, Zustand |
| Backend            | Node.js + TypeScript, Express / Fastify, Zod validation |
| Databases          | PostgreSQL (core), MongoDB (documents & logs), Redis (cache / sessions) |
| Auth               | JWT + Refresh Tokens, RBAC          |
| API Style          | RESTful + OpenAPI                   |
| Infrastructure     | Docker, Docker Compose, GitHub Actions |
| Testing            | Vitest / Jest, Supertest, Playwright |
| Observability      | Structured logging, health checks, basic metrics |

---

## Repository Structure

```
claimforge/
├── frontend/                    # React + TypeScript frontend
│   ├── src/
│   │   ├── components/         # Reusable UI components
│   │   ├── pages/              # Page-level components
│   │   ├── hooks/              # Custom React hooks
│   │   ├── services/           # API client services
│   │   ├── store/              # Zustand state management
│   │   ├── types/              # TypeScript type definitions
│   │   └── utils/              # Utility functions
│   ├── public/                 # Static assets
│   └── package.json
├── backend/                     # Node.js + TypeScript backend
│   ├── src/
│   │   ├── controllers/        # Request handlers
│   │   ├── services/           # Business logic
│   │   ├── models/             # Data models & schemas
│   │   ├── routes/             # API route definitions
│   │   │   ├── api/           # General API routes
│   │   │   ├── auth/          # Authentication routes
│   │   │   ├── claims/        # Claims management routes
│   │   │   ├── users/         # User management routes
│   │   │   └── audit/         # Audit log routes
│   │   ├── middleware/         # Express middleware
│   │   ├── utils/              # Utility functions
│   │   ├── types/              # TypeScript types
│   │   └── config/             # Configuration files
│   └── package.json
├── docs/                        # Documentation
│   ├── diagrams/              # Architecture diagrams
│   ├── architecture/           # Architecture documentation
│   └── api/                    # API documentation
├── docker/                      # Docker configurations
├── database/                    # Database scripts
│   └── scripts/                # Migration & seed scripts
├── tests/                       # Test suites
│   ├── unit/                  # Unit tests
│   ├── integration/           # Integration tests
│   └── e2e/                   # End-to-end tests
├── docker-compose.yml           # Multi-container orchestration
├── .github/                     # GitHub Actions workflows
└── README.md
```

---

## Getting Started

### Prerequisites

- Node.js 18+
- Docker & Docker Compose
- PostgreSQL 15+
- MongoDB 6+
- Redis 7+

### Installation

1. Clone the repository:
```bash
git clone https://github.com/mburu1/ClaimForge-an-enterprise-claims-management-platform.git
cd ClaimForge-an-enterprise-claims-management-platform
```

2. Install dependencies:
```bash
# Frontend
cd frontend
npm install

# Backend
cd ../backend
npm install
```

3. Set up environment variables:
```bash
# Backend
cp .env.example .env
# Edit .env with your configuration
```

4. Start databases with Docker Compose:
```bash
docker-compose up -d postgres mongodb redis
```

5. Run database migrations:
```bash
cd backend
npm run db:migrate
```

6. Start the development servers:
```bash
# Backend (in one terminal)
cd backend
npm run dev

# Frontend (in another terminal)
cd frontend
npm run dev
```

---

## Development

### Running Tests

```bash
# Backend unit tests
cd backend
npm test

# Frontend unit tests
cd frontend
npm test

# E2E tests
cd frontend
npm run test:e2e
```

### Building for Production

```bash
# Frontend
cd frontend
npm run build

# Backend
cd backend
npm run build
```

---

## Architecture

See [docs/architecture/](docs/architecture/) for detailed architecture documentation.

### Key Design Decisions

- **Multi-database approach**: PostgreSQL for transactional data, MongoDB for document storage, Redis for caching
- **Role-based access control**: Granular permissions for adjusters, reviewers, admins
- **Audit logging**: Immutable audit trail for compliance
- **Event-driven**: Uses pub/sub for real-time updates where applicable

---

## Contributing

Contributions are welcome! Please read our contributing guidelines before submitting PRs.

---

## License

MIT License - see LICENSE file for details
