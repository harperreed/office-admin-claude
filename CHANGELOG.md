# Changelog

All notable changes to the office-admin-claude plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2025-12-02

### Added

#### Email Management
- Gmail integration via MCP server for full email operations
- Draft email creation with proper threading support
- Email archiving and labeling capabilities
- Email search and retrieval
- Attachment download functionality
- Thread message listing
- Signature management for primary and organizational emails

#### Calendar Management
- Google Calendar integration via MCP server
- Event creation with timezone support (America/Chicago)
- Free/busy calendar queries
- Quick event addition
- Event retrieval and listing
- Calendar conflict detection
- Multi-calendar support

#### CRM Management
- Pagen CRM integration for contact and company management
- Contact creation and relationship tracking
- Company management and organization
- Deal pipeline tracking
- Interaction logging
- Follow-up cadence management
- GraphViz relationship visualization

#### Productivity Features
- Chronicle integration for ambient activity logging
- Toki task management for human-only work items
- Automated email triage workflows
- Proactive calendar conflict detection
- Session logging for all operations

### Features

- Ultra-concise email drafting matching Harper's communication style
- Automatic calendar event extraction from emails
- Proper email threading for all draft replies
- Subagent support for bulk email operations (10+ emails)
- Working hours awareness (9:30am - 4:30pm, lunch 12-1:30pm)

### Known Issues

- Email threading may require verification on first attempt
- Calendar timezone conversions need manual verification for non-Chicago zones
- Some MCP tools require explicit recipient email addresses (not auto-extracted)

### Security

- All credentials managed via MCP server authentication
- No sensitive data stored in plugin code
- Archive-only policy (never delete emails)

## Future Roadmap

### Planned Features
- Notion integration for note-taking and documentation
- Slack integration for team communication
- Enhanced calendar scheduling with multiple timezone support
- Smart email categorization and auto-labeling
- Meeting preparation automation
- Follow-up reminder system
- Integration with additional productivity services

### Potential Improvements
- Better error handling for API rate limits
- Caching layer for frequently accessed data
- Bulk operation performance optimizations
- Enhanced calendar conflict resolution
- Automated meeting notes generation

[1.0.0]: https://github.com/harperreed/office-admin-claude/releases/tag/v1.0.0
