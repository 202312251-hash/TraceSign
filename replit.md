# TraceSign — Cryptographic Document Leak Attribution System

## Project Overview

TraceSign is a competition-ready cryptographic document traceability system. It generates uniquely fingerprinted copies of documents for different recipients and, if a copy is later leaked, can identify exactly which recipient's version was leaked — even if the content was partially modified.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **Frontend**: React + Vite (artifacts/tracesign, at path /)
- **Backend API**: Express 5 (artifacts/api-server, at path /api)
- **Database**: PostgreSQL + Drizzle ORM
- **Validation**: Zod (zod/v4), drizzle-zod
- **API codegen**: Orval (from OpenAPI spec)

## How Fingerprinting Works

1. **Generate**: Admin enters document text + recipient names
2. **Fingerprint**: For each recipient, generate SHA-256(recipientName | documentId | title | serverSalt)
3. **Embed**: Encode fingerprint as zero-width Unicode characters (U+200B = 0, U+200C = 1) and append invisibly at end of text
4. **Store**: Record fingerprint, integrity hash (SHA-256 of visible content), and metadata in PostgreSQL
5. **Detect**: On upload, extract zero-width chars, decode fingerprint, match against DB, recompute integrity hash to check for tampering

## Pages

| Route | Purpose |
|-------|---------|
| `/` | Home — hero + how-it-works steps |
| `/dashboard` | System overview with stats and recent activity |
| `/generate` | Create fingerprinted document copies |
| `/detect` | Upload leaked text for forensic analysis |
| `/history` | Full detection event history |
| `/documents/:id` | View single document copy details |
| `/demo` | Live demo tools for presentations |

## API Routes

| Method | Path | Description |
|--------|------|-------------|
| GET | /api/documents | List all fingerprinted copies |
| POST | /api/documents | Create document + generate copies |
| GET | /api/documents/:id | Get one record |
| GET | /api/documents/:id/download | Get fingerprinted text for download |
| GET | /api/leak-detections | List detection history |
| POST | /api/leak-detections | Analyze leaked document |
| GET | /api/stats | Dashboard statistics |
| POST | /api/demo/seed | Seed demo data (3 recipients + 2 detections) |
| POST | /api/demo/test-leak | Simulate leak from seeded data (tampered or clean) |

## Key Commands

- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks from OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- `pnpm --filter @workspace/api-server run dev` — run API server locally
- `pnpm --filter @workspace/tracesign run dev` — run frontend locally

## Security Notes

- Server-side salt (TRACESIGN_SALT env var) prevents fingerprint forgery
- Zero-width chars are invisible in rendered text but recoverable programmatically
- Integrity hash detects visible content changes even when fingerprint survives
- Fingerprint + integrity separation allows two independent forensic conclusions

## Competition Criteria

- **Originality**: Novel application of zero-width steganography + SHA-256 for document attribution
- **Practicality**: Fully working demo with real cryptography, not a simulation
- **Cybersecurity impact**: Solves real insider threat / leak attribution problem
- **Technical methodology**: Deterministic fingerprinting + integrity verification
- **Presentation**: Polished dark forensic UI, live demo tools, seeded data

See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details.
