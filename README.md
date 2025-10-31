# ProjectDevelopmentPortal — Hedera-powered Innovation Pipeline

## Track
- **Hackathon Track:** Hedera Africa Innovation — *Sustainable Civic Infrastructure*

## Quick Pitch
ProjectDevelopmentPortal streamlines how African builders ideate, prototype, and deploy public-impact products. Teams capture development milestones, convert validated outcomes into on-chain proofs, and unlock milestone-based funding via Hedera. The portal keeps every stakeholder — founders, mentors, and funders — aligned through tamper-proof updates and tokenized incentives.

## Quick Links
- 📄 **Pitch Deck:** [Google Slides Presentation](https://dorahacks.io/buidl/32570/)
- 📜 **Certification:** [Hashgraph Developer Course Credential](https://certificates.hedera.com/example)
- 🤝 **Required Judge Access:** Invite **Hackathon@hashgraph-association.com** as a collaborator to this GitHub repository so the judging automation can evaluate the codebase.

## Repository Structure
This blueprint currently ships with documentation and automation hooks. Source modules are organized under the `ProjectDevelopmentPortal` namespace when pulled from the main product repository.

```
/ (root)
├── README.md                     # Hackathon-ready project blueprint (this file)
└── ProjectDevelopmentPortal/     # Frontend + backend workspace (public submodules synced from main repo)
    ├── frontend/                 # React/Next.js civic innovation dashboard
    ├── backend/                  # Node.js/TypeScript API gateway + Hedera SDK jobs
    ├── smart-contracts/          # Solidity smart contracts deployed to Hedera
    └── docs/                     # Supplemental diagrams, UML, and product notes
```

> **Note:** If you cloned this blueprint directly from the `.github` repo snapshot, run `git submodule update --init --recursive` to pull the public subdirectories listed above.

## Architecture Overview
```
+----------------------+        HTTPS / GraphQL        +--------------------------+
|  React Frontend UI   | <----------------------------> |  Node.js API & Services  |
+----------------------+                                +--------------------------+
           |                                                        |
           |  Hedera SDK calls (HTS / HCS / Smart Contract Service)  |
           v                                                        v
    +-----------------------------------------------------------------------+
    |                    Hedera Network & Mirror Nodes                      |
    |  - Token & account state via Mirror Node REST APIs                    |
    |  - Consensus topics for milestone attestations                        |
    |  - Smart contract execution for milestone-based payouts               |
    +-----------------------------------------------------------------------+
```

## Hedera Integration Summary

### Hedera Token Service (HTS)
We chose HTS to tokenize verified development milestones as transferable reward tokens. Each milestone NFT or fungible unit reflects real progress, allowing funders to automate tranche releases. Hedera’s fixed ~$0.001 tokenization fees mean accelerators can onboard dozens of teams without unpredictable operating expenses.

- **Transaction Types:** `TokenCreateTransaction`, `TokenAssociateTransaction`, `TokenMintTransaction`, `TransferTransaction`, `TokenBurnTransaction`
- **Economic Justification:** Minting and transferring milestone tokens on Hedera remains consistently below a cent, enabling community funds to reward micro-achievements across thousands of builders without budget overruns. HTS finality in ~5 seconds keeps incentive loops fast, boosting adoption.

### Hedera Consensus Service (HCS)
We rely on HCS to notarize project updates, mentor reviews, and compliance checks. Each status update becomes an immutable message, guaranteeing a tamper-proof audit trail when multiple government and NGO stakeholders collaborate.

- **Transaction Types:** `TopicCreateTransaction`, `TopicMessageSubmitTransaction`
- **Economic Justification:** HCS messages cost ~$0.0001 each, so daily reporting for hundreds of active projects remains affordable. ABFT finality ensures civil society stakeholders can trust the timeline of interventions even under limited connectivity.

### Hedera Smart Contract Service (HSCS)
Milestone-based disbursements and DAO-style governance rules are handled via smart contracts. HSCS coordinates when milestone tokens unlock fiat-equivalent payouts or grant new permissions to project teams.

- **Transaction Types:** `ContractCreateTransaction`, `ContractExecuteTransaction`, `ContractCallQuery`
- **Economic Justification:** HSCS lets us encode localized funding logic while keeping execution under a fraction of a cent. Deterministic gas pricing and high throughput remove volatility, which is critical for community accelerators planning multi-year budgets.

### Hedera Accounts & Scheduled Transactions
Operational accounts manage multisig treasury actions and scheduled payouts once consensus conditions are met. Scheduled transactions ensure that approvals collected off-chain trigger payments on Hedera without manual intervention.

- **Transaction Types:** `AccountCreateTransaction`, `AccountUpdateTransaction`, `ScheduleCreateTransaction`, `ScheduleSignTransaction`
- **Economic Justification:** Predictable transaction fees let grassroots programs grant co-signing rights to local officials without risking frozen funds. Scheduled transactions remove idle capital costs by releasing funds exactly when deliverables finalize.

## Deployed Hedera Testnet IDs
| Component | Testnet ID | Notes |
|-----------|------------|-------|
| Milestone Token (HTS) | `0.0.4507890` | NFT collection representing validated milestones |
| Governance Token (HTS) | `0.0.4507891` | Fungible utility token for DAO voting |
| Consensus Topic | `0.0.2509876` | Tracks mentor sign-offs & compliance attestations |
| Smart Contract Registry | `0.0.1823400` | Router contract for milestone payouts |
| Treasury Account | `0.0.9834567` | Multi-sig treasury orchestrating scheduled payouts |

> These IDs are configured for Hedera **Testnet**. Production IDs will be published during mainnet rollout.

## Deployment & Setup Instructions
1. **Clone the repository**
   ```bash
   git clone https://github.com/Independent-Impact/ProjectDevelopmentPortal.git
   cd ProjectDevelopmentPortal
   git submodule update --init --recursive
   ```
2. **Install prerequisites**
   - Node.js 20+
   - pnpm 8+ (recommended) or npm/yarn
   - Docker Desktop (optional for local mirror node caching)
3. **Install dependencies**
   ```bash
   cd frontend
   pnpm install

   cd ../backend
   pnpm install
   ```
4. **Configure environment variables**
   - Copy the provided examples:
     ```bash
     cp frontend/.env.example frontend/.env.local
     cp backend/.env.example backend/.env.local
     ```
   - Populate the following variables using the Hedera Testnet credentials provided in the DoraHacks submission notes:
     - `HEDERA_OPERATOR_ID`
     - `HEDERA_OPERATOR_KEY`
     - `HEDERA_MIRROR_NODE_URL`
     - `HEDERA_MILESTONE_TOPIC_ID`
     - `HEDERA_GOV_TOKEN_ID`
5. **Launch the stack (Hedera Testnet)**
   - **Frontend:**
     ```bash
     cd frontend
     pnpm dev
     ```
     Runs at `http://localhost:3000`.
   - **Backend:**
     ```bash
     cd backend
     pnpm start
     ```
     Runs at `http://localhost:4000` with GraphQL and REST endpoints.
   - **Background workers (optional):**
     ```bash
     pnpm worker
     ```
     Executes scheduled Hedera transactions and webhook relays.

6. **Run automated checks**
   ```bash
   pnpm lint
   pnpm test
   pnpm format:check
   ```

## Running Environment Expectations
- Frontend: React/Next.js dev server on **`localhost:3000`**.
- Backend API & webhooks: Node.js/Express on **`localhost:4000`**.
- Workers: Node.js queue processor orchestrating Hedera SDK jobs.
- Hedera Network: Testnet endpoints via `testnet.mirrornode.hedera.com`.

## Security & Secrets
- 🔐 **Never commit** private keys, `.env`, keystore files, or DoraHacks-issued credentials.
- ✅ Include only `.env.example` files documenting required variables.
- 🔑 **Judge Credentials:** Hedera Testnet account ID and private key are supplied in the DoraHacks submission text field. Judges should copy those values into their local `.env.local` files to validate transactions.
- 🛡️ Enable 2FA on all Hedera accounts tied to treasury access. Use multi-party signatures (3-of-5) for treasury and governance wallets.

## Testing & Auditability
- **Linters & Formatters:** ESLint + Prettier configs ensure consistent TypeScript style across frontend and backend packages.
- **Unit & Integration Tests:** Jest + Playwright cover milestone lifecycle, consensus anchoring, and contract execution paths.
- **Continuous Integration:** GitHub Actions (`.github/workflows/ci.yml`) run linting, tests, and security scanning on each pull request.
- **Code Comments:** Complex Hedera SDK flows include inline comments referencing Hedera docs sections, expediting audits for mentors.

## Roadmap
1. **MVP Launch:** Complete DAO governance UI, finalize grant workflows, and onboard first civic innovation cohort.
2. **Analytics Dashboard:** Mirror Node ETL jobs powering KPI dashboards for municipal partners.
3. **Cross-Chain Bridges:** Evaluate HIP-751 connectors for cross-network asset portability.
4. **Impact Credentials:** Integrate verifiable credentials to authenticate training completion and compliance certificates.

## Support & Contact
- **Product Lead:** Eunice Akinyi — eunice@independentimpact.africa
- **Technical Lead:** Chinedu Okeke — chinedu@independentimpact.africa
- **Community:** Join the Hedera Africa Discord (#project-development-portal)

> Built for the DoraHacks Hedera Africa Hackathon to accelerate transparent public-impact innovation across the continent.
