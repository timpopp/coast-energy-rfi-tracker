# Coast Energy RFI Tracker

## What this is
A single-file HTML/JS/CSS RFI management app for Coast Energy's NEM 2.0 solar construction program.
No framework, no build step — everything is in `index.html`.
Deployed on GitHub Pages at: https://yourusername.github.io/rfi-tracker

## Data
- localStorage key: `ce_rfis_v9`
- 41 RFIs across 4 projects
- NEM 2.0 deadline: April 14, 2026

## Projects
| Name | Code | Address |
|------|------|---------|
| BioMed PRC (Newark) | BMR-PRC-7999 | 7999 Gateway Boulevard, Newark, CA 94560 |
| AMLI Via Marina (MDR) | AMLI-MDR | 4242 Via Marina, Marina Del Rey, CA 90292 |
| AMLI Broadway (Long Beach) | AMLI-LB | 245 West Broadway, Long Beach, CA 90802 |
| Berkshire (San Jose) | BBE-BER-001-3401 | 3401 Iron Point Drive, San Jose, CA 95134 |

## Key contacts
- **Coast Energy:** Pierre Quincy, Dustin Albanese, Haris Hadzic, Chris Jones, Shai Bergerfroind
- **Morpho Energy (EOR/Designer):** Danny Hinks, Sadia Afrin, Gina Gavagni, Cole Conway
- **BPI (EPC):** Dirk Vollbrecht, Wynand van der Wateren
- **Tim Popp** — CDO at Coast Energy, primary user of this app

## Architecture
Single HTML file. Structure: `<style>` → `<body>` → `<script>`. No build process.

### Key globals
- `DATA` — live array of RFI objects, loaded from localStorage or SEED
- `SEED` — hardcoded default data (41 RFIs)
- `sortField`, `sortDir` — current table sort state
- `selectedId` — currently open drawer RFI id
- `colOrder` — draggable column order, persisted to `ce_col_order_v1`

### Key functions
| Function | Purpose |
|----------|---------|
| `renderDashboard()` | Stat tiles, project cards, priority panel, BIC leaderboard |
| `renderTable()` | RFI log table with dynamic column headers |
| `buildRow(r, ...)` | Single table row, respects colOrder |
| `openDrawer(id)` | Opens RFI detail drawer |
| `buildDrawerDetail(r)` | Returns HTML for Detail tab content |
| `renderAuditLog(r)` | Returns HTML for History tab content |
| `renderAttachments(r)` | Document link cards (Original RFI + Revision slots) |
| `auditLog(r, action, actor, note, meta)` | Appends timestamped entry to r.timeline |
| `switchDrawerTab(id, tab)` | Toggles Detail / History tabs |
| `quickFieldUpdate(id, field, value)` | Inline table field edit with audit log |
| `confirmCloseRFI()` | Close RFI intercept — requires official response |
| `filteredData()` | Applies all active filters and returns sorted rows |
| `sortedData(rows)` | Default open-first sort or column sort |
| `save()` | Writes DATA to localStorage |

### RFI data schema
```js
{
  id: "BER-001-Orig",
  num: "001",
  rev: "Orig",           // "Orig" or "Rev"
  project: "Berkshire (San Jose)",
  site: "Roof Deck",
  disc: "Electrical",
  priority: "Critical",  // Critical, Urgent, High, Medium, Standard
  status: "Closed",      // Open, Closed, Overdue
  bic: "Morpho Energy / Danny Hinks",
  submittedBy: "BPi",
  dateSub: "2026-01-22",
  dateReq: "2026-02-05",
  dateResp: "2026-01-23",
  subject: "Blocking to roof member attachment",
  problem: "...",
  resolution: "...",
  planRev: "No",         // No, Yes, TBD
  costImp: "No",         // No, Yes, TBD, Unknown
  schedImp: "No",        // No, Yes, TBD, Unknown (drives NEM Risk flag)
  drawRef: "",
  attachments: [],       // legacy, unused
  boxLinks: [],          // legacy, unused
  docLinks: [
    { type: "Original RFI", url: "https://...", label: "Original RFI", addedAt: "" },
    { type: "Revision", url: "https://...", label: "Response", addedAt: "" }
  ],
  officialResponse: { text: "...", by: "...", date: "2026-01-23" },
  timeline: [
    {
      date: "2026-01-22",
      ts: "01/22/2026 09:15:30 AM",
      actor: "BPi",
      action: "Submitted",
      note: "...",
      source: "manual",  // manual, system, official
      official: true,    // optional
      meta: { field: "Status", oldVal: "Open", newVal: "Closed" }  // optional
    }
  ]
}
```

## Views
1. **Dashboard** — stat tiles (Total, Open, Closed, Critical Open, NEM Risk, Cost Risk), project cards, "Overdue Critical & At-Risk" panel, BIC leaderboard
2. **RFI Log** — filterable/sortable table with inline editing, draggable columns, collapsible revision rows

## Drawer (RFI detail)
- **Detail tab** — Accountability, official response, resolution timeline, problem/resolution, impact summary, document links
- **History tab** — full audit log newest-first with timestamps, actor, source badge, old→new change pills

## Filter bar (RFI Log)
Row 1: Search | Project | Status | Priority | Discipline | All Companies | Impact | [CSV] [Print]
Row 2: [✕ Reset Filters] [↺ Reset Sort] [↺ Reset Columns]

## Column sorting
Click header to sort ▲, click again to reverse ▼. Active column highlights gold.
Default: open first by RFI #, closed last.
Priority order: Critical → Urgent → High → Medium → Standard

## Document links
Each RFI has an "Original RFI" slot and a "Revision" slot.
Stored in `r.docLinks[]` — type, url, label, addedAt.
Berkshire RFIs fully populated. PRC, MDR, LB still need links.

## Pending next priorities
1. Populate Box links for PRC, MDR, AMLI Broadway from their tracker spreadsheets
2. Airtable or Supabase backend for real multi-user sync
3. Email/notification on BIC reassignment or overdue
4. Role-based access (Coast admin vs. read-only external)
5. VNEM/NEM-A deadline tracking (Feb 2027)

## Deploy process
```bash
git add index.html
git commit -m "description"
git push
# GitHub Pages updates in ~60 seconds
```

## Version history
| Version | Key changes |
|---------|-------------|
| v1-v3 | Excel → basic web app → close modal, official response, print, collapsible revisions |
| v4 | Drag-to-reorder columns, NEM Risk / Cost Risk naming, dashboard priority panel |
| v5 | Company filter, clickable BIC leaderboard, inline dropdowns, clickable stat tiles |
| v6 | Required resolution to close, submittedBy picklist, filter reset, dashboard sort |
| v7 | Document link cards, audit log History tab, sort indicators |
| v8 | Berkshire data audit + corrections, all 22 Box links mapped, clean audit trail |
