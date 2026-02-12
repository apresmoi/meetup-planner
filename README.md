# Meetup Planner

Never miss an event that matches your interests! This OpenClaw skill automatically finds, tracks, and reminds you about meetups, conferences, workshops, and other events tailored to your preferences.

## 🎯 Features

- **🔍 Intelligent Search**: Uses Brave Search and Firecrawl to discover events across the web
- **🤖 Automated Daily Scanning**: Searches every morning for new events matching your profile
- **📊 Smart Tracking**: Maintains a local database of discovered events
- **⏰ Timely Reminders**: Notifies you 24 hours and 2 hours before registered events
- **🎨 Personalized**: Learns your preferences for topics, locations, formats, and schedules
- **🔒 Privacy-First**: All data stored locally on your machine

## 📦 Installation

### Prerequisites

1. **Get API Keys** (the skill will guide you through this):
   - [Brave Search API Key](https://brave.com/search/api/) - For searching events
   - [Firecrawl API Key](https://firecrawl.dev/app/api-keys) - For scraping event details

### Install via ClawHub

```bash
npx clawhub@latest install meetup-planner
```

### Manual Installation

```bash
# Clone or download this skill
git clone https://github.com/apresmoi/meetup-planner.git

# Copy to your Claude skills directory
mkdir -p ~/.claude/skills/meetup-planner
cp -r meetup-planner/* ~/.claude/skills/meetup-planner/
```

## 🚀 Quick Start

1. **Initial Setup**: Invoke the skill in your Claude session. It will:
   - Install required skills (`firecrawl/cli` and `brave-search`)
   - Help you configure API keys
   - Interview you about your event preferences
   - Set up daily automated searches

2. **Daily Use**: The skill runs automatically every morning to find new events

3. **Review Events**: When new events are found, you'll be notified to review them

4. **Register for Events**: Tell Claude which events interest you, and it will:
   - Provide the registration link
   - Set up reminders before the event

## 🎨 Example Usage

```
You: Run meetup-planner

Claude: Let me set up your event finder! First, I'll check if you have
        the required skills installed...

        [Installs firecrawl/cli and brave-search if needed]

        Great! Now I need to learn about your preferences.
        What types of events are you interested in?

You: I love AI/ML meetups, hackathons, and tech conferences

Claude: Awesome! What's your location?

You: San Francisco

Claude: Perfect! [continues preference collection...]

        All set! I'll search for events every morning at 8 AM and
        let you know what I find. 🎉
```

## 📋 Commands

Once set up, you can use these commands:

- `update preferences` - Modify your event preferences
- `show upcoming` - Display all tracked events you're registered for
- `show new events` - See events discovered but not yet reviewed
- `search now` - Manually trigger an event search
- `remove event [name]` - Stop tracking a specific event
- `pause search` - Temporarily stop daily searches
- `resume search` - Resume daily searches
- `list past events` - Show events that have occurred

## 🗂️ Data Storage

All data is stored locally in `~/.claude/meetup-finder/`:

```
~/.claude/meetup-finder/
├── user-preferences.json    # Your event preferences
├── events.json              # All discovered and tracked events
├── reminders.json           # Scheduled reminders
├── config.json              # Skill configuration
└── backups/                 # Automatic backups
```

## 🔐 Privacy & Security

- **No data leaves your machine** except API calls to search for events
- **API keys are stored as environment variables** (never hardcoded)
- **All event data is local** - no cloud storage or third-party services
- **Open source** - review the code yourself!

## 🛠️ Technical Details

### Dependencies

- **Skills**: `firecrawl/cli`, `brave-search` (auto-installed)
- **APIs**: Brave Search API, Firecrawl API (keys required)
- **System**: Cron or equivalent for scheduled tasks

### Event Sources

The skill searches across:
- Eventbrite
- Meetup.com
- Luma
- Conference websites
- Community forums
- And more!

## 🐛 Troubleshooting

**Skill doesn't find events:**
- Try broadening your search terms in preferences
- Check that your API keys are valid
- Ensure your location is correctly set

**Reminders not working:**
- Verify cron job is set up: `crontab -l`
- Check `~/.claude/meetup-finder/reminders.json` for scheduled reminders

**API errors:**
- Confirm API keys are set correctly
- Check API rate limits (Brave Search: varies by plan)

## 🤝 Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## 📝 License

MIT License

## 🙏 Credits

Created at [Claude Code Office Hours](https://luma.com/8k58g33a?tk=hJfl8z)

Built with:
- [Brave Search API](https://brave.com/search/api/)
- [Firecrawl](https://firecrawl.dev/)
- [OpenClaw](https://openclaw.ai/)
- [ClawHub Skills](https://clawhub.ai/)

## 📞 Support

- Issues: [GitHub Issues](https://github.com/apresmoi/meetup-planner/issues)
- Discussions: [GitHub Discussions](https://github.com/apresmoi/meetup-planner/discussions)

---

**Made with ❤️ for the OpenClaw community**
