Perform bulk email triage on your inbox:

1. Use the office:email-management skill
2. Search for unread emails in your inbox
3. Categorize each email as:
   - **ACTION NEEDED** - Requires response or calendar addition
   - **CALENDAR** - Contains event information (extract and check conflicts)
   - **ARCHIVE** - Informational only, no action needed
4. For ACTION NEEDED emails, draft replies using your personal style from `~/.claude/office-admin-config.json`
5. For CALENDAR emails, proactively add events and check for conflicts using office:calendar-management skill
6. Present a comprehensive summary organized by priority

If there are more than 10 unread emails, use a Task tool with general-purpose subagent for efficient bulk processing.

Format the output as:
```
## 📧 Inbox Triage Summary

### 🔴 ACTION NEEDED ([count])
1. **From:** [sender] | **Subject:** [subject]
   **Summary:** [brief summary]
   **Draft:** [link to created draft or "Draft created in Gmail"]

### 📅 CALENDAR ([count])
1. **From:** [sender] | **Subject:** [subject]
   **Event:** [event details]
   **Conflicts:** [none | list conflicts]
   **Status:** [added to calendar | hold created | awaiting confirmation]

### 📋 ARCHIVE ([count])
1. **From:** [sender] | **Subject:** [subject]
   **Summary:** [brief summary]

---
**Total processed:** [count] emails
```
