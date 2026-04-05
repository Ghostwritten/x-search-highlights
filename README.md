# X Search Highlights

🔍 Search and extract high-value posts from X (Twitter) with engagement-based ranking.

![Version](https://img.shields.io/badge/version-1.0.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![ClawHub](https://img.shields.io/badge/ClawHub-x--search--highlights-purple)

## ✨ Features

- 🎯 **Engagement-based ranking**: Sorts posts by weighted score (likes, retweets, views)
- 📊 **Flexible filtering**: Configure minimum likes, max results, scroll depth
- 📱 **Multiple outputs**: Markdown, JSON, or summary formats
- 🚀 **Easy to use**: Simple CLI interface with sensible defaults
- 🔒 **Persistent login**: Uses bb-browser profile for authenticated sessions

## 📦 Installation

### Via ClawHub (Recommended)

```bash
clawhub install x-search-highlights
```

### From GitHub

```bash
# Clone to your skills directory
git clone https://github.com/Ghostwritten/x-search-highlights.git ~/.openclaw/skills/x-search-highlights

# Or install manually
mkdir -p ~/.openclaw/skills
cd ~/.openclaw/skills
git clone https://github.com/Ghostwritten/x-search-highlights.git
```

### Dependencies

- [bb-browser](https://github.com/OpenClaw/bb-browser) ≥ 0.11.2
- Chrome/Chromium browser
- X.com login state (in bb-browser profile)

## 🚀 Usage

### Basic Search

```bash
~/.openclaw/skills/x-search-highlights/scripts/x-search.sh "Claude Code"
```

### With Filters

```bash
# Get 10 posts with minimum 1000 likes, scroll 5 times
~/.openclaw/skills/x-search-highlights/scripts/x-search.sh \
  "AI Agent" \
  10 \        # maxResults
  5 \         # scrollTimes
  1000 \      # minLikes
  markdown     # outputFormat
```

### JSON Output

```bash
~/.openclaw/skills/x-search-highlights/scripts/x-search.sh \
  "RAG" \
  20 \
  3 \
  0 \
  json
```

## 📋 Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `topic` | string | required | Search query (e.g., "Claude Code", "AI Agent") |
| `maxResults` | number | 5 | Maximum number of posts to return |
| `scrollTimes` | number | 3 | Number of scroll iterations (more = more candidates) |
| `minLikes` | number | 0 | Minimum likes threshold (filter low-engagement) |
| `outputFormat` | string | markdown | Output format: `markdown`, `json`, `summary` |

## 🎯 Ranking Algorithm

Posts are ranked by engagement score:

```
Score = (likes × 2) + (retweets × 5) + (views × 0.01)
```

**Weight rationale:**
- **Retweets (×5)**: Strongest signal - indicates public sharing
- **Likes (×2)**: Approval signal - low barrier to interaction
- **Views (×0.01)**: Reach indicator - easily inflated, lowest weight

## 📊 Output Examples

### Markdown Format

```markdown
## 1. Google Gemma 4 发布

- **标题**：Gemma 4 — Google 最智能的开源模型
- **日期**：2026-04-02
- **标签**：#Google #Gemma4 #OpenModel #AI
- **亮点**：
  🎯 技术突破：与 Gemini 3 相同研究构建
  💡 核心特性：可在自有硬件上运行
- **互动**：632 回复 · 4.1K 转发 · 20K 点赞 · 7.1M 浏览
- **链接**：[点击阅读原文](https://x.com/Google/status/...)
```

### JSON Format

```json
{
  "total": 5,
  "posts": [
    {
      "text": "...",
      "author": "Google",
      "handle": "@Google",
      "date": "2026-04-02T16:06:26.000Z",
      "url": "https://x.com/Google/status/...",
      "replies": 632,
      "retweets": 4092,
      "likes": 20041,
      "views": 7064771
    }
  ]
}
```

## 🔧 Troubleshooting

### No posts found

**Cause:** bb-browser not running or not logged in

**Solution:**
```bash
# Check bb-browser status
bb-browser status

# Open bb-browser
bb-browser open

# Navigate to X.com and log in
bb-browser open "https://x.com"
```

### JSON parsing errors

**Cause:** bb-browser version too old

**Solution:**
```bash
# Check version
bb-browser --version

# Update bb-browser
pnpm install -g @openclaw/bb-browser
```

### Rate limits

**Cause:** Too many scroll operations

**Solution:**
- Reduce `scrollTimes` parameter
- Add delays between operations
- Respect X's rate limits

## 📝 Use Cases

- **Content curation**: Find best posts for newsletters or roundups
- **Market research**: Discover trending discussions on specific topics
- **Competitive analysis**: Monitor competitor announcements and engagement
- **Expert discovery**: Identify influential voices in a niche
- **Research aggregation**: Collect high-value insights for reports

## 🤝 Contributing

Contributions welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 🔗 Links

- [ClawHub](https://clawhub.ai/skills/x-search-highlights)
- [bb-browser](https://github.com/OpenClaw/bb-browser)
- [OpenClaw](https://github.com/openclaw/openclaw)

## 🙏 Acknowledgments

- Built with [bb-browser](https://github.com/OpenClaw/bb-browser) for browser automation
- Inspired by engagement metrics research from social media analytics

## 📧 Contact

- **Author**: Ghostwritten
- **GitHub**: [@Ghostwritten](https://github.com/Ghostwritten)
- **Twitter/X**: [@ghostwritten01](https://x.com/ghostwritten01)
