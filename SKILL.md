---
name: youtube-apify-transcript
version: 1.3.1
description: Fetch YouTube transcripts via APIFY API. Works from cloud IPs (Hetzner, AWS, etc.) by bypassing YouTube's bot detection. Features local caching (FREE repeat requests!), batch mode, and optional AI summarization via claude CLI. Free tier includes $5/month credits (~714 videos). No credit card required.
tags: [youtube, transcript, apify, video, subtitles, captions, cloud-ip, free-tier, web-scraping, caching, batch]
metadata: {"openclaw":{"requires":{"bins":["python3","claude"],"env":{"APIFY_API_TOKEN":"required","YT_TRANSCRIPT_CACHE_DIR":"optional - defaults to .cache/ in skill dir"}}}}
---

# youtube-apify-transcript

Fetch YouTube transcripts via APIFY API (works from cloud IPs, bypasses YouTube bot detection).

## Why APIFY?

YouTube blocks transcript requests from cloud IPs (AWS, GCP, etc.). APIFY runs the request through residential proxies, bypassing bot detection reliably.

## Free Tier

- **$5/month free credits** (~714 videos)
- No credit card required
- Perfect for personal use

## Cost

- **$0.007 per video** (less than 1 cent!)
- Track usage at: https://console.apify.com/billing

## Links

- 🔗 [APIFY Pricing](https://apify.com/pricing)
- 🔑 [Get API Key](https://console.apify.com/account/integrations)
- 🎬 [YouTube Transcripts Actor](https://apify.com/topaz_sharingan/Youtube-Transcript-Scraper-1)

## Setup

1. Create free APIFY account: https://apify.com/
2. Get your API token: https://console.apify.com/account/integrations
3. Set environment variable:

```bash
# Add to ~/.bashrc or ~/.zshrc
export APIFY_API_TOKEN="apify_api_YOUR_TOKEN_HERE"

# Or use .env file (never commit this!)
echo 'APIFY_API_TOKEN=apify_api_YOUR_TOKEN_HERE' >> .env
```

## Usage

### Basic Usage

```bash
# Get transcript as text (uses cache by default)
python3 scripts/fetch_transcript.py "https://www.youtube.com/watch?v=VIDEO_ID"

# Short URL also works
python3 scripts/fetch_transcript.py "https://youtu.be/VIDEO_ID"
```

### Options

```bash
# Output to file
python3 scripts/fetch_transcript.py "URL" --output transcript.txt

# JSON format (includes timestamps)
python3 scripts/fetch_transcript.py "URL" --json

# Both: JSON to file
python3 scripts/fetch_transcript.py "URL" --json --output transcript.json

# Specify language preference
python3 scripts/fetch_transcript.py "URL" --lang de

# Summarize instead of printing full transcript
python3 scripts/fetch_transcript.py "URL" --summarize

# Detailed German summary using Sonnet
python3 scripts/fetch_transcript.py "URL" --summarize --summary-style detailed --summary-lang de --summary-model sonnet

# Include summary in JSON output
python3 scripts/fetch_transcript.py "URL" --json --summarize
```

### Summaries

Summaries are generated via the `claude` CLI and cached inside the same per-video `.json` cache file under `summaries`.

```bash
# Default summary: brief English summary (3-5 sentences)
python3 scripts/fetch_transcript.py "URL" --summarize

# Bullet summary
python3 scripts/fetch_transcript.py "URL" -s --summary-style bullets

# TL;DR in German
python3 scripts/fetch_transcript.py "URL" -s --summary-style tldr --summary-lang de

# Use a different model alias or full Claude model ID
python3 scripts/fetch_transcript.py "URL" -s --summary-model sonnet
python3 scripts/fetch_transcript.py "URL" -s --summary-model anthropic/claude-sonnet-4-6

# JSON output includes a top-level "summary" field
python3 scripts/fetch_transcript.py "URL" --json --summarize
```

Supported summary styles:
- `brief` — 3-5 sentence summary
- `detailed` — fuller breakdown of topics and key points
- `bullets` — up to 10 key bullet points
- `tldr` — one-sentence summary

### Caching (saves money!)

Transcripts are cached locally by default. Repeat requests for the same video cost $0.

```bash
# First request: fetches from APIFY ($0.007)
python3 scripts/fetch_transcript.py "URL"

# Second request: uses cache (FREE!)
python3 scripts/fetch_transcript.py "URL"
# Output: [cached] Transcript for: VIDEO_ID

# Bypass cache (force fresh fetch)
python3 scripts/fetch_transcript.py "URL" --no-cache

# View cache stats
python3 scripts/fetch_transcript.py --cache-stats

# Clear all cached transcripts
python3 scripts/fetch_transcript.py --clear-cache
```

Cache location: `.cache/` in skill directory (override with `YT_TRANSCRIPT_CACHE_DIR` env var)

### Batch Mode

Process multiple videos at once:

```bash
# Create a file with URLs (one per line)
cat > urls.txt << EOF
https://youtube.com/watch?v=VIDEO1
https://youtu.be/VIDEO2
https://youtube.com/watch?v=VIDEO3
EOF

# Process all URLs
python3 scripts/fetch_transcript.py --batch urls.txt

# Output: 
# [1/3] Fetching VIDEO1...
# [2/3] [cached] VIDEO2
# [3/3] Fetching VIDEO3...
# Batch complete: 2 fetched, 1 cached, 0 failed
# [Cost: ~$0.014 for 2 API call(s)]

# Batch with JSON output to file
python3 scripts/fetch_transcript.py --batch urls.txt --json --output all_transcripts.json
```

### Output Formats

**Text (default):**
```
Hello and welcome to this video.
Today we're going to talk about...
```

**JSON (--json):**
```json
{
  "video_id": "dQw4w9WgXcQ",
  "title": "Video Title",
  "transcript": [
    {"start": 0.0, "duration": 2.5, "text": "Hello and welcome"},
    {"start": 2.5, "duration": 3.0, "text": "to this video"}
  ],
  "full_text": "Hello and welcome to this video..."
}
```

## Error Handling

The script handles common errors:
- Invalid YouTube URL
- Video has no transcript
- API quota exceeded
- Network errors

## Metadata

```yaml
metadata:
  clawdbot:
    emoji: "📹"
    requires:
      env: ["APIFY_API_TOKEN"]
      bins: ["python3"]
```
