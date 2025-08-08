# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Structure

This is a multi-project GitHub repository containing 6 distinct projects:

1. **Journey** - Full-stack MERN app for ABA behavior tracking
2. **NorcalSlippiLeaderboard** - React/TypeScript gaming leaderboard with cron jobs  
3. **PolicyEngine App** - Large-scale React policy analysis platform (has its own CLAUDE.md)
4. **GPT Fusion** - Python package with AI utilities and optional components
5. **Top Viewer Games** - Node.js backend with D3.js frontend for Twitch data
6. **Static Sites** - Simple HTML/CSS projects (costasford.github.io, git-workflow)

## Common Development Commands

### Journey (MERN Stack)
```bash
npm start              # Start React development server
npm run build         # Build for production
npm test              # Run tests
```

### NorcalSlippiLeaderboard (React/TypeScript)
```bash
npm start             # Start webpack dev server
npm run build         # Production build
npm run deploy        # Build and deploy to GitHub Pages
npm run lint          # TypeScript/ESLint linting
./cron/run.sh         # Run data fetching cron job manually
```

### PolicyEngine App (React - see its own CLAUDE.md for details)
```bash
make install          # Install dependencies
make debug            # Start development server
make test             # Run all tests
make format           # Lint and format (run before committing)
```

### GPT Fusion (Python Package)
```bash
pip install -r requirements-dev.txt  # Install dev dependencies
pytest                               # Run test suite
python examples/tutorial.py         # Try example script
python scripts/run_checks.py        # Run all CI checks locally
```

### Top Viewer Games (Node.js + Frontend)
```bash
# Backend
npm run dev           # Start with nodemon
npm start             # Production start

# Frontend (in /frontend directory)
npm start             # Webpack watch mode
npm run build         # Build frontend assets
```

## Architecture Overview

### Journey - Traditional MVC MERN Stack
- **Backend**: Express.js with controllers, models, routes structure
- **Database**: MongoDB with Mongoose ODM
- **Auth**: JWT tokens stored in localStorage
- **API**: RESTful endpoints under `/routes/api/`
- **Frontend**: React with component-based architecture
- **Development**: Uses proxy to localhost:3001 for API calls

### NorcalSlippiLeaderboard - Static React with External APIs
- **Build**: Custom Webpack + TypeScript configuration
- **Data**: Cron jobs fetch from Slippi API, store in JSON files
- **Rate Limiting**: Custom rate limiter for external API calls
- **Deployment**: Automated GitHub Pages deployment via `gh-pages`
- **Visualization**: D3.js integration for charts

### PolicyEngine App - Enterprise React Application
- **Auth**: Auth0 integration with custom hooks
- **API**: Custom abstraction layer with country-specific endpoints
- **State**: URL-driven state management with React Router
- **Structure**: Feature-based organization (not flat file structure)
- **Multi-Country**: Dynamic routing for US/UK policy differences

### GPT Fusion - Modular Python Package
- **Pattern**: Lazy loading with optional dependencies
- **Components**: Core utilities + optional FastAPI/Twitter/web scraping
- **Build**: Modern Python packaging with pyproject.toml
- **Testing**: Comprehensive pytest coverage

### Top Viewer Games - Data Visualization Service
- **Architecture**: Dual package structure (backend + separate frontend)
- **Data Flow**: Twitch API → caching → D3.js visualization
- **Caching**: 10-minute cache cycles to minimize API calls
- **Auth**: OAuth token management with refresh

## Working with Specific Projects

### When working with Journey:
- Look for business logic in `/controllers/`
- Database models in `/models/` with Mongoose schemas
- API routes in `/routes/api/` with separate files per resource
- Frontend utilities in `/src/utils/` for API interactions

### When working with NorcalSlippiLeaderboard:
- Understand the cron job system in `/cron/` for data fetching
- Check `/src/lib/` for utility functions and rate limiting logic
- Data processing happens in cron jobs, display happens in React components

### When working with PolicyEngine App:
- **Use its own CLAUDE.md for detailed guidance**
- API calls use `useAuthenticatedApiCall` hook - don't call directly
- Country-specific logic managed through routing and configuration
- Feature-based file organization - look in subdirectories

### When working with GPT Fusion:
- Understand lazy loading - not all modules available until dependencies installed
- Check `pyproject.toml` for dependency groups and optional components
- Core functionality separate from optional backends/integrations

### When working with Top Viewer Games:
- Backend (root) and frontend (./frontend/) are separate concerns
- Understand caching patterns before modifying data flow
- Check environment variables for API configuration

## Common Patterns

### Authentication:
- **Custom JWT**: Journey (localStorage-based)
- **Auth0**: PolicyEngine (hook-based with context)
- **API Keys**: Top Viewer Games, NorcalSlippiLeaderboard

### State Management:
- **Local State**: Most projects use React hooks (useState/useEffect)
- **URL State**: PolicyEngine uses search params for persistence
- **Props Drilling**: Journey passes auth state down from App level

### API Integration:
- **Abstraction Layers**: PolicyEngine has sophisticated API wrappers
- **Direct Calls**: Most projects use axios or fetch directly
- **Rate Limiting**: NorcalSlippiLeaderboard and Top Viewer Games implement rate limiting

### Build/Deploy:
- **GitHub Pages**: NorcalSlippiLeaderboard (automated)
- **Traditional Hosting**: Journey, Top Viewer Games
- **Package Distribution**: GPT Fusion (PyPI)
- **Cloud Ready**: PolicyEngine

## Important Notes

1. **PolicyEngine App** has its own comprehensive CLAUDE.md - refer to that for detailed guidance on that project
2. **Multiple package.json files** - each project has its own dependencies and scripts
3. **Rate limiting** is critical for NorcalSlippiLeaderboard and Top Viewer Games due to external API usage
4. **Cron jobs** in NorcalSlippiLeaderboard require understanding of the data fetching cycle
5. **Authentication patterns vary significantly** between projects - check each project's approach before modifying auth-related code