# Smart Meeting Scheduler & Conflict Resolver

An n8n workflow that receives a meeting request via webhook, checks Google Calendar for conflicts, uses AI (Google Gemini) to suggest 3 alternative time slots when a conflict exists, builds a styled HTML email, and sends it — either the alternatives or a confirmation.

## How it works

1. **Meeting Request (Webhook)** — receives a POST request with `title`, `attendees`, `durationMinutes`, `requestedStart`, `requesterEmail`.
2. **Normalize Request** — cleans and standardizes the incoming payload.
3. **Get Existing Calendar Events** — pulls the requester's Google Calendar events for that day.
4. **Detect Conflict** — checks for time overlap between the request and existing events.
5. **Has Conflict?** (branch)
   - **Yes →** Gemini (`gemini-2.5-flash`) suggests 3 non-overlapping slots → parsed → styled HTML built → **Email: Send Alternatives**.
   - **No →** **Create Calendar Event** → styled HTML built → **Email: Send Confirmation**.
6. **Respond to Webhook** — returns a JSON status.

## Requirements

- [n8n](https://n8n.io/) (self-hosted or cloud)
- Google Calendar OAuth2 credentials
- Google Gemini (PaLM) API credentials
- SMTP credentials (for sending emails)

## Setup

1. Import `workflow.json` into your n8n instance (**Workflows → Import from File**).
2. Reconnect the credential placeholders shown as `REPLACE_WITH_CREDENTIAL_ID`:
   - Google Calendar OAuth2 (used twice)
   - Google Gemini (PaLM) API
   - SMTP (used twice)
3. In the **Get Existing Calendar Events** node, set the calendar to your own account.
4. Update the `fromEmail` field in both "Email: Send" nodes to your own sending address.
5. Activate the workflow and send a test POST request to the webhook URL, e.g.:

```json
{
  "title": "Project Sync",
  "attendees": ["a@example.com", "b@example.com"],
  "durationMinutes": 30,
  "requestedStart": "2026-08-10T10:00:00+06:00",
  "requesterEmail": "you@example.com"
}
```

## Notes

- This repo contains **no real credentials, API keys, or personal account identifiers** — all credential IDs, the workflow/version/instance IDs, and the calendar account were replaced with placeholders before upload. Reconnect everything inside your own n8n instance after import.
- Built as a personal automation/portfolio project using n8n, Google Calendar API, Google Gemini, and SMTP.
