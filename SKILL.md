# Skill: x-search-highlights

Search and extract high-value posts from X (Twitter) with engagement-based ranking.

## Description

Extract valuable posts from X search results based on engagement metrics (likes, retweets, replies, views). Ideal for discovering trending discussions, finding expert insights, or curating content for research.

## When to Use

Activate when user asks to:
- Search X/Twitter for specific topics
- Find "best" or "high-value" posts on a subject
- Extract posts with engagement data
- Curate content from X discussions
- Discover trending discussions

Trigger phrases:
- "Search X for [topic]"
- "Find best posts about [topic] on Twitter"
- "Get highlights from X search"
- "Extract valuable tweets"

## Input Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `topic` | string | required | Search query (e.g., "Claude Code", "AI Agent") |
| `maxResults` | number | 5 | Maximum number of posts to return |
| `minLikes` | number | 0 | Minimum likes threshold (filter low-engagement) |
| `scrollTimes` | number | 3 | Number of scroll iterations (more = more candidates) |
| `sortBy` | string | "engagement" | Sort method: engagement, likes, views, recent |
| `outputFormat` | string | "markdown" | Output format: markdown, json, summary |

## Output Format

### Markdown (default)

```markdown
## 1. [Post Title/Summary]

- **标题**：[Content summary]
- **日期**：YYYY-MM-DD
- **标签**：#tag1 #tag2
- **亮点**：🎯 [Key insight] 💡 [Unique perspective]
- **互动**：X 回复 · X 转发 · X 点赞 · X 浏览
- **链接**：[点击阅读原文](URL)
```

### JSON

```json
{
  "topic": "Claude Code",
  "totalFound": 20,
  "returned": 5,
  "posts": [
    {
      "text": "...",
      "author": "@handle",
      "date": "2026-04-01",
      "likes": 1669,
      "retweets": 408,
      "views": 240000,
      "url": "https://x.com/..."
    }
  ]
}
```

## Prerequisites

- **bb-browser** installed and running (`npm install -g bb-browser@latest`)
- X.com login state established in bb-browser profile
- Chrome/Chromium browser available

## Workflow

1. **Navigate to Search**: Open X search page with topic
2. **Load Content**: Scroll multiple times to load candidate pool
3. **Extract Data**: Parse posts using JavaScript (article elements)
4. **Calculate Scores**: Weight engagement metrics (bookmarks highest, likes medium, views lowest)
5. **Sort & Filter**: Remove low-engagement, sort by score
6. **Format Output**: Return formatted results per outputFormat parameter

## Engagement Scoring Formula

```
Score = (bookmarks × 10) + (likes × 2) + (retweets × 5) + (views × 0.01)
```

**Why this weights:**
- Bookmarks = strongest signal of value (user saves for later)
- Retweets = reach indicator (user shares publicly)
- Likes = approval signal (easy to give)
- Views = reach but easily inflated

## Technical Implementation

### Core Extraction Script

```javascript
(function() {
  const posts = document.querySelectorAll('article');
  const results = [];
  
  const getNum = (el) => {
    if (!el) return 0;
    const aria = el.getAttribute('aria-label') || '';
    const m = aria.match(/(\d+\.?\d*[KM]?)/i);
    if (!m) return 0;
    let n = m[1].toUpperCase();
    if (n.includes('K')) return parseFloat(n) * 1000;
    if (n.includes('M')) return parseFloat(n) * 1000000;
    return parseInt(n);
  };
  
  posts.forEach(p => {
    const txt = p.querySelector('[data-testid="tweetText"]');
    const tm = p.querySelector('time');
    const au = p.querySelector('[data-testid="User-Name"]');
    
    if (!txt) return;
    
    results.push({
      text: txt.innerText.trim(),
      author: au ? au.innerText.split('\n')[0] : '',
      handle: au ? (au.innerText.match(/@[\w]+/)?.[0] || '') : '',
      date: tm ? tm.getAttribute('datetime') : '',
      url: tm ? tm.closest('a')?.href : '',
      replies: getNum(p.querySelector('button[aria-label*="Repl"]')),
      retweets: getNum(p.querySelector('button[aria-label*=" repost"]')),
      likes: getNum(p.querySelector('button[aria-label*="Like"]')),
      views: getNum(p.querySelector('a[href*="/analytics"]'))
    });
  });
  
  // Sort by engagement score
  results.sort((a,b) => {
    const sA = (a.likes||0)*2 + (a.retweets||0)*5 + (a.views||0)*0.01;
    const sB = (b.likes||0)*2 + (b.retweets||0)*5 + (b.views||0)*0.01;
    return sB - sA;
  });
  
  return JSON.stringify(results.slice(0, maxResults));
})();
```

### Execution Commands

```bash
# 1. Open search page
bb-browser open "https://x.com/search?q=${TOPIC}&f=top"

# 2. Scroll to load more
for i in $(seq 1 $SCROLL_TIMES); do
  bb-browser eval 'window.scrollTo(0, document.body.scrollHeight);'
  sleep 2
done

# 3. Extract and parse
bb-browser eval "$EXTRACT_SCRIPT" --json
```

## Safety Guidelines

- **Rate Limit**: Avoid excessive scrolling (max 10 iterations)
- **Delay**: Add 2-3 second delays between operations
- **Session State**: Requires existing X login (don't re-login each time)
- **Profile**: Uses bb-browser profile (~/.bb-browser/browser/user-data)

## Limitations

- X lazy loading limits initial results (3-10 posts visible)
- Engagement data may not include bookmarks (X API limitation)
- Some posts may be rate-limited or shadow-banned (won't appear)
- Search quality depends on X algorithm (may miss older posts)

## Example Usage

### Basic Search

```
User: "Search X for Claude Code best posts"

Assistant activates x-search-highlights:
- topic: "Claude Code"
- maxResults: 5
- sortBy: "engagement"

Output: 5 formatted posts with engagement data
```

### Filtered Search

```
User: "Find AI Agent posts with at least 1000 likes"

Parameters:
- topic: "AI Agent"
- minLikes: 1000
- maxResults: 10
```

### JSON Output

```
User: "Get JSON data for RAG posts"

Parameters:
- topic: "RAG"
- outputFormat: "json"
- maxResults: 20
```

## Error Handling

| Error | Cause | Solution |
|-------|-------|----------|
| "Daemon did not start" | bb-browser not running | Run `bb-browser open` first |
| "No articles found" | Page not loaded or no results | Check topic spelling, scroll more |
| "Login required" | No X login state | User must login first via bb-browser |
| "Rate limited" | Too many requests | Add delays, reduce scrollTimes |

## Dependencies

- `bb-browser` ≥ 0.11.2
- Chrome/Chromium browser
- X.com account (logged in)

## Version

- Created: 2026-04-05
- Version: 1.0.0
- Author: Platform Admin (based on actual task execution)

## Related Skills

- `browser` - OpenClaw native browser control
- `web-search` - DuckDuckGo search (no login needed)
- `web-fetch` - Extract content from URLs