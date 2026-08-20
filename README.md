[English](README.md) | [中文](README.zh.md) | [日本語](README.ja.md) | [Français](README.fr.md) | [Español](README.es.md)
n<div align="center">

<img src=".banner.svg" width="100%" alt="banner">

</div>


<div align="center">

# Awesome Video to Text

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/Skills-12-blue)](#skills)

**Turn any video into structured text.**

12 AI skills for transcription, summarization, notes, and subtitles.

</div>

---

## Overview

**2-hour lecture video → organized study notes with key points, chapters, flashcards, and subtitles.**

```
Before: 📹 lecture_recording.mp4 (2 hours, 1.2GB)
After:  📝 lecture_notes.md (structured Cornell notes)
        📋 key_points.json (timestamped insights)
        📑 chapters.txt (YouTube-ready chapters)
        🃏 flashcards.apkg (Anki spaced repetition)
        🎯 quiz.json (self-assessment questions)
        📄 subtitles.srt (Chinese + English)
```

This repository is a curated collection of **AI skills** that extract content from videos and organize it into structured, searchable text. Use these skills in Cursor, Claude Code, Kimi Code, or any AI coding assistant.

---

## Skills

### Transcription

| Skill | Description | Key Features |
|-------|-------------|--------------|
| [Whisper Transcription](skills/转录技术/whisper-transcription.md) | Local video transcription with OpenAI Whisper | GPU optimization, batch processing, 99 languages |
| [YouTube Transcript](skills/转录技术/youtube-transcript.md) | Extract transcripts from YouTube videos | Auto-generated captions, playlist batch, multi-language |
| [Meeting Transcription](skills/转录技术/meeting-transcription.md) | Meeting and interview transcription with speaker ID | Speaker diarization, action items, real-time mode |

### Content Extraction

| Skill | Description | Key Features |
|-------|-------------|--------------|
| [Video Summarization](skills/内容提取/video-summarization.md) | Video content summarization | Extractive + abstractive, chapter summaries, multi-type |
| [Key Points Extraction](skills/内容提取/key-points-extraction.md) | Key points and insights extraction | Quotes, decisions, action items, keyword extraction |
| [Chapter Generation](skills/内容提取/chapter-generation.md) | Video chapter and timestamp generation | Topic segmentation, YouTube/podcast format, SEO titles |

### Note Organization

| Skill | Description | Key Features |
|-------|-------------|--------------|
| [Video Notes](skills/笔记整理/video-notes.md) | Structured note-taking from videos | Cornell method, mind maps, outline format |
| [Knowledge Extraction](skills/笔记整理/knowledge-extraction.md) | Knowledge extraction and organization | Concept maps, Obsidian integration, graph database |

### Subtitle Processing

| Skill | Description | Key Features |
|-------|-------------|--------------|
| [Subtitle Generation](skills/字幕处理/subtitle-generation.md) | Automatic subtitle generation | SRT/ASS/VTT formats, style customization, timing sync |
| [Subtitle Translation](skills/字幕处理/subtitle-translation.md) | Subtitle translation | Bilingual subtitles, cultural adaptation, timing adjust |

### Learning Aid

| Skill | Description | Key Features |
|-------|-------------|--------------|
| [Lecture Notes](skills/学习辅助/lecture-notes.md) | Lecture and course note-taking | Anki flashcards, quiz generation, spaced repetition |
| [Tutorial Documentation](skills/学习辅助/tutorial-documentation.md) | Tutorial documentation from videos | Step extraction, code capture, MkDocs integration |

---

## Quick Start

### Cursor

1. Clone or download this repository
2. Open your project in Cursor
3. Copy the skill file content into your `.cursorrules` or reference it in chat
4. Ask Cursor to process your video

```
@whisper-transcription.md Transcribe my lecture video at ./videos/lecture.mp4
```

### Claude Code

1. Clone this repository to your project
2. Reference skills in your prompts

```bash
# In your project directory
claude "Using the whisper-transcription skill, transcribe ./video.mp4 and generate study notes"
```

### Kimi Code

1. Add this repository as a skill source
2. Reference skills using `/skill` or by including the skill file

```bash
kimi "Follow skills/转录技术/whisper-transcription.md to transcribe my video"
```

---

## Tool Comparison

| Tool | Speed | Quality | Cost | Offline | Best For |
|------|-------|---------|------|---------|----------|
| **Whisper (local)** | Medium | ★★★★☆ | Free | Yes | Privacy, batch processing |
| **Whisper API** | Fast | ★★★★☆ | $0.006/min | No | Quick one-off transcriptions |
| **faster-whisper** | Fast | ★★★★☆ | Free | Yes | Production pipelines |
| **whisper.cpp** | Fast | ★★★★☆ | Free | Yes | No Python environment |
| **Google Cloud STT** | Fast | ★★★★☆ | $0.006/min | No | Real-time streaming |
| **Azure Speech** | Fast | ★★★★★ | $1/hr | No | Enterprise, speaker ID |
| **AssemblyAI** | Fast | ★★★★★ | $0.015/min | No | Meeting transcription |

### Model Selection Guide

| Whisper Model | Size | Speed | Quality | VRAM | Recommendation |
|---------------|------|-------|---------|------|----------------|
| `tiny` | 39M | ~32x | ★★☆☆☆ | ~1GB | Quick drafts only |
| `base` | 74M | ~16x | ★★★☆☆ | ~1GB | Clean audio, short clips |
| `small` | 244M | ~6x | ★★★★☆ | ~2GB | Good balance |
| `medium` | 769M | ~2x | ★★★★☆ | ~5GB | **Most use cases** |
| `large-v3` | 1.5G | 1x | ★★★★★ | ~10GB | Maximum accuracy |
| `turbo` | 809M | ~8x | ★★★★☆ | ~6GB | **Best speed/quality** |

---

## Project Structure

```
awesome-video-to-text/
├── README.md
├── README.zh.md
├── LICENSE
├── CONTRIBUTING.md
├── SECURITY.md
├── CODE_OF_CONDUCT.md
├── CHANGELOG.md
├── .gitignore
├── .github/
│   ├── workflows/
│   │   └── ci.yml
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   ├── feature_request.md
│   │   └── new_skill.md
│   └── pull_request_template.md
└── skills/
    ├── 转录技术/                    # Transcription
    │   ├── whisper-transcription.md
    │   ├── youtube-transcript.md
    │   └── meeting-transcription.md
    ├── 内容提取/                    # Content Extraction
    │   ├── video-summarization.md
    │   ├── key-points-extraction.md
    │   └── chapter-generation.md
    ├── 笔记整理/                    # Note Organization
    │   ├── video-notes.md
    │   └── knowledge-extraction.md
    ├── 字幕处理/                    # Subtitle Processing
    │   ├── subtitle-generation.md
    │   └── subtitle-translation.md
    └── 学习辅助/                    # Learning Aid
        ├── lecture-notes.md
        └── tutorial-documentation.md
```

---

## See Also

- [awesome-video-skills](https://github.com/nicekate/awesome-video-skills) - AI skills for video editing and processing
- [awesome-video-prompts](https://github.com/nicekate/awesome-video-prompts) - Curated prompts for video AI
- [awesome-skills](https://github.com/nicekate/awesome-skills) - General collection of AI skills
- [awesome-ai-rules](https://github.com/nicekate/awesome-ai-rules) - AI coding assistant rules and configurations

---

## Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

- Add new skills
- Improve existing skills
- Fix bugs and typos
- Translate content

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

Copyright (c) 2024 liangzhengtao

---

<div align="center">

**Made with ❤️ for the video-to-text community**

</div>
