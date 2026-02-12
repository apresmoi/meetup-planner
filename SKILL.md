---
name: meetup-planner
description: An intelligent event finder that searches for meetups and events based on your interests, tracks them, and reminds you before they happen
---

# Meetup Planner

An intelligent assistant that helps you discover, track, and never miss events that match your interests.

## What This Skill Does

1. **Setup Phase**: Installs required skills (Firecrawl, Brave Search) and helps you get API credentials
2. **Preference Collection**: Learns about your event preferences and interests
3. **Daily Search**: Automatically searches for events matching your profile every morning
4. **Event Tracking**: Saves and presents new events for your review
5. **Smart Reminders**: Sets up notifications 24 hours and 2 hours before confirmed events

## First Time Setup

When you first run this skill, I will:

1. **Install Required Skills**:
   - Check if `firecrawl/cli` skill is installed, if not run: `npx skills add firecrawl/cli`
   - Check if `brave-search` skill is installed, if not run: `npx clawhub@latest install brave-search`

2. **Setup API Keys**:
   - **Brave Search API**: Check if `BRAVE_API_KEY` is configured
     - If not found, send your human to: https://brave.com/search/api/ to register and get an API key
     - Wait for human to provide the key, then configure it
   - **Firecrawl API**: Check if `FIRECRAWL_API_KEY` is configured
     - If not found, send your human to: https://firecrawl.dev/app/api-keys to register and get an API key
     - Wait for human to provide the key, then configure it

3. **Learn Your Human's Preferences**:
   - What types of events are you interested in? (tech meetups, networking, conferences, workshops, hackathons, etc.)
   - What topics excite you? (AI/ML, web development, blockchain, entrepreneurship, design, data science, etc.)
   - What's your location or preferred event locations? (city, region, or "remote" for virtual)
   - Do you prefer in-person, virtual, or hybrid events?
   - What days/times work best for you? (weekday evenings, weekends, etc.)
   - What time commitment are you looking for? (1-2 hours, half-day, full-day, multi-day)
   - Any specific organizations or communities you follow?
   - Any deal-breakers or must-haves? (free events, small groups, beginner-friendly, etc.)

4. **Setup Daily Search**:
   - Configure a morning routine to search for new events (default: 8 AM daily)
   - Set your preferred notification time

## How to Use

### Initial Run
```
Run the meetup-planner skill to begin setup
```

### Daily Operations
Once set up, the skill will:
- Search for events every morning automatically
- Save findings to `events.json`
- Present new events for your review
- Track events you're interested in

### When You Find an Event You Like
Tell me "I'm interested in [event name]" and I will:
- Mark it as confirmed
- Send you the registration link
- Set up reminders (24h and 2h before the event)

### Commands
- `update preferences` - Modify your event preferences
- `show upcoming` - Display all tracked events
- `remove event [name]` - Stop tracking an event
- `pause search` - Temporarily stop daily searches
- `resume search` - Resume daily searches

## Data Storage

The skill maintains:
- `user-preferences.json` - Your event preferences
- `events.json` - All discovered and tracked events
- `event-reminders.json` - Scheduled reminders

## Technical Details

**APIs Used:**
- Brave Search API - For discovering events across the web
- Firecrawl API - For scraping event details from websites

**Scheduling:**
- Uses system cron jobs (or equivalent) for daily searches
- Uses scheduled tasks for event reminders

## Privacy Note

All data is stored locally on your machine. Your preferences and tracked events are never sent anywhere except to search for new events via the configured APIs.

## Data Transmission & External API Usage

This skill makes external network requests to two services. Here's exactly what data is transmitted:

### To Brave Search API (brave.com)
**What is sent:**
- Search query strings constructed from your preferences (e.g., "AI meetup San Francisco February 2026")
- Your API key (in HTTP headers for authentication)
- Your IP address (automatically sent by your network stack)

**What is NOT sent:**
- Your complete preference profile
- Event registration status or history
- Personal notes or modifications
- Other tracked events

**Purpose:** To discover public events matching your interests across the web.

