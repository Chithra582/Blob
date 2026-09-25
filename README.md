> This Document is partially made by AI, if you find any issues, please fix it.

# Blob - AI-Powered Study Tool

[![OpenGAP Spec 0.1.0](https://img.shields.io/badge/OpenGAP-0.1.0-blue.svg)](https://opengitagent.org)
[![GitAgent Passport](https://img.shields.io/badge/GitAgent%20Passport-Ready-brightgreen.svg)](https://app.hidevs.xyz/passport/submit)
[![Category](https://img.shields.io/badge/Category-Education-purple.svg)](https://app.hidevs.xyz/passport/submit)
[![Compliance](https://img.shields.io/badge/Compliance-FERPA%20%7C%20GDPR-orange.svg)](EXPLAINABILITY.md)

> An open-source mobile app that transforms your study materials into interactive flashcards, mind maps, and quizzes using AI.

## What is Blob?

Blob is a study companion app that helps students learn more effectively. Simply provide a syllabus, topic, subject, or course content, and Blob's AI will automatically generate:

- **Flashcards** - For memorization and quick recall
- **Mind Maps** - For visualizing connections between concepts
- **Quizzes** - For testing your knowledge
- **More** - want something else too? discussions are open, stage is yours

### Privacy-First: Bring Your Own API Key (BYOK)

Blob uses a "Bring Your Own API Key" model, meaning you use your own AI service API key (like OpenAI, Anthropic, etc.). This ensures:

- You control your data
- You only pay for what you use

## Quick Start

New to coding? No problem! Check out our [CONTRIBUTING.md](./CONTRIBUTING.md) for a step-by-step guide to getting started.

### Prerequisites

Before you begin, make sure you have these tools installed on your system:

#### Required Tools

1. **Node.js (v18 or higher)**
   - JavaScript runtime that powers the development environment
   - **Download**: [nodejs.org](https://nodejs.org/) (get the LTS version)
   - **Check if installed**: `node --version`
   - **Why needed**: Runs the API server, build tools, and development scripts

2. **pnpm (Package Manager)**
   - Fast, efficient package manager for installing project dependencies
   - **Install**: `npm install -g pnpm`
   - **Check if installed**: `pnpm --version`
   - **Why needed**: Manages monorepo workspaces and installs all project dependencies

3. **Git (Version Control)**
   - Version control system for tracking code changes
   - **Download**: [git-scm.com](https://git-scm.com/downloads)
   - **Check if installed**: `git --version`
   - **Why needed**: Clone the repository and manage your contributions

4. **Docker (Container Platform)**
   - Runs PostgreSQL database in an isolated container
   - **Download**: [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop)
   - **Check if installed**: `docker --version`
   - **Why needed**: Provides local PostgreSQL database for development
   - **Note**: Make sure Docker Desktop is running before setup

#### For Mobile Testing

5. **Expo Go (Mobile App)**
   - Test the app on your physical device
   - **Download**: [iOS](https://apps.apple.com/app/expo-go/id982107779) | [Android](https://play.google.com/store/apps/details?id=host.exp.exponent)
   - **Why needed**: Preview and test the mobile app during development
   - **Alternative**: Use Android Emulator or iOS Simulator (Mac only)

#### Optional but Recommended

- **Code Editor**: [VS Code](https://code.visualstudio.com/), [Neovim](https://neovim.io/), or your preferred editor
- **GitHub Account**: For contributing to the project

### Installation

1. Clone the repository:

```bash
git clone https://github.com/opencodeiiita/blob.git
cd blob
```

2. Run the automated setup script:

```bash
pnpm setup
```

This interactive script will guide you through the entire setup process, including:

- Installing dependencies
- Setting up environment files
- Starting Docker containers
- Configuring the database

3. Start developing! The setup script will guide you through the next steps. For detailed manual setup instructions, see [CONTRIBUTING.md](./CONTRIBUTING.md).

## Project Structure

```
blob/
├── apps/
│   ├── server/       # HTTP server (Hono) that serves the API
│   └── mobile/       # Expo React Native mobile app
├── packages/
│   ├── api/          # API logic (tRPC routers and setup)
│   └── db/           # Database layer with Drizzle ORM
├── docs/             # Documentation
└── scripts/          # Utility scripts (setup, etc.)
```

### Architecture Overview

- **`packages/api`** - Contains all API business logic, tRPC routers, and configuration
- **`apps/server`** - Lightweight HTTP server wrapper (Hono) that serves the API
- **`apps/mobile`** - React Native app that consumes the API with full type safety
- **`packages/db`** - Shared database layer with Drizzle ORM schemas

## Tech Stack

This project uses modern, industry-standard technologies:

### Backend

- **API Server**: [Hono](https://hono.dev/) - Lightweight, ultrafast web framework
- **Runtime**:
  - **Development**: Node.js with TypeScript (via [tsx](https://tsx.is/)) - Hot reload, better debugging
  - **Production**: [Bun](https://bun.sh/) - Native performance, faster startup
- **RPC Layer**: [tRPC v11](https://trpc.io/) - End-to-end typesafe APIs
- **Database ORM**: [Drizzle ORM](https://orm.drizzle.team/) - TypeScript ORM with great DX
- **Database**: PostgreSQL
  - Local development: Docker PostgreSQL
  - Production: [Neon Postgres](https://neon.tech/) (serverless PostgreSQL)
- **Validation**: [Zod](https://zod.dev/) - TypeScript-first schema validation

### Mobile App

- **Framework**: [Expo](https://expo.dev/) - React Native framework
- **UI Library**: [React Native](https://reactnative.dev/) - Cross-platform mobile development
- **Routing**: [Expo Router](https://docs.expo.dev/router/introduction/) - File-based routing
- **Styling**: [NativeWind](https://www.nativewind.dev/) - Tailwind CSS for React Native
- **State Management**: [Zustand](https://zustand-demo.pmnd.rs/) - Lightweight state management
- **Data Fetching**: [TanStack Query](https://tanstack.com/query) (React Query) with tRPC

### Development Tools

- **Monorepo**: [Turborepo](https://turbo.build/repo) - High-performance build system
- **Package Manager**: [pnpm](https://pnpm.io/) - Fast, disk space efficient
- **Language**: [TypeScript](https://www.typescriptlang.org/) - Type-safe JavaScript
- **Linting**: [ESLint](https://eslint.org/) - Code quality
- **Formatting**: [Prettier](https://prettier.io/) - Code formatting
- **Containerization**: [Docker](https://www.docker.com/) - For local PostgreSQL

### Why These Technologies?

- **Type Safety**: TypeScript + tRPC ensures type safety across the entire stack
- **Developer Experience**: Hot reload with Node.js (dev), fast startup with Bun (production)
- **Performance**: Hono is extremely fast, Bun provides near-native performance in production
- **Scalability**: Serverless PostgreSQL (Neon) scales automatically
- **Cross-Platform**: Single codebase runs on iOS, Android, and Web
- **Flexibility**: Different runtimes optimized for development vs production use cases

## Contributing

We welcome contributions from developers of all skill levels! Whether you're fixing a typo, adding a feature, or improving documentation, your help is appreciated.

Please read our [CONTRIBUTING.md](./CONTRIBUTING.md) for:

- Development environment setup
- How to run the project locally
- Code style guidelines
- How to submit pull requests

## Support

- **Issues**: Found a bug? [Open an issue](https://github.com/opencodeiiita/blob/issues)
- **Discussions**: Have questions? [Start a discussion](https://github.com/opencodeiiita/blob/discussions)
- **Discord**: Join [opencodeiiita community](https://discord.gg/jnHGrDQu)

## License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.

## Ownership

This project is owned and maintained by individual contributors.
The hosting GitHub organization claims no ownership,
copyright, or control over this repository.

---

## GitAgent Passport Qualification

This repository is fully compliant with the **OpenGAP Spec 0.1.0** standard and qualified for the **HiDevs GitAgent Passport**:

- **Checkpoint 1 (Validate):** Verified OpenGAP spec 0.1.0 compliance via [`agent.yaml`](agent.yaml), [`SOUL.md`](SOUL.md), [`skills/`](skills/), and [`tools/`](tools/).
- **Checkpoint 2 (Explain):** Comprehensive 5-section transparency report in [`EXPLAINABILITY.md`](EXPLAINABILITY.md) detailing active-recall synthesis, BYOK privacy guarantees, FERPA/GDPR compliance, and failure mode mitigations.
- **Checkpoint 3 (Export):** Cross-framework export compatibility tested across OpenAI SDK, CrewAI, Claude Code, and Lyzr.
- **Target Category:** **`Education`** (AI-Powered Study Tools & Knowledge Synthesis).
