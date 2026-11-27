# Automatic Subtitle Generation

Generate professional subtitles for videos in multiple formats (SRT, ASS, VTT). Includes timing synchronization, style customization, and platform-specific optimization.

## When to Use

- Adding subtitles to YouTube videos
- Creating closed captions for accessibility
- Generating subtitles for social media clips
- Adding translated subtitles to presentations
- Creating karaoke-style lyrics display
- Producing broadcast-quality captions

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [OpenAI Whisper](https://github.com/openai/whisper) | Latest | Speech recognition |
| [ffmpeg](https://ffmpeg.org/) | 4.0+ | Subtitle embedding |

### Optional

| Tool | Purpose |
|------|---------|
| [whisperx](https://github.com/m-bain/whisperX) | Word-level alignment |
| [pysrt](https://github.com/byroot/pysrt) | Subtitle manipulation |
| [aegisub](https://aegisub.org/) | Manual subtitle editing |
| [Subtitle Edit](https://github.com/SubtitleEdit/subtitleedit) | GUI editor |

### Installation

```bash
# Core
pip install openai-whisper

# WhisperX for precise word-level timing
pip install whisperx

# ffmpeg
# macOS
brew install ffmpeg
# Ubuntu/Debian
sudo apt install ffmpeg
# Windows
winget install ffmpeg
```

## Step-by-Step Workflow

### Step 1: Transcribe with Precise Timing

```bash
whisper video.mp4 --model medium --word_timestamps True --output_format srt
```

### Step 2: Refine Timing

```python
# Adjust subtitle timing, merge short segments
```

### Step 3: Style Subtitles

```python
# Add styling for ASS format
```

### Step 4: Embed in Video (Optional)

```bash
ffmpeg -i video.mp4 -vf subtitles=output.srt output_with_subs.mp4
```

## Code Templates

### Template 1: Professional Subtitle Generator

```python
#!/usr/bin/env python3
"""Generate professional subtitles in multiple formats."""

import whisper
import json
import re
from pathlib import Path
from datetime import timedelta


class SubtitleGenerator:
    """Generate and customize video subtitles."""
    
    def __init__(self, model_size: str = "medium"):
        self.model = whisper.load_model(model_size)
    
    def generate(
        self,
        video_path: str,
        language: str = None,
        max_chars_per_line: int = 42,
        max_lines: int = 2,
        min_duration: float = 1.0,
        max_duration: float = 6.0
    ) -> list:
        """Generate subtitles with optimized timing."""
        
        print(f"Transcribing: {video_path}")
        result = self.model.transcribe(
            video_path,
            language=language,
            word_timestamps=True,
            verbose=False
        )
        
        segments = result["segments"]
        
        # Optimize segments
        optimized = self._optimize_segments(
            segments,
            max_chars=max_chars_per_line,
            max_lines=max_lines,
            min_duration=min_duration,
            max_duration=max_duration
        )
        
        print(f"Generated {len(optimized)} subtitle segments")
        return optimized
    
    def _optimize_segments(
        self,
        segments: list,
        max_chars: int = 42,
        max_lines: int = 2,
        min_duration: float = 1.0,
        max_duration: float = 6.0
    ) -> list:
        """Optimize subtitle segments for readability."""
        
        optimized = []
        
        for seg in segments:
            text = seg["text"].strip()
            start = seg["start"]
            end = seg["end"]
            
            # Split long text
            if len(text) > max_chars * max_lines:
                sub_segments = self._split_long_text(text, max_chars, max_lines)
                sub_duration = (end - start) / len(sub_segments)
                
                for i, sub_text in enumerate(sub_segments):
                    optimized.append({
                        "index": len(optimized) + 1,
                        "start": start + i * sub_duration,
                        "end": start + (i + 1) * sub_duration,
                        "text": sub_text
                    })
            else:
                # Ensure minimum duration
                duration = end - start
                if duration < min_duration:
                    end = start + min_duration
                if duration > max_duration:
                    end = start + max_duration
                
                optimized.append({
                    "index": len(optimized) + 1,
                    "start": start,
                    "end": end,
                    "text": text
                })
        
        # Re-number
        for i, seg in enumerate(optimized, 1):
            seg["index"] = i
        
        return optimized
    
    def _split_long_text(self, text: str, max_chars: int, max_lines: int) -> list:
        """Split long text into subtitle-sized chunks."""
        
        words = text.split()
        chunks = []
        current_lines = []
        current_line = []
        current_len = 0
        
        for word in words:
            if current_len + len(word) + 1 > max_chars:
                current_lines.append(" ".join(current_line))
                current_line = [word]
                current_len = len(word)
                
                if len(current_lines) >= max_lines:
                    chunks.append("\n".join(current_lines))
                    current_lines = []
            else:
                current_line.append(word)
                current_len += len(word) + 1
        
        if current_line:
            current_lines.append(" ".join(current_line))
        if current_lines:
            chunks.append("\n".join(current_lines))
        
        return chunks if chunks else [text]
    
    def to_srt(self, segments: list) -> str:
        """Convert to SRT format."""
        
        lines = []
        for seg in segments:
            start = self._fmt_srt(seg["start"])
            end = self._fmt_srt(seg["end"])
            lines.append(f"{seg['index']}")
            lines.append(f"{start} --> {end}")
            lines.append(seg["text"])
            lines.append("")
        
        return "\n".join(lines)
    
    def to_vtt(self, segments: list) -> str:
        """Convert to WebVTT format."""
        
        lines = ["WEBVTT", ""]
        for seg in segments:
            start = self._fmt_vtt(seg["start"])
            end = self._fmt_vtt(seg["end"])
            lines.append(f"{seg['index']}")
            lines.append(f"{start} --> {end}")
            lines.append(seg["text"])
            lines.append("")
        
        return "\n".join(lines)
    
    def to_ass(
        self,
        segments: list,
        font: str = "Arial",
        font_size: int = 48,
        primary_color: str = "&H00FFFFFF",
        outline_color: str = "&H00000000",
        outline_width: int = 2,
        position: int = 2
    ) -> str:
        """Convert to ASS (Advanced SubStation Alpha) format."""
        
        header = f"""[Script Info]
Title: Generated Subtitles
ScriptType: v4.00+
PlayResX: 1920
PlayResY: 1080
WrapStyle: 0

[V4+ Styles]
Format: Name, Fontname, Fontsize, PrimaryColour, SecondaryColour, OutlineColour, BackColour, Bold, Italic, Underline, StrikeOut, ScaleX, ScaleY, Spacing, Angle, BorderStyle, Outline, Shadow, Alignment, MarginL, MarginR, MarginV, Encoding
Style: Default,{font},{font_size},{primary_color},&H000000FF,{outline_color},&H80000000,0,0,0,0,100,100,0,0,1,{outline_width},1,{position},10,10,40,1

[Events]
Format: Layer, Start, End, Style, Name, MarginL, MarginR, MarginV, Effect, Text
"""
        
        events = []
        for seg in segments:
            start = self._fmt_ass(seg["start"])
            end = self._fmt_ass(seg["end"])
            text = seg["text"].replace("\n", "\\N")
            events.append(f"Dialogue: 0,{start},{end},Default,,0,0,0,,{text}")
        
        return header + "\n".join(events) + "\n"
    
    def save(
        self,
        segments: list,
        output_dir: str,
        base_name: str,
        formats: list = None
    ):
        """Save subtitles in specified formats."""
        
        output_path = Path(output_dir)
        output_path.mkdir(parents=True, exist_ok=True)
        
        formats = formats or ["srt", "vtt", "ass"]
        
        for fmt in formats:
            if fmt == "srt":
                content = self.to_srt(segments)
            elif fmt == "vtt":
                content = self.to_vtt(segments)
            elif fmt == "ass":
                content = self.to_ass(segments)
            else:
                continue
            
            file_path = output_path / f"{base_name}.{fmt}"
            file_path.write_text(content, encoding="utf-8")
            print(f"Saved: {file_path}")
    
    def embed_subtitles(
        self,
        video_path: str,
        subtitle_path: str,
        output_path: str,
        burn_in: bool = False,
        language: str = "eng"
    ):
        """Embed subtitles into video file."""
        
        if burn_in:
            # Burn subtitles into video (hardcoded)
            cmd = (
                f'ffmpeg -i "{video_path}" -vf subtitles="{subtitle_path}" '
                f'"{output_path}"'
            )
        else:
            # Soft subtitles (can be toggled)
            cmd = (
                f'ffmpeg -i "{video_path}" -i "{subtitle_path}" '
                f'-c copy -c:s mov_text '
                f'-metadata:s:s:0 language={language} '
                f'"{output_path}"'
            )
        
        import subprocess
        subprocess.run(cmd, shell=True, check=True)
        print(f"Saved: {output_path}")
    
    @staticmethod
    def _fmt_srt(seconds: float) -> str:
        h = int(seconds // 3600)
        m = int((seconds % 3600) // 60)
        s = int(seconds % 60)
        ms = int((seconds % 1) * 1000)
        return f"{h:02d}:{m:02d}:{s:02d},{ms:03d}"
    
    @staticmethod
    def _fmt_vtt(seconds: float) -> str:
        h = int(seconds // 3600)
        m = int((seconds % 3600) // 60)
        s = int(seconds % 60)
        ms = int((seconds % 1) * 1000)
        return f"{h:02d}:{m:02d}:{s:02d}.{ms:03d}"
    
    @staticmethod
    def _fmt_ass(seconds: float) -> str:
        h = int(seconds // 3600)
        m = int((seconds % 3600) // 60)
        s = int(seconds % 60)
        cs = int((seconds % 1) * 100)
        return f"{h}:{m:02d}:{s:02d}.{cs:02d}"


def main():
    gen = SubtitleGenerator(model_size="medium")
    
    segments = gen.generate(
        video_path="video.mp4",
        language="en",
        max_chars_per_line=42
    )
    
    gen.save(segments, "./subtitles", "video", formats=["srt", "vtt", "ass"])
    
    # Optionally embed in video
    gen.embed_subtitles(
        video_path="video.mp4",
        subtitle_path="./subtitles/video.srt",
        output_path="video_subtitled.mp4",
        burn_in=False
    )


if __name__ == "__main__":
    main()
```

## Output Format Examples

### SRT Format
```
1
00:00:00,000 --> 00:00:03,500
Welcome to this tutorial on Python
web scraping with BeautifulSoup.

2
00:00:03,500 --> 00:00:07,200
Today we'll learn how to extract data
from HTML pages using Python.

3
00:00:07,200 --> 00:00:11,800
First, let me show you how to install
the required libraries.
```

### ASS Format (with styling)
```
[Script Info]
Title: Generated Subtitles
PlayResX: 1920
PlayResY: 1080

[V4+ Styles]
Style: Default,Arial,48,&H00FFFFFF,&H000000FF,&H00000000,...

[Events]
Dialogue: 0,0:00:00.00,0:00:03.50,Default,,0,0,0,,Welcome to this tutorial
```

## Common Pitfalls

1. **Subtitles Too Fast**
   - **Problem**: Text disappears before viewers can read it
   - **Solution**: Minimum 1 second per subtitle, adjust reading speed

2. **Lines Too Long**
   - **Problem**: Text overflows screen width
   - **Solution**: Max 42 characters per line for TV, 37 for cinema

3. **Poor Timing Sync**
   - **Problem**: Subtitles appear too early/late
   - **Solution**: Use word-level timestamps from WhisperX

4. **Encoding Issues**
   - **Problem**: Special characters display incorrectly
   - **Solution**: Always save with UTF-8 BOM for SRT

5. **Overlapping Subtitles**
   - **Problem**: Previous subtitle not cleared before new one
   - **Solution**: Add 0.1s gap between subtitles

## Pro Tips

- **42-character rule**: Max 42 characters per line for readability
- **2-line maximum**: Never more than 2 lines per subtitle
- **Reading speed**: 17 characters/second for adults
- **Use ASS for styling**: More control than SRT
- **Font choice**: Arial or Helvetica for clarity
- **Contrast**: White text with black outline works on any background

---

## 中文版本

### 适用场景

- 为 YouTube 视频添加字幕
- 创建无障碍闭幕字幕
- 为社交媒体片段生成字幕
- 为演示添加翻译字幕
- 创建卡拉 OK 风格歌词显示
- 制作广播级字幕

### 快速开始

```bash
pip install openai-whisper

# 生成 SRT 字幕
whisper video.mp4 --model medium --language zh --output_format srt

# 嵌入字幕到视频
ffmpeg -i video.mp4 -vf subtitles=output.srt output_with_subs.mp4
```

### 中文字幕注意事项

1. **每行字数**：建议不超过 18 个汉字
2. **字体选择**：使用黑体或宋体，避免花哨字体
3. **标点符号**：使用全角标点
4. **断句位置**：在语义自然处断句
