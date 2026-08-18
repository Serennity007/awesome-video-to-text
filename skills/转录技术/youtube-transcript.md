# YouTube Video Transcript Extraction

Extract transcripts and subtitles directly from YouTube videos without downloading the video itself. Supports auto-generated and manual captions in multiple languages.

## When to Use

- Extracting transcripts from YouTube lectures or tutorials
- Building searchable databases of video content
- Research and analysis of YouTube video content
- Creating notes from educational YouTube channels
- Accessing auto-generated captions for correction
- Batch processing playlists for content analysis

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [youtube-transcript-api](https://github.com/jdepoix/youtube-transcript-api) | Latest | Direct transcript API access |
| [yt-dlp](https://github.com/yt-dlp/yt-dlp) | Latest | Subtitle download + fallback |

### Optional

| Tool | Purpose |
|------|---------|
| [pytube](https://github.com/pytube/pytube) | Alternative YouTube library |
| [scrapetube](https://github.com/dermasmid/scrapetube) | Playlist scraping |

### Installation

```bash
# Primary tools
pip install youtube-transcript-api yt-dlp

# Optional alternatives
pip install pytube scrapetube

# yt-dlp standalone (recommended, updates frequently)
# macOS
brew install yt-dlp

# Windows
winget install yt-dlp

# pip
pip install -U yt-dlp
```

## Step-by-Step Workflow

### Step 1: Get Video ID

```
URL: https://www.youtube.com/watch?v=dQw4w9WgXcQ
Video ID: dQw4w9WgXcQ

URL: https://youtu.be/dQw4w9WgXcQ
Video ID: dQw4w9WgXcQ
```

### Step 2: Check Available Transcripts

```python
from youtube_transcript_api import YouTubeTranscriptApi

ytt_api = YouTubeTranscriptApi()
transcript_list = ytt_api.list(video_id="dQw4w9WgXcQ")

for transcript in transcript_list:
    print(f"Language: {transcript.language} ({transcript.language_code})")
    print(f"  Auto-generated: {transcript.is_generated}")
```

### Step 3: Extract Transcript

```python
# Get English transcript
transcript = ytt_api.fetch(video_id="dQw4w9WgXcQ", languages=["en"])

for entry in transcript:
    print(f"[{entry.start:.1f}s] {entry.text}")
```

### Step 4: Export to File

```python
# Save as SRT, VTT, or TXT
```

## Code Templates

### Template 1: Basic Transcript Extraction

```python
#!/usr/bin/env python3
"""Extract YouTube video transcript with timestamps."""

from youtube_transcript_api import YouTubeTranscriptApi
from youtube_transcript_api.formatters import (
    SRTFormatter,
    TextFormatter,
    JSONFormatter
)
from pathlib import Path
import json
import re


def extract_video_id(url_or_id: str) -> str:
    """Extract video ID from URL or return if already an ID."""
    patterns = [
        r'(?:v=|/v/|youtu\.be/)([a-zA-Z0-9_-]{11})',
        r'^([a-zA-Z0-9_-]{11})$'
    ]
    for pattern in patterns:
        match = re.search(pattern, url_or_id)
        if match:
            return match.group(1)
    raise ValueError(f"Could not extract video ID from: {url_or_id}")


def get_transcript(
    video_url: str,
    languages: list = None,
    output_format: str = "txt",
    output_dir: str = "./transcripts"
):
    """Extract transcript from a YouTube video."""
    
    video_id = extract_video_id(video_url)
    
    ytt_api = YouTubeTranscriptApi()
    
    # List available transcripts
    transcript_list = ytt_api.list(video_id)
    print("Available transcripts:")
    for t in transcript_list:
        print(f"  - {t.language} ({t.language_code}) "
              f"{'[auto]' if t.is_generated else '[manual]'}")
    
    # Fetch transcript
    lang_list = languages or ["en", "zh", "ja"]
    transcript = ytt_api.fetch(video_id, languages=lang_list)
    
    # Format output
    Path(output_dir).mkdir(parents=True, exist_ok=True)
    output_file = Path(output_dir) / f"{video_id}.{output_format}"
    
    if output_format == "srt":
        formatter = SRTFormatter()
        formatted = formatter.format_transcript(transcript)
    elif output_format == "txt":
        formatter = TextFormatter()
        formatted = formatter.format_transcript(transcript)
    elif output_format == "json":
        formatter = JSONFormatter()
        formatted = formatter.format_transcript(transcript)
    elif output_format == "vtt":
        # Manual VTT formatting
        lines = ["WEBVTT\n"]
        for entry in transcript:
            start = _fmt_vtt(entry.start)
            end = _fmt_vtt(entry.start + entry.duration)
            lines.append(f"{start} --> {end}\n{entry.text}\n")
        formatted = "\n".join(lines)
    else:
        formatted = " ".join(entry.text for entry in transcript)
    
    with open(output_file, "w", encoding="utf-8") as f:
        f.write(formatted)
    
    print(f"\nSaved transcript: {output_file}")
    print(f"Entries: {len(transcript)}")
    
    return transcript


def _fmt_vtt(seconds: float) -> str:
    h = int(seconds // 3600)
    m = int((seconds % 3600) // 60)
    s = int(seconds % 60)
    ms = int((seconds % 1) * 1000)
    return f"{h:02d}:{m:02d}:{s:02d}.{ms:03d}"


if __name__ == "__main__":
    get_transcript(
        video_url="https://www.youtube.com/watch?v=example",
        languages=["en"],
        output_format="srt",
        output_dir="./transcripts"
    )
```

### Template 2: Batch Playlist Processing

```python
#!/usr/bin/env python3
"""Extract transcripts from all videos in a YouTube playlist."""

import yt_dlp
from youtube_transcript_api import YouTubeTranscriptApi
from pathlib import Path
import json
import time


def get_playlist_videos(playlist_url: str) -> list:
    """Get all video IDs from a playlist."""
    ydl_opts = {
        'quiet': True,
        'extract_flat': True,
        'force_generic_extractor': False,
    }
    
    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        result = ydl.extract_info(playlist_url, download=False)
        
    videos = []
    for entry in result.get('entries', []):
        videos.append({
            'id': entry.get('id'),
            'title': entry.get('title'),
            'url': entry.get('url')
        })
    
    return videos


def batch_extract_transcripts(
    playlist_url: str,
    languages: list = None,
    output_dir: str = "./playlist_transcripts"
):
    """Extract transcripts from all videos in a playlist."""
    
    videos = get_playlist_videos(playlist_url)
    print(f"Found {len(videos)} videos in playlist")
    
    output_path = Path(output_dir)
    output_path.mkdir(parents=True, exist_ok=True)
    
    ytt_api = YouTubeTranscriptApi()
    successful = 0
    failed = []
    
    for i, video in enumerate(videos, 1):
        video_id = video['id']
        title = video['title']
        print(f"\n[{i}/{len(videos)}] {title}")
        
        try:
            # Check if transcript exists
            transcript_list = ytt_api.list(video_id)
            
            # Get transcript
            transcript = ytt_api.fetch(
                video_id, 
                languages=languages or ["en"]
            )
            
            # Save as text
            text_file = output_path / f"{i:03d}_{video_id}.txt"
            with open(text_file, "w", encoding="utf-8") as f:
                f.write(f"Title: {title}\n")
                f.write(f"URL: https://www.youtube.com/watch?v={video_id}\n")
                f.write("-" * 60 + "\n\n")
                for entry in transcript:
                    f.write(f"[{entry.start:.1f}s] {entry.text}\n")
            
            print(f"  ✓ Saved ({len(transcript)} entries)")
            successful += 1
            
            # Rate limiting
            time.sleep(1)
            
        except Exception as e:
            print(f"  ✗ Failed: {e}")
            failed.append({'title': title, 'id': video_id, 'error': str(e)})
    
    # Save summary
    summary = {
        'total': len(videos),
        'successful': successful,
        'failed': len(failed),
        'failed_videos': failed
    }
    
    with open(output_path / "summary.json", "w") as f:
        json.dump(summary, f, indent=2)
    
    print(f"\n{'='*50}")
    print(f"Complete: {successful}/{len(videos)} successful")
    if failed:
        print(f"Failed: {len(failed)} videos")
    
    return summary


if __name__ == "__main__":
    batch_extract_transcripts(
        playlist_url="https://www.youtube.com/playlist?list=PLxxxxx",
        languages=["en", "zh"],
        output_dir="./playlist_transcripts"
    )
```

### Template 3: yt-dlp Subtitle Download

```python
#!/usr/bin/env python3
"""Download subtitles using yt-dlp with fallback options."""

import yt_dlp
from pathlib import Path


def download_subtitles(
    video_url: str,
    output_dir: str = "./subtitles",
    languages: list = None,
    include_auto: bool = True,
    output_format: str = "srt"
):
    """Download subtitles using yt-dlp."""
    
    Path(output_dir).mkdir(parents=True, exist_ok=True)
    
    lang_str = ",".join(languages) if languages else "en,zh,ja"
    
    ydl_opts = {
        'skip_download': True,  # Don't download video
        'writesubtitles': True,
        'writeautomaticsub': include_auto,
        'subtitleslangs': languages or ['en'],
        'subtitlesformat': output_format,
        'outtmpl': f'{output_dir}/%(title)s.%(ext)s',
        'quiet': False,
    }
    
    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        ydl.download([video_url])
    
    # List downloaded files
    subtitle_files = list(Path(output_dir).glob(f"*.{output_format}"))
    print(f"\nDownloaded {len(subtitle_files)} subtitle file(s):")
    for f in subtitle_files:
        print(f"  - {f.name}")


def download_with_fallback(
    video_url: str,
    output_dir: str = "./subtitles"
):
    """Try multiple methods to get subtitles."""
    
    # Method 1: yt-dlp (most reliable)
    try:
        download_subtitles(
            video_url,
            output_dir=output_dir,
            languages=['en', 'zh'],
            include_auto=True
        )
        return
    except Exception as e:
        print(f"yt-dlp failed: {e}")
    
    # Method 2: youtube-transcript-api
    try:
        import re
        from youtube_transcript_api import YouTubeTranscriptApi
        
        video_id = re.search(r'(?:v=|youtu\.be/)([a-zA-Z0-9_-]{11})', video_url)
        if video_id:
            ytt_api = YouTubeTranscriptApi()
            transcript = ytt_api.fetch(video_id.group(1))
            
            output_file = Path(output_dir) / f"{video_id.group(1)}.srt"
            Path(output_dir).mkdir(parents=True, exist_ok=True)
            
            with open(output_file, "w", encoding="utf-8") as f:
                for i, entry in enumerate(transcript, 1):
                    start = _fmt(entry.start)
                    end = _fmt(entry.start + entry.duration)
                    f.write(f"{i}\n{start} --> {end}\n{entry.text}\n\n")
            
            print(f"Fallback successful: {output_file}")
            return
    except Exception as e:
        print(f"Transcript API failed: {e}")
    
    print("All methods failed. Video may not have subtitles.")


def _fmt(seconds: float) -> str:
    h = int(seconds // 3600)
    m = int((seconds % 3600) // 60)
    s = int(seconds % 60)
    ms = int((seconds % 1) * 1000)
    return f"{h:02d}:{m:02d}:{s:02d},{ms:03d}"


if __name__ == "__main__":
    download_with_fallback("https://www.youtube.com/watch?v=example")
```

## Output Format Examples

### Plain Text with Timestamps
```
[0.0s] Welcome to this tutorial on Python web scraping.
[3.5s] Today we'll be covering the basics of BeautifulSoup
[7.2s] and how to extract data from HTML pages.
[11.8s] First, let me show you how to install the library.
```

### SRT Format
```
1
00:00:00,000 --> 00:00:03,500
Welcome to this tutorial on Python web scraping.

2
00:00:03,500 --> 00:00:07,200
Today we'll be covering the basics of BeautifulSoup

3
00:00:07,200 --> 00:00:11,800
and how to extract data from HTML pages.
```

## Common Pitfalls

1. **No Transcripts Available**
   - **Problem**: Some videos have no subtitles at all
   - **Solution**: Fall back to downloading the video and using Whisper

2. **Auto-generated Captions Quality**
   - **Problem**: Auto-generated captions have many errors
   - **Solution**: Use them as a starting point, then correct manually or with LLM

3. **Rate Limiting**
   - **Problem**: YouTube blocks requests after too many calls
   - **Solution**: Add delays between requests:
     ```python
     import time
     time.sleep(2)  # 2 second delay between videos
     ```

4. **Age-restricted Videos**
   - **Problem**: Cannot access transcripts of age-restricted content
   - **Solution**: Use yt-dlp with cookies: `--cookies-from-browser chrome`

5. **Members-only Content**
   - **Problem**: Premium content requires authentication
   - **Solution**: Provide cookies or use yt-dlp's authentication options

## Pro Tips

- **Always try manual captions first**: They're more accurate than auto-generated ones
- **Use `youtube-transcript-api`** for simple extraction - it's fastest and has no video download
- **Use `yt-dlp`** when you need subtitle files in specific formats
- **Combine both tools**: Try API first, fall back to yt-dlp
- **Cache transcripts**: Save transcripts locally to avoid repeated API calls
- **Multi-language**: Try fetching in multiple languages to compare quality
- **Watch for updates**: YouTube changes their API; keep tools updated

---

## 中文版本

### 适用场景

- 从 YouTube 讲座或教程中提取文字记录
- 构建视频内容的可搜索数据库
- 研究和分析 YouTube 视频内容
- 从教育 YouTube 频道创建笔记
- 访问自动生成的字幕进行校正
- 批量处理播放列表用于内容分析

### 快速开始

```bash
# 安装工具
pip install youtube-transcript-api yt-dlp

# Python 快速提取
python3 -c "
from youtube_transcript_api import YouTubeTranscriptApi
ytt_api = YouTubeTranscriptApi()
transcript = ytt_api.fetch('VIDEO_ID', languages=['zh', 'en'])
for entry in transcript:
    print(f'[{entry.start:.1f}s] {entry.text}')
"
```

### 中文视频处理

```python
# 优先获取中文字幕
from youtube_transcript_api import YouTubeTranscriptApi
ytt_api = YouTubeTranscriptApi()
transcript = ytt_api.fetch('视频ID', languages=['zh-Hans', 'zh-Hant', 'zh', 'en'])
```

### 注意事项

1. 自动字幕质量参差不齐，建议人工校对
2. 注意请求频率限制，建议添加延迟
3. 部分视频可能没有字幕可用
4. 使用最新版工具以应对 YouTube API 变化
