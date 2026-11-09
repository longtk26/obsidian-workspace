---
name: interview
description: Daily fullstack interview practice check-in. Reads Interview/Practice Plan.md and Interview/Practice Log.md, picks today's topic per the rotating cadence, and reports a checklist. Use when the user invokes /interview or asks what to review today for interview prep.
---

# Interview practice daily check-in

Drives the ongoing fullstack interview prep tracked in this vault:
- `Interview/Practice Plan.md` — 10-day rotating cadence + per-track checklists (Backend, Frontend, DevOps Docker, DevOps Kubernetes, System Design, Coding/DS&A, Soft skills).
- `Interview/Practice Log.md` — date/track/topic/confidence log of sessions actually done.

## Steps

1. Read both `Interview/Practice Plan.md` and `Interview/Practice Log.md` in full.
2. Get today's date from context.
3. **Check for an existing entry today.** If `Practice Log.md` already has a row dated today, tell the user that's today's assigned topic (don't reassign). Ask if they want an *additional* session — if so, continue to step 4 treating the cadence position as "one past" the most recent log entry; otherwise stop here.
4. **Determine the next track.** The cadence order is: Backend → Frontend → Coding → DevOps Docker → Frontend → System Design → DevOps Kubernetes → Backend → Coding → Soft skills (repeats). Look at the most recent row in `Practice Log.md` to find the last track covered, then take the next track after it in this sequence. If the log is empty, start at Backend (position 1).
5. **Pick the topic within that track.** In `Practice Plan.md`, find the first unchecked `- [ ]` item under that track's heading. If every item in that track is already checked, instead pick whichever topic under that track has the oldest date (or lowest confidence score) in `Practice Log.md` — i.e. drop into review-lap mode for that track.
6. **Report today's checklist to the user**: the track name, the specific topic (with any wikilinked note it points to), and 1-3 concrete things to actually do/review for it (e.g. "explain X out loud," "solve one problem on Y," "sketch a design for Z"). Keep it short and actionable, not a wall of text.
7. **Log it.** Append a new row to `Practice Log.md` with today's date, the track, and the topic, leaving confidence/notes blank. Tell the user you logged it and that they should fill in a confidence score (1-5) and any notes once they've actually done the session (or offer to fill it in for them if they report back in the same conversation).
8. If, later in the same conversation, the user reports finishing the topic or gives a confidence level, update that day's log row (confidence + notes) and check off the corresponding box in `Practice Plan.md`.

Don't invent topics outside what's in `Practice Plan.md` — if the user wants to add a new topic or track, edit the plan file directly rather than freelancing one in chat.
