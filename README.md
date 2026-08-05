 # Smart Meeting Scheduler & Conflict Resolver

An n8n workflow that automatically schedules meetings, detects calendar conflicts, and uses AI to suggest alternative time slots — built as part of an **n8n and AI Automation Engineer** course project.

## Overview

This workflow takes a meeting request, checks it against Google Calendar, and:
- **No conflict** → automatically creates the calendar event and sends a styled confirmation email
- **Conflict detected** → uses AI (Google Gemini) to suggest 3 alternative time slots and emails them to the requester

## How It Works

```
Meeting Request (Webhook)
        ↓
  Normalize Request
        ↓
Get Existing Calendar Events (Google Calendar)
        ↓
   Detect Conflict (Code node — checks time overlap)
        ↓
   Has Conflict?
    ↙        ↘
  Yes          No
   ↓            ↓
AI Suggest    Create Calendar Event
Alternative        ↓
Slots (Gemini) Build Confirmation Email HTML (Code node)
   ↓                ↓
Parse AI          Email: Send Confirmation
Suggestions
   ↓
Build Alternatives Email HTML (Code node)
   ↓
Email: Send Alternatives
```

## Features

- ✅ Webhook-triggered meeting intake (title, attendees, time, duration)
- ✅ Real-time Google Calendar conflict detection
- ✅ AI-powered alternative time slot suggestions (Google Gemini)
- ✅ Automatic calendar event creation when no conflict exists
- ✅ Styled HTML email notifications (confirmation or conflict + alternatives), built via dedicated Code nodes for reliable rendering

## Tech Stack

- **n8n** (workflow automation / orchestration)
- **Google Calendar API** (event lookup & creation)
- **Google Gemini** (AI-based conflict resolution & slot suggestion)
- **SMTP / Gmail** (email notifications)

## Setup

1. Import `Smart_Meeting_Scheduler_Conflict_Resolver.json` into your n8n instance
2. Configure credentials:
   - **Google Calendar** (OAuth2) — for reading/creating events
   - **Google Gemini API** — for AI-generated alternative slots
   - **SMTP** — for sending confirmation/alternative emails
3. Activate the workflow and send a test request to the webhook URL, e.g.:

```json
{
  "title": "Project Sync Meeting",
  "requestedStart": "2026-08-10T10:00:00+06:00",
  "durationMinutes": 30,
  "attendees": ["attendee@example.com"],
  "requesterEmail": "you@example.com"
}
```

## Notes

- Gemini's response is parsed from `content.parts[0].text` — this structure differs from OpenAI's `message.content` format, so the parsing node is written specifically for Gemini's API response shape.
- Email HTML for both the confirmation and alternative-slots messages is generated in dedicated Code nodes (rather than inline expressions) to avoid template-literal parsing issues inside n8n's expression editor.

## Project Context

Built as part of the **n8n and AI Automation Engineer** course, demonstrating calendar API integration, conditional workflow logic, AI-assisted decision making, and automated email notifications.
