[English](README.md) | [中文](README.zh.md) | [日本語](README.ja.md) | [Français](README.fr.md) | [Español](README.es.md) | [العربية](README.ar.md) | [한국어](README.ko.md) | [Português](README.pt.md) | [Русский](README.ru.md) | [Deutsch](README.de.md)

<div align="center">

<img src=".banner.svg" width="100%" alt="banner">

</div>


<div align="center">

# Awesome Video to Text

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/Skills-12-blue)](#skills)

**Verwandeln Sie jedes Video in strukturierten Text.**

12 KI-Skills für Transkription, Zusammenfassung, Notizen und Untertitel.

</div>

---

## Überblick

**2-Stunden-Vorlesungsvideo → organisierte Lernnotizen mit Kernpunkten, Kapiteln, Lernkarten und Untertiteln.**

```
Vorher: 📹 lecture_recording.mp4 (2 Stunden, 1.2 GB)
Nachher:  📝 lecture_notes.md (strukturierte Cornell-Notizen)
          📋 key_points.json (zeitgestempelte Erkenntnisse)
          👑 chapters.txt (YouTube-fertige Kapitel)
          🃏 flashcards.apkg (Anki-Lernkarten mit Abstandswiederholung)
          🎯 quiz.json (Selbsttest-Fragen)
          📄 subtitles.srt (Chinesisch + Englisch)
```

Dieses Repository ist eine kuratierte Sammlung von **KI-Skills**, die Inhalte aus Videos extrahieren und in strukturierten, durchsuchbaren Text organisieren. Verwenden Sie diese Skills in Cursor, Claude Code, Kimi Code oder jedem KI-Coding-Assistenten.

---

## Skills

### Transkription

| Skill | Beschreibung | Hauptmerkmale |
|-------|-------------|--------------|
| [Whisper Transcription](skills/转录技术/whisper-transcription.md) | Lokale Videotranskription mit OpenAI Whisper | GPU-Optimierung, Stapelverarbeitung, 99 Sprachen |
| [YouTube Transcript](skills/转录技术/youtube-transcript.md) | Transkriptionen aus YouTube-Videos extrahieren | Automatisch generierte Untertitel, Playlist-Stapelverarbeitung, mehrsprachig |
| [Meeting Transcription](skills/转录技术/meeting-transcription.md) | Meeting- und Interview-Transkription mit Sprecheridentifikation | Sprecherdiarisierung, Aktionspunkte, Echtzeitmodus |

### Inhaltsextraktion

| Skill | Beschreibung | Hauptmerkmale |
|-------|-------------|--------------|
| [Video Summarization](skills/内容提取/video-summarization.md) | Videointhaltszusammenfassung | Extraktiv + abstraktiv, Kapitelzusammenfassungen, mehrere Typen |
| [Key Points Extraction](skills/内容提取/key-points-extraction.md) | Kernpunkte und Erkenntnisse extrahieren | Zitate, Entscheidungen, Aktionspunkte, Keyword-Extraktion |
| [Chapter Generation](skills/内容提取/chapter-generation.md) | Video-Kapitel und Zeitstempel generieren | Themen-Segmentierung, YouTube/Podcast-Format, SEO-Titel |

### Notizenorganisation

| Skill | Beschreibung | Hauptmerkmale |
|-------|-------------|--------------|
| [Video Notes](skills/笔记整理/video-notes.md) | Strukturierte Notizen aus Videos | Cornell-Methode, Mindmaps, Gliederungsformat |
| [Knowledge Extraction](skills/笔记整理/knowledge-extraction.md) | Wissensextraktion und -organisation | Konzeptkarten, Obsidian-Integration, Graphdatenbank |

### Untertitelverarbeitung

| Skill | Beschreibung | Hauptmerkmale |
|-------|-------------|--------------|
| [Subtitle Generation](skills/字幕处理/subtitle-generation.md) | Automatische Untertitelgenerierung | SRT/ASS/VTT-Formate, Stilanpassung, Zeitsynchronisation |
| [Subtitle Translation](skills/字幕处理/subtitle-translation.md) | Untertitelübersetzung | Zweisprachige Untertitel, kulturelle Anpassung, Timing-Anpassung |

### Lernhilfe

| Skill | Beschreibung | Hauptmerkmale |
|-------|-------------|--------------|
| [Lecture Notes](skills/学习辅助/lecture-notes.md) | Vorlesungs- und Kursnotizen | Anki-Lernkarten, Quizgenerierung, Abstandswiederholung |
| [Tutorial Documentation](skills/学习辅助/tutorial-documentation.md) | Tutorial-Dokumentation aus Videos | Schritte extrahieren, Code erfassen, MkDocs-Integration |

---

## Schnellstart

### Cursor

1. Klonen oder laden Sie dieses Repository herunter
2. Öffnen Sie Ihr Projekt in Cursor
3. Kopieren Sie den Inhalt der Skill-Datei in Ihre `.cursorrules` oder referenzieren Sie ihn im Chat
4. Bitten Sie Cursor, Ihr Video zu verarbeiten

```
@whisper-transcription.md Transcribe my lecture video at ./videos/lecture.mp4
```

### Claude Code

1. Klonen Sie dieses Repository in Ihr Projekt
2. Referenzieren Sie Skills in Ihren Prompts

```bash
# In your project directory
claude "Using the whisper-transcription skill, transcribe ./video.mp4 and generate study notes"
```

### Kimi Code

1. Fügen Sie dieses Repository als Skill-Quelle hinzu
2. Referenzieren Sie Skills über `/skill` oder durch Einbinden der Skill-Datei

```bash
kimi "Follow skills/转录技术/whisper-transcription.md to transcribe my video"
```

---

## Werkzeugvergleich

| Werkzeug | Geschwindigkeit | Qualität | Kosten | Offline | Am besten für |
|------|-------|---------|------|---------|----------|
| **Whisper (lokal)** | Mittel | ★★★★☆ | Kostenlos | Ja | Privatsphäre, Stapelverarbeitung |
| **Whisper API** | Schnell | ★★★★☆ | $0.006/Min | Nein | Schnelle Einzeltranskriptionen |
| **faster-whisper** | Schnell | ★★★★☆ | Kostenlos | Ja | Produktions-Pipelines |
| **whisper.cpp** | Schnell | ★★★★☆ | Kostenlos | Ja | Ohne Python-Umgebung |
| **Google Cloud STT** | Schnell | ★★★★☆ | $0.006/Min | Nein | Echtzeit-Streaming |
| **Azure Speech** | Schnell | ★★★★★ | $1/Stunde | Nein | Unternehmen, Sprecheridentifikation |
| **AssemblyAI** | Schnell | ★★★★★ | $0.015/Min | Nein | Meeting-Transkription |

### Modellauswahl-Leitfaden

| Whisper-Modell | Größe | Geschwindigkeit | Qualität | VRAM | Empfehlung |
|---------------|------|-------|---------|------|----------------|
| `tiny` | 39M | ~32x | ★★☆☆☆ | ~1GB | Nur schnelle Entwürfe |
| `base` | 74M | ~16x | ★★★☆☆ | ~1GB | Saubere Audio, kurze Clips |
| `small` | 244M | ~6x | ★★★★☆ | ~2GB | Gutes Gleichgewicht |
| `medium` | 769M | ~2x | ★★★★☆ | ~5GB | **Die meisten Anwendungsfälle** |
| `large-v3` | 1.5G | 1x | ★★★★★ | ~10GB | Maximale Genauigkeit |
| `turbo` | 809M | ~8x | ★★★★☆ | ~6GB | **Bestes Geschwindigkeits-/Qualitätsverhältnis** |

---

## Projektstruktur

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

## Siehe auch

- [awesome-video-skills](https://github.com/nicekate/awesome-video-skills) - KI-Skills für Videobearbeitung und -verarbeitung
- [awesome-video-prompts](https://github.com/nicekate/awesome-video-prompts) - Kuratierte Prompts für Video-KI
- [awesome-skills](https://github.com/nicekate/awesome-skills) - Allgemeine Sammlung von KI-Skills
- [awesome-ai-rules](https://github.com/nicekate/awesome-ai-rules) - KI-Coding-Assistent-Regeln und -Konfigurationen

---

## Mitwirken

Wir freuen uns über Beiträge! Siehe [CONTRIBUTING.md](CONTRIBUTING.md) für Richtlinien.

- Neue Skills hinzufügen
- Bestehende Skills verbessern
- Fehler und Tippfehler korrigieren
- Inhalte übersetzen

---

## Lizenz

Dieses Projekt ist unter der MIT-Lizenz lizenziert — Details in der Datei [LICENSE](LICENSE).

Copyright (c) 2024 liangzhengtao

---

<div align="center">

**Mit ❤️ für die Video-zu-Text-Community gemacht**

</div>
