---
name: expert-profiler
description: Build comprehensive profiles on experts, executives, and thought leaders for sales calls, podcast prep, pitches, and content creation. Use when the user wants to research a person, prepare for a meeting, find someone's digital footprint, or create a profile. Triggered by requests like "research [name]", "profile [person]", "prepare for a call with [name]", "find out about [executive]", or "who is [person]". Collects LinkedIn posts, tweets, YouTube interview transcripts, blog posts, and synthesizes into actionable intelligence.
---

# Expert Profiler

Build comprehensive profiles on people by collecting their digital footprint and synthesizing it into actionable intelligence.

## Prerequisites

**Required:** Apify MCP connection with API key configured.

The skill uses these Apify actors:
- `apimaestro/linkedin-profile-posts` - LinkedIn posts (no cookies required)
- `apidojo/tweet-scraper` - Twitter/X timeline
- `starvibe/youtube-video-transcript` - YouTube transcripts
- `apify/rag-web-browser` - Blog posts and web content

## Workflow

### Step 1: Identity Discovery

When given a name (and optional company/role/email), search the web to find their profiles.

**Search queries to run:**
```
"{name}" "{company}" linkedin
"{name}" "{company}" twitter
"{name}" site:youtube.com interview
"{name}" "{company}" podcast
"{name}" blog OR "{name}" writes OR "{name}" author
```

**Present to user for confirmation:**
- LinkedIn profile URL
- Twitter/X handle
- YouTube videos featuring them (top 3)
- Personal blog or publication URLs
- Any other relevant profiles (Drupal.org, GitHub, Medium, Substack)

**Wait for user confirmation before proceeding.** Ask: "Is this the right person? Confirm and I'll collect their content."

### Step 2: Content Collection

After confirmation, collect content using Apify actors:

**LinkedIn Posts (30):**
```
Actor: apimaestro/linkedin-profile-posts
Input: {
  "username": "<linkedin-username>",
  "total_posts": 30
}
```

**Twitter/X Posts (30):**
```
Actor: apidojo/tweet-scraper
Input: {
  "twitterHandles": ["<handle>"],
  "maxItems": 30
}
```

**YouTube Transcripts (top 3 interviews):**
```
Actor: starvibe/youtube-video-transcript
Input: {
  "youtube_url": "<video-url>",
  "include_transcript_text": true
}
```
Run for each of the top 3 videos.

**Blog Posts (last 10):**

For each blog/publication URL identified:

1. First, fetch the blog index/archive page to identify the last 10 post URLs:
```
Actor: apify/rag-web-browser
Input: {
  "query": "<blog-url>",
  "maxResults": 1,
  "outputFormats": ["markdown"]
}
```

2. Extract individual post URLs from the index page

3. Fetch each of the last 10 blog posts:
```
Actor: apify/rag-web-browser
Input: {
  "query": "<individual-post-url>",
  "maxResults": 1,
  "outputFormats": ["markdown"]
}
```

**Blog Collection Strategy:**
- If blog has an archive page, use it to find recent posts
- If blog has RSS, look for /feed or /rss URL patterns
- For Medium: use `medium.com/@{username}` 
- For Substack: use `{publication}.substack.com/archive`
- Collect full text of each post for synthesis

### Step 3: Profile Synthesis

Analyze all collected content and create a comprehensive profile using the template in `references/profile-template.md`.

The profile should include:
1. **Identity Summary** - Who they are, roles, companies
2. **Public Positions** - What they believe and advocate (with sources)
3. **Blog Positions** - Deep analysis of their written thought leadership
4. **Communication Style** - How they talk, vocabulary, patterns
5. **Key Stories** - Signature anecdotes they repeat
6. **Current Focus** - What they're working on NOW
7. **Potential Angles** - How to connect based on their interests

### Step 4: Deliver Profile

Create a markdown file with the full profile and present it to the user.

**For different use cases, emphasize:**

| Use Case | Emphasize |
|----------|-----------|
| Sales call | Pain points, current initiatives, decision-making style |
| Podcast prep | Stories they tell, contrarian positions, unexplored areas |
| Pitch/proposal | Their vocabulary, problems they care about, values |
| Blog/content | Quotable positions, frameworks they use, their audience |

## Output Location

Save the profile to `/mnt/user-data/outputs/{name}-profile.md`

## Error Handling

- If LinkedIn scrape fails: Note in profile, continue with other sources
- If Twitter handle not found: Search for alternative handles, ask user
- If no YouTube interviews: Search for podcast appearances instead
- If blog not found: Note in profile, continue with other sources
- If blog posts fail to load: Try alternative URLs, note partial collection
- If Apify actor fails: Check API key, report specific error to user

## Example Usage

**User:** "Research Chris Teitzel from Cellar Door for a podcast"

**Claude:**
1. Searches web for Chris Teitzel Cellar Door
2. Finds LinkedIn (christeitzel), Twitter (@technerdteitzel), YouTube videos, blog at cellardoor.com/blog
3. Presents links: "I found these profiles. Is this the right Chris Teitzel?"
4. User confirms
5. Collects 30 LinkedIn posts, 30 tweets, 3 video transcripts, 10 blog posts
6. Synthesizes into comprehensive profile with deep blog analysis
7. Delivers profile with podcast-specific angles

## Reference Files

- `references/profile-template.md` - Full profile structure template
- `references/apify-actors.md` - Actor input/output schemas
