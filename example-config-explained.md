# Office Plugin Configuration Guide

This file explains all the configuration options available in `~/.claude/cc-office-config.json`.

## Configuration File Location

Place your configuration at: `~/.claude/cc-office-config.json`

The easiest way to create this file is to run: `/setup-office`

## Migration from Old Format

**If you have a config file from before v1.0**, the schema has changed. The new format uses these top-level keys:
- `version` - Config schema version
- `personal` - Your info and working hours
- `email` - Email style preferences
- `calendar` - Calendar settings
- `crm` - CRM workflow preferences

See the "Migration Example" section at the bottom of this file.

---

## Configuration Structure

### `version`
- **Type**: String
- **Value**: `"1.0"`
- **Description**: Configuration schema version

---

## `personal` Section

Your basic info and working hours.

### `name`
- **Type**: String
- **Description**: Your full name or preferred name
- **Example**: `"Harper Reed"`

### `timezone`
- **Type**: String (IANA timezone)
- **Default**: `"America/Chicago"`
- **Description**: Your primary timezone for all scheduling
- **Examples**: `"America/New_York"`, `"Europe/London"`, `"Asia/Tokyo"`
- **Used by**: Calendar skill for event creation and conflict checking

### `workingHours`
- **Type**: Object
- **Description**: Your typical working hours

#### `workingHours.start`
- **Type**: String (24-hour format "HH:MM")
- **Example**: `"09:30"`
- **Description**: When your workday starts

#### `workingHours.end`
- **Type**: String (24-hour format "HH:MM")
- **Example**: `"16:30"`
- **Description**: When your workday ends

#### `workingHours.lunchStart`
- **Type**: String (24-hour format "HH:MM")
- **Example**: `"12:00"`
- **Description**: When your lunch break starts

#### `workingHours.lunchEnd`
- **Type**: String (24-hour format "HH:MM")
- **Example**: `"13:30"`
- **Description**: When your lunch break ends

---

## `email` Section

Controls how emails are drafted on your behalf.

### `tone`
- **Type**: String
- **Options**: `"ultra-terse"`, `"conversational"`, `"professional"`, `"warm"`
- **Description**: Overall email tone

**Examples:**
- **`"ultra-terse"`**: "that works perfect" (minimal words, no fluff)
- **`"conversational"`**: "Hey! That works great for me." (casual but complete)
- **`"professional"`**: "Thank you for reaching out. That time works well for me." (formal, structured)
- **`"warm"`**: "Hi! So great to hear from you. That time works perfectly!" (friendly, personal)

### `signature`
- **Type**: String
- **Options**: `"none"`, `"minimal"`, `"full"`
- **Description**: Email signature preference

**Examples:**
- **`"none"`**: No sign-off (message ends immediately)
- **`"minimal"`**: Just "Best," or "Thanks,"
- **`"full"`**: Complete signature block with name, title, contact info

### `formality`
- **Type**: String
- **Options**: `"casual"`, `"standard"`, `"formal"`
- **Description**: Grammar and capitalization style

**Examples:**
- **`"casual"`**: lowercase ok, minimal punctuation ("sure. that works")
- **`"standard"`**: Proper grammar and capitalization
- **`"formal"`**: Perfect grammar, structured sentences

### `responseLength`
- **Type**: String
- **Options**: `"minimal"`, `"balanced"`, `"detailed"`
- **Description**: How long responses should be

**Examples:**
- **`"minimal"`**: One-liners when possible
- **`"balanced"`**: 2-3 sentences typical
- **`"detailed"`**: Thorough explanations with context

### `patterns`
- **Type**: Array of strings
- **Description**: Your frequently used phrases
- **Purpose**: Helps maintain consistency with your personal voice
- **Example**: `["that works perfect", "sure.", "how about [time]?"]`

### `customGuidelines`
- **Type**: String
- **Description**: Freeform additional style notes
- **Example**: `"Use minimal punctuation. Self-deprecating humor is fine. Never use greetings like 'Hi' or 'Hello'. Get straight to the point. Lowercase is preferred for casual emails."`

---

## `calendar` Section

Controls calendar and scheduling behavior.

### `defaultDuration`
- **Type**: Integer (minutes)
- **Default**: `30`
- **Description**: Default meeting length if not specified
- **Used when**: Creating events from vague email requests ("let's meet")

