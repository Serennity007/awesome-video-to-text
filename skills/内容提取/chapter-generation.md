# Video Chapter and Timestamp Generation

Create navigable chapters and timestamps for long videos. Automatically detect topic changes, generate meaningful chapter titles, and produce platform-compatible chapter formats.

## When to Use

- Adding chapters to YouTube video descriptions
- Creating table of contents for long lectures
- Making podcast episodes navigable
- Organizing meeting recordings by agenda items
- Building searchable video indexes
- Improving video accessibility and SEO

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [OpenAI Whisper](https://github.com/openai/whisper) | Latest | Transcription with timestamps |
| [OpenAI API](https://platform.openai.com/) | GPT-4o | Chapter title generation |

### Optional

| Tool | Purpose |
|------|---------|
| [pyannote-audio](https://github.com/pyannote/pyannote-audio) | Speaker change detection |
| [scikit-learn](https://scikit-learn.org/) | Topic segmentation |
| [BERTopic](https://github.com/MaartenGr/BERTopic) | Topic modeling |

### Installation

```bash
# Core
pip install openai-whisper openai

# For advanced topic segmentation
pip install scikit-learn sentence-transformers
pip install bertopic

# For speaker-based chapter detection
pip install pyannote.audio
```

## Step-by-Step Workflow

### Step 1: Transcribe with Segments

```python
import whisper
model = whisper.load_model("medium")
result = model.transcribe("video.mp4", verbose=False)
segments = result["segments"]
```

### Step 2: Detect Topic Boundaries

```python
# Identify where topics change using:
# - Silence gaps
# - Semantic similarity
# - Speaker changes
# - Keyword shifts
```

### Step 3: Generate Chapter Titles

```python
# Use LLM to create descriptive titles
```

### Step 4: Format Output

```python
# Generate YouTube, podcast, or custom formats
```

## Code Templates

### Template 1: Automatic Chapter Generator

```python
#!/usr/bin/env python3
"""Generate video chapters automatically from transcript."""

import whisper
import numpy as np
from pathlib import Path
from openai import OpenAI
from sentence_transformers import SentenceTransformer
from sklearn.metrics.pairwise import cosine_similarity
from datetime import timedelta
import json


class ChapterGenerator:
    """Generate navigable chapters for videos."""
    
    def __init__(
        self,
        whisper_model: str = "medium",
        llm_model: str = "gpt-4o",
        embedding_model: str = "all-MiniLM-L6-v2"
    ):
        self.whisper = whisper.load_model(whisper_model)
        self.llm = OpenAI()
        self.llm_model = llm_model
        self.embedder = SentenceTransformer(embedding_model)
    
    def generate(
        self,
        video_path: str,
        language: str = None,
        target_chapters: int = None,
        min_chapter_duration: float = 60,
        silence_threshold: float = 3.0
    ) -> list:
        """Generate chapters for a video."""
        
        # Step 1: Transcribe
        print("Transcribing...")
        result = self.whisper.transcribe(video_path, language=language)
        segments = result["segments"]
        
        # Step 2: Detect boundaries
        print("Detecting topic boundaries...")
        boundaries = self._detect_boundaries(
            segments,
            silence_threshold=silence_threshold
        )
        
        # Step 3: Semantic boundary detection
        semantic_boundaries = self._detect_semantic_boundaries(segments)
        boundaries = list(set(boundaries + semantic_boundaries))
        boundaries.sort()
        
        # Step 4: Merge and filter boundaries
        chapters = self._create_chapters(
            segments,
            boundaries,
            min_duration=min_chapter_duration,
            target_count=target_chapters
        )
        
        # Step 5: Generate titles
        print("Generating chapter titles...")
        chapters = self._generate_titles(chapters)
        
        return chapters
    
    def _detect_boundaries(
        self,
        segments: list,
        silence_threshold: float = 3.0
    ) -> list:
        """Detect boundaries from silence gaps."""
        
        boundaries = []
        
        for i in range(1, len(segments)):
            gap = segments[i]["start"] - segments[i-1]["end"]
            
            if gap >= silence_threshold:
                boundaries.append({
                    "time": segments[i]["start"],
                    "type": "silence",
                    "confidence": min(gap / 10.0, 1.0)
                })
        
        return boundaries
    
    def _detect_semantic_boundaries(
        self,
        segments: list,
        window_size: int = 5,
        threshold: float = 0.3
    ) -> list:
        """Detect topic changes using semantic similarity."""
        
        boundaries = []
        
        # Create embeddings for each segment
        texts = [seg["text"] for seg in segments]
        embeddings = self.embedder.encode(texts)
        
        # Calculate similarity between windows
        for i in range(window_size, len(segments) - window_size):
            before = embeddings[i-window_size:i].mean(axis=0)
            after = embeddings[i:i+window_size].mean(axis=0)
            
            similarity = cosine_similarity(
                before.reshape(1, -1),
                after.reshape(1, -1)
            )[0][0]
            
            if similarity < (1 - threshold):
                boundaries.append({
                    "time": segments[i]["start"],
                    "type": "semantic",
                    "confidence": 1 - similarity
                })
        
        return boundaries
    
    def _create_chapters(
        self,
        segments: list,
        boundaries: list,
        min_duration: float = 60,
        target_count: int = None
    ) -> list:
        """Create chapters from detected boundaries."""
        
        # Sort boundaries by confidence
        boundaries.sort(key=lambda x: x["confidence"], reverse=True)
        
        # Select boundaries
        selected = []
        last_time = 0
        
        for boundary in boundaries:
            if boundary["time"] - last_time >= min_duration:
                selected.append(boundary["time"])
                last_time = boundary["time"]
        
        # If target count specified, adjust
        if target_count and len(selected) > target_count:
            selected = sorted(selected, key=lambda x: next(
                b["confidence"] for b in boundaries if b["time"] == x
            ), reverse=True)[:target_count]
            selected.sort()
        
        # Create chapters
        selected = [0] + selected + [segments[-1]["end"]]
        chapters = []
        
        for i in range(len(selected) - 1):
            start = selected[i]
            end = selected[i + 1]
            
            chapter_text = " ".join(
                seg["text"] for seg in segments
                if start <= seg["start"] < end
            )
            
            chapters.append({
                "index": i + 1,
                "start": start,
                "end": end,
                "duration": end - start,
                "text": chapter_text,
                "title": ""  # To be generated
            })
        
        return chapters
    
    def _generate_titles(self, chapters: list) -> list:
        """Generate descriptive chapter titles using LLM."""
        
        # Build prompt with all chapter texts
        chapter_descriptions = []
        for ch in chapters:
            preview = ch["text"][:500]
            chapter_descriptions.append(
                f"Chapter {ch['index']} ({self._fmt_time(ch['start'])}): {preview}"
            )
        
        prompt = "Generate concise, descriptive titles (3-8 words each) for these video chapters:\n\n"
        prompt += "\n\n".join(chapter_descriptions)
        prompt += "\n\nReturn as JSON array of strings, one title per chapter."
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": "Generate short, SEO-friendly chapter titles. Return ONLY a JSON array of strings."},
                {"role": "user", "content": prompt}
            ],
            max_tokens=500,
            temperature=0.3
        )
        
        try:
            titles = json.loads(response.choices[0].message.content)
            for ch, title in zip(chapters, titles):
                ch["title"] = title
        except (json.JSONDecodeError, ValueError):
            for ch in chapters:
                ch["title"] = f"Chapter {ch['index']}"
        
        return chapters
    
    def format_youtube(self, chapters: list) -> str:
        """Format chapters for YouTube description."""
        
        lines = []
        for ch in chapters:
            timestamp = self._fmt_time(ch["start"])
            lines.append(f"{timestamp} {ch['title']}")
        
        return "\n".join(lines)
    
    def format_vtt_chapters(self, chapters: list) -> str:
        """Format chapters as WebVTT chapters."""
        
        lines = ["WEBVTT\n"]
        
        for ch in chapters:
            start = self._fmt_vtt(ch["start"])
            end = self._fmt_vtt(ch["end"])
            lines.append(f"CHAPTER {ch['index']}")
            lines.append(f"{start} --> {end}")
            lines.append(ch["title"])
            lines.append("")
        
        return "\n".join(lines)
    
    def format_podcast_chapters(self, chapters: list) -> str:
        """Format chapters as podcast chapters (Podcast Namespace)."""
        
        xml_chapters = []
        for ch in chapters:
            xml_chapters.append(
                f'  <chapter start="{int(ch["start"])}" title="{ch["title"]}"/>'
            )
        
        return "<chapters>\n" + "\n".join(xml_chapters) + "\n</chapters>"
    
    def format_markdown(self, chapters: list) -> str:
        """Format chapters as markdown table of contents."""
        
        lines = ["# Video Chapters\n"]
        lines.append(f"**Total Duration**: {self._fmt_time(chapters[-1]['end'])}")
        lines.append(f"**Chapters**: {len(chapters)}\n")
        
        for ch in chapters:
            timestamp = self._fmt_time(ch["start"])
            duration = self._fmt_duration(ch["duration"])
            lines.append(f"- [{timestamp}] **{ch['title']}** ({duration})")
        
        return "\n".join(lines)
    
    def save(self, chapters: list, output_dir: str, base_name: str):
        """Save chapters in multiple formats."""
        
        output_path = Path(output_dir)
        output_path.mkdir(parents=True, exist_ok=True)
        
        # YouTube format
        yt = output_path / f"{base_name}_youtube.txt"
        yt.write_text(self.format_youtube(chapters), encoding="utf-8")
        
        # Markdown
        md = output_path / f"{base_name}_chapters.md"
        md.write_text(self.format_markdown(chapters), encoding="utf-8")
        
        # VTT chapters
        vtt = output_path / f"{base_name}_chapters.vtt"
        vtt.write_text(self.format_vtt_chapters(chapters), encoding="utf-8")
        
        # JSON
        data = [{
            "index": ch["index"],
            "title": ch["title"],
            "start": ch["start"],
            "end": ch["end"],
            "duration": ch["duration"]
        } for ch in chapters]
        
        json_file = output_path / f"{base_name}_chapters.json"
        json_file.write_text(json.dumps(data, indent=2), encoding="utf-8")
        
        print(f"Saved chapters to {output_path}")
        print(f"  - {yt.name} (YouTube)")
        print(f"  - {md.name} (Markdown)")
        print(f"  - {vtt.name} (WebVTT)")
        print(f"  - {json_file.name} (JSON)")
    
    @staticmethod
    def _fmt_time(seconds: float) -> str:
        m, s = divmod(int(seconds), 60)
        h, m = divmod(m, 60)
        if h:
            return f"{h}:{m:02d}:{s:02d}"
        return f"{m}:{s:02d}"
    
    @staticmethod
    def _fmt_vtt(seconds: float) -> str:
        h = int(seconds // 3600)
        m = int((seconds % 3600) // 60)
        s = int(seconds % 60)
        ms = int((seconds % 1) * 1000)
        return f"{h:02d}:{m:02d}:{s:02d}.{ms:03d}"
    
    @staticmethod
    def _fmt_duration(seconds: float) -> str:
        m, s = divmod(int(seconds), 60)
        if m:
            return f"{m}m {s}s"
        return f"{s}s"


def main():
    gen = ChapterGenerator(whisper_model="medium")
    chapters = gen.generate(
        video_path="lecture.mp4",
        language="en",
        target_chapters=8,
        min_chapter_duration=120
    )
    
    gen.save(chapters, "./output", "lecture")
    
    # Print YouTube chapters
    print("\nYouTube Chapters:")
    print(gen.format_youtube(chapters))


if __name__ == "__main__":
    main()
```

## Output Format Examples

### YouTube Description Format
```
0:00 Introduction
2:30 What is Machine Learning?
8:15 Supervised Learning
15:40 Unsupervised Learning
22:00 Reinforcement Learning
28:30 Practical Applications
35:15 Q&A
```

### Markdown Format
```markdown
# Video Chapters

**Total Duration**: 42:30
**Chapters**: 7

- [0:00] **Introduction** (2m 30s)
- [2:30] **What is Machine Learning?** (5m 45s)
- [8:15] **Supervised Learning** (7m 25s)
- [15:40] **Unsupervised Learning** (6m 20s)
- [22:00] **Reinforcement Learning** (6m 30s)
- [28:30] **Practical Applications** (6m 45s)
- [35:15] **Q&A** (7m 15s)
```

## Common Pitfalls

1. **Too Many Chapters**
   - **Problem**: Every topic change creates a chapter
   - **Solution**: Set minimum chapter duration (2-5 minutes)

2. **Vague Chapter Titles**
   - **Problem**: "Part 1", "Part 2" etc.
   - **Solution**: Use LLM to generate descriptive titles

3. **Missing YouTube 0:00 Rule**
   - **Problem**: YouTube requires first chapter at 0:00
   - **Solution**: Always start first chapter at 0:00

4. **Overlapping Boundaries**
   - **Problem**: Multiple boundaries too close together
   - **Solution**: Merge boundaries within minimum gap

## Pro Tips

- **YouTube rule**: First timestamp must be 0:00:00 and at least 3 chapters needed
- **Optimal length**: 3-8 minutes per chapter for most content
- **SEO-friendly titles**: Include keywords in chapter titles
- **Combine methods**: Use both silence and semantic detection
- **Manual refinement**: Let LLM suggest, humans approve

---

## 中文版本

### 适用场景

- 为 YouTube 视频描述添加章节
- 为长讲座创建目录
- 使播客剧集可导航
- 按议程项目组织会议录音
- 构建可搜索的视频索引
- 提高视频可访问性和 SEO

### 快速开始

```bash
pip install openai-whisper openai sentence-transformers

python chapter_generator.py --video 讲座.mp4 --language zh --target 8
```

### YouTube 章节格式要求

1. 第一个时间戳必须是 0:00
2. 至少需要 3 个章节
3. 每个章节至少 10 秒
4. 时间戳格式：HH:MM:SS 或 MM:SS

### 中文章节标题建议

- 使用简洁的中文标题（4-8 个字）
- 包含关键词以提高搜索排名
- 避免使用过于笼统的标题
