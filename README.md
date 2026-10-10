# Full-Cycle Trade CRM

> A self-built export CRM covering lead capture, automatic follow-up scheduling, quotes, contracts, procurement, shipping, after-sales and repurchase reminders — with a restricted API that lets an AI agent work inside it safely.

## Results

| Metric | Number |
|---|---|
| Active customers | 281 (Sep 18, 2026 snapshot); numbering past ZB0390 by Oct 10 |
| Follow-up records | 494, plus 93 timeline entries and 43 audit log entries |
| Lead sources | Trade fairs 231 · AI agent 38 · website 6 · existing clients 5 · LinkedIn 1 |
| Transactions | 6 quotations, 2 orders closed |
| API surface | ~30 actions across 16 SQLite tables |

## The Problem

Contacts from trade fairs, the website, LinkedIn and old clients lived in spreadsheets and chat histories. Who to follow up with — and when — depended on memory, so hot leads went cold.

After a deal closed, quotes, contracts, packing calculations, after-sales tickets and repurchase reminders scattered across more spreadsheets. And letting an AI helper register leads meant giving it dangerous powers — voiding records or rewriting tables had to stay human-only.

## What I Built

**Automatic follow-up cadence by tier:** VIP every 2 days, WATCH every 20, COLD every 30; HOT prospects get golden-day follow-ups on days 3/7/12 after a quote — with the counter automatically pushed past the buyer's national holidays.

**Dual-identity auth with an AI allow-list:** browser sessions get full access via `password_hash`; scripts and the AI agent authenticate with `X-API-Key` + `hash_equals`, but the key can only touch an allow-list (`load`, `list_due`, `log_followup`, `add_customer`, …). Destructive actions — voiding, signing contracts, whole-table rewrites — always return 403 for API callers.

**AI-created leads land in quarantine:** records created via API get status WATCH with source `jason-auto`, surface for human review after 20 days, and are deduplicated by company/name before insert.

**Everything soft-deleted:** voiding only sets flags — no hard deletes anywhere — with a full audit log.

**Zero-friction deployment:** single SQLite file, auto-creates all 16 tables on first visit, lossless schema migration on every connect. Runs on shared hosting (Hostinger) with plain FTP deploys.

## Tech Stack

Native PHP (no framework) · PDO + SQLite · vanilla JavaScript · SheetJS (Excel import/export) · single-file JSON API · Python CLI companion tool (auto-backup before writes)

## API Design (excerpt)

```
GET  api.php?action=list_due       # today's follow-up list
POST api.php?action=add_customer   # dedupe → WATCH + source=jason-auto (API)
POST api.php?action=log_followup   # appends to customer timeline
POST api.php?action=save           # whole-table rewrite — session only, never API
```

## Status

In daily production use since July 2026 at Hangzhou Zhenbao Trading Co., Ltd. A white-label deployment edition is in preparation.

Source code will be added after sanitisation.

## Contact

Open to white-label deployments and custom CRM builds for export businesses.

Maggie Yue · Hangzhou Zhenbao Trading Co., Ltd. · [sales@zhenbaotrading.com](mailto:sales@zhenbaotrading.com)