### To Firecrawl API (firecrawl.dev)
**What is sent:**
- URLs of event pages to scrape (discovered from Brave Search results)
- Your API key (in HTTP headers for authentication)
- Your IP address (automatically sent by your network stack)

**What is NOT sent:**
- Your preferences or search queries
- Event tracking status
- Any personal information

**Purpose:** To extract structured event details (title, date, location, description) from event pages.

### Data Minimization Practices
- Only essential data is transmitted to accomplish the task
- API keys are transmitted securely over HTTPS only
- No telemetry, analytics, or usage tracking is performed
- No data is sent to any other third-party services

### Reviewing Network Activity
To monitor what this skill sends:
```bash
# Monitor network connections (macOS)
sudo tcpdump -i any host brave.com or host firecrawl.dev

# Monitor network connections (Linux)
sudo tcpdump -i any host brave.com or host firecrawl.dev
```

---

## Agent Instructions

When this skill is invoked:

### Phase 1: Setup (First Time Only)

1. **Check for required skills:**
   ```bash
   # Check if firecrawl skill exists
   ls ~/.claude/skills/firecrawl*
   # If not found, install it with pinned version
   npx skills@1.x add firecrawl/cli@1

   # Check if brave-search skill exists
   ls ~/.claude/skills/brave-search*
   # If not found, install it with pinned version
   npx clawhub@1.x install brave-search@1
   ```

   **Security Note**: Version pinning prevents automatic installation of potentially compromised newer versions. Before upgrading, always review the changelog and verify package integrity.

2. **Verify API credentials:**
   - Check if `BRAVE_API_KEY` environment variable is set or can be retrieved from secure storage
   - If not, tell your human: "I need you to get a Brave Search API key. Please visit https://brave.com/search/api/ to register and get your API key. Once you have it, let me know and I'll help you configure it securely."
   - Wait for human response with the key
   - **Store the key securely using one of these methods (in order of preference):**
     a. **macOS Keychain** (most secure):
        ```bash
        security add-generic-password -a "$USER" -s "claude-meetup-planner-brave" -w "BRAVE_API_KEY_VALUE"
        ```
     b. **Linux Secret Service**:
        ```bash
        secret-tool store --label='Brave API Key for Meetup Planner' application claude-meetup-planner service brave-api
        ```
     c. **Environment variable** (least secure, only for trusted environments):
        ```bash
        # Add to shell profile
        export BRAVE_API_KEY="key-value"
        ```
   - Inform the human which method was used and where the key is stored

   - Check if `FIRECRAWL_API_KEY` environment variable is set or can be retrieved from secure storage
   - If not, tell your human: "I also need a Firecrawl API key. Please visit https://firecrawl.dev/app/api-keys to register and get your API key. Once you have it, let me know and I'll configure it securely."
   - Wait for human response with the key
   - **Store the key securely using the same method as above:**
     a. **macOS Keychain**:
        ```bash
        security add-generic-password -a "$USER" -s "claude-meetup-planner-firecrawl" -w "FIRECRAWL_API_KEY_VALUE"
        ```
     b. **Linux Secret Service**:
        ```bash
        secret-tool store --label='Firecrawl API Key for Meetup Planner' application claude-meetup-planner service firecrawl-api
        ```
     c. **Environment variable**:
        ```bash
        export FIRECRAWL_API_KEY="key-value"
        ```
   - **IMPORTANT**: Never log or display the full API keys. Always show them redacted (e.g., "sk-****...abc123")
   - Save a reference to the storage method in `~/.claude/meetup-finder/config.json`:
     ```json
     {
       "credential_storage": "keychain|secret-service|env",
       "last_credential_check": "ISO-timestamp"
     }
     ```

3. **Collect preferences through a conversational interview:**
   - Ask each question one at a time, in a friendly conversational way
   - Save responses to `~/.claude/meetup-finder/user-preferences.json`
   - Use the human's answers to build a rich preference profile

