# Meetup Planner Bootstrap (one-time setup)

Run this setup **once** after installing the skill. The agent will check `.openclaw/workspace/memory/meetup-planner.json` to see if bootstrap is complete.

---

## 1) Install required skills

Check and install dependencies:

```bash
# Check if firecrawl skill exists
ls ~/.openclaw/skills/firecrawl* 2>/dev/null || npx skills@1.x add firecrawl/cli@1

# Check if brave-search skill exists
ls ~/.openclaw/skills/brave-search* 2>/dev/null || npx clawhub@1.x install brave-search@1
```

**Security Note**: Version pinning prevents automatic installation of potentially compromised newer versions.

---

## 2) Configure API credentials securely

### Check existing credentials

Check if credentials are already configured:
```bash
# Check for Brave API key
echo ${BRAVE_API_KEY:0:10}... 2>/dev/null

# Check for Firecrawl API key
echo ${FIRECRAWL_API_KEY:0:10}... 2>/dev/null
```

### If missing, get API keys from human

**BRAVE_API_KEY:**
- Tell human: "I need you to get a Brave Search API key. Please visit https://brave.com/search/api/ to register and get your API key. Once you have it, let me know and I'll help you configure it securely."
- Wait for human to provide the key
- Store securely (see storage methods below)

**FIRECRAWL_API_KEY:**
- Tell human: "I also need a Firecrawl API key. Please visit https://firecrawl.dev/app/api-keys to register and get your API key. Once you have it, let me know and I'll configure it securely."
- Wait for human to provide the key
- Store securely (see storage methods below)

### Secure storage methods (in order of preference)

**Option A: macOS Keychain** (most secure):
```bash
# Store Brave API key
security add-generic-password -a "$USER" -s "claude-meetup-planner-brave" -w "BRAVE_API_KEY_VALUE"

# Store Firecrawl API key
security add-generic-password -a "$USER" -s "claude-meetup-planner-firecrawl" -w "FIRECRAWL_API_KEY_VALUE"

# Retrieve when needed:
# security find-generic-password -a "$USER" -s "claude-meetup-planner-brave" -w
```

**Option B: Linux Secret Service**:
```bash
# Store Brave API key
secret-tool store --label='Brave API Key for Meetup Planner' application claude-meetup-planner service brave-api

# Store Firecrawl API key
secret-tool store --label='Firecrawl API Key for Meetup Planner' application claude-meetup-planner service firecrawl-api

# Retrieve when needed:
# secret-tool lookup application claude-meetup-planner service brave-api
```

**Option C: Environment variables** (least secure, only for trusted environments):
```bash
# Add to shell profile (~/.zshrc, ~/.bashrc, etc.)
export BRAVE_API_KEY="your-brave-api-key"
export FIRECRAWL_API_KEY="your-firecrawl-api-key"

# Then reload:
source ~/.zshrc  # or source ~/.bashrc
```

**CRITICAL SECURITY:**
- **Never log or display the full API keys** - always show redacted (e.g., "bra_****...abc123")
- Never commit keys to version control
- Use least-privilege keys (create keys specifically for this skill)
- Rotate keys every 90 days

---

## 3) Collect user preferences

Ask the human these questions **one at a time** in a friendly, conversational way:

1. **Event types**: What types of events are you interested in?
   *(Examples: tech meetups, networking, conferences, workshops, hackathons)*

2. **Topics**: What topics excite you?
   *(Examples: AI/ML, web development, blockchain, entrepreneurship, design, data science)*

3. **Location**: What's your location or preferred event locations?
   *(Examples: "San Francisco", "Berlin", "remote" for virtual)*

4. **Format preference**: Do you prefer in-person, virtual, or hybrid events?

5. **Schedule**: What days/times work best for you?
   *(Examples: weekday evenings, weekends, any)*

6. **Time commitment**: What time commitment are you looking for?
   *(Examples: 1-2 hours, half-day, full-day, multi-day)*

7. **Organizations** (optional): Any specific organizations or communities you follow?

8. **Requirements**: Any deal-breakers or must-haves?
   *(Examples: free events only, small groups, beginner-friendly)*

### Store preferences

Save all responses to `.openclaw/workspace/memory/meetup-planner.json`:

```json
{
  "bootstrapComplete": true,
  "bootstrapVersion": "1.0.0",
  "credentialStorage": "keychain|secret-service|env",
  "lastCredentialCheck": "2026-02-12T19:00:00Z",
  "preferences": {
    "eventTypes": ["tech meetups", "workshops"],
    "topics": ["AI/ML", "web development"],
    "location": "San Francisco",
    "formatPreference": "in-person",
    "schedule": "weekday evenings",
    "timeCommitment": "1-2 hours",
    "organizations": [],
    "requirements": ["beginner-friendly"]
  },
  "searchSchedule": {
    "enabled": true,
    "frequency": "daily",
    "time": "08:00",
    "timezone": "America/Los_Angeles"
  },
  "lastSearchAt": null,
  "lastHeartbeatAt": null
}
```

