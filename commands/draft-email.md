Draft an email in your personal style with guided assistance. Use the office:email-management skill.

**Workflow:**

1. **Ask clarifying questions:**
   - Who is this email to? (name or email address)
   - Is this a reply to an existing email? (yes/no)
   - What's the main purpose? (scheduling, confirming, declining, requesting, etc.)

2. **If replying to existing email:**
   - Search for the original email thread
   - Get thread ID, message ID, and recipient email
   - Read original email for context

3. **If calendar-related (scheduling):**
   - Use office:calendar-management skill to check availability
   - Include specific available times in draft
   - Note any conflicts that need to be resolved

4. **Draft the email:**
   - Load user's style from `~/.claude/office-admin-config.json`
   - Match their tone, formality, and response length preferences
   - Use their common phrases from config
   - Apply signature preference (none/minimal/full)
   - **CRITICAL:** Always include explicit `To:` email address

5. **Create the draft:**
   - Save as DRAFT (never send)
   - Ensure proper threading if reply
   - Verify subject line is appropriate
   - Provide link to draft for user review

6. **Post-draft actions:**
   - If email contains event info, offer to add to calendar
   - If mentioning a new contact, offer to add to CRM
   - Summarize what was created

**Example interaction:**
```
User: /draft-email

Claude: I'll help you draft an email. Let me ask a few questions:

1. Who is this email to? (name or email address)
2. Is this a reply to an existing email?
3. What's the main purpose? (scheduling, confirming, declining, requesting, etc.)

User: It's a reply to Sarah about scheduling a call next week

Claude: Let me search for Sarah's email... Found it!

I see Sarah asked about scheduling a call to discuss the Q1 project.

Let me check your calendar for next week...

You're available:
- Monday 2:00pm-4:30pm
- Tuesday 9:30am-12:00pm, 1:30pm-4:30pm
- Wednesday 10:00am-12:00pm

I'll draft a reply in your ultra-terse style:

---
**DRAFT CREATED:**

To: sarah@example.com
Subject: Re: Q1 Project Call

that works perfect. tuesday 2pm?

---

Draft saved to Gmail. Would you like me to:
- Add this to your calendar as a hold?
- Adjust the wording?
- Send it now?
```