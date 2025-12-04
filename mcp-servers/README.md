# MCP Servers for Office Plugin

The Office plugin bundles several MCP servers to provide comprehensive productivity features. This document explains what each server does and how to set them up.

## Bundled MCP Servers

The Office plugin automatically configures the following MCP servers via `.mcp.json`:

### 1. Pipedream MCP Server (Auto-configured ✅)

**Purpose:** Email (Gmail) and Calendar (Google Calendar) management

**What it provides:**
- 📧 Gmail: Reading, drafting, threading, sending emails
- 📅 Google Calendar: Event creation, conflict checking, scheduling

**Installation:**
✅ **Automatically configured when you install the plugin!** No manual setup needed.

**Setup:**
1. Install the Office plugin: `/plugin install office@harper.dev`
2. First time you use email/calendar features, you'll be prompted to authenticate via OAuth
3. Grant permissions for Gmail and Google Calendar
4. Done!

**Required OAuth Scopes:**
- Gmail: `gmail.readonly`, `gmail.send`, `gmail.compose`, `gmail.labels`, `gmail.modify`
- Calendar: `calendar.events`, `calendar.readonly`, `calendar.freebusy`

### 2. Toki - Todo Tracker (Auto-configured ✅)

**Purpose:** Straightforward todo tracking and task management

**What it provides:**
- ✅ Create, list, update, and complete todos
- 🏷️ Tag and categorize tasks
- 📊 Project-based organization
- 🔍 Search and filter capabilities

**Installation:**
```bash
brew install harperreed/tap/toki
```

**More info:** https://github.com/harperreed/toki

**Setup:**
1. Install via Homebrew (see above)
2. The MCP server is auto-configured by the plugin
3. Restart Claude Code after installing
4. Use naturally: "Add a todo to follow up with Sarah"

### 3. Chronicle - Agent Action Log (Auto-configured ✅)

**Purpose:** Log and track what your agents are doing

**What it provides:**
- 📝 Log agent actions and decisions
- 🔍 Search through action history
- ⏱️ Timestamp tracking
- 📊 Activity patterns and insights

**Installation:**
```bash
brew install harperreed/tap/chronicle
```

**More info:** https://github.com/harperreed/chronicle

**Setup:**
1. Install via Homebrew (see above)
2. The MCP server is auto-configured by the plugin
3. Restart Claude Code after installing
4. Agents will automatically log important actions

### 4. Pagen - CRM (Auto-configured ✅)

**Purpose:** Contact, company, and deal management (the CRM backend for Office plugin)

**What it provides:**
- 👥 Contact management
- 🏢 Company tracking
- 💼 Deal pipeline
- 🔗 Relationship mapping
- 📊 Communication history

**Installation:**
```bash
brew install harperreed/tap/pagen
```

**More info:** https://github.com/harperreed/pagen

**Setup:**
1. Install via Homebrew (see above)
2. Initialize database: `pagen init`
3. The MCP server is auto-configured by the plugin
4. Restart Claude Code after installing
5. Start adding contacts naturally: "Add John Doe from Acme Corp to my contacts"

**Note:** Pagen IS the CRM for the Office plugin. There is no separate "CC-Office CRM" server - Pagen provides all CRM functionality.

## Quick Setup Guide

### Minimum (Email + Calendar only)

1. **Install the Office plugin:**
   ```bash
   /plugin install office@harper.dev
   ```

2. **Authenticate with Google:**
   When you first use email/calendar features, you'll be prompted to authenticate

3. **Configure your style:**
   ```bash
   /setup-office
   ```

4. **Start using it:**
   ```bash
   /triage-email
   ```

That's it! Pipedream MCP is auto-configured by the plugin.

### Full Setup (All Features)

Get the complete productivity stack:

1. **Install the Office plugin:**
   ```bash
   /plugin install office@harper.dev
   ```

2. **Install the Harper Reed productivity stack:**
   ```bash
   brew install harperreed/tap/toki
   brew install harperreed/tap/chronicle
   brew install harperreed/tap/pagen
   ```

3. **Initialize Pagen CRM:**
   ```bash
   pagen init
   ```