**Contract (schema):**
- Required fields: `bootstrapComplete`, `bootstrapVersion`, `credentialStorage`, `preferences`
- Optional fields: `lastSearchAt`, `lastHeartbeatAt`, `searchSchedule`

---

## 4) Set up daily search automation

Ask human: "Would you like me to set up automated daily event searches? I can search every morning and notify you of new events."

If yes:

### Configure search schedule

Ask:
- "What time should I search for events each day?" (default: 8:00 AM)
- "What timezone are you in?" (default: system timezone)

Update the `searchSchedule` section in `.openclaw/workspace/memory/meetup-planner.json`.

### Create cron job

```bash
# Create cron job for daily searches (example for 8 AM)
(crontab -l 2>/dev/null; echo "0 8 * * * cd ~/.openclaw/workspace && echo 'run meetup-planner search' | claude-cli") | crontab -
```

**Note**: Adjust cron syntax based on the human's preferred time and timezone.

### Inform human

Tell them:
- "✅ Daily search configured for [TIME] [TIMEZONE]"
- "I'll search for new events every morning and notify you of matches"
- "You can pause/resume searches anytime with 'pause search' or 'resume search'"

---

## 5) Create workspace directories

Ensure workspace structure exists:

```bash
# Create main workspace directory
mkdir -p ~/.openclaw/workspace/memory

# Create event data directory
mkdir -p ~/.openclaw/workspace/meetup-planner/{events,backups}

# Set proper permissions (owner only)
chmod 700 ~/.openclaw/workspace/meetup-planner
chmod 600 ~/.openclaw/workspace/memory/meetup-planner.json
```

Initialize empty data files:

```bash
# Events database
echo '{"events":[],"lastUpdated":null}' > ~/.openclaw/workspace/meetup-planner/events/events.json

# Reminders
echo '{"reminders":[]}' > ~/.openclaw/workspace/meetup-planner/events/reminders.json
```

---

## 6) Enable the heartbeat (optional, for active monitoring)

Add to `.openclaw/workspace/HEARTBEAT.md`:

```md
## Meetup Planner (daily or as configured)
If due (check `lastHeartbeatAt` in `.openclaw/workspace/memory/meetup-planner.json`), run event search and update timestamp.
```

---

## 7) Confirmation & first search

After all steps complete:

1. **Confirm to human:**
   ```
   ✅ Meetup Planner setup complete!

   Summary:
   • Required skills: installed ✓
   • API credentials: configured securely ✓
   • Your preferences: saved ✓
   • Daily searches: [enabled/disabled] ✓

   Credential storage: [keychain/secret-service/environment variables]
   Search schedule: [Daily at X AM / Manual only]

   What's next:
   • I can run a search right now to find events matching your interests
   • Or wait until tomorrow morning for the first automated search
   • You can update preferences anytime with "update preferences"
   ```

2. **Ask**: "Would you like me to run an event search right now to test everything?"

3. **If yes**: Run Phase 2 (Event Search) from SKILL.md

---

## Idempotency (important!)

**Before starting bootstrap:**
1. Check if `.openclaw/workspace/memory/meetup-planner.json` exists
2. Check if `bootstrapComplete: true` is set
3. If already complete, ask human: "Meetup Planner is already set up. Would you like to:
   - Update your preferences
   - Reconfigure API credentials
   - Run a search now
   - Skip (nothing to do)"

**Don't redo setup if already complete** unless human explicitly asks to reconfigure.

---

## Security Checklist

Before completing bootstrap, verify:

- [ ] API keys stored securely (not in plaintext files)
- [ ] Credentials never logged to console or files
- [ ] File permissions set to 600/700 (owner only)
- [ ] Version-pinned dependencies installed
- [ ] Human confirmed and understands what data is sent externally
- [ ] Workspace directories created with proper permissions

---

## Troubleshooting

**Skills fail to install:**
- Provide manual installation instructions from SKILL.md
- Link to GitHub repositories

**API keys invalid:**
- Ask human to verify keys from provider dashboards
- Test with a simple API call
- Provide new keys and reconfigure

**Cron setup fails:**
- Provide manual cron configuration instructions
- Offer alternative: "I can search manually when you ask me to"

**Permissions errors:**
- Check file permissions with `ls -la`
- Fix with `chmod 600` or `chmod 700`

---

**Bootstrap version**: 1.0.0
**Last updated**: 2026-02-12
