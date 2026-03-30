# Prisma Seed Scripts

This document describes every seed and utility script in the `prisma/` directory, how to run each one, and what it does.

All scripts are executed with `ts-node` (or via the `pnpm` shortcuts defined in `package.json`).

---

## Table of Contents

- [Core Setup Scripts](#core-setup-scripts)
- [Project Dev Settings Scripts](#project-dev-settings-scripts)
- [Dashboard Data Source Scripts](#dashboard-data-source-scripts)
- [Utility Scripts](#utility-scripts)

---

## Core Setup Scripts

### `seed.user.ts`

**Purpose:** Seeds the initial database with default roles, permissions, a default admin user, and their auth/user-role associations.

**Seeded data:**
- Roles: `Admin`, `Manager`, `User`, `Vendor`
- Permissions: `Admin → manage:all`, `Manager → manage:user`, `Vendor → manage:user`, `User → read:user`
- Default user: `Rumsan Admin` (`rumsan@mailinator.com`)

**Run:**
```bash
pnpm seed
# or
ts-node prisma/seed.user.ts
```

---

### `seed.settings.ts`

**Purpose:** Interactively seeds SMTP email configuration. Prompts for `SMTP_USER` and `SMTP_PASSWORD`, then stores the settings as the `SMTP` record in the database.

**Seeded setting key:** `SMTP`

**Fields stored:** `HOST`, `PORT`, `SECURE`, `USERNAME`, `PASSWORD`

**Run:**
```bash
pnpm seed:setup
# or
ts-node prisma/seed.settings.ts
```

You will be prompted:
```
Please enter SMTP_USER: <your-smtp-user>
Please enter SMTP_PASSWORD: <your-smtp-password>
Do you want to proceed? (Y/n)
```

---

### `seed.chainsettings.ts`

**Purpose:** Seeds blockchain network configuration from environment variables. Creates or updates two settings: `CHAIN_SETTINGS` (chain metadata) and `SUBGRAPH_URL`.

**Required `.env` variables:**
| Variable | Description |
|---|---|
| `CHAIN_ID` | Chain ID (e.g. `1337`) |
| `CHAIN_NAME` | Network name (e.g. `localhost`) |
| `CHAIN_TYPE` | Chain type (default: `evm`) |
| `NETWORK_PROVIDER` | RPC URL |
| `BLOCK_EXPLORER_URL` | Block explorer URL (default: `https://etherscan.io`) |
| `CURRENCY_NAME` | Currency name (e.g. `Ether`) |
| `CURRENCY_SYMBOL` | Currency symbol (e.g. `ETH`) |
| `SUBGRAPH_URL` | Subgraph endpoint URL |

**Run:**
```bash
pnpm seed:chainsettings
# or
ts-node prisma/seed.chainsettings.ts
```

---

### `seed.navSettings.ts`

**Purpose:** Seeds the UI navigation menu configuration as the `NAV_SETTINGS` setting. Defines the primary nav links (Dashboard, Project, Beneficiaries, Communications) and sub-nav links (Vendors, Users).

**Seeded setting key:** `NAV_SETTINGS`

**Run:**
```bash
ts-node prisma/seed.navSettings.ts
```

---

### `seed.communication-settings.ts`

**Purpose:** Seeds the communication/SMS broadcast configuration as the `COMMUNICATION` setting. Configures the Rumsan Connect broadcast API, app ID, transport, and message template.

**Seeded setting key:** `COMMUNICATION`

**Run:**
```bash
ts-node prisma/seed.communication-settings.ts
```

> Note: Review the hardcoded `APP_ID` and `SMS_TRANSPORT_ID` values in the file before running in a new environment.

---

### `seed.offramp.ts`

**Purpose:** Seeds the off-ramp payment provider configuration as the `OFFRAMP_SETTINGS` setting. If the setting already exists it is deleted and re-created. Can also be imported and called as a function from other seed scripts.

**Optional `.env` variables:**
| Variable | Default |
|---|---|
| `PAYMENT_PROVIDER_URL` | `https://api-offramp-dev.rahat.io/v1` |
| `PAYMENT_PROVIDER_APP_ID` | `1234567890` |

**Run:**
```bash
ts-node prisma/seed.offramp.ts
```

---

## Project Dev Settings Scripts

These scripts seed private blockchain credentials (private key + admin accounts) for each project type into the database. They all share the same pattern:

1. Accept a **path argument** pointing to a directory that contains:
   - `.env` — updated with the extracted private key
   - `accounts.json` — exported Ganache account data
2. Extract the first private key and first two admin accounts from `accounts.json`.
3. Write the private key back into the project's `.env` file.
4. Upsert the setting record in the database.

**Usage pattern:**
```bash
pnpm <script-name> <path-to-project-directory>
```

---

### `seed.eldevsettings.ts`

**Purpose:** Seeds `EL_DEV` settings for the Electronic Ledger (EL) project.

**Seeded setting key:** `EL_DEV`

**Run:**
```bash
pnpm seed:eldevsettings /path/to/el-project
# or
ts-node prisma/seed.eldevsettings.ts /path/to/el-project
```

---

### `seed.aadevsettings.ts`

**Purpose:** Seeds `AA_DEV` settings for the Anticipatory Action (AA) project. Updates only `PRIVATE_KEY` in the project's `.env`.

**Seeded setting key:** `AA_DEV`

**Run:**
```bash
pnpm seed:aadevsettings /path/to/aa-project
# or
ts-node prisma/seed.aadevsettings.ts /path/to/aa-project
```

---

### `seed.c2cdevsettings.ts`

**Purpose:** Seeds `C2C_DEV` settings for the Cash to Code (C2C) project. Updates both `PRIVATE_KEY` and `DEPLOYER_PRIVATE_KEY` in the project's `.env`.

**Seeded setting key:** `C2C_DEV`

**Run:**
```bash
pnpm seed:c2cdevsettings /path/to/c2c-project
# or
ts-node prisma/seed.c2cdevsettings.ts /path/to/c2c-project
```

---

### `seed.cvadevsettings.ts`

**Purpose:** Seeds `CVA_DEV` settings for the Cash & Voucher Assistance (CVA) project. Updates both `PRIVATE_KEY` and `DEPLOYER_PRIVATE_KEY` in the project's `.env`.

**Seeded setting key:** `CVA_DEV`

**Run:**
```bash
pnpm seed:cvadevsettings /path/to/cva-project
# or
ts-node prisma/seed.cvadevsettings.ts /path/to/cva-project
```

---

### `seed.rpdevsettings.ts`

**Purpose:** Seeds `RP_DEV` settings for the Rahat Platform (RP) project. Updates both `PRIVATE_KEY` and `DEPLOYER_PRIVATE_KEY` in the project's `.env`.

**Seeded setting key:** `RP_DEV`

**Run:**
```bash
pnpm seed:rpdevsettings /path/to/rp-project
# or
ts-node prisma/seed.rpdevsettings.ts /path/to/rp-project
```

---

### `seed.kenyadevsettings.ts`

**Purpose:** Seeds `KENYA_DEV` settings for the Kenya deployment. Updates both `PRIVATE_KEY` and `DEPLOYER_PRIVATE_KEY` in the project's `.env`.

**Seeded setting key:** `KENYA_DEV`

**Run:**
```bash
pnpm seed:kenyadevsettings /path/to/kenya-project
# or
ts-node prisma/seed.kenyadevsettings.ts /path/to/kenya-project
```

---

### `seed.cambodiadevsettings.ts`

**Purpose:** Seeds `CAMBODIA_DEV` settings for the Cambodia deployment. Updates both `PRIVATE_KEY` and `DEPLOYER_PRIVATE_KEY` in the project's `.env`.

**Seeded setting key:** `CAMBODIA_DEV`

**Run:**
```bash
pnpm seed:cambodiadevsettings /path/to/cambodia-project
# or
ts-node prisma/seed.cambodiadevsettings.ts /path/to/cambodia-project
```

---

## Dashboard Data Source Scripts

These scripts seed dashboard chart layout configurations into the `Stats` table. Each configuration defines data sources (API URLs) and UI component mappings (charts, data cards, maps).

There are two variants: **interactive** (prompts via stdin) and **args-based** (arguments passed on the command line).

---

### `seed.add-datasource.ts` *(interactive)*

**Purpose:** Seeds a global dashboard source (`DASHBOARD_SOURCE`) with beneficiary stats charts (banked status, gender, internet/phone status).

**Prompts:** `Enter URL:`

**Run:**
```bash
ts-node prisma/seed.add-datasource.ts
```

---

### `seed.add-datasource-args.ts` *(CLI args)*

**Purpose:** Same as `seed.add-datasource.ts` but accepts the URL as a command-line argument instead of prompting.

**Run:**
```bash
ts-node prisma/seed.add-datasource-args.ts <api-base-url>
# Example:
ts-node prisma/seed.add-datasource-args.ts http://localhost:5500
```

---

### `seed.add-project-datasource.ts` *(interactive)*

**Purpose:** Seeds a per-project dashboard source (`DASHBOARD_SOURCE_<uuid>`) scoped to a specific project's stats endpoint. Includes beneficiary stats charts.

**Prompts:** `Enter project UUID:`, `Enter URL:`

**Run:**
```bash
ts-node prisma/seed.add-project-datasource.ts
```

---

### `seed.add-project-ds-args.ts` *(CLI args)*

**Purpose:** Same as `seed.add-project-datasource.ts` but uses CLI arguments. Seeds an EL-style per-project dashboard source with voucher/redemption/reimbursement stats.

**Run:**
```bash
ts-node prisma/seed.add-project-ds-args.ts <projectUUID> <api-base-url>
# Example:
ts-node prisma/seed.add-project-ds-args.ts abc-123 http://localhost:5500
```

---

### `seed.add-aadatasource.ts` *(interactive)*

**Purpose:** Seeds the Anticipatory Action (AA) project dashboard source (`AA_DASHBOARD_SOURCE`). Includes beneficiary summary charts (gender donut, caste pie, vulnerability bar, phone/bank status, GPS map).

**Prompts:** `Enter URL:`

**Run:**
```bash
ts-node prisma/seed.add-aadatasource.ts
```

---

### `seed.add-c2c-datasource.ts` *(interactive)*

**Purpose:** Seeds a C2C per-project dashboard source (`DATA_SOURCE`) with gender, age, and disbursement method charts. Uses both a beneficiary stats source and a project actions source for reporting.

**Prompts:** `Enter project UUID:`, `Enter URL:`

**Run:**
```bash
ts-node prisma/seed.add-c2c-datasource.ts
```

---

### `seed.add-coredatasource.ts` *(interactive)*

**Purpose:** Seeds a core/generic dashboard source (`DASHBOARD_SOURCE`) with total beneficiary count, gender, phone availability, bank status, and GPS map charts.

**Prompts:** `Enter URL:`

**Run:**
```bash
ts-node prisma/seed.add-coredatasource.ts
```

---

### `seed.add-elKenyadatasource.ts` *(interactive)*

**Purpose:** Seeds a per-project EL Kenya dashboard source (`DASHBOARD_SOURCE_<uuid>`) with beneficiary counts, voucher assignment/redemption, vendor counts, and donut charts.

**Prompts:** `Enter project UUID:`, `Enter URL:`

**Run:**
```bash
ts-node prisma/seed.add-elKenyadatasource.ts
```

---

### `seed.add-el-keyna-args-datasource.ts` *(CLI args)*

**Purpose:** Same as `seed.add-elKenyadatasource.ts` but accepts arguments from the command line instead of prompting. Uses only project-scoped stats (single data source).

**Run:**
```bash
ts-node prisma/seed.add-el-keyna-args-datasource.ts <projectUUID> <api-base-url>
```

---

### `seed.add-el-sms-voucher-args-datasource.ts` *(CLI args)*

**Purpose:** Seeds a per-project EL SMS Voucher dashboard source (`DASHBOARD_SOURCE_<uuid>`) with consumer/vendor counts, voucher usage type, glass purchase type, consent, and age group charts.

**Run:**
```bash
ts-node prisma/seed.add-el-sms-voucher-args-datasource.ts <projectUUID> <api-base-url>
```

---

### `seed.add-kenya-coredatasource.ts` *(interactive)*

**Purpose:** Seeds a Kenya core dashboard source (`DASHBOARD_SOURCE`) similar to the AA datasource but without the map widget. Includes phone availability, bank status, gender, caste, and vulnerability charts.

**Prompts:** `Enter URL:`

**Run:**
```bash
ts-node prisma/seed.add-kenya-coredatasource.ts
```

---

### `seed.add-cambodiadatasource.ts` *(interactive)*

**Purpose:** Seeds a Cambodia project dashboard source (`DASHBOARD_SOURCE_<uuid>`) using the Cambodia-specific `cambodia.app.stats` action. Includes wearers, referrals, eye checkups, health workers, vision centers, eyewear dispensed, and conversion type charts.

**Prompts:** `Enter project UUID:`, `Enter URL:`

**Run:**
```bash
ts-node prisma/seed.add-cambodiadatasource.ts
```

---

## Utility Scripts

### `getAuthSessions.ts`

**Purpose:** Queries all users with the `Admin` role, collects their auth service records and login logs, and writes the result to `authSessions.json` in the project root.

**Output file:** `authSessions.json`

**Run:**
```bash
ts-node prisma/getAuthSessions.ts
```

---

### `delete-benef-vendor.ts`

**Purpose:** Utility script for deleting all beneficiaries and/or vendors associated with a specific project. **The `PROJECT_ID` constant must be set manually in the file before running.**

> This script is destructive. Set `PROJECT_ID` in the file and uncomment the relevant function call(s) before executing.

**Run:**
```bash
ts-node prisma/delete-benef-vendor.ts
```