4. **Restart Claude Code** to activate all MCP servers

5. **Authenticate with Google:**
   OAuth prompts will appear on first use

6. **Configure your style:**
   ```bash
   /setup-office
   ```

7. **You now have:**
   - 📧 Email management (Gmail)
   - 📅 Calendar management (Google Calendar)
   - ✅ Todo tracking (Toki)
   - 📝 Agent action logging (Chronicle)
   - 👥 CRM (Pagen)

## Verifying Setup

After configuration, verify MCP servers are working:

```bash
# In Claude Code
/mcp

# Should show all active servers:
# - pd (Pipedream) - active ✅
# - toki (if installed) - active ✅
# - chronicle (if installed) - active ✅
# - pagen (if installed) - active ✅
```

Test each server:

**Gmail:**
```
"Check my inbox for unread emails"
```

**Calendar:**
```
"Show me my calendar for tomorrow"
```

**Toki:**
```
"Add a todo to follow up with Sarah next week"
"Show me all my pending todos"
```

**Chronicle:**
```
"Show me what actions I took today"
"Search my agent history for email-related actions"
```

**Pagen CRM:**
```
"Add John Doe from Acme Corp to my contacts"
"Show me all contacts from Acme Corp"
```

## Troubleshooting

### Pipedream (Gmail/Calendar) not working
- Check OAuth credentials are valid
- Re-authenticate: `/mcp` → select pd → reconnect
- Verify scopes include all required permissions
- Check internet connection

### Toki not working
- Verify installation: `which toki` (should show path)
- Test CLI directly: `toki list`
- Restart Claude Code to reload MCP servers
- Check logs: `~/.claude/logs/`

### Chronicle not working
- Verify installation: `which chronicle` (should show path)
- Test CLI directly: `chronicle list`
- Restart Claude Code to reload MCP servers
- Check logs: `~/.claude/logs/`

### Pagen not working
- Verify installation: `which pagen` (should show path)
- Check database initialized: `pagen status`
- Re-initialize if needed: `pagen init`
- Test CLI directly: `pagen contacts list`
- Restart Claude Code to reload MCP servers

### General MCP issues
- Restart Claude Code completely
- Check `~/.claude/logs/` for error messages
- Verify all tools are in PATH: `echo $PATH`
- Check MCP status: `/mcp` (shows all servers and their status)
- For homebrew tools, try reinstalling: `brew reinstall harperreed/tap/toki`

## Security Notes

- OAuth tokens (Pipedream) are stored securely by the MCP server
- Local data (Toki, Chronicle, Pagen) is stored in your home directory
- Never commit credentials or tokens to version control
- Regularly review connected apps in Google account settings
- All MCP servers run locally on your machine

## What Each Tool Does

**Quick reference:**

| Tool | Purpose | Data Storage |
|------|---------|-------------|
| **Pipedream** | Gmail + Calendar access | Cloud (OAuth tokens) |
| **Toki** | Todo tracking | Local SQLite |
| **Chronicle** | Agent action logging | Local file-based |
| **Pagen** | CRM (contacts/companies/deals) | Local database |

## Next Steps

After setting up MCP servers:

1. **Configure your style:**
   ```bash
   /setup-office
   ```

2. **Test email functionality:**
   ```bash
   /triage-email
   ```

3. **Draft a test email:**
   ```bash
   /draft-email
   ```

4. **Add a todo:**
   ```
   "Add a todo to review the office plugin documentation"
   ```

5. **Add a contact (if Pagen installed):**
   ```
   "Add Jane Smith from Example Corp to my contacts"
   ```

6. **Check agent history (if Chronicle installed):**
   ```
   "What have I accomplished today?"
   ```

## Support

For issues and questions:

- **Office plugin:** https://github.com/harperreed/cc-office-plugin/issues
- **Toki:** https://github.com/harperreed/toki/issues
- **Chronicle:** https://github.com/harperreed/chronicle/issues
- **Pagen:** https://github.com/harperreed/pagen/issues
- **Pipedream MCP:** https://github.com/PipedreamHQ/pipedream/issues
