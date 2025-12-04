# CC-Office: Claude Code Office Productivity Plugin

**CC-Office** is a comprehensive productivity plugin for Claude Code that helps you manage email, calendar, and contacts in your personal style. It learns your communication preferences and handles routine tasks while maintaining your authentic voice.

## Features

### 📧 Email Management
- **Smart inbox triage** - Categorize emails, draft replies, extract action items
- **Personal voice matching** - Learns your email style (tone, length, patterns)
- **Proper threading** - Drafts appear in correct conversations
- **Bulk processing** - Efficient handling of large inboxes via subagents
- **Calendar integration** - Auto-detects scheduling and checks conflicts

### 📅 Calendar Management
- **Conflict detection** - Checks availability before committing
- **Timezone handling** - Accurate conversion for international contacts
- **Working hours respect** - Honors your configured schedule
- **Event extraction** - Pulls meeting details from emails automatically
- **Tentative holds** - Creates "HOLD" events for pending confirmations

### 👥 CRM Management (Optional)
- **Contact deduplication** - Prevents duplicate entries
- **Company associations** - Links contacts to organizations
- **Interaction logging** - Tracks communication history
- **Deal tracking** - Manages business opportunities
- **Relationship mapping** - Connects contacts to each other

## Quick Start

### 1. Install MCP Servers

CC-Office requires these MCP servers:

**Required:**
- Pipedream (Gmail + Google Calendar)

**Optional:**
- Pagen CRM

Add to `~/.claude/settings.json`:
```json
{
  "mcpServers": {
    "pd": {
      "command": "npx",
      "args": ["-y", "@pipedream/mcp-server@latest"]
    }
  }
}
```

See [`mcp-servers/README.md`](./mcp-servers/README.md) for detailed setup instructions.

### 2. Install CC-Office Plugin

```bash
# Add the plugin marketplace
/plugin marketplace add harperreed/cc-office-plugin

# Install the plugin
/plugin install cc-office

# Restart Claude Code
```

### 3. Run Onboarding

```bash
/setup-cc-office
```

This interactive process will:
- Ask about your email communication style
- Configure calendar preferences
- Set up CRM workflow (if enabled)
- Create your personal config at `~/.claude/cc-office-config.json`

### 4. Start Using

Try these commands:
```bash
# Triage your inbox
/triage-email

# Draft a new email
/draft-email

# Or just ask naturally:
"Check my inbox and draft replies to anything urgent"
"Add the lunch meeting from Mike's email to my calendar"
"Find contact info for Jane Smith"
```

## How It Works

CC-Office consists of four integrated skills that work together:

### Skills

1. **office:onboarding** - Sets up your personal style
2. **office:email-management** - Handles email workflows
3. **office:calendar-management** - Manages scheduling
4. **office:crm-management** - Tracks contacts and relationships

These skills automatically activate when you perform related tasks. You don't need to explicitly invoke them - just work naturally with Claude Code.

### Slash Commands

- `/setup-cc-office` - Run onboarding (or update preferences)
- `/triage-email` - Bulk inbox triage with categorization and drafts
- `/draft-email` - Guided email composition in your style

## Configuration

Your config is stored at `~/.claude/cc-office-config.json`:

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
    "tone": "ultra-terse | conversational | professional | warm",
    "signature": "none | minimal | full",
    "formality": "casual | standard | formal",
    "responseLength": "minimal | balanced | detailed",
    "patterns": ["your", "common", "phrases"],
    "customGuidelines": "Additional style notes..."
  },
  "calendar": {
    "defaultDuration": 30,
    "bufferMinutes": 15,
    "schedulingLink": "https://your.cal.link",
    "autoAddFromEmail": true,
    "eventNaming": {
      "includeLocation": true,
      "includeAttendees": false
    }
  },
  "crm": {
    "contactTypes": "professional | personal | mixed",
    "detailLevel": "minimal | standard | detailed",
    "autoLogInteractions": true,
    "trackRelationships": true
  }
}
```

You can edit this file directly or re-run `/setup-cc-office` to update preferences.

## Example Workflows

### Morning Email Routine

```
You: /triage-email

Claude: Analyzing 15 unread emails...

## 📧 Inbox Triage Summary

### 🔴 ACTION NEEDED (3)
1. **From:** Mike | **Subject:** Quick sync tomorrow?
   **Summary:** Wants to schedule a call
   **Draft:** Created - "how about 2pm est tomorrow?"

2. **From:** Sarah | **Subject:** Project proposal review
   **Summary:** Needs feedback by Friday
   **Draft:** Created - "will review by thursday and send notes"

### 📅 CALENDAR (2)
1. **From:** Team | **Subject:** Q1 Planning Meeting
   **Event:** Thursday Dec 7, 10:00 AM - 11:30 AM
   **Conflicts:** None
   **Status:** Added to calendar

