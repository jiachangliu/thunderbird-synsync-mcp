# thunderbird-tbsync-mcp — Troubleshooting

## Port 8766 not listening
Symptoms:
- `ss -ltnp | grep 8766` shows nothing
- curl fails: `curl: (7) Failed to connect`

Fix checklist:
1) Ensure Thunderbird is running on the remote Ubuntu machine.
2) Restart Thunderbird (required after XPI updates).
3) Ensure the add-on is enabled in Thunderbird Add-ons Manager.
4) Check `/tmp/thunderbird.log` for WebExtensions / server startup logs.

## Calls hang / never return
Some calendar/TbSync operations can block longer than expected depending on the provider.

Recommended approach:
- Treat the call as “may still be processing”, and verify in the Thunderbird calendar UI and/or on another client (macOS Thunderbird).
- Avoid repeatedly re-running create calls (risk of duplicate events).

If you must proceed programmatically:
- Prefer non-destructive operations first (`tbsyncListAccounts`, `listCalendars`).
- If a create call hangs, do **not** assume failure; check the calendar UI.

## "TbSync is not installed/enabled" / "EAS provider not installed/enabled"
This add-on hard-requires:
- TbSync: `tbsync@jobisoft.de`
- Provider for Exchange ActiveSync: `eas4tbsync@jobisoft.de`

Install/enable them in Thunderbird, then restart Thunderbird.

## Calendar not found
Use `listCalendars` and copy the exact `calendarName` (or use `calendarId`).

## Cloud sync uncertainty
TbSync sync triggers are not guaranteed to be fully completed before the tool returns.

Best practice:
- Use `syncAndCreateCalendarEvent`.
- Confirm cloud propagation by checking from another client.
