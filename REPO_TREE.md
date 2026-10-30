# ClaimForge Repository Tree Structure

```
claimforge/
├── .git/
├── .gitignore
├── README.md
├── REPO_TREE.md
├── docker-compose.yml
├── claimforge.md
├── DokBi.jpg
├── frontend/
│   ├── package.json
│   ├── public/
│   └── src/
│       ├── components/
│       │   └── .gitkeep
│       ├── hooks/
│       │   └── .gitkeep
│       ├── pages/
│       │   └── .gitkeep
│       ├── services/
│       │   └── .gitkeep
│       ├── store/
│       │   └── .gitkeep
│       ├── types/
│       │   └── .gitkeep
│       └── utils/
│           └── .gitkeep
├── backend/
│   ├── package.json
│   └── src/
│       ├── config/
│       │   └── .gitkeep
│       ├── controllers/
│       │   └── .gitkeep
│       ├── middleware/
│       │   └── .gitkeep
│       ├── models/
│       │   └── .gitkeep
│       ├── routes/
│       │   ├── api/
│       │   │   └── .gitkeep
│       │   ├── audit/
│       │   │   └── .gitkeep
│       │   ├── auth/
│       │   │   └── .gitkeep
│       │   ├── claims/
│       │   │   └── .gitkeep
│       │   └── users/
│       │       └── .gitkeep
│       ├── services/
│       │   └── .gitkeep
│       ├── types/
│       │   └── .gitkeep
│       └── utils/
│           └── .gitkeep
├── docs/
│   ├── api/
│   ├── architecture/
│   └── diagrams/
├── docker/
│   └── .gitkeep
├── database/
│   └── scripts/
│       └── .gitkeep
└── tests/
    ├── e2e/
    │   └── .gitkeep
    ├── integration/
    │   └── .gitkeep
    └── unit/
        └── .gitkeep
```

## Directory Purpose

- **frontend/**: React + TypeScript frontend application
- **backend/**: Node.js + TypeScript backend API
- **docs/**: Documentation including diagrams and architecture docs
- **docker/**: Docker configurations for containers
- **database/**: Database migration and seed scripts
- **tests/**: Test suites (unit, integration, e2e)
