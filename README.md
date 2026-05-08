# Expert Profile Skill

A Claude Skill that builds comprehensive profiles on experts, executives, and thought leaders by collecting their digital footprint and synthesizing it into actionable intelligence.

## What it does

Given a person's name (and optionally their company), this skill:

1. Searches the web for their LinkedIn, Twitter/X, YouTube, and blog
2. Asks you to confirm the right person before scraping anything
3. Collects 30 LinkedIn posts, 30 tweets, top 3 YouTube interview transcripts, and 10 recent blog posts
4. Synthesizes everything into a 10-section markdown profile

**Cost per profile:** ~$0.22 in Apify usage.

**What you get:** A markdown file ready for sales calls, podcast prep, pitches, or content where you need to quote someone accurately.

## Repo structure

```
expert-profile-skill/
├── SKILL.md                          (workflow — read first)
├── README.md
├── LICENSE
└── references/
    ├── profile-template.md           (10-section output template)
    └── apify-actors.md               (actor schemas, alternatives, cost math)
```

The split is deliberate. `SKILL.md` is what Claude reads every time. The reference files only get loaded during the steps that need them — keeping the context window lean.

## Installation

The blog post that explains the install in detail: **[What Is a Claude Skill? Let's Look Closely at One.](https://chrislema.com/what-is-a-claude-skill)**

Short version, two parts:

### 1. Connect Apify

You'll need an [Apify account](https://apify.com) (free tier is plenty) and the Apify MCP server connected to Claude. If you've never set up an MCP, paste this into a Claude conversation:

> *"I want to connect the Apify MCP server to Claude so I can use it with the Expert Profiler skill. I've never set up an MCP before. Before you give me any instructions, ask me what version of Claude I'm using, what operating system I'm on, and where I'd prefer to store config files. Then walk me through the setup one step at a time. Confirm each step worked before moving to the next one."*

Claude will guide you through it.

### 2. Install the skill

Drop the contents of this repo into your Claude skills folder. For most setups: `~/.claude/skills/expert-profile-skill/`.

Same prompt pattern works:

> *"I want to install this Claude skill from GitHub: https://github.com/chrislema/expert-profile-skill. I've never installed a skill before. Before you give me instructions, ask me what version of Claude I'm using and what operating system I'm on. Then walk me through it one step at a time, confirming each step worked before moving to the next."*

## One small note

The skill saves profiles to `/mnt/user-data/outputs/`, which is the path Claude.ai uses. If you're running this in Claude Code, change line 137 of `SKILL.md` to a path that exists on your machine. `~/Documents/profiles/` works fine. Or just delete that line and let Claude ask you where to save it.

## What to do with the output

A few patterns that work:

- **Pre-meeting research.** Run it the night before any sales call or prospect conversation.
- **Podcast prep.** Run it on a guest, then ask Claude to draft 10 questions that pull on threads from the profile.
- **Cold outreach that doesn't suck.** Run it on someone you want to reach, then ask Claude to draft an opener that references something specific from their public writing.
- **Content research.** Run it on someone you're writing about, then quote them accurately and reference their actual frameworks instead of guessing.

## License

MIT — see [LICENSE](LICENSE). Take it. Modify it. Build something better. If you do, send me the link — I want to see it.

## About

Built by [Chris Lema](https://chrislema.com). Find more at [chrislema.com](https://chrislema.com).
