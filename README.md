# Daily AI Engineering Briefing

A GitHub Actions workflow that sends you a daily email briefing on the latest AI news for software engineers — researched live every morning by Claude using real-time web search.

No server. No database. No dashboard to check. Just an email in your inbox every day.

---

## What's in each briefing

Claude searches the web every morning and surfaces 5–7 of the most relevant items for engineers, including:

- **Community signal** — what engineers are actually talking about on Hacker News, X/Twitter, and Reddit about Claude and AI tooling (not press releases)
- **Claude Code tips** — workflows, tricks, and techniques people are discovering
- **MCP servers** — new or noteworthy servers useful for dev workflows
- **Early-stage repos** — recently launched or newly viral GitHub repos with high star velocity, sourced from Trendshift, HN, and X — things people are just starting to talk about, not repos that have been popular for months
- **One official update** — at most one item from official Claude/Anthropic release notes

The prompt is tuned for senior software engineers but is easy to customise for your own role and interests.

---

## How it works

```
Every morning at 8:00 AM (your timezone)
         │
         ▼
GitHub Actions triggers the workflow
         │
         ▼
Claude API + web search researches today's AI news
         │
         ▼
Briefing is formatted as a clean HTML email
         │
         ▼
SendGrid delivers it to your inbox
```

No compute to manage. Runs entirely on GitHub's free tier.

---

## Setup

### 1. Fork or clone this repo

```bash
git clone https://github.com/your-username/daily-ai-briefing
cd daily-ai-briefing
```

### 2. Get your API keys

**Anthropic API key**
1. Go to [platform.claude.com](https://platform.claude.com)
2. API Keys → Create Key
3. Copy it

**SendGrid** (free tier handles 100 emails/day)
1. Sign up at [sendgrid.com](https://sendgrid.com)
2. Settings → API Keys → Create API Key (Full Access) → copy it
3. Settings → Sender Authentication → verify the email address you want to send from

### 3. Add secrets to your GitHub repo

Go to your repo → **Settings → Secrets and variables → Actions → New repository secret**

| Secret | Value |
|--------|-------|
| `ANTHROPIC_API_KEY` | Your Anthropic API key |
| `SENDGRID_API_KEY` | Your SendGrid API key |
| `FROM_EMAIL` | The sender email you verified in SendGrid |
| `TO_EMAIL` | The email address to deliver the briefing to |

### 4. Test it

Go to **Actions → Daily AI Briefing → Run workflow → Run workflow**

Check your inbox within 2–3 minutes. If nothing arrives, check the Actions tab for error logs.

---

## Customising the prompt

The prompt lives inside `.github/workflows/daily-ai-briefing.yml`. Find the `const prompt` variable and edit it to match your role, stack, or interests.

For example, you might change:

```
senior fullstack software engineer
```

to something like:

```
senior backend engineer focused on distributed systems and Go
```

or add a focus area:

```
... also interested in anything related to Rust, WebAssembly, or edge computing
```

The prompt instructs Claude to return structured JSON, so keep that section intact. Everything above it is fair game.

---

## Scheduling

The workflow runs at `0 16 * * *` UTC, which is **8:00 AM PST** (UTC-8).

During daylight saving time (March–November), PST becomes PDT (UTC-7), so the email arrives at 9:00 AM PDT. To keep it at 8:00 AM year-round, update the cron to `0 15 * * *` when clocks spring forward.

To change the delivery time, edit the cron line in the workflow file. Use [crontab.guru](https://crontab.guru) to convert your local time to UTC.

---

## Cost

| Service | Cost |
|---------|------|
| Anthropic API | ~$0.05–0.15 per briefing |
| SendGrid | Free up to 100 emails/day |
| GitHub Actions | Free on public repos; 2,000 min/month free on private repos |

Running daily, the Anthropic API cost works out to roughly **$1.50–4.50/month**.

---

## Troubleshooting

**No email received**
- Check the Actions tab for workflow errors
- Confirm SendGrid sender authentication is complete
- Make sure `TO_EMAIL` and `FROM_EMAIL` secrets are set correctly

**API errors from Anthropic**
- Verify `ANTHROPIC_API_KEY` has no extra spaces or line breaks
- Check that your Anthropic account has available credits

**Wrong delivery time**
- Adjust the cron schedule in the workflow file (all times are UTC)

**Briefing content feels stale**
- The prompt instructs Claude to look at the last 48 hours — if topics feel old, try adding "prioritise items from the last 24 hours" to the rules section of the prompt

---

## Stack

- **Claude** (Anthropic) — research and content generation with live web search
- **SendGrid** — email delivery
- **GitHub Actions** — scheduling and execution

---

## Contributing

PRs welcome. Some ideas if you want to contribute:

- Support for additional email providers (Resend, Postmark, SES)
- Multiple recipients
- A `/unsubscribe` link in the email footer
- Configurable delivery time via a repo variable instead of hardcoded cron

---

## License

MIT
