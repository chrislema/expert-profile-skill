# Apify Actor Reference

Input/output schemas for the actors used by Expert Profiler.

---

## LinkedIn Posts

**Actor:** `apimaestro/linkedin-profile-posts`  
**Cost:** ~$5 per 1000 posts  
**No cookies required**

### Input Schema

```json
{
  "username": "string (LinkedIn username from URL)",
  "total_posts": "integer (number of posts to fetch, default 10)"
}
```

### Example Input

```json
{
  "username": "christeitzel",
  "total_posts": 30
}
```

### Output Schema

```json
{
  "urn": {
    "activity_urn": "string",
    "share_urn": "string"
  },
  "posted_at": {
    "date": "YYYY-MM-DD HH:MM:SS",
    "relative": "string (e.g., '1 day ago')",
    "timestamp": "integer (unix ms)"
  },
  "text": "string (full post content)",
  "url": "string (post URL)",
  "post_type": "string (regular, article, etc.)",
  "author": {
    "first_name": "string",
    "last_name": "string",
    "headline": "string",
    "username": "string",
    "profile_url": "string",
    "profile_picture": "string (URL)"
  },
  "stats": {
    "total_reactions": "integer",
    "like": "integer",
    "support": "integer",
    "love": "integer",
    "insight": "integer",
    "celebrate": "integer",
    "funny": "integer",
    "comments": "integer",
    "reposts": "integer"
  },
  "media": {
    "type": "string (image, images, video, etc.)",
    "url": "string",
    "images": "array of image objects"
  }
}
```

### Key Fields for Profiling

- `text` - The actual post content (primary analysis source)
- `posted_at.date` - For recency analysis
- `stats.total_reactions` - For engagement analysis
- `author.headline` - Their self-description

---

## Twitter/X Posts

**Actor:** `apidojo/tweet-scraper`  
**Cost:** Platform usage based

### Input Schema

```json
{
  "twitterHandles": ["array of handles without @"],
  "maxItems": "integer (max tweets to fetch)",
  "sort": "string ('Top' or 'Latest')"
}
```

### Example Input

```json
{
  "twitterHandles": ["technerdteitzel"],
  "maxItems": 30
}
```

### Output Schema

```json
{
  "url": "string (tweet URL)",
  "twitterUrl": "string (alternate URL format)",
  "id": "string (tweet ID)",
  "text": "string (full tweet content)",
  "retweetCount": "integer",
  "replyCount": "integer",
  "likeCount": "integer",
  "quoteCount": "integer",
  "createdAt": "string (e.g., 'Wed Aug 27 21:57:33 +0000 2025')",
  "bookmarkCount": "integer",
  "isRetweet": "boolean",
  "isQuote": "boolean"
}
```

### Key Fields for Profiling

- `text` - The actual tweet content
- `createdAt` - For timeline analysis
- `likeCount` - For engagement analysis
- `isRetweet` / `isQuote` - Filter for original content vs. shares

### Filtering Tips

- Filter out `isRetweet: true` for original thoughts only
- Quote tweets (`isQuote: true`) often show reactions/opinions
- High `likeCount` indicates resonant content

---

## YouTube Transcripts

**Actor:** `starvibe/youtube-video-transcript`  
**Cost:** ~$5 per 1000 results  
**Success rate:** 99.9%

### Input Schema

```json
{
  "youtube_url": "string (full YouTube video URL)",
  "language": "string (ISO 639-1, e.g., 'en')",
  "include_transcript_text": "boolean (true for plain text)"
}
```

### Example Input

```json
{
  "youtube_url": "https://www.youtube.com/watch?v=fvXRqZOkNrU",
  "language": "en",
  "include_transcript_text": true
}
```

### Output Schema

```json
{
  "title": "string (video title)",
  "channel_name": "string",
  "channel_id": "string",
  "description": "string (video description)",
  "duration_seconds": "integer",
  "view_count": "integer",
  "like_count": "integer",
  "comment_count": "integer",
  "published_at": "string (ISO datetime)",
  "thumbnail": "string (URL)",
  "available_languages": ["array of language codes"],
  "selected_language": "string",
  "is_auto_generated": "boolean",
  "transcript": [
    {
      "text": "string (segment text)",
      "start": "float (seconds)",
      "end": "float (seconds)"
    }
  ],
  "transcript_text": "string (full transcript as single text block)"
}
```

### Key Fields for Profiling

- `transcript_text` - Full spoken content (primary analysis source)
- `title` - Context for the conversation
- `channel_name` - Who interviewed them
- `duration_seconds` - Length indicates depth
- `view_count` - Reach/influence indicator

### Finding Interview Videos

**Search patterns that work:**

```
"{name}" interview
"{name}" podcast
"{name}" "{company}" keynote
"{name}" "{company}" talk
"{name}" fireside chat
```

**Prioritize:**
1. Podcast appearances (deep conversation)
2. Conference talks (their prepared message)
3. Interview shows (Q&A reveals thinking)
4. Panel discussions (shows interaction style)

---

## Alternative Actors

If primary actors fail, these alternatives exist:

### LinkedIn Alternatives
- `curious_coder/linkedin-post-search-scraper` - Search-based
- `bebity/linkedin-profile-scraper` - Full profile data

### Twitter Alternatives
- `memo23/apify-twitter-profile-scraper` - Profile + tweets
- `quacker/twitter-scraper` - Search-based

### YouTube Alternatives
- `karamelo/youtube-transcripts` - Batch transcripts
- `pintostudio/youtube-transcript-scraper` - With timestamps

---

## Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| "Actor not found" | Wrong actor name | Check spelling, use exact slug |
| "Trial expired" | Paid actor, trial used | Switch to alternative or purchase |
| "No results" | Profile doesn't exist or is private | Verify handle, try alternatives |
| "Rate limited" | Too many requests | Wait and retry, reduce batch size |
| "Timeout" | Large request | Reduce `total_posts` or `maxItems` |

---

## Cost Optimization

- LinkedIn: ~$0.005 per post
- Twitter: Platform usage, typically minimal
- YouTube: ~$0.005 per transcript

**For a typical profile (30 LI + 30 TW + 3 YT):**
- LinkedIn: ~$0.15
- Twitter: ~$0.05
- YouTube: ~$0.015
- **Total: ~$0.22 per profile**
