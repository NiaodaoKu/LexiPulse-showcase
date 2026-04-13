# LexisPulse

A full-stack spaced-repetition vocabulary app focused on practical learning flow, offline reliability, and low-cost deployment.

- Live Demo: https://lexispulse.pages.dev
- Public Showcase: this repository
- Private Source (full production repo): available on request for interview review

## Why This Project

Most vocabulary apps solve practice; fewer solve continuity.
LexisPulse is designed so users can keep learning with unstable network and still maintain progression integrity.
The core idea started from a "decreasing learning" approach to vocabulary memorization, but I could not find mainstream apps implementing this algorithm in a practical daily workflow.

## What I Built

- Custom SRS (Spaced Repetition System) scheduling engine (Ebbinghaus-inspired)
- Multi-tenant data model on Google Sheets (per-user sheet isolation)
- Offline-first workflow with queued sync and optimistic cache updates
- Cloudflare Function proxy to bridge browser and Apps Script constraints
- PWA installability for mobile-first daily usage

## Technical Highlights

- Frontend: Vanilla JS SPA (no framework)
- Backend: Google Apps Script action router (30+ API actions)
- Data: Google Sheets as serverless datastore
- Edge layer: Cloudflare Pages Functions
- Auth: Google Identity Services (OAuth 2.0)
- Offline: Service Worker + IndexedDB + sync queue

## Architecture

```text
Presentation Layer (Browser SPA)
  - Route/UI state, study flows, and local cache hydration
  - Offline queue enqueue/replay via IndexedDB + Service Worker
        |
        v
Edge API Layer (Cloudflare Pages Function)
  - Stable browser-facing endpoint
  - Proxy/normalize requests to Apps Script Web App
        |
        v
Application Layer (Google Apps Script)
  - Action router (`doPost`) with 30+ domain operations
  - SRS scheduling, task completion, wrong-answer lifecycle
        |
        v
Data Layer (Google Sheets)
  - Per-user sheet isolation (multi-tenant boundary)
  - Lightweight analytics and dashboard aggregation
```

### Design Decisions

- **Offline-first consistency**: user actions are accepted locally first, then reconciled through queue replay.
- **Low-ops architecture**: Cloudflare + Apps Script + Sheets reduces infra overhead while preserving full-stack capability.
- **Fast feedback loop**: completion UX prioritizes immediate response (optimistic update + short dashboard return).

Detailed notes: [docs/architecture.md](docs/architecture.md)

## SRS Strategy (Simplified)

```text
R0: 3 reviews
R1: 3 reviews
R2: 2 reviews
R3: 2 reviews
R4: 1 review
R5+: 1 review, interval = max(2, round-3) days
```

Wrong-answer retirement: 3 consecutive correct answers.

## Reliability Design

- Completion flow confirms backend update when online.
- If offline, action is queued and local cache is updated immediately.
- Dashboard return is intentionally fast (about 300ms minimum stay).
- Queue replay runs when connectivity recovers.

## Product Footprint

- Installable PWA
- Daily task mode + review mode
- Vocabulary sets + sentence support
- Category and filtering support
- Monthly recap metrics

## Recent Feature Updates

- Starred vocabulary system:
  - Added cross-device starred persistence (frontend local state + backend index synchronization)
  - Added starred study flow and starred quiz entry
- Quiz flow resilience:
  - Preserved quiz source draft state during async background refresh to prevent accidental source reset
  - Improved source-to-mode transition stability for longer sessions
- Dictionary experience upgrades:
  - Added bilingual lookup refinement for `en-zh` and `zh-en` scenarios
  - Added candidate translation cleanup/ranking and context-focused meaning presentation
  - Improved IPA normalization and pronunciation interaction behavior
- Input and authoring workflow:
  - Updated bulk paste and AI-scan import behavior to append mode for faster batch entry
  - Strengthened duplicate detection logic across punctuation/case variants
- Completion experience on weak networks:
  - Daily completion no longer blocks on remote round-finalization response
  - Local completion returns quickly while remote sync continues in background

## Repository Guide

- [docs/architecture.md](docs/architecture.md): system architecture and data flow
- [CONTRIBUTING.md](CONTRIBUTING.md): contribution process
- [SECURITY.md](SECURITY.md): vulnerability reporting
- [LICENSE](LICENSE): MIT license
