# LexiPulse

A full-stack spaced-repetition vocabulary app focused on practical learning flow, offline reliability, and low-cost deployment.

- Live Demo: https://lexipulse.pages.dev
- Public Showcase: this repository
- Private Source (full production repo): available on request for interview review

## Why This Project

Most vocabulary apps solve practice; fewer solve continuity.
LexiPulse is designed so users can keep learning with unstable network and still maintain progression integrity.
The core idea started from a "decreasing learning" approach to vocabulary memorization, but I could not find mainstream apps implementing this algorithm in a practical daily workflow.

## What I Built

- Custom SRS scheduling engine (Ebbinghaus-inspired)
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
Client SPA
  -> Cloudflare Function Proxy
  -> Google Apps Script API
  -> Google Sheets storage
```

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

## Repository Guide

- [docs/architecture.md](docs/architecture.md): system architecture and data flow
- [CONTRIBUTING.md](CONTRIBUTING.md): contribution process
- [SECURITY.md](SECURITY.md): vulnerability reporting
- [LICENSE](LICENSE): MIT license
