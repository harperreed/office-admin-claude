# CC-Office Plugin Testing Guide

This comprehensive guide covers testing procedures for the cc-office plugin, from pre-installation verification through advanced integration testing.

## Table of Contents

1. [Pre-Installation Checklist](#pre-installation-checklist)
2. [Installation Testing](#installation-testing)
3. [Functional Testing](#functional-testing)
   - [Onboarding Flow](#onboarding-flow)
   - [Email Management](#email-management)
   - [Calendar Management](#calendar-management)
   - [CRM Operations](#crm-operations)
4. [Integration Testing](#integration-testing)
5. [Performance Testing](#performance-testing)
6. [Common Issues and Debugging](#common-issues-and-debugging)
7. [Test Environment Setup](#test-environment-setup)

---

## Pre-Installation Checklist

Before installing cc-office, verify these prerequisites:

### 1. Claude Code Environment

```bash
# Verify Claude Code is installed and working
claude --version

# Check you can access Claude Code CLI
which claude
```

**Expected:** Claude Code version output and valid path.

### 2. Required MCP Servers

#### Gmail MCP Server (Required)

```bash
# Check settings.json contains Pipedream MCP server
cat ~/.claude/settings.json | jq '.mcpServers.pd'

# Should output:
# {
#   "command": "npx",
#   "args": ["-y", "@pipedream/mcp-server@latest"]
# }
```

**Test Gmail OAuth:**
```
In Claude Code: "List my unread emails"
```
**Expected:** Email list or OAuth prompt to authenticate.

#### Google Calendar MCP Server (Required)

**Test Calendar OAuth:**
```
In Claude Code: "Show my calendar for today"
```
**Expected:** Today's calendar events or OAuth prompt.

#### Pagen CRM MCP Server (Optional)

```bash
# If using CRM, verify database is accessible
psql pagen_crm -c "SELECT COUNT(*) FROM contacts;"

# Check MCP server config exists
cat ~/.claude/settings.json | jq '.mcpServers.pagen'
```

**Test CRM connection:**
```
In Claude Code: "Search for contacts"
```
**Expected:** Contact list or message that CRM is not configured.

### 3. Permissions Verification

**Gmail Scopes Required:**
- `gmail.readonly` - Read emails
- `gmail.send` - Send emails
- `gmail.compose` - Create drafts
- `gmail.labels` - Manage labels
- `gmail.modify` - Archive/mark as read

**Calendar Scopes Required:**
- `calendar.events` - Create and read events
- `calendar.readonly` - Read calendar
- `calendar.freebusy` - Check availability

**Verify permissions:**
1. Go to https://myaccount.google.com/permissions
2. Find "Claude Code" or "Pipedream" app
3. Verify all required scopes are granted

### 4. File System Access

```bash
# Verify write access to config directory
touch ~/.claude/test-write && rm ~/.claude/test-write
echo $?

# Should output: 0 (success)

# Verify Claude can access home directory
ls -la ~/.claude/
```

**Expected:** Directory listing without errors.

---

## Installation Testing

### Step 1: Add Plugin Marketplace

```bash
# Add the cc-office plugin marketplace
/plugin marketplace add harperreed/office-admin-claude
```

**Expected output:**
```
✓ Added marketplace: harperreed/office-admin-claude
```

**Verify:**
```bash
/plugin marketplace list
```
Should show `harperreed/office-admin-claude` in the list.

### Step 2: Install Plugin

```bash
/plugin install cc-office
```

**Expected output:**
```
✓ Installing cc-office plugin...
✓ Plugin installed successfully
✓ 4 skills registered
✓ 3 commands registered
```

**Verify installation:**
```bash
/plugin list
```

Should show:
```
✓ cc-office (v1.0.0) - active
  Skills: 4
  Commands: 3
```

### Step 3: Verify Skills Registration

Test that skills are registered:

```bash
# List available skills
/skill list
```

**Expected skills:**
- `cc-office:onboarding`
- `cc-office:email-management`
- `cc-office:calendar-management`
- `cc-office:crm-management`

### Step 4: Verify Commands Registration

```bash
# List available commands
/command list
```

**Expected commands:**
- `/setup-cc-office`
- `/triage-email`
- `/draft-email`

### Step 5: Restart Claude Code

```bash
# Restart Claude Code completely
# (Method depends on how you run Claude Code)
```

**Verify after restart:**
```bash
/plugin list
```
cc-office should still show as active.

---

## Functional Testing

### Onboarding Flow

#### Test 1: Initial Configuration

**Command:**
```bash
/setup-cc-office
```

**Test Steps:**
1. Respond to all onboarding questions
2. Verify each question makes sense
3. Provide diverse answers (test edge cases)

**Expected Interaction:**
```
Assistant: I'm setting up your cc-office productivity style. Let's start with some basics.

What should I call you?
> Test User

What's your timezone?
> America/Chicago

What are your typical working hours?
> 9:00am - 5:00pm, lunch 12:00-1:00pm

[... continues through all preference questions ...]

✓ Configuration saved to ~/.claude/cc-office-config.json
✓ Style guide created at ~/.claude/docs/cc-office-style.md
```

**Verify Config File:**
```bash
# Check config was created
cat ~/.claude/cc-office-config.json | jq .

# Verify structure
cat ~/.claude/cc-office-config.json | jq 'keys'
# Should show: ["version", "personal", "email", "calendar", "crm"]
```

**Verify Style Guide:**
```bash
cat ~/.claude/docs/cc-office-style.md
```
Should contain personalized style examples.

#### Test 2: Update Existing Configuration

**Command:**
```bash
/setup-cc-office
```

**Test Steps:**
1. Run onboarding again
2. Change some preferences
3. Verify old config is preserved/updated correctly

**Expected:**
- Assistant detects existing config
- Offers to update or start fresh
- Updates config file correctly

**Verify:**
```bash
# Check updated timestamp
ls -l ~/.claude/cc-office-config.json

# Verify changes were applied
cat ~/.claude/cc-office-config.json | jq '.email.tone'
```

#### Test 3: Configuration Validation

**Test invalid configs:**

```bash
# Create invalid JSON
echo "invalid json" > ~/.claude/cc-office-config.json

# Try to use cc-office
/triage-email
```

**Expected:**
- Clear error message about invalid config
- Suggestion to re-run `/setup-cc-office`

---

### Email Management

#### Test 4: Find Email Thread

**Natural language:**
```
Find the email from Mike about the project
```

**Expected:**
- Search results showing relevant email(s)
- Display: sender, subject, date, snippet
- Thread ID and message ID captured

**Verify:**
```
Assistant: Found email:
From: Mike <mike@example.com>
Subject: Project Discussion
Date: Dec 1, 2025
Thread ID: 19a5fc252ad4dd3a
Message ID: 19a711957d96874d

[Email content preview...]
```

#### Test 5: Draft Simple Reply (Ultra-Terse Style)

**Setup:** Configure ultra-terse email style in onboarding

**Command:**
```
Draft a reply to Mike saying that time works
```

**Expected Flow:**
1. Assistant finds the original email
2. Extracts thread ID, message ID, recipient email
3. Drafts ultra-terse reply matching user's style
4. Creates as DRAFT (not sent)
5. Confirms threading

**Expected Output:**
```
Assistant: Found Mike's email. Drafting reply...

Created draft reply:
To: mike@example.com
Subject: Re: Project Discussion
Thread: 19a5fc252ad4dd3a

---
that works perfect
---

✓ Saved as draft (not sent)
✓ Threaded correctly in conversation
```

**Verify in Gmail:**
1. Open Gmail drafts
2. Find the draft
3. Verify it appears in the correct thread
4. Check recipient is correct (not @example.com)

#### Test 6: Draft Reply with Calendar Event

**Command:**
```
Draft a reply to Sarah's lunch invite saying I'm free and add it to my calendar
```

**Expected Flow:**
1. Find Sarah's email
2. Extract event details (date, time, location)
3. Check calendar for conflicts
4. Report availability
5. Draft reply in user's style
6. Add event to calendar
7. Provide calendar link

**Expected Output:**
```
Assistant: Found Sarah's email about lunch on Dec 5.

Checking your calendar...
Your schedule on Dec 5:
- 10:00 AM - 12:00 PM: Team Meeting
- 12:00 PM - 1:30 PM: FREE
- 2:00 PM - 4:00 PM: Client Call

You're free for lunch! Adding to calendar...

✓ Created draft reply: "that works perfect. 12:30 at soho house?"
✓ Added to calendar: "Lunch with Sarah at Soho House" - Dec 5, 12:30-1:30 PM
✓ Calendar link: [link]
```

**Verify:**
```bash
# Check calendar event was created
# In Claude Code:
Show my calendar for Dec 5
```

#### Test 7: Threading Verification

**Command:**
```
Reply to the most recent email in my inbox
```

**Test Cases:**
- Draft must be threaded to original conversation
- Subject line should maintain "Re:" format
- Thread ID must match original
- In-Reply-To header must be set

**Verify Threading:**
1. Create draft
2. Check in Gmail UI that draft appears in conversation
3. Verify subject line format
4. Check recipient is correct

**Common Threading Failure:**
```
# If draft appears as standalone (not in thread):
❌ FAIL - Threading broken
```

**Debug:**
- Check thread ID was captured
- Check message ID was captured
- Verify both were passed to draft creation

#### Test 8: Email Style Matching

**Test different styles:**

**Ultra-Terse:**
```
Configuration: {"email": {"tone": "ultra-terse", "signature": "none"}}
Expected: "sure" or "that works"
```

**Conversational:**
```
Configuration: {"email": {"tone": "conversational", "signature": "minimal"}}
Expected: "Hey! That sounds great. Looking forward to it.\n\nBest,"
```

**Professional:**
```
Configuration: {"email": {"tone": "professional", "signature": "full"}}
Expected: "Dear [Name],\n\nThank you for your email...\n\n[Full signature]"
```

**Verify:**
- Draft matches configured tone
- Signature presence/absence is correct
- Response length matches preferences
- Uses patterns from config

#### Test 9: Bulk Email Triage

**Command:**
```bash
/triage-email
```

**Expected Flow:**
1. Fetch unread emails
2. Categorize each:
   - ACTION NEEDED
   - CALENDAR
   - ARCHIVE
3. Draft replies for ACTION NEEDED
4. Add events for CALENDAR
5. Present comprehensive summary

**Expected Output:**
```
Assistant: Analyzing 15 unread emails...

## 📧 Inbox Triage Summary

### 🔴 ACTION NEEDED (3)
1. **From:** Mike | **Subject:** Quick sync?
   **Summary:** Wants to schedule call
   **Draft:** Created - "how about 2pm est tomorrow?"

2. **From:** Sarah | **Subject:** Proposal review
   **Summary:** Needs feedback by Friday
   **Draft:** Created - "will review by thursday"

3. **From:** Client | **Subject:** Invoice question
   **Summary:** Asking about payment terms
   **Draft:** Created - "net 30. sent via stripe"

### 📅 CALENDAR (2)
1. **From:** Team | **Subject:** Q1 Planning
   **Event:** Dec 7, 10:00 AM - 11:30 AM
   **Status:** ✓ Added (no conflicts)

2. **From:** Vendor | **Subject:** Demo invitation
   **Event:** Dec 10, 2:00 PM - 3:00 PM
   **Status:** ⚠️ Conflicts with "Client Call"

### 📋 ARCHIVE (10)
- Newsletters (5)
- Receipts (3)
- Notifications (2)

All drafts saved for your review.
```

**Verify:**
1. Check Gmail drafts - should have 3 drafts
2. Check calendar - should have 1-2 new events
3. Verify categorization accuracy

#### Test 10: Missing To: Field Error

**Intentionally create error condition:**

```bash
# Manually test draft creation without recipient
# (This tests error handling)
```

**Expected:**
- Clear error message about missing recipient
- Suggestion to find original email
- Does NOT create draft with @example.com

**Correct Behavior:**
```
Assistant: I need the recipient's email address. Let me find the original email...
[Searches and finds recipient]
Now creating draft with correct To: field.
```

---

### Calendar Management

#### Test 11: Check Calendar Availability

**Command:**
```
Am I free tomorrow at 2pm?
```

**Expected:**
1. Load user's timezone from config
2. Query calendar for tomorrow 2pm
3. Check for conflicts
4. Report schedule

**Expected Output:**
```
Assistant: Checking your calendar for tomorrow (Dec 3) at 2:00 PM...

Your schedule:
- 1:00 PM - 2:00 PM: Team Standup
- 2:00 PM - 2:00 PM: FREE
- 3:00 PM - 4:00 PM: Client Call

You're free at 2pm!
```

#### Test 12: Add Event with Conflict Detection

**Command:**
```
Add a meeting with John tomorrow at 2pm
```

**Expected Flow:**
1. Check calendar for tomorrow 2pm
2. Detect any conflicts
3. Report conflicts to user
4. Ask for confirmation
5. Add event if confirmed

**With Conflict:**
```
Assistant: That time conflicts with "Client Call" (2:00-3:00 PM).

Should I:
1. Add anyway (double-booking)
2. Suggest alternative time
3. Cancel current task
```

**Without Conflict:**
```
Assistant: You're free at 2pm tomorrow. Adding event...

✓ Added: "Meeting with John" - Dec 3, 2:00-2:30 PM
```

#### Test 13: Timezone Handling

**Command:**
```
Schedule a call with someone in London at 9am my time
```

**Expected:**
1. Load user's timezone (e.g., America/Chicago)
2. Calculate London time (e.g., 3pm GMT)
3. Include both timezones in event
4. Save with correct timezone

**Expected Output:**
```
Assistant: Scheduling for 9:00 AM America/Chicago (3:00 PM London time)

✓ Added: "Call with [Name]" - Dec 3, 9:00-9:30 AM CST
Description: 9:00 AM Chicago / 3:00 PM London
```

**Verify:**
```bash
# Check event in calendar
Show me details of that event
```

Should show correct timezone conversion.

#### Test 14: Tentative Event Creation

**Command:**
```
Mike wants to meet next week but it's not confirmed yet
```

**Expected:**
1. Create event with "HOLD:" prefix
2. Mark as tentative in description
3. Easy to find and update later

**Expected Output:**
```
Assistant: Creating tentative hold...

✓ Added: "HOLD: Meeting with Mike (pending confirmation)"
Date: [Next available time based on working hours]
Status: Tentative - update once confirmed
```

#### Test 15: Working Hours Validation

**Command:**
```
Add a meeting at 6pm tomorrow
```

**Expected:**
1. Check working hours from config (e.g., 9am-5pm)
2. Notice 6pm is outside working hours
3. Warn user

**Expected Output:**
```
Assistant: Note: 6:00 PM is outside your typical working hours (9:00 AM - 5:00 PM).

Should I:
1. Add anyway (occasional late meeting)
2. Suggest alternative time during work hours
```

#### Test 16: Event Extraction from Email

**Command:**
```
Check my inbox for any events I need to add
```

**Expected:**
1. Scan recent emails
2. Extract event information:
   - Date/time mentions
   - Location references
   - Meeting invitations
3. Offer to add each event

**Expected Output:**
```
Assistant: Found 2 potential events:

1. From Mike: "Lunch next Tuesday at Soho House"
   Detected: Dec 5, 12:00 PM at Soho House
   Add to calendar?

2. From Team: "All-hands next Friday 10am"
   Detected: Dec 8, 10:00 AM
   Add to calendar?
```

---

### CRM Operations

**Note:** These tests require Pagen CRM MCP server configured.

#### Test 17: Check for Duplicate Contact

**Command:**
```
Add John Doe from Acme Corp to my CRM
```

**Expected Flow:**
1. Search for "John Doe"
2. Search for "john" + "acme"
3. If found, report existing contact
4. If not found, proceed with adding

**If Duplicate Exists:**
```
Assistant: Found existing contact:
Name: John Doe
Email: john@acme.com
Company: Acme Corp
Added: Nov 15, 2025

This appears to be the same person. Update existing record instead?
```

**If Not Found:**
```
Assistant: No existing contact found. Adding John Doe...
[Proceeds with adding]
```

#### Test 18: Add Contact with Company Association

**Command:**
```
Add Jane Smith, jane@bigcorp.com, works at BigCorp
```

**Expected Flow:**
1. Check if Jane Smith exists (no)
2. Check if BigCorp exists (no)
3. Add BigCorp company first
4. Add Jane Smith with company association
5. Confirm addition

**Expected Output:**
```
Assistant: Checking for existing contact... Not found.
Checking for company BigCorp... Not found.

Adding company: BigCorp
✓ Company added

Adding contact: Jane Smith
- Email: jane@bigcorp.com
- Company: BigCorp

✓ Contact added
ID: abc-123-def-456
```

**Verify:**
```bash
# Search for contact
Search for Jane Smith in CRM
```

Should show Jane associated with BigCorp.

#### Test 19: Log Interaction

**Command:**
```
Log that I had coffee with Jane today, discussed AI strategy
```

**Expected Flow:**
1. Find contact "Jane"
2. Create interaction log
3. Timestamp correctly
4. Store note

**Expected Output:**
```
Assistant: Logging interaction with Jane Smith...

✓ Interaction logged:
Contact: Jane Smith (BigCorp)
Date: Dec 2, 2025
Note: Had coffee, discussed AI strategy
```

#### Test 20: Detail Level Matching

**Test with different detail level configs:**

**Minimal:**
```
Config: {"crm": {"detailLevel": "minimal"}}
Command: Log interaction with Jane

Expected note: "Coffee meeting. Discussed AI."
```

**Standard:**
```
Config: {"crm": {"detailLevel": "standard"}}
Command: Log interaction with Jane

Expected note: "Coffee meeting at Soho House. Discussed their AI strategy for Q1. Interested in consulting opportunities."
```

**Detailed:**
```
Config: {"crm": {"detailLevel": "detailed"}}
Command: Log interaction with Jane

Expected note: "Coffee meeting at Soho House, 10:00 AM. Discussed their company's AI strategy in depth. Key points: Planning Q1 implementation of LLM-based customer support, concerns about hallucinations, budget around $50k. Jane is decision-maker on this project. Follow-up scheduled for next Tuesday 2pm to review proposal draft."
```

**Verify:**
```bash
# Check interaction log
Show me recent interactions with Jane
```

Note detail should match configured level.

#### Test 21: Create Deal

**Command:**
```
Create a deal for BigCorp AI consulting project, $50k, prospecting stage
```

**Expected Flow:**
1. Check company exists (BigCorp)
2. Create deal linked to company
3. Set stage correctly
4. Set amount correctly

**Expected Output:**
```
Assistant: Creating deal for BigCorp...

✓ Deal created:
Title: BigCorp AI Consulting Project
Company: BigCorp
Contact: Jane Smith
Stage: Prospecting
Amount: $50,000
Expected Close: [3 months from now]

ID: deal-123-abc
```

**Verify:**
```bash
# List deals
Show me open deals
```

Should show the new deal.

#### Test 22: Relationship Tracking

**Command:**
```
Link Jane and Bob - they used to work together at OldCorp
```

**Expected Flow:**
1. Find contact "Jane"
2. Find contact "Bob"
3. Create relationship link
4. Store context

**Expected Output:**
```
Assistant: Linking Jane Smith and Bob Jones...

✓ Relationship created:
Jane Smith ←→ Bob Jones
Type: Colleague
Context: Worked together at OldCorp
```

**Verify:**
```bash
# Check relationships
Show me Jane's connections
```

Should show link to Bob.

#### Test 23: CRM Integration with Email

**Command:**
```
Triage my inbox and add any new contacts to CRM
```

**Expected Flow:**
1. Process inbox
2. For each email from unknown sender:
   - Check if contact exists
   - Extract contact info from email
   - Add to CRM if significant
   - Log interaction

**Expected Output:**
```
Assistant: Processing inbox and updating CRM...

Added 2 new contacts:

1. Sarah Johnson (Tech Startup Inc)
   - Email: sarah@techstartup.com
   - Context: Reached out about consulting opportunity
   - Interaction logged: Initial email inquiry

2. Mark Thompson (Vendor Co)
   - Email: mark@vendor.com
   - Context: Sales outreach
   - Interaction logged: Initial contact

Existing contacts:
- Jane Smith: Logged follow-up email
- Bob Jones: Logged project update
```

---

## Integration Testing

### Test 24: Email → Calendar Flow

**Scenario:** Email contains meeting invitation

**Command:**
```
Check my inbox
```

**Expected Flow:**
1. Detect email with meeting details
2. Extract date, time, location
3. Check calendar for conflicts
4. Proactively suggest adding event
5. Draft reply
6. Add event if confirmed

**Expected Output:**
```
Assistant: Found email from Mike:
"Want to grab lunch Tuesday at noon at Soho House?"

Checking calendar for Tuesday, Dec 5, 12:00 PM...
✓ You're free!

Should I:
1. Draft reply confirming + add to calendar
2. Just draft reply
3. Just add to calendar
```

**After confirmation:**
```
✓ Draft created: "that works perfect"
✓ Event added: "Lunch with Mike at Soho House" - Dec 5, 12:00-1:00 PM
```

### Test 25: Email → CRM → Calendar Flow

**Scenario:** Email from new contact proposing meeting

**Command:**
```
Process Sarah's email about the consulting project
```

**Expected Flow:**
1. Find email from Sarah
2. Check if Sarah exists in CRM (no)
3. Add Sarah to CRM
4. Extract meeting proposal
5. Check calendar
6. Draft reply
7. Add event
8. Log interaction in CRM

**Expected Output:**
```
Assistant: Processing email from Sarah Johnson...

New contact detected:
✓ Added to CRM: Sarah Johnson (Tech Startup Inc)

Meeting proposed: Thursday, Dec 7, 2:00 PM

Checking calendar... You're free!

✓ Draft created: "that works. 2pm thursday at your office?"
✓ Event added: "Meeting with Sarah - Tech Startup Inc"
✓ CRM interaction logged: "Initial meeting scheduled"

Complete!
```

### Test 26: Bulk Triage with Multi-Skill Integration

**Command:**
```bash
/triage-email
```

**Expected Flow:**
1. Fetch unread emails (email skill)
2. For each email:
   - Check if sender is in CRM (CRM skill)
   - Add new contacts (CRM skill)
   - Extract events (calendar skill)
   - Check conflicts (calendar skill)
   - Draft replies (email skill)
   - Log interactions (CRM skill)
3. Present comprehensive report

**Expected Output:**
```
Assistant: Triaging 20 unread emails...

## CRM Updates
- Added 3 new contacts
- Logged 15 interactions
- Updated 2 existing contacts

## Calendar
- Added 2 events
- Detected 1 conflict (resolved)
- Updated 1 tentative hold

## Email Actions
- Created 8 draft replies
- Archived 10 FYI emails
- 2 require manual attention

[Detailed breakdown follows...]
```

### Test 27: Cross-Timezone Scheduling with CRM

**Command:**
```
Schedule a call with Jean in Paris next week
```

**Expected Flow:**
1. Find Jean in CRM
2. Get Jean's timezone (from CRM notes or infer from location)
3. Propose time that works for both timezones
4. Check calendar for conflicts
5. Draft email with both timezones
6. Add event
7. Log in CRM

**Expected Output:**
```
Assistant: Found Jean Dupont in CRM (located in Paris, CET timezone).

Checking mutual availability...

Your working hours: 9:00 AM - 5:00 PM CST
Jean's working hours: 9:00 AM - 5:00 PM CET (2:00 AM - 10:00 AM CST overlap)

Best time: 9:00 AM CST (4:00 PM CET) on Monday, Dec 4

✓ Draft created: "how about 9am my time (4pm your time) on monday?"
✓ Event added: "Call with Jean Dupont" - 9:00-9:30 AM CST (4:00-4:30 PM CET)
✓ CRM interaction logged: "Scheduled call for Dec 4"
```

---

## Performance Testing

### Test 28: Bulk Email Processing with Subagents

**Setup:** Inbox with 50+ unread emails

**Command:**
```bash
/triage-email
```

**Measure:**
- Time to complete
- Accuracy of categorization
- Proper use of subagents

**Expected:**
- Uses Task tool to spawn subagent
- Subagent processes emails in parallel where possible
- Returns comprehensive report
- Completes in reasonable time (<2 minutes for 50 emails)

**Monitor:**
```
[Observe Claude Code using Task tool]
✓ Spawned subagent for bulk triage
[Subagent processes emails]
✓ Subagent completed
[Main agent synthesizes results]
```

### Test 29: Large CRM Import

**Scenario:** Import 100+ contacts from email history

**Command:**
```
Backfill my CRM from last 3 months of sent emails
```

**Expected Flow:**
1. Spawn subagent for processing
2. Subagent iterates through sent emails
3. Extract recipients
4. Check for duplicates
5. Add new contacts
6. Log interactions
7. Report progress

**Performance Expectations:**
- Proper duplicate detection (no redundant API calls)
- Batch operations where possible
- Progress updates during processing
- Completion in reasonable time (<5 minutes for 100 emails)

### Test 30: Real-time Calendar Conflict Checking

**Setup:** Dense calendar (back-to-back meetings)

**Command:**
```
Find me a 30-minute slot this week
```

**Expected:**
- Quick calendar query
- Efficient conflict detection
- Respects working hours and buffers
- Returns results in <5 seconds

---

## Common Issues and Debugging

### Issue 1: Email Drafts Not Threading

**Symptoms:**
- Draft appears as new email, not in conversation
- No "Re:" in subject line
- Original sender doesn't see it as reply

**Debug Steps:**

1. **Check if thread ID was captured:**
```
Show me the details of that draft
```
Look for thread_id in output.

2. **Verify message ID extraction:**
```
Find that original email again
```
Should show message_id clearly.

3. **Check MCP tool capability:**
```
Can you create a threaded draft?
```
Test if MCP server supports threading.

**Common Fixes:**
- Re-search for original email
- Explicitly provide thread_id and in_reply_to
- Verify Gmail MCP server version

### Issue 2: Calendar Events Wrong Timezone

**Symptoms:**
- Events appear at wrong time
- Timezone shows incorrectly

**Debug Steps:**

1. **Check user config:**
```bash
cat ~/.claude/cc-office-config.json | jq '.personal.timezone'
```

2. **Check Google Calendar timezone:**
- Open Google Calendar settings
- Verify timezone matches config

3. **Test timezone conversion:**
```
What timezone am I configured for?
```

**Common Fixes:**
- Update config timezone
- Re-run `/setup-cc-office`
- Recreate events after fix

### Issue 3: CRM Duplicates Created

**Symptoms:**
- Multiple entries for same person
- Contact appears twice in searches

**Debug Steps:**

1. **Check search was performed:**
```
Did you search for existing contacts before adding?
```

2. **Review skill logs:**
Look for "Checking for existing contact" messages.

3. **Test search manually:**
```
Search CRM for john@example.com
```

**Common Fixes:**
- Merge duplicate contacts manually
- Verify search is happening in CRM skill
- Check search query accuracy

### Issue 4: Email Style Not Matching

**Symptoms:**
- Drafts too verbose/terse
- Wrong signature applied
- Doesn't sound like user

**Debug Steps:**

1. **Check config loaded:**
```
What's my configured email style?
```

2. **Verify config file:**
```bash
cat ~/.claude/cc-office-config.json | jq '.email'
```

3. **Test with explicit style:**
```
Draft an email in ultra-terse style
```

**Common Fixes:**
- Update email config
- Re-run `/setup-cc-office`
- Provide examples of preferred style

### Issue 5: Skills Not Activating

**Symptoms:**
- Commands don't work
- Skills not being used
- Generic responses instead of cc-office behavior

**Debug Steps:**

1. **Check plugin status:**
```bash
/plugin list
```
Should show cc-office as active.

2. **Verify skills registered:**
```bash
/skill list
```
Should show all 4 cc-office skills.

3. **Check config exists:**
```bash
ls -la ~/.claude/cc-office-config.json
```

4. **Restart Claude Code:**
Complete restart, not just reload.

**Common Fixes:**
- Reinstall plugin
- Re-run onboarding
- Check for conflicting plugins

### Issue 6: MCP Server Connection Failures

**Symptoms:**
- "MCP server unavailable" errors
- Timeouts when accessing Gmail/Calendar/CRM
- Missing data in responses

**Debug Steps:**

1. **Check MCP server status:**
```bash
# In Claude Code
/mcp list
```
All required servers should show "active".

2. **Test individual server:**
```
# Test Gmail
List my inbox

# Test Calendar
Show my calendar

# Test CRM (if configured)
Search CRM for contacts
```

3. **Check server logs:**
```bash
tail -f ~/.claude/logs/mcp-*.log
```

4. **Verify credentials:**
- Re-authenticate Gmail
- Re-authenticate Calendar
- Check CRM database connection

**Common Fixes:**
- Restart MCP servers: Restart Claude Code
- Re-authenticate OAuth: `/mcp reconnect pd`
- Check network connection
- Verify MCP server versions

---

## Test Environment Setup

### Creating Test Data

#### Test Email Inbox

```bash
# Create test emails for development:
# 1. Send yourself emails from different addresses
# 2. Create threads (reply to yourself)
# 3. Include meeting invitations
# 4. Add various content types

# Example test email scenarios:
# - Simple scheduling request
# - Complex multi-person thread
# - Email with calendar invite
# - Email with new contact info
# - Bulk newsletter/receipts to test filtering
```

#### Test Calendar

```bash
# Create test calendar events:
# 1. Various event types (meetings, lunches, all-day)
# 2. Some with conflicts
# 3. Events at different times (morning, afternoon, evening)
# 4. Events with location
# 5. Events with multiple attendees
```

#### Test CRM Data

```bash
# If using CRM, create test data:
# 1. Sample contacts (10-20)
# 2. Sample companies (5-10)
# 3. Sample deals (3-5)
# 4. Contact relationships
# 5. Interaction history
```

### Automated Test Suite

Create a test script for regression testing:

```bash
#!/bin/bash
# cc-office-tests.sh

echo "Running CC-Office Plugin Tests..."

# Test 1: Plugin installed
echo "Test 1: Plugin installation"
claude /plugin list | grep cc-office || exit 1

# Test 2: Config exists
echo "Test 2: Configuration file"
test -f ~/.claude/cc-office-config.json || exit 1

# Test 3: Skills registered
echo "Test 3: Skills registration"
claude /skill list | grep "cc-office:email-management" || exit 1

# Test 4: MCP servers active
echo "Test 4: MCP server connectivity"
# Test actual MCP calls...

echo "All tests passed!"
```

### Manual Test Checklist

Print this checklist for manual testing sessions:

```
CC-Office Manual Test Checklist
================================

Pre-Installation:
[ ] Claude Code installed and working
[ ] Gmail MCP server configured
[ ] Calendar MCP server configured
[ ] CRM MCP server configured (optional)
[ ] OAuth authenticated for all services

Installation:
[ ] Plugin marketplace added
[ ] Plugin installed successfully
[ ] Skills registered (4 skills)
[ ] Commands registered (3 commands)
[ ] Claude Code restarted

Onboarding:
[ ] /setup-cc-office completes successfully
[ ] Config file created
[ ] Style guide created
[ ] All preferences captured correctly

Email:
[ ] Can find email threads
[ ] Drafts are threaded correctly
[ ] Email style matches config
[ ] To: field populated correctly
[ ] Drafts not sent automatically

Calendar:
[ ] Conflict detection works
[ ] Events added correctly
[ ] Timezone handling accurate
[ ] Working hours respected
[ ] Tentative events created properly

CRM (if enabled):
[ ] Duplicate detection works
[ ] Contacts added with company association
[ ] Interaction logging works
[ ] Detail level matches config
[ ] Deals created correctly

Integration:
[ ] Email → Calendar flow works
[ ] Email → CRM flow works
[ ] Calendar → Email flow works
[ ] Bulk triage uses all skills

Performance:
[ ] Bulk operations use subagents
[ ] Processing completes in reasonable time
[ ] No obvious bottlenecks
```

---

## Test Scenarios by User Persona

### Persona 1: Ultra-Terse Communicator

**Config:**
```json
{
  "email": {
    "tone": "ultra-terse",
    "signature": "none",
    "formality": "casual",
    "responseLength": "minimal"
  }
}
```

**Test Cases:**
- Draft should be 1-2 lines maximum
- No signatures or sign-offs
- Direct and to the point
- Example: "sure" or "that works perfect"

### Persona 2: Professional Communicator

**Config:**
```json
{
  "email": {
    "tone": "professional",
    "signature": "full",
    "formality": "formal",
    "responseLength": "detailed"
  }
}
```

**Test Cases:**
- Proper greetings and closings
- Full signature block
- Structured paragraphs
- Formal language

### Persona 3: Mixed CRM User

**Config:**
```json
{
  "crm": {
    "contactTypes": "mixed",
    "detailLevel": "standard",
    "autoLogInteractions": true,
    "trackRelationships": true
  }
}
```

**Test Cases:**
- Both professional and personal contacts
- Relationship tracking active
- Standard detail level
- Auto-logging enabled

---

## Continuous Testing

### Regression Test Suite

Run these tests before each release:

1. **Core Functionality**
   - [ ] Email drafting works
   - [ ] Calendar integration works
   - [ ] CRM operations work (if enabled)

2. **Integration Points**
   - [ ] Email → Calendar flow
   - [ ] Email → CRM flow
   - [ ] Bulk processing with subagents

3. **Edge Cases**
   - [ ] Threading with long threads (20+ messages)
   - [ ] Events near midnight
   - [ ] Contacts with special characters in names
   - [ ] Emails with no clear subject

4. **Performance**
   - [ ] Bulk triage (50+ emails)
   - [ ] Large calendar queries (1 month view)
   - [ ] CRM search with many results

### User Acceptance Testing

Have actual users test:

1. Real inbox triage
2. Actual calendar scheduling
3. Real contact management
4. Feedback on style matching
5. Overall workflow efficiency

---

## Test Results Documentation

For each test session, document:

```markdown
# Test Session: [Date]

## Environment
- Claude Code Version:
- Plugin Version:
- MCP Servers:
  - Gmail: [version]
  - Calendar: [version]
  - CRM: [version]

## Tests Performed
1. [Test name]: PASS/FAIL
2. [Test name]: PASS/FAIL
...

## Issues Found
1. [Issue description]
   - Severity: High/Medium/Low
   - Reproducible: Yes/No
   - Steps to reproduce:
   - Expected vs Actual:

## Performance Notes
- Bulk triage (50 emails): [X] seconds
- Calendar conflict check: [X] seconds
- CRM search: [X] seconds

## User Feedback
[Quotes or observations from users]

## Action Items
- [ ] Fix [issue]
- [ ] Improve [feature]
- [ ] Document [behavior]
```

---

## Support and Resources

- **Plugin Issues:** https://github.com/harperreed/office-admin-claude/issues
- **MCP Server Issues:**
  - Pipedream: https://github.com/PipedreamHQ/pipedream/issues
  - Pagen CRM: https://github.com/harperreed/pagen-crm/issues
- **Claude Code Docs:** https://docs.anthropic.com/claude-code
- **Community Discussions:** https://github.com/harperreed/office-admin-claude/discussions

---

**Last Updated:** 2025-12-02
**Version:** 1.0.0