### 📋 ARCHIVE (10)
...
```

### Scheduling with Conflict Detection

```
You: Mike wants to meet for lunch on Tuesday

Claude: Let me check your calendar for Tuesday...

Your schedule on Tuesday, Dec 5:
- 10:00 AM - 12:00 PM: Team standup
- 12:00 PM - 1:30 PM: FREE (lunch window)
- 1:30 PM - 3:00 PM: Client call

You're free for lunch from 12:00-1:30pm. Should I draft a reply and add it to your calendar?

You: yes

Claude: Done! Created draft: "that works perfect. 12:30 at soho house?"
Added to calendar: "Lunch with Mike at Soho House" on Dec 5, 12:30-1:30 PM
```

### Adding Contact from Email

```
You: Add Jane from that email to my CRM

Claude: Checking for existing contact...
Not found. Adding Jane Smith:
- Email: jane@example.com
- Company: Acme Corp
- Note: Met via introduction from Bob. Interested in AI consulting.

Logged interaction: "Initial email exchange about AI consulting project. Scheduled call for next week."

Done! Jane Smith added to CRM and linked to Acme Corp.
```

## Customization

### Email Style Examples

**Ultra-terse** (minimal communication):
```
that works perfect

sure

unsubscribe
```

**Conversational** (casual but complete):
```
Hey Sarah! That time works great for me.
Looking forward to catching up.
```

**Professional** (formal, structured):
```
Dear Ms. Johnson,

Thank you for reaching out regarding the proposal. I have reviewed the materials and would be happy to schedule a call at your earliest convenience.

Best regards,
[Your Name]
```

**Warm** (friendly, personal):
```
Hi Mike!

Great to hear from you! I'd love to grab coffee next week.
How about Tuesday morning? I know a great spot near your office.

Hope you're doing well!
[Your Name]
```

CC-Office will match whichever style you configure.

## Best Practices

1. **Run onboarding first** - Sets up your preferences properly
2. **Review drafts before sending** - CC-Office creates drafts, you send them
3. **Use bulk triage for efficiency** - Process 10+ emails at once with `/triage-email`
4. **Let calendar auto-add events** - Enable `autoAddFromEmail` for seamless scheduling
5. **Keep config updated** - Re-run `/setup-cc-office` as your preferences change

## Troubleshooting

### "Email drafts aren't threaded properly"
- Check that Gmail MCP has proper permissions
- Verify thread ID and message ID are being captured
- Try creating the draft manually once to test

### "Calendar events have wrong timezone"
- Update `timezone` in `~/.claude/cc-office-config.json`
- Verify Google Calendar is set to same timezone
- Re-create the event after fixing config

### "CRM not working"
- Check if Pagen CRM MCP server is configured (optional component)
- Verify database is running: `psql pagen_crm -c "SELECT 1;"`
- See [`mcp-servers/README.md`](./mcp-servers/README.md) for CRM setup

### "Skills not activating"
- Restart Claude Code completely
- Check plugin is installed: `/plugin list`
- Verify config exists: `cat ~/.claude/cc-office-config.json`

## Development

Want to contribute or customize CC-Office?

```bash
# Clone the repo
git clone https://github.com/harperreed/cc-office-plugin.git

# Create development marketplace
cd cc-office-plugin
cat > .claude-plugin/marketplace.json <<EOF
{
  "name": "cc-office-dev",
  "plugins": [{"name": "cc-office", "source": "./"}]
}
EOF

# Install in dev mode
/plugin marketplace add /path/to/cc-office-plugin
/plugin install cc-office@cc-office-dev

# Make changes and test
# Restart Claude Code after changes
```

### Plugin Structure

```
cc-office-plugin/
├── .claude-plugin/
│   ├── plugin.json          # Plugin manifest
│   └── marketplace.json      # Dev marketplace config
├── skills/
│   ├── onboarding.md         # Interactive setup
│   ├── email-management.md   # Email workflows
│   ├── crm-management.md     # Contact management
│   └── calendar-management.md # Scheduling logic
├── commands/
│   ├── setup-cc-office.md    # Onboarding command
│   ├── triage-email.md       # Bulk triage command
│   └── draft-email.md        # Guided drafting command
├── mcp-servers/
│   └── README.md             # MCP setup docs
└── README.md                 # This file
```

## License

MIT License - See LICENSE file for details

## Credits

Created by [Harper Reed](https://harper.lol)

Based on patterns from the [pagen](https://github.com/harperreed/pw) productivity agent.

## Support

- **Issues:** https://github.com/harperreed/cc-office-plugin/issues
- **Discussions:** https://github.com/harperreed/cc-office-plugin/discussions
- **Twitter:** [@harper](https://twitter.com/harper)

---

**CC-Office** - *Your personal office productivity agent, in Claude Code*
