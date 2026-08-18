# Whisper Video Transcription

Transcribe video and audio files locally using OpenAI's Whisper speech recognition model. Supports 99 languages with automatic language detection.

## When to Use

- Transcribing lecture recordings into text
- Converting interview audio to searchable transcripts
- Extracting dialogue from video files
- Creating text records of podcast episodes
- Processing meeting recordings for documentation
- Batch converting a library of video/audio files

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [OpenAI Whisper](https://github.com/openai/whisper) | Latest | Core transcription engine |
| [ffmpeg](https://ffmpeg.org/) | 4.0+ | Audio extraction from video |
| Python | 3.8+ | Runtime environment |

### Optional

| Tool | Version | Purpose |
|------|---------|---------|
| [faster-whisper](https://github.com/SYSTRAN/faster-whisper) | Latest | 4x faster inference via CTranslate2 |
| [whisper.cpp](https://github.com/ggerganov/whisper.cpp) | Latest | C++ implementation, no Python needed |
| [insanely-fast-whisper](https://github.com/Vaibhavs10/insanely-fast-whisper) | Latest | Optimized for modern GPUs |

### Installation

```bash
# Install OpenAI Whisper
pip install openai-whisper

# Install ffmpeg (required)
# macOS
brew install ffmpeg

# Ubuntu/Debian
sudo apt update && sudo apt install ffmpeg

# Windows (with Chocolatey)
choco install ffmpeg

# Windows (with winget)
winget install ffmpeg

# Alternative: faster-whisper (recommended for production)
pip install faster-whisper

# Alternative: insanely-fast-whisper (best for modern GPUs)
pip install insanely-fast-whisper
```

## Step-by-Step Workflow

### Step 1: Prepare Your Files

```bash
# Check supported formats
ls *.mp4 *.mkv *.avi *.mov *.webm *.mp3 *.wav *.flac *.m4a

# Extract audio from video (optional, Whisper handles video directly)
ffmpeg -i input.mp4 -vn -acodec pcm_s16le -ar 16000 -ac 1 output.wav
```

### Step 2: Select Model

| Model | Size | Speed | Quality | VRAM | Best For |
|-------|------|-------|---------|------|----------|
| `tiny` | 39M | ~32x | Low | ~1GB | Quick drafts, testing |
| `base` | 74M | ~16x | Fair | ~1GB | Clean audio, short clips |
| `small` | 244M | ~6x | Good | ~2GB | General use, good balance |
| `medium` | 769M | ~2x | Great | ~5GB | Most use cases |
| `large-v3` | 1550M | 1x | Best | ~10GB | Maximum accuracy |
| `turbo` | 809M | ~8x | Great | ~6GB | Fast + accurate |

### Step 3: Transcribe

```bash
# Basic transcription
whisper input.mp4 --model medium

# Specify language (skip auto-detection)
whisper input.mp4 --model medium --language zh

# Output to specific directory
whisper input.mp4 --model medium --output_dir ./output

# Output specific formats
whisper input.mp4 --model medium --output_format srt txt vtt

# With word-level timestamps
whisper input.mp4 --model medium --word_timestamps True
```

### Step 4: Review and Edit

```bash
# View the transcription
cat output/input.srt

# Or open the text file
cat output/input.txt
```

## Code Templates

### Template 1: Basic Batch Transcription

```python
#!/usr/bin/env python3
"""Batch transcribe all video/audio files in a directory."""

import whisper
import os
from pathlib import Path

def transcribe_directory(
    input_dir: str,
    output_dir: str = "./transcripts",
    model_name: str = "medium",
    language: str = None,
    output_format: str = "txt"
):
    """Transcribe all media files in a directory."""
    
    # Create output directory
    Path(output_dir).mkdir(parents=True, exist_ok=True)
    
    # Load model
    print(f"Loading Whisper model: {model_name}")
    model = whisper.load_model(model_name)
    
    # Supported extensions
    extensions = {".mp4", ".mkv", ".avi", ".mov", ".webm", 
                  ".mp3", ".wav", ".flac", ".m4a", ".ogg"}
    
    # Find all media files
    input_path = Path(input_dir)
    media_files = [
        f for f in input_path.rglob("*") 
        if f.suffix.lower() in extensions
    ]
    
    print(f"Found {len(media_files)} media files")
    
    for i, file_path in enumerate(media_files, 1):
        print(f"\n[{i}/{len(media_files)}] Processing: {file_path.name}")
        
        try:
            # Transcribe
            result = model.transcribe(
                str(file_path),
                language=language,
                verbose=False
            )
            
            # Generate output path
            output_file = Path(output_dir) / f"{file_path.stem}.{output_format}"
            
            # Save transcription
            if output_format == "txt":
                with open(output_file, "w", encoding="utf-8") as f:
                    f.write(result["text"])
            elif output_format == "srt":
                with open(output_file, "w", encoding="utf-8") as f:
                    for j, segment in enumerate(result["segments"], 1):
                        start = format_timestamp(segment["start"])
                        end = format_timestamp(segment["end"])
                        text = segment["text"].strip()
                        f.write(f"{j}\n{start} --> {end}\n{text}\n\n")
            
            print(f"  Saved: {output_file}")
            
        except Exception as e:
            print(f"  Error: {e}")
    
    print("\nTranscription complete!")


def format_timestamp(seconds: float) -> str:
    """Convert seconds to SRT timestamp format."""
    hours = int(seconds // 3600)
    minutes = int((seconds % 3600) // 60)
    secs = int(seconds % 60)
    millis = int((seconds % 1) * 1000)
    return f"{hours:02d}:{minutes:02d}:{secs:02d},{millis:03d}"


if __name__ == "__main__":
    transcribe_directory(
        input_dir="./videos",
        output_dir="./transcripts",
        model_name="medium",
        language=None,  # Auto-detect
        output_format="txt"
    )
```

### Template 2: GPU-Optimized Transcription with faster-whisper

```python
#!/usr/bin/env python3
"""GPU-optimized transcription using faster-whisper."""

from faster_whisper import WhisperModel
from pathlib import Path
import time

def transcribe_gpu(
    input_file: str,
    model_size: str = "large-v3",
    device: str = "cuda",
    compute_type: str = "float16",
    language: str = None,
    beam_size: int = 5,
    output_dir: str = "./output"
):
    """Transcribe with GPU acceleration using faster-whisper."""
    
    print(f"Loading model {model_size} on {device}...")
    model = WhisperModel(
        model_size,
        device=device,
        compute_type=compute_type
    )
    
    print(f"Transcribing: {input_file}")
    start_time = time.time()
    
    segments, info = model.transcribe(
        input_file,
        language=language,
        beam_size=beam_size,
        vad_filter=True,  # Voice Activity Detection
        vad_parameters=dict(
            min_silence_duration_ms=500,
            speech_pad_ms=200
        )
    )
    
    print(f"Detected language: {info.language} (probability: {info.language_probability:.2f})")
    
    # Collect segments
    output_segments = []
    full_text = []
    
    for segment in segments:
        output_segments.append({
            "start": segment.start,
            "end": segment.end,
            "text": segment.text.strip()
        })
        full_text.append(segment.text.strip())
        
        # Progress indicator
        print(f"  [{segment.start:.1f}s -> {segment.end:.1f}s] {segment.text.strip()[:60]}...")
    
    elapsed = time.time() - start_time
    duration = info.duration
    speed = duration / elapsed if elapsed > 0 else 0
    
    print(f"\nCompleted in {elapsed:.1f}s ({speed:.1f}x realtime)")
    
    # Save outputs
    output_path = Path(output_dir)
    output_path.mkdir(parents=True, exist_ok=True)
    stem = Path(input_file).stem
    
    # Save as SRT
    srt_path = output_path / f"{stem}.srt"
    with open(srt_path, "w", encoding="utf-8") as f:
        for i, seg in enumerate(output_segments, 1):
            start = _fmt_ts(seg["start"])
            end = _fmt_ts(seg["end"])
            f.write(f"{i}\n{start} --> {end}\n{seg['text']}\n\n")
    
    # Save as plain text
    txt_path = output_path / f"{stem}.txt"
    with open(txt_path, "w", encoding="utf-8") as f:
        f.write(" ".join(full_text))
    
    print(f"Saved: {srt_path}")
    print(f"Saved: {txt_path}")
    
    return output_segments


def _fmt_ts(seconds: float) -> str:
    h = int(seconds // 3600)
    m = int((seconds % 3600) // 60)
    s = int(seconds % 60)
    ms = int((seconds % 1) * 1000)
    return f"{h:02d}:{m:02d}:{s:02d},{ms:03d}"


if __name__ == "__main__":
    transcribe_gpu(
        input_file="lecture.mp4",
        model_size="large-v3",
        device="cuda",
        language="en"
    )
```

### Template 3: Whisper API Integration

```python
#!/usr/bin/env python3
"""Transcribe using OpenAI's Whisper API (cloud-based)."""

from openai import OpenAI
from pathlib import Path
import os

def transcribe_api(
    input_file: str,
    language: str = None,
    response_format: str = "verbose_json",
    prompt: str = None
):
    """Transcribe using OpenAI Whisper API."""
    
    client = OpenAI()  # Uses OPENAI_API_KEY env var
    
    with open(input_file, "rb") as audio_file:
        kwargs = {
            "model": "whisper-1",
            "file": audio_file,
            "response_format": response_format,
        }
        if language:
            kwargs["language"] = language
        if prompt:
            kwargs["prompt"] = prompt  # Helps with technical terms
        
        result = client.audio.transcriptions.create(**kwargs)
    
    if response_format == "verbose_json":
        print(f"Language: {result.language}")
        print(f"Duration: {result.duration:.1f}s")
        print(f"Segments: {len(result.segments)}")
        
        # Save SRT
        stem = Path(input_file).stem
        with open(f"{stem}.srt", "w", encoding="utf-8") as f:
            for i, seg in enumerate(result.segments, 1):
                start = _fmt(seg.start)
                end = _fmt(seg.end)
                f.write(f"{i}\n{start} --> {end}\n{seg.text.strip()}\n\n")
    else:
        print(result)
    
    return result


def _fmt(seconds: float) -> str:
    h = int(seconds // 3600)
    m = int((seconds % 3600) // 60)
    s = int(seconds % 60)
    ms = int((seconds % 1) * 1000)
    return f"{h:02d}:{m:02d}:{s:02d},{ms:03d}"


if __name__ == "__main__":
    transcribe_api(
        input_file="interview.mp3",
        language="zh",
        prompt="这是一段关于人工智能的技术访谈。"
    )
```

## Output Format Examples

### Plain Text Output
```
Welcome to today's lecture on machine learning. We'll cover three main topics: 
supervised learning, unsupervised learning, and reinforcement learning. Let's 
start with supervised learning, which is perhaps the most commonly used approach 
in practice...
```

### SRT Output
```
1
00:00:00,000 --> 00:00:05,240
Welcome to today's lecture on machine learning.

2
00:00:05,240 --> 00:00:10,480
We'll cover three main topics: supervised learning,

3
00:00:10,480 --> 00:00:14,920
unsupervised learning, and reinforcement learning.
```

### VTT Output
```
WEBVTT

00:00:00.000 --> 00:00:05.240
Welcome to today's lecture on machine learning.

00:00:05.240 --> 00:00:10.480
We'll cover three main topics: supervised learning,
```

## Common Pitfalls

1. **Out of Memory (OOM)**
   - **Problem**: Large model runs out of GPU memory
   - **Solution**: Use `--fp16 False` or switch to `float32` compute type, or use a smaller model
   
2. **Poor Quality on Long Videos**
   - **Problem**: Accuracy degrades on files longer than 30 minutes
   - **Solution**: Split audio into chunks using ffmpeg:
     ```bash
     ffmpeg -i long.mp4 -f segment -segment_time 1800 -c copy chunk_%03d.mp4
     ```

3. **Hallucinated Repetitions**
   - **Problem**: Whisper repeats the same phrase endlessly
   - **Solution**: Use `--condition_on_previous_text False` to prevent loop

4. **Wrong Language Detection**
   - **Problem**: Whisper detects wrong language
   - **Solution**: Always specify `--language` when you know the language

5. **Background Music Interference**
   - **Problem**: Music causes incorrect transcriptions
   - **Solution**: Use VAD filter or pre-process audio to isolate speech:
     ```bash
     # Use ffmpeg high-pass filter to reduce music
     ffmpeg -i input.mp4 -af "highpass=f=200,lowpass=f=3000" cleaned.wav
     ```

## Pro Tips

- **Start with `turbo` model**: Best speed/quality ratio for most use cases
- **Use `--word_timestamps True`** when you need precise word-level timing for subtitles
- **Provide a prompt** with technical terms or names to improve accuracy
- **Use VAD filtering** with faster-whisper to skip silence and reduce processing time
- **Batch process overnight**: Use `nohup` or `tmux` for long batch jobs
- **Cache models**: First run downloads models; subsequent runs use the cache at `~/.cache/whisper/`
- **Language-specific tips**: For Chinese, use `large-v3` for best character accuracy
- **Combine with diarization**: Use `pyannote-audio` for speaker identification after transcription

---

## 中文版本

### 适用场景

- 将讲座录音转录为文字
- 将采访音频转换为可搜索的文本
- 从视频文件中提取对话内容
- 为播客剧集创建文字记录
- 处理会议录音用于文档记录
- 批量转换视频/音频文件库

### 快速开始

```bash
# 安装 Whisper
pip install openai-whisper

# 安装 ffmpeg
# Ubuntu/Debian
sudo apt update && sudo apt install ffmpeg

# macOS
brew install ffmpeg

# 基础转录
whisper 视频.mp4 --model medium --language zh --output_format srt

# 批量转录当前目录所有视频
for f in *.mp4; do whisper "$f" --model medium --language zh; done
```

### 模型选择建议

| 模型 | 适用场景 |
|------|----------|
| `tiny` | 快速预览、测试 |
| `base` | 清晰音频、短片段 |
| `small` | 一般用途，平衡速度和质量 |
| `medium` | 大多数场景的最佳选择 |
| `large-v3` | 需要最高精度时使用 |
| `turbo` | 快速且准确，推荐日常使用 |

### 中文转录注意事项

1. **使用 `large-v3` 模型**：中文识别最准确
2. **指定语言**：始终使用 `--language zh` 避免语言检测错误
3. **提供提示词**：包含专业术语可提高准确率
4. **检查标点**：Whisper 对中文标点处理有时需要后处理
