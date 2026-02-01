# thunderbird-tbsync-mcp — Usage

This add-on exposes a small localhost HTTP JSON-RPC server to let an agent:
- trigger TbSync sync for a specific EAS account
- create calendar events in Thunderbird calendars
- follow a safer workflow: **pre-sync → local edit → post-sync**

## Prerequisites
- Thunderbird running on the machine where the add-on is installed (remote Ubuntu desktop).
- TbSync installed + enabled: `tbsync@jobisoft.de`
- Provider for Exchange ActiveSync installed + enabled: `eas4tbsync@jobisoft.de`

## Port
- `thunderbird-tbsync-mcp` listens on: **http://localhost:8766**

## Protocol
JSON-RPC 2.0 over HTTP POST.
- List tools: `{"jsonrpc":"2.0","id":1,"method":"tools/list"}`
- Call tool: `{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"...","arguments":{...}}}`

## Quick start

### 1) Verify server is up
```bash
curl -s -X POST http://localhost:8766 \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

### 2) List TbSync accounts (confirm account IDs)
```bash
curl -s -X POST http://localhost:8766 \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/call","params":{"name":"tbsyncListAccounts","arguments":{}}}'
```

Expected users include:
- Cornell: `jl4624@cornell.edu`
- Duke: `jl888@duke.edu`

### 3) List Thunderbird calendars
```bash
curl -s -X POST http://localhost:8766 \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"listCalendars","arguments":{}}}'
```

### 4) Create an all-day event (recommended workflow)
Use `syncAndCreateCalendarEvent` so we trigger TbSync sync before/after the local write.

```bash
curl -s -X POST http://localhost:8766 \
  -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":4,"method":"tools/call","params":{"name":"syncAndCreateCalendarEvent","arguments":{
    "tbsyncUser":"jl4624@cornell.edu",
    "calendarName":"Jiachang Liu Cornell (Calendar)",
    "title":"Cancel CLEAR+ Subscription",
    "description":"Reminder: cancel CLEAR+.",
    "allDay":true,
    "date":"2026-02-08"
  }}}'
```

Notes:
- v0.1.0 supports **all-day events only** (`allDay=true`).
- Date format is **YYYY-MM-DD**.

## Verification (practical)
The most reliable confirmation that the event reached Outlook cloud is that it appears on another synced client (e.g., macOS Thunderbird).

## Safety
- This repo is intended for **calendar operations** via TbSync/EAS.
- Be cautious about creating duplicates; when in doubt, verify on the calendar UI first.
