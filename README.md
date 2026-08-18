<div align="center">

# 🎬 Awesome Video to Text

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/Skills-12-blue)](#skills)

**Turn any video into structured text.**

12 AI skills for transcription, summarization, notes, and subtitles.

[English](#overview) · [中文](#概述)

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

### 🎙️ Transcription (转录技术)

| Skill | Description | Key Features |
|-------|-------------|--------------|
| [Whisper Transcription](skills/转录技术/whisper-transcription.md) | Local video transcription with OpenAI Whisper | GPU optimization, batch processing, 99 languages |
| [YouTube Transcript](skills/转录技术/youtube-transcript.md) | Extract transcripts from YouTube videos | Auto-generated captions, playlist batch, multi-language |
| [Meeting Transcription](skills/转录技术/meeting-transcription.md) | Meeting and interview transcription with speaker ID | Speaker diarization, action items, real-time mode |

### 📊 Content Extraction (内容提取)

| Skill | Description | Key Features |
|-------|-------------|--------------|
| [Video Summarization](skills/内容提取/video-summarization.md) | Video content summarization | Extractive + abstractive, chapter summaries, multi-type |
| [Key Points Extraction](skills/内容提取/key-points-extraction.md) | Key points and insights extraction | Quotes, decisions, action items, keyword extraction |
| [Chapter Generation](skills/内容提取/chapter-generation.md) | Video chapter and timestamp generation | Topic segmentation, YouTube/podcast format, SEO titles |

### 📝 Note Organization (笔记整理)

| Skill | Description | Key Features |
|-------|-------------|--------------|
| [Video Notes](skills/笔记整理/video-notes.md) | Structured note-taking from videos | Cornell method, mind maps, outline format |
| [Knowledge Extraction](skills/笔记整理/knowledge-extraction.md) | Knowledge extraction and organization | Concept maps, Obsidian integration, graph database |

### 💬 Subtitle Processing (字幕处理)

| Skill | Description | Key Features |
|-------|-------------|--------------|
| [Subtitle Generation](skills/字幕处理/subtitle-generation.md) | Automatic subtitle generation | SRT/ASS/VTT formats, style customization, timing sync |
| [Subtitle Translation](skills/字幕处理/subtitle-translation.md) | Subtitle translation | Bilingual subtitles, cultural adaptation, timing adjust |

### 📚 Learning Aid (学习辅助)

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
| **Whisper (local)** | Medium | ★★★★☆ | Free | ✅ | Privacy, batch processing |
| **Whisper API** | Fast | ★★★★☆ | $0.006/min | ❌ | Quick one-off transcriptions |
| **faster-whisper** | Fast | ★★★★☆ | Free | ✅ | Production pipelines |
| **whisper.cpp** | Fast | ★★★★☆ | Free | ✅ | No Python environment |
| **Google Cloud STT** | Fast | ★★★★☆ | $0.006/min | ❌ | Real-time streaming |
| **Azure Speech** | Fast | ★★★★★ | $1/hr | ❌ | Enterprise, speaker ID |
| **AssemblyAI** | Fast | ★★★★★ | $0.015/min | ❌ | Meeting transcription |

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

[⬆ Back to top](#-awesome-video-to-text)

</div>

---

# 概述

<div align="center">

**将任何视频转换为结构化文本。**

12 个 AI 技能，涵盖转录、摘要、笔记和字幕。

</div>

**2 小时的讲座视频 → 整理好的学习笔记，包含关键要点、章节、闪卡和字幕。**

```
处理前: 📹 lecture_recording.mp4 (2小时, 1.2GB)
处理后: 📝 lecture_notes.md (结构化康奈尔笔记)
        📋 key_points.json (带时间戳的要点)
        📑 chapters.txt (YouTube 章节格式)
        🃏 flashcards.apkg (Anki 间隔重复卡片)
        🎯 quiz.json (自测题目)
        📄 subtitles.srt (中英双语字幕)
```

本仓库是一系列 **AI 技能** 的集合，用于从视频中提取内容并将其组织成结构化、可搜索的文本。这些技能可在 Cursor、Claude Code、Kimi Code 或任何 AI 编程助手 中使用。

---

## 技能列表

### 🎙️ 转录技术

| 技能 | 描述 | 主要特点 |
|------|------|----------|
| [Whisper 转录](skills/转录技术/whisper-transcription.md) | 使用 OpenAI Whisper 进行本地视频转录 | GPU 优化、批处理、99 种语言 |
| [YouTube 转录](skills/转录技术/youtube-transcript.md) | 从 YouTube 视频提取文字记录 | 自动生成字幕、播放列表批量处理、多语言 |
| [会议转录](skills/转录技术/meeting-transcription.md) | 会议和访谈转录（含说话人识别） | 说话人分离、行动项提取、实时模式 |

### 📊 内容提取

| 技能 | 描述 | 主要特点 |
|------|------|----------|
| [视频摘要](skills/内容提取/video-summarization.md) | 视频内容摘要 | 抽取式+生成式、章节摘要、多种类型 |
| [关键要点提取](skills/内容提取/key-points-extraction.md) | 提取关键要点和洞察 | 引言、决策、行动项、关键词提取 |
| [章节生成](skills/内容提取/chapter-generation.md) | 视频章节和时间戳生成 | 主题分割、YouTube/播客格式、SEO 标题 |

### 📝 笔记整理

| 技能 | 描述 | 主要特点 |
|------|------|----------|
| [视频笔记](skills/笔记整理/video-notes.md) | 从视频创建结构化笔记 | 康奈尔笔记法、思维导图、大纲格式 |
| [知识提取](skills/笔记整理/knowledge-extraction.md) | 知识提取与组织 | 概念图、Obsidian 集成、图数据库 |

### 💬 字幕处理

| 技能 | 描述 | 主要特点 |
|------|------|----------|
| [字幕生成](skills/字幕处理/subtitle-generation.md) | 自动生成字幕 | SRT/ASS/VTT 格式、样式定制、时间同步 |
| [字幕翻译](skills/字幕处理/subtitle-translation.md) | 字幕翻译 | 双语字幕、文化适应、时间调整 |

### 📚 学习辅助

| 技能 | 描述 | 主要特点 |
|------|------|----------|
| [讲座笔记](skills/学习辅助/lecture-notes.md) | 讲座和课程笔记 | Anki 闪卡、测验生成、间隔重复 |
| [教程文档](skills/学习辅助/tutorial-documentation.md) | 从视频生成教程文档 | 步骤提取、代码捕获、MkDocs 集成 |

---

## 快速开始

### Cursor

1. 克隆或下载本仓库
2. 在 Cursor 中打开项目
3. 将技能文件内容复制到 `.cursorrules` 或在聊天中引用
4. 让 Cursor 处理你的视频

```
@whisper-transcription.md 转录我的讲座视频 ./videos/lecture.mp4
```

### Claude Code

1. 将本仓库克隆到项目目录
2. 在提示中引用技能

```bash
# 在项目目录中
claude "使用 whisper-transcription 技能转录 ./video.mp4 并生成学习笔记"
```

### Kimi Code

1. 将本仓库添加为技能源
2. 使用 `/skill` 或包含技能文件来引用技能

```bash
kimi "按照 skills/转录技术/whisper-transcription.md 转录我的视频"
```

---

## 工具对比

| 工具 | 速度 | 质量 | 成本 | 离线 | 最适合 |
|------|------|------|------|------|--------|
| **Whisper (本地)** | 中等 | ★★★★☆ | 免费 | ✅ | 隐私保护、批处理 |
| **Whisper API** | 快速 | ★★★★☆ | $0.006/分钟 | ❌ | 快速单次转录 |
| **faster-whisper** | 快速 | ★★★★☆ | 免费 | ✅ | 生产环境流水线 |
| **whisper.cpp** | 快速 | ★★★★☆ | 免费 | ✅ | 无 Python 环境 |
| **Google Cloud STT** | 快速 | ★★★★☆ | $0.006/分钟 | ❌ | 实时流式处理 |
| **Azure Speech** | 快速 | ★★★★★ | $1/小时 | ❌ | 企业级、说话人识别 |
| **AssemblyAI** | 快速 | ★★★★★ | $0.015/分钟 | ❌ | 会议转录 |

### 模型选择指南

| Whisper 模型 | 大小 | 速度 | 质量 | 显存 | 推荐场景 |
|--------------|------|------|------|------|----------|
| `tiny` | 39M | ~32x | ★★☆☆☆ | ~1GB | 快速预览 |
| `base` | 74M | ~16x | ★★★☆☆ | ~1GB | 清晰音频、短片段 |
| `small` | 244M | ~6x | ★★★★☆ | ~2GB | 良好平衡 |
| `medium` | 769M | ~2x | ★★★★☆ | ~5GB | **大多数场景** |
| `large-v3` | 1.5G | 1x | ★★★★★ | ~10GB | 最高精度 |
| `turbo` | 809M | ~8x | ★★★★☆ | ~6GB | **最佳速度/质量比** |

---

## 相关项目

- [awesome-video-skills](https://github.com/nicekate/awesome-video-skills) - 视频编辑和处理的 AI 技能
- [awesome-video-prompts](https://github.com/nicekate/awesome-video-prompts) - 视频 AI 精选提示词
- [awesome-skills](https://github.com/nicekate/awesome-skills) - 通用 AI 技能集合
- [awesome-ai-rules](https://github.com/nicekate/awesome-ai-rules) - AI 编程助手规则和配置

---

## 贡献

欢迎贡献！请查看 [CONTRIBUTING.md](CONTRIBUTING.md) 了解指南。

- 添加新技能
- 改进现有技能
- 修复错误和拼写
- 翻译内容

---

## 许可证

本项目基于 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件。

版权所有 (c) 2024 liangzhengtao

---

<div align="center">

**为视频转文本社区用 ❤️ 制作**

[⬆ 回到顶部](#概述)

</div>
