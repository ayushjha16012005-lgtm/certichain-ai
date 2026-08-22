# CertiChain AI

**AI-Powered Decentralized Credential Trust & Fraud Detection Network**

CertiChain AI is a decentralized credential-verification platform that combines **AI-based document forensics** with **blockchain-anchored cryptographic integrity** to determine whether an academic or professional credential can be trusted — not just whether it exists.

Built for **Track 1 — AI-Powered Decentralized Applications**.

> AI determines whether a document *looks* legitimate.
> Blockchain determines whether the credential's issued record has been *altered or revoked*.
> Together, they answer the question a recruiter actually cares about: **can this certificate be trusted?**

---

## Table of Contents

- [The Problem](#the-problem)
- [The Solution](#the-solution)
- [How It Works](#how-it-works)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)
- [Smart Contract Overview](#smart-contract-overview)
- [Getting Started](#getting-started)
- [Roadmap](#roadmap)
- [Team](#team)
- [License](#license)

---

## The Problem

When a recruiter or institution receives a certificate PDF, four problems go unanswered:

| # | Problem | Why it matters |
|---|---------|-----------------|
| A | **Is the document genuine?** | Fields like CGPA, dates, and names can be silently edited. |
| B | **Did the institution actually issue it?** | A well-formatted PDF proves nothing on its own. |
| C | **Has the credential been revoked?** | A static PDF never reflects a later revocation. |
| D | **Verification is centralized** | Manual email/call verification is slow and doesn't scale across institutions. |

## The Solution

CertiChain models three participants — **Issuer** (institution), **Holder** (student/professional), and **Verifier** (recruiter/agency) — aligned with the [W3C Verifiable Credentials 2.0](https://www.w3.org/news/2025/the-verifiable-credentials-2-0-family-of-specifications-is-now-a-w3c-recommendation/) model.

- **AI layer** — multi-signal document forensics (OCR/text consistency, visual forgery detection, metadata analysis) producing an explainable risk score.
- **Blockchain layer** — a role-gated smart contract registry that anchors a SHA-256 hash of each credential, and tracks issuance/revocation status. No PII on-chain.
- **Verification engine** — combines cryptographic hash matching + issuer authorization + revocation status + AI risk into a single trust report.

## How It Works

1. **Issue** — Institution uploads a certificate → OCR + AI forensic analysis run → document is hashed (SHA-256) → hash + metadata anchored on-chain → credential ID + QR code generated.
2. **Verify** — Verifier scans the QR / uploads the document → current hash is recomputed and compared against the on-chain record → issuer authorization and revocation status are checked → AI risk score is attached → a full trust report is returned.
3. **Revoke** — Issuer can revoke a credential on-chain at any time; all future verifications immediately reflect the revoked status while the historical record stays auditable.

### Live demo flow (tamper detection)

```
Original certificate  →  Hash A  →  anchored on-chain
Edited certificate    →  Hash B  →  Hash B != Hash A  →  ❌ TAMPERED
AI forensic layer     →  flags font, metadata, and layout anomalies
```

## Architecture

```
                         CERTICHAIN AI
                              │
           ┌──────────────────┼──────────────────┐
           ▼                  ▼                  ▼
     AI FORENSICS      CRYPTOGRAPHIC        DECENTRALIZED
                          INTEGRITY              TRUST
           │                  │                  │
     OCR / CV / ML         SHA-256          Smart Contract
           │                  │                  │
           └──────────────────┼──────────────────┘
                              ▼
                      VERIFICATION ENGINE
                              │
                   ┌──────────┴──────────┐
                   ▼                     ▼
              TRUST SCORE            AUDIT TRAIL
                   │                     │
                   └──────────┬──────────┘
                              ▼
                          VERIFIER
```

Service-level view:

```
        Next.js Web  ──REST/RPC──►  FastAPI Gateway
                                          │
                     ┌────────────────────┼────────────────────┐
                     ▼                    ▼                    ▼
              AI Service (Py)     Credential Service       Blockchain Service
                     │              (PostgreSQL)              (ethers.js)
                     ▼                                          │
                AI Models                                Smart Contracts
                                                          (Ethereum Sepolia)
```

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Next.js, TypeScript, Tailwind CSS, shadcn/ui |
| Backend | FastAPI, Python, Pydantic |
| AI / Forensics | PyTorch, OpenCV, PaddleOCR / Tesseract, scikit-learn |
| Database | PostgreSQL |
| Blockchain | Solidity, Hardhat, ethers.js, OpenZeppelin (AccessControl) |
| Network | Ethereum Sepolia (testnet) |
| Storage | Supabase Storage (MVP) → IPFS (production path) |

## Repository Structure

```
certichain-ai/
├── frontend/          # Next.js app — issuer, holder, and verifier portals
├── backend/           # FastAPI gateway + credential service
├── ai-service/        # OCR, visual forgery detection, metadata risk engine
├── blockchain/
│   ├── contracts/     # Solidity smart contracts (CredentialRegistry, roles)
│   └── scripts/       # Hardhat deploy / verify scripts
├── docs/              # Architecture notes, API design, diagrams
└── .github/workflows/ # CI pipelines
```

## Smart Contract Overview

`CredentialRegistry.sol` anchors each credential's hash and status on-chain, gated by role-based access control (`OpenZeppelin AccessControl`):

- `issueCredential(credentialId, documentHash, credentialType)` — `ISSUER_ROLE` only
- `revokeCredential(credentialId)` — `ISSUER_ROLE` only
- `verifyCredential(credentialId, documentHash)` — public, read-only
- Emits `CredentialIssued` and `CredentialRevoked` events consumed by the backend event listener

No personal data is stored on-chain — only `credentialId`, `documentHash`, `issuer`, `issuedAt`, and `status`.

## Getting Started

> Scaffolding in progress — setup instructions for each service will land in their respective directories (`frontend/`, `backend/`, `ai-service/`, `blockchain/`) as they're built out.

```bash
git clone https://github.com/<your-username>/certichain-ai.git
cd certichain-ai
```

## Roadmap

- [ ] Institution issuer dashboard (upload + issue credential)
- [ ] AI forensic pipeline (OCR + visual anomaly + metadata risk)
- [ ] `CredentialRegistry` smart contract + Hardhat deployment to Sepolia
- [ ] Student credential view (QR code + verification link)
- [ ] Employer/verifier portal with trust report
- [ ] Tamper-detection demo (original vs. modified certificate)
- [ ] Revocation flow end-to-end
- [ ] Migrate document storage to IPFS

## Team

**The Crypto Knights**
Built by [Ayush Jha](https://ayushjha-portfolioo.netlify.app) and team.

## License

MIT — see [LICENSE](LICENSE).
