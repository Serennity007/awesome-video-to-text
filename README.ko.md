[English](README.md) | [中文](README.zh.md) | [日本語](README.ja.md) | [Français](README.fr.md) | [Español](README.es.md) | [العربية](README.ar.md) | [한국어](README.ko.md) | [Português](README.pt.md) | [Русский](README.ru.md) | [Deutsch](README.de.md)

<div align="center">

<img src=".banner.svg" width="100%" alt="banner">

</div>


<div align="center">

# Awesome Video to Text

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/Skills-12-blue)](#스킬-목록)

**모든 영상을 구조화된 텍스트로 변환하세요.**

전사, 요약, 메모, 자막을 위한 12가지 AI 스킬.

</div>

---

## 개요

**2시간 강의 영상 → 핵심 포인트, 챕터, 플래시카드, 자막이 포함된 체계적인 학습 노트.**

```
이전: 📹 lecture_recording.mp4 (2시간, 1.2GB)
이후:  📝 lecture_notes.md (구조화된 코넬 노트)
      📋 key_points.json (타임스탬프 포함 핵심 인사이트)
      👑 chapters.txt (유튜브용 챕터)
      🃏 flashcards.apkg (Anki 간격 반복 학습)
      🎯 quiz.json (자기 평가 문제)
      📄 subtitles.srt (중국어 + 영어)
```

이 저장소는 영상에서 콘텐츠를 추출하여 구조화되고 검색 가능한 텍스트로 정리하는 **AI 스킬**의 큐레이션 컬렉션입니다. Cursor, Claude Code, Kimi Code 또는 모든 AI 코딩 어시스턴트에서 이 스킬을 사용하세요.

---

## 스킬 목록

### 전사

| 스킬 | 설명 | 주요 기능 |
|-------|-------------|--------------|
| [Whisper Transcription](skills/转录技术/whisper-transcription.md) | OpenAI Whisper로 로컬 영상 전사 | GPU 최적화, 일괄 처리, 99개 언어 |
| [YouTube Transcript](skills/转录技术/youtube-transcript.md) | YouTube 영상에서 자막 추출 | 자동 생성 자막, 재생목록 일괄 처리, 다국어 |
| [Meeting Transcription](skills/转录技术/meeting-transcription.md) | 화자 식별이 포함된 회의 및 인터뷰 전사 | 화자 분리, 액션 아이템, 실시간 모드 |

### 콘텐츠 추출

| 스킬 | 설명 | 주요 기능 |
|-------|-------------|--------------|
| [Video Summarization](skills/内容提取/video-summarization.md) | 영상 콘텐츠 요약 | 추출적 + 추상적, 챕터 요약, 다중 유형 |
| [Key Points Extraction](skills/内容提取/key-points-extraction.md) | 핵심 포인트 및 인사이트 추출 | 인용, 결정 사항, 액션 아이템, 키워드 추출 |
| [Chapter Generation](skills/内容提取/chapter-generation.md) | 영상 챕터 및 타임스탬프 생성 | 주제 분할, 유튜브/팟캐스트 형식, SEO 제목 |

### 노트 정리

| 스킬 | 설명 | 주요 기능 |
|-------|-------------|--------------|
| [Video Notes](skills/笔记整理/video-notes.md) | 영상을 통한 구조화된 노트 필기 | 코넬 방법, 마인드맵, 아웃라인 형식 |
| [Knowledge Extraction](skills/笔记整理/knowledge-extraction.md) | 지식 추출 및 정리 | 개념 맵, Obsidian 통합, 그래프 데이터베이스 |

### 자막 처리

| 스킬 | 설명 | 주요 기능 |
|-------|-------------|--------------|
| [Subtitle Generation](skills/字幕处理/subtitle-generation.md) | 자동 자막 생성 | SRT/ASS/VTT 형식, 스타일 커스터마이징, 타이밍 동기화 |
| [Subtitle Translation](skills/字幕处理/subtitle-translation.md) | 자막 번역 | 이중 언어 자막, 문화적 적응, 타이밍 조정 |

### 학습 보조

| 스킬 | 설명 | 주요 기능 |
|-------|-------------|--------------|
| [Lecture Notes](skills/学习辅助/lecture-notes.md) | 강의 및 코스 노트 필기 | Anki 플래시카드, 퀴즈 생성, 간격 반복 |
| [Tutorial Documentation](skills/学习辅助/tutorial-documentation.md) | 영상을 통한 튜토리얼 문서화 | 단계 추출, 코드 캡처, MkDocs 통합 |

---

## 빠른 시작

### Cursor

1. 이 저장소를 클론하거나 다운로드하세요
2. Cursor에서 프로젝트를 여세요
3. 스킬 파일 내용을 `.cursorrules`에 복사하거나 채팅에서 참조하세요
4. Cursor에게 영상 처리를 요청하세요

```
@whisper-transcription.md Transcribe my lecture video at ./videos/lecture.mp4
```

### Claude Code

1. 이 저장소를 프로젝트에 클론하세요
2. 프롬프트에서 스킬을 참조하세요

```bash
# In your project directory
claude "Using the whisper-transcription skill, transcribe ./video.mp4 and generate study notes"
```

### Kimi Code

1. 이 저장소를 스킬 소스로 추가하세요
2. `/skill` 또는 스킬 파일 포함으로 스킬을 참조하세요

```bash
kimi "Follow skills/转录技术/whisper-transcription.md to transcribe my video"
```

---

## 도구 비교

| 도구 | 속도 | 품질 | 비용 | 오프라인 | 적합한 용도 |
|------|-------|---------|------|---------|----------|
| **Whisper (로컬)** | 중간 | ★★★★☆ | 무료 | 예 | 프라이버시, 일괄 처리 |
| **Whisper API** | 빠름 | ★★★★☆ | $0.006/분 | 아니오 | 빠른 일회성 전사 |
| **faster-whisper** | 빠름 | ★★★★☆ | 무료 | 예 | 프로덕션 파이프라인 |
| **whisper.cpp** | 빠름 | ★★★★☆ | 무료 | 예 | Python 환경 불필요 |
| **Google Cloud STT** | 빠름 | ★★★★☆ | $0.006/분 | 아니오 | 실시간 스트리밍 |
| **Azure Speech** | 빠름 | ★★★★★ | $1/시간 | 아니오 | 기업용, 화자 식별 |
| **AssemblyAI** | 빠름 | ★★★★★ | $0.015/분 | 아니오 | 회의 전사 |

### 모델 선택 가이드

| Whisper 모델 | 크기 | 속도 | 품질 | VRAM | 권장 사항 |
|---------------|------|-------|---------|------|----------------|
| `tiny` | 39M | ~32x | ★★☆☆☆ | ~1GB | 빠른 초안 전용 |
| `base` | 74M | ~16x | ★★★☆☆ | ~1GB | 깨끗한 오디오, 짧은 클립 |
| `small` | 244M | ~6x | ★★★★☆ | ~2GB | 적절한 균형 |
| `medium` | 769M | ~2x | ★★★★☆ | ~5GB | **대부분의 용도** |
| `large-v3` | 1.5G | 1x | ★★★★★ | ~10GB | 최대 정확도 |
| `turbo` | 809M | ~8x | ★★★★☆ | ~6GB | **최고 속도/품질 비율** |

---

## 프로젝트 구조

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

## 관련 링크

- [awesome-video-skills](https://github.com/Serennity007/awesome-video-skills) - 영상 편집 및 처리를 위한 AI 스킬
- [awesome-video-prompts](https://github.com/Serennity007/awesome-video-prompts) - 영상 AI를 위한 큐레이션 프롬프트
- [awesome-skills](https://github.com/Serennity007/awesome-skills) - AI 스킬 일반 컬렉션
- [awesome-ai-rules](https://github.com/Serennity007/awesome-ai-rules) - AI 코딩 어시스턴트 규칙 및 설정

---

## 기여하기

기여를 환영합니다! 가이드라인은 [CONTRIBUTING.md](CONTRIBUTING.md)를 참조하세요.

- 새로운 스킬 추가
- 기존 스킬 개선
- 버그 및 오타 수정
- 콘텐츠 번역

---

## 라이선스

이 프로젝트는 MIT 라이선스에 따라 라이선스가 부여됩니다 - 자세한 내용은 [LICENSE](LICENSE) 파일을 참조하세요.

Copyright (c) 2024 liangzhengtao

---

<div align="center">

**영상-텍스트 커뮤니티를 위해 ❤️로 제작**

</div>