### `bufferMinutes`
- **Type**: Integer
- **Default**: `15`
- **Description**: Minutes of buffer between meetings
- **Purpose**: Prevents back-to-back scheduling, gives breathing room

### `schedulingLink`
- **Type**: String (URL)
- **Description**: Your public calendar/scheduling link
- **Example**: `"https://harper.lol/schedule/"`
- **Used in**: Email drafts when sharing availability

### `autoAddFromEmail`
- **Type**: Boolean
- **Default**: `false` (recommended)
- **Description**: Whether to automatically add events to calendar without asking
- **Note**: Set to `false` for safety - agent will ask first before creating events

### `eventNaming`
- **Type**: Object
- **Description**: How calendar event titles should be formatted

#### `eventNaming.includeLocation`
- **Type**: Boolean
- **Default**: `true`
- **Description**: Include location in event title
- **Examples**:
  - `true`: "Lunch with Mike at Soho House"
  - `false`: "Lunch with Mike"

#### `eventNaming.includeAttendees`
- **Type**: Boolean
- **Default**: `false`
- **Description**: Include attendee names in event title
- **Examples**:
  - `true`: "Meeting with Mike, Sarah, John"
  - `false`: "Team Meeting"

---

## `crm` Section

Controls contact/company/deal management.

### `contactTypes`
- **Type**: String
- **Options**: `"professional"`, `"personal"`, `"mixed"`
- **Description**: Type of contacts you manage

**Options:**
- **`"professional"`**: Work relationships, clients, vendors
- **`"personal"`**: Friends, family, social contacts
- **`"mixed"`**: Both personal and professional

### `detailLevel`
- **Type**: String
- **Options**: `"minimal"`, `"standard"`, `"detailed"`
- **Description**: How detailed contact notes should be

**Examples:**
- **`"minimal"`**: "Met at conference. Works at Acme Corp."
- **`"standard"`**: "Met at AI Summit 2025. VP of Engineering at Acme Corp. Interested in our consulting services. Follow up in Q1."
- **`"detailed"`**: "Met at AI Summit 2025, introduced by Bob. VP of Engineering at Acme Corp (Series B, ~50 employees). Looking to implement RAG system for customer support. Budget around $50k. Prefers technical deep-dives. Based in SF but travels frequently. Follow up first week of January."

### `autoLogInteractions`
- **Type**: Boolean
- **Default**: `true`
- **Description**: Automatically log email interactions to CRM
- **Note**: Respects `detailLevel` setting for note verbosity

### `trackRelationships`
- **Type**: Boolean
- **Default**: `true`
- **Description**: Track how contacts know each other
- **Examples**: Who introduced you, colleague relationships, etc.

---

## Complete Example

```json
{
  "version": "1.0",
  "personal": {
    "name": "Harper Reed",
    "timezone": "America/Chicago",
    "workingHours": {
      "start": "09:30",
      "end": "16:30",
      "lunchStart": "12:00",
      "lunchEnd": "13:30"
    }
  },
  "email": {
    "tone": "ultra-terse",
    "signature": "none",
    "formality": "casual",
    "responseLength": "minimal",
    "patterns": [
      "that works perfect",
      "sure.",
      "unsubscribe",
      "sure. https://harper.lol/schedule/ is my calendar. it is a horrible place."
    ],
    "customGuidelines": "Use minimal punctuation. Self-deprecating humor is fine. Never use greetings like 'Hi' or 'Hello'. Get straight to the point. Lowercase is preferred for casual emails."
  },
  "calendar": {
    "defaultDuration": 30,
    "bufferMinutes": 15,
    "schedulingLink": "https://harper.lol/schedule/",
    "autoAddFromEmail": false,
    "eventNaming": {
      "includeLocation": true,
      "includeAttendees": false
    }
  },
  "crm": {
    "contactTypes": "mixed",
    "detailLevel": "standard",
    "autoLogInteractions": true,
    "trackRelationships": true
  }
}
```

---

## Style Profiles

### Ultra-Terse (Harper's Style)

Best for: Speed-focused communication, startup culture

```json
"email": {
  "tone": "ultra-terse",
  "signature": "none",
  "formality": "casual",
  "responseLength": "minimal",
  "patterns": ["that works perfect", "sure.", "unsubscribe"],
  "customGuidelines": "Lowercase ok. No greetings. Get to the point."
}
```

