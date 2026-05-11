# Sources

Two sections: (1) full catalog of all sources DVH has access to; (2) initiative-to-source mapping used by skills to pull updates.

---

## 1. Full Source Catalog

### Slack — Channels

| Channel | Type | ID |
|---------|------|----|
| #advertiser-controls-team | Private | C09CBNQ9H89 |
| #android-performance-weekly-2026 | Private | C0B1405RNBD |
| #mau-2026 | Public | C0APAQ6B1SS |
| #tmp-apis-and-owners | Private | — |
| #searchlight-pm-tpm | Private | — |
| #office-newyork | Public | — |

### Slack — DMs

| Person | Role |
|--------|------|
| Camille Wormser | Eng lead, experiments |
| Jean-Luc Nahon | VP Engineering |
| Guillem Sanchez | PM, Bidding / Android |
| Doug Pereira | PM, Remarketing |
| Ruoyu Zhu | PM, Identity & Signals |
| Santiago Melo | PM, Campaign Excellence |
| Ernest Yip | PM, Campaign Experience |
| Sechan Oh | Eng, IAA / Identity |
| Steven Powers | GDS lead |
| Sasha Clarke | GDS |
| Aparna Bijlani | PM |
| Ernest Yip | PM |
| Eric Wu | — |
| Jimmy Morrow | — |
| Muriel Kao | EA |
| Stephanie Mactas | Chief of Staff, MA Steering |
| Self | Personal notes |

### Slack — Group DMs

| Members |
|---------|
| Ethan Kottke, Steven Powers, Camille Wormser, Jean-Luc Nahon |
| Ethan Kottke, Sasha Clarke, Santiago Melo |
| Emilien Pouradier, Jean-Luc Nahon |
| Ben Jeger, Emilien Pouradier |

---

### Google Docs & Sheets — Team Mechanisms

| Source | Type | URL |
|--------|------|-----|
| Q2 2026 Consolidated Moloco Ads OKRs | Google Doc | https://docs.google.com/document/d/1oTqHfzu05_oVi0YA2U1B_xHWhW8RhaepvV9YXUi7ehA |
| Weekly Product Metrics Review | Google Doc | https://docs.google.com/document/d/1d8g2dsKQJXgbvlcit1DdiLyli6EPa0seEucijWw3kR0 |
| Advertiser Controls & Performance Newsletter | Google Doc | https://docs.google.com/document/d/1wmkc5rT2jQgA3-AsWyxPaKMoWoJdvNkpgx4HaIpw6BI |
| Core LT: Deep Dive Topics | Google Sheet | https://docs.google.com/spreadsheets/d/1T6OFW2QJrHbG4T2R40Rs80k_0b8lotpyno-2qFRM4mY |
| 2026 MA Leadership Meetings — Weekly Calendar (MA Steering) | Google Sheet | Owned by Stephanie Mactas — fetch via Glean search "MA Leadership Meetings Stephanie Mactas" |
| Android Performance Weekly | Google Doc | https://docs.google.com/document/d/181Nm-acW0Q0blxLdmdnA72RtBTXfY92nCify9olIHnE |
| Signals & Identity Strategy Brief | Google Doc | https://docs.google.com/document/d/1g3N51ZBqyDTN1fouC_y57__SzWLZAEtQ4da6fIe8y_8 |

### Notion — Meeting Logs & Databases

| Source | Type | URL |
|--------|------|-----|
| ML/RS/Bidder Quality Weekly Team Sync | Meeting log | https://www.notion.so/moloco/ML-RS-Bidder-Quality-Weekly-Team-Sync-Meeting-log-461303e369654bd4be748cb36a6c4313 |
| Strategic Priorities DB | Notion DB | https://www.notion.so/f551ee8631a54c488c395dae5acf0875 |

### Tools

| Source | Type | URL |
|--------|------|-----|
| Explab | Experiment tracker | https://explab-prod.adsmoloco.com |

---

## 2. Initiative-to-Source Mapping

Used by skills to know where to pull updates for each strategic priority.

| Priority | Primary Sources |
|----------|----------------|
| tKPI GA Launch | Q2 2026 OKRs, Weekly Product Metrics Review, #advertiser-controls-team |
| IAA GA Launch | Q2 2026 OKRs, Weekly Product Metrics Review, #advertiser-controls-team |
| Remarketing $160M GAS ARR | Q2 2026 OKRs, DM: Doug Pereira, DM: Guillem Sanchez |
| Bidding Control Tower | #android-performance-weekly-2026, Android Performance Weekly doc, ML/RS/Bidder Quality Weekly Sync |
| Campaign Excellence / Blueprint | Q2 2026 OKRs, DM: Santiago Melo, Group DM: Ethan Kottke, Sasha Clarke, Santiago Melo |
| Simplified Campaign Experience / Next-Gen Ads Manager | Q2 2026 OKRs, DM: Ernest Yip |
| Event SDK MVP | Signals & Identity Strategy Brief, DM: Ruoyu Zhu |
| Person & Household Identity Graph | Signals & Identity Strategy Brief, ML/RS/Bidder Quality Weekly Sync, DM: Ruoyu Zhu |
| pLTV Alpha + Signal Utilization Infra | Signals & Identity Strategy Brief, Explab, ML/RS/Bidder Quality Weekly Sync |
| Key Experiments | Explab, ML/RS/Bidder Quality Weekly Sync, DM: Camille Wormser |
| Modeling Progress | Explab, ML/RS/Bidder Quality Weekly Sync, DM: Camille Wormser |

---

## Adding a New Source

When adding a new priority via `/strategic-priorities`:
1. Check Section 1 — does the source already exist?
2. If yes: reference it in Section 2 only (no new row in Section 1).
3. If no: add a row to the appropriate Section 1 table, then reference it in Section 2.
4. Never duplicate rows. If a source covers multiple initiatives, list it once in Section 1 and reference it in multiple rows of Section 2.