4. **Setup automation:**
   - Create a cron job (or equivalent) for daily searches
   - Default: Every day at 8:00 AM
   - Store cron configuration in `~/.claude/meetup-finder/config.json`

### Phase 2: Daily Search Routine

1. **Load preferences:**
   - Read `~/.claude/meetup-finder/user-preferences.json`
   - Parse the human's interests, location, preferred event types, etc.

2. **Search for events:**
   - Use the **brave-search skill** to search for events matching preferences
   - Search queries should be constructed like:
     - "{topic} meetup {location} {current_month}"
     - "{topic} conference {location} upcoming"
     - "{topic} workshop {location}"
   - Run multiple searches covering all their topics of interest

3. **Extract event details:**
   - For each promising search result, use the **firecrawl skill** to scrape the event page
   - Extract: event name, date, time, location, description, registration link, cost
   - Look for: Eventbrite, Meetup.com, Luma, conference sites, etc.

4. **Filter and save:**
   - Load existing events from `~/.claude/meetup-finder/events.json`
   - Filter out duplicates and events that don't match criteria
   - Add new events to the file
   - Mark each event with: `{id, name, date, time, location, url, description, cost, added_date, status: "new"}`

5. **Present to human:**
   - Format new events nicely with all key details
   - Ask: "I found X new events that match your interests. Would you like to hear about them?"
   - Share event details one by one or as a list
   - For each event, ask if they're interested

### Phase 3: Event Confirmation & Tracking

1. **When human expresses interest:**
   - Update event status to "interested" in `events.json`
   - Provide the registration link: "Here's the link to register: {url}"
   - Ask: "Let me know when you've registered!"

2. **When human confirms registration:**
   - Update event status to "registered" in `events.json`
   - Schedule reminders in `~/.claude/meetup-finder/reminders.json`:
     ```json
     {
       "event_id": "abc123",
       "event_name": "...",
       "reminders": [
         {"time": "24_hours_before", "sent": false},
         {"time": "2_hours_before", "sent": false}
       ]
     }
     ```
   - Confirm: "Great! I'll remind you 24 hours before and 2 hours before the event."

### Phase 4: Reminder System

1. **Check for due reminders** (run this check every hour):
   - Load `~/.claude/meetup-finder/reminders.json`
   - Check current time against event time
   - If within 24-25 hours before event and 24h reminder not sent:
     - Notify human: "Reminder: {event_name} is tomorrow at {time}! Location: {location}"
     - Mark 24h reminder as sent
   - If within 2-3 hours before event and 2h reminder not sent:
     - Notify human: "Heads up! {event_name} starts in 2 hours at {time}. Time to get ready!"
     - Mark 2h reminder as sent

2. **Post-event cleanup:**
   - After event date passes, move event to "past" status
   - Optionally ask: "How was {event_name}? Would you like me to look for similar events?"

### Phase 5: Ongoing Commands

Support these commands from your human:

- **"update preferences"** / **"change preferences"**: Re-run the preference collection interview
- **"show upcoming"**: Display all events with status "interested" or "registered"
- **"show new events"**: Display events with status "new" that haven't been reviewed
- **"remove event [name]"**: Remove an event from tracking
- **"pause search"**: Stop daily automated searches (update config)
- **"resume search"**: Resume daily automated searches
- **"search now"**: Run the search routine immediately
- **"list past events"**: Show events that have already occurred

## Error Handling

- **If skills fail to install:** Provide manual instructions and the GitHub links
- **If API keys are invalid:** Ask human to verify and provide new keys
- **If searches return no results:** Try broader search terms or suggest different topics
- **If cron setup fails:** Provide manual cron configuration instructions
- **If event scraping fails:** Fall back to showing just the search result link
- **Always preserve data:** Never overwrite `events.json` or `preferences.json` without backing up

## File Structure

```
~/.claude/meetup-finder/
├── user-preferences.json    # Human's event preferences
├── events.json              # All discovered and tracked events
├── reminders.json           # Scheduled reminders
├── config.json              # Skill configuration (cron schedule, etc.)
└── backups/                 # Automatic backups of data files
```