**Result**: "that works perfect"

---

### Professional Balance

Best for: Corporate environments, client communication

```json
"email": {
  "tone": "professional",
  "signature": "minimal",
  "formality": "standard",
  "responseLength": "balanced",
  "patterns": ["I'd be happy to", "Please let me know", "Looking forward to"],
  "customGuidelines": "Proper grammar. Brief but complete. Professional but not stuffy."
}
```

**Result**:
```
That time works well for me. Looking forward to it!

Best,
```

---

### Warm & Friendly

Best for: Personal brand, community engagement

```json
"email": {
  "tone": "warm",
  "signature": "full",
  "formality": "casual",
  "responseLength": "balanced",
  "patterns": ["Great to hear from you!", "So excited about", "Hope you're doing well!"],
  "customGuidelines": "Friendly and personal. Show enthusiasm. Use exclamation points."
}
```

**Result**:
```
Hi!

Great to hear from you! That time works perfectly. So excited to catch up!

Hope you're doing well,
Harper
```

---

## Quick Start

1. **Run setup wizard:**
   ```bash
   /setup-office
   ```

2. **Or copy example:**
   ```bash
   cp example-config.json ~/.claude/cc-office-config.json
   ```

3. **Edit the values** to match your style

4. **Test with:**
   ```bash
   /draft-email
   ```

5. **Iterate** until it feels natural

---

## Testing Your Configuration

After creating your config, test with:

1. **Scheduling request**: "Draft a reply to Sarah about meeting next week"
   - Check tone and formality
   - Verify calendar integration works

2. **Quick confirmation**: "Draft a quick yes to Mike's email"
   - Check signature preference
   - Verify response length

3. **Technical discussion**: "Reply to the bug report from engineering"
   - Check if you need different tone for technical topics
   - Adjust customGuidelines if needed

4. **Calendar sharing**: Send someone your scheduling link
   - Check if custom patterns are used
   - Verify schedulingLink is correct

Adjust config and test again until it sounds like YOU.

---

## Migration Example

### Old Format (before v1.0)
```json
{
  "email_style": {
    "conciseness": "ultra-terse",
    "formality": "casual",
    "signatures": false
  },
  "scheduling": {
    "timezone": "America/Chicago",
    "working_hours": {"start": "09:30", "end": "16:30"}
  }
}
```

### New Format (v1.0+)
```json
{
  "version": "1.0",
  "personal": {
    "name": "Your Name",
    "timezone": "America/Chicago",
    "workingHours": {
      "start": "09:30",
      "end": "16:30",
      "lunchStart": "12:00",
      "lunchEnd": "13:30"
    }
  },
  "email": {
    "tone": "ultra-terse",
    "signature": "none",
    "formality": "casual",
    "responseLength": "minimal",
    "patterns": [],
    "customGuidelines": ""
  },
  "calendar": {
    "defaultDuration": 30,
    "bufferMinutes": 15,
    "schedulingLink": "",
    "autoAddFromEmail": false,
    "eventNaming": {
      "includeLocation": true,
      "includeAttendees": false
    }
  },
  "crm": {
    "contactTypes": "mixed",
    "detailLevel": "standard",
    "autoLogInteractions": true,
    "trackRelationships": true
  }
}
```

**Migration Steps:**
1. Backup your old config: `cp ~/.claude/cc-office-config.json ~/.claude/cc-office-config.json.backup`
2. Run `/setup-office` to create new format config
3. Copy any custom values from your backup

---

## Tips

### Email Tone
- Start with what feels natural to YOU
- Test with a few emails before committing
- You can always adjust later

### Calendar Settings
- **Always use `autoAddFromEmail: false`** for safety
- Buffer of 15 minutes prevents burnout
- Include location in titles for context

### CRM Detail Level
- **Minimal**: When you have lots of contacts
- **Standard**: Best for most people
- **Detailed**: When every relationship matters (fundraising, sales)

### Common Mistakes
- ❌ Setting signature to "full" when Gmail already adds one (results in double signatures)
- ❌ Setting autoAddFromEmail to true (creates events without confirmation)
- ❌ Choosing tone that doesn't match your actual style
- ❌ Not including common phrases (misses your voice)

---

## Getting Help

- Run `/setup-office` to interactively configure
- Test with `/draft-email` and `/triage-email`
- Check example-config.json for a working example
- See README.md for more documentation
