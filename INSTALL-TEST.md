# CC-Office Installation and Testing Guide

## Quick Install for Testing

### Step 1: Add Development Marketplace

```bash
/plugin marketplace add /Users/harper/Library/Mobile\ Documents/com~apple~CloudDocs/src/pw/cc-office-plugin
```

This registers the local plugin directory as a marketplace.

### Step 2: Install the Plugin

```bash
/plugin install cc-office@cc-office-dev
```

### Step 3: Restart Claude Code

**CRITICAL:** You must completely restart Claude Code for the plugin to load.

```bash
# Exit Claude Code completely, then restart
```

### Step 4: Verify Installation

After restart, check that the plugin loaded:

```bash
/plugin list
```

You should see:
```
cc-office@cc-office-dev - installed
```

Check skills are registered:

```bash
# Skills should auto-activate, but you can list them:
# (There's no /skill list command, so just try using one)
```

Check commands are registered:

```bash
/help
```

Look for:
- `/setup-cc-office`
- `/triage-email`
- `/draft-email`

## Step 5: Run Onboarding

```bash
/setup-cc-office
```

This will:
1. Ask about your email style
2. Configure calendar preferences
3. Set up CRM workflow
4. Create `~/.claude/cc-office-config.json`

## Step 6: Test Basic Functionality

### Test Email Management

```bash
# Test finding emails (requires Gmail MCP configured)
Find unread emails in my inbox
```

```bash
# Test email drafting
/draft-email
```

### Test Calendar Management

```bash
# Test availability checking
Check my calendar for tomorrow
```

```bash
# Test event creation
Add a test event to my calendar tomorrow at 2pm
```

### Test CRM (if configured)

```bash
# Test contact search
Search for contacts named John
```

```bash
# Test adding contact
Add a test contact: Jane Doe, jane@example.com
```

## Step 7: Test Bulk Operations

```bash
# Test bulk email triage
/triage-email
```

This should:
1. Find unread emails
2. Categorize them
3. Draft replies for ACTION NEEDED
4. Add calendar events from CALENDAR emails
5. Present summary

## Debugging

### Plugin Not Loading

1. **Check marketplace added correctly:**
   ```bash
   # Should show cc-office-dev
   /plugin marketplace list
   ```

2. **Check plugin directory structure:**
   ```bash
   ls -R /Users/harper/Library/Mobile\ Documents/com~apple~CloudDocs/src/pw/cc-office-plugin/
   ```

   Should show:
   - `.claude-plugin/plugin.json`
   - `.claude-plugin/marketplace.json`
   - `skills/*.md`
   - `commands/*.md`

3. **Check plugin.json syntax:**
   ```bash
   jq . /Users/harper/Library/Mobile\ Documents/com~apple~CloudDocs/src/pw/cc-office-plugin/.claude-plugin/plugin.json
   ```

### Skills Not Activating

1. **Ensure config exists:**
   ```bash
   cat ~/.claude/cc-office-config.json
   ```

   If missing, run `/setup-cc-office`

2. **Try explicit skill invocation:**
   Ask Claude to use a specific skill explicitly:
   ```
   Use the cc-office:email-management skill to draft an email
   ```

### Commands Not Showing

1. **Verify command files exist:**
   ```bash
   ls /Users/harper/Library/Mobile\ Documents/com~apple~CloudDocs/src/pw/cc-office-plugin/commands/
   ```

2. **Check command file format:**
   Commands should be plain markdown with instructions

3. **Restart Claude Code** - Commands only load on startup

## Uninstalling (for testing iterations)

```bash
# Uninstall the plugin
/plugin uninstall cc-office@cc-office-dev

# Make changes to plugin files

# Reinstall
/plugin install cc-office@cc-office-dev

# Restart Claude Code
```

## Next Steps After Testing

Once basic testing works:

1. **Run full test suite** from `TESTING.md`
2. **Document any issues** found
3. **Fix bugs** and iterate
4. **Create GitHub repo** when stable
5. **Push to GitHub**
6. **Update README** with real repo URL
7. **Share with community**

## Quick Reference

**Plugin location:** `/Users/harper/Library/Mobile Documents/com~apple~CloudDocs/src/pw/cc-office-plugin/`

**Config location:** `~/.claude/cc-office-config.json`

**Style guide location:** `~/.claude/docs/cc-office-style.md`

**Logs location:** `~/.claude/logs/`

**Install commands:**
```bash
/plugin marketplace add /Users/harper/Library/Mobile\ Documents/com~apple~CloudDocs/src/pw/cc-office-plugin
/plugin install cc-office@cc-office-dev
# Restart Claude Code
/setup-cc-office
```
