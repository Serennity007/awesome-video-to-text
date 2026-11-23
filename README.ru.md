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

**Преобразуйте любое видео в структурированный текст.**

12 ИИ-навыков для транскрипции, резюмирования, заметок и субтитров.

</div>

---

## Обзор

**2-часовое видео лекции → организованные учебные заметки с ключевыми моментами, главами, карточками и субтитрами.**

```
До: 📹 lecture_recording.mp4 (2 часа, 1.2 ГБ)
После:  📝 lecture_notes.md (структурированные заметки Корнелла)
        📋 key_points.json (ключевые идеи с таймкодами)
        👑 chapters.txt (главы для YouTube)
        🃏 flashcards.apkg (карточки Anki с интервальным повторением)
        🎯 quiz.json (вопросы для самопроверки)
        📄 subtitles.srt (китайский + английский)
```

Этот репозиторий — кураторская коллекция **ИИ-навыков**, которые извлекают контент из видео и организуют его в структурированный, легко ищемый текст. Используйте эти навыки в Cursor, Claude Code, Kimi Code или любом ИИ-ассистенте для программирования.

---

## Навыки

### Транскрипция

| Навык | Описание | Ключевые возможности |
|-------|-------------|--------------|
| [Whisper Transcription](skills/转录技术/whisper-transcription.md) | Локальная транскрипция видео с OpenAI Whisper | Оптимизация GPU, пакетная обработка, 99 языков |
| [YouTube Transcript](skills/转录技术/youtube-transcript.md) | Извлечение транскрипций из видео YouTube | Автоматические субтитры, пакетная обработка плейлистов, мультиязычность |
| [Meeting Transcription](skills/转录技术/meeting-transcription.md) | Транскрипция встреч и интервью с идентификацией говорящего | Диаризация говорящих, элементы действий, режим реального времени |

### Извлечение контента

| Навык | Описание | Ключевые возможности |
|-------|-------------|--------------|
| [Video Summarization](skills/内容提取/video-summarization.md) | Резюмирование видеоконтента | Экстрактивный + абстрактивный, резюме по главам, множественные типы |
| [Key Points Extraction](skills/内容提取/key-points-extraction.md) | Извлечение ключевых моментов и идей | Цитаты, решения, элементы действий, извлечение ключевых слов |
| [Chapter Generation](skills/内容提取/chapter-generation.md) | Генерация глав и таймкодов видео | Сегментация по темам, формат YouTube/подкаст, SEO-заголовки |

### Организация заметок

| Навык | Описание | Ключевые возможности |
|-------|-------------|--------------|
| [Video Notes](skills/笔记整理/video-notes.md) | Структурированные заметки из видео | Метод Корнелла, интеллект-карты, формат плана |
| [Knowledge Extraction](skills/笔记整理/knowledge-extraction.md) | Извлечение и организация знаний | Концептуальные карты, интеграция Obsidian, графовая база данных |

### Обработка субтитров

| Навык | Описание | Ключевые возможности |
|-------|-------------|--------------|
| [Subtitle Generation](skills/字幕处理/subtitle-generation.md) | Автоматическая генерация субтитров | Форматы SRT/ASS/VTT, настройка стиля, синхронизация тайминга |
| [Subtitle Translation](skills/字幕处理/subtitle-translation.md) | Перевод субтитров | Двуязычные субтитры, культурная адаптация, корректировка тайминга |

### Учебные пособия

| Навык | Описание | Ключевые возможности |
|-------|-------------|--------------|
| [Lecture Notes](skills/学习辅助/lecture-notes.md) | Заметки по лекциям и курсам | Карточки Anki, генерация викторин, интервальное повторение |
| [Tutorial Documentation](skills/学习辅助/tutorial-documentation.md) | Документация туториалов из видео | Извлечение шагов, захват кода, интеграция MkDocs |

---

## Быстрый старт

### Cursor

1. Клонируйте или скачайте этот репозиторий
2. Откройте проект в Cursor
3. Скопируйте содержимое файла навыка в `.cursorrules` или упомяните его в чате
4. Попросите Cursor обработать ваше видео

```
@whisper-transcription.md Transcribe my lecture video at ./videos/lecture.mp4
```

### Claude Code

1. Клонируйте репозиторий в свой проект
2. Упоминайте навыки в промптах

```bash
# In your project directory
claude "Using the whisper-transcription skill, transcribe ./video.mp4 and generate study notes"
```

### Kimi Code

1. Добавьте этот репозиторий как источник навыков
2. Упоминайте навыки через `/skill` или включая файл навыка

```bash
kimi "Follow skills/转录技术/whisper-transcription.md to transcribe my video"
```

---

## Сравнение инструментов

| Инструмент | Скорость | Качество | Стоимость | Офлайн | Лучше всего для |
|------|-------|---------|------|---------|----------|
| **Whisper (локальный)** | Средняя | ★★★★☆ | Бесплатно | Да | Приватность, пакетная обработка |
| **Whisper API** | Быстрая | ★★★★☆ | $0.006/мин | Нет | Быстрая разовая транскрипция |
| **faster-whisper** | Быстрая | ★★★★☆ | Бесплатно | Да | Продакшн-пайплайны |
| **whisper.cpp** | Быстрая | ★★★★☆ | Бесплатно | Да | Без Python-окружения |
| **Google Cloud STT** | Быстрая | ★★★★☆ | $0.006/мин | Нет | Потоковая передача в реальном времени |
| **Azure Speech** | Быстрая | ★★★★★ | $1/час | Нет | Корпоративный, идентификация говорящего |
| **AssemblyAI** | Быстрая | ★★★★★ | $0.015/мин | Нет | Транскрипция встреч |

### Руководство по выбору модели

| Модель Whisper | Размер | Скорость | Качество | VRAM | Рекомендация |
|---------------|------|-------|---------|------|----------------|
| `tiny` | 39M | ~32x | ★★☆☆☆ | ~1GB | Только быстрые черновики |
| `base` | 74M | ~16x | ★★★☆☆ | ~1GB | Чистый аудио, короткие клипы |
| `small` | 244M | ~6x | ★★★★☆ | ~2GB | Хороший баланс |
| `medium` | 769M | ~2x | ★★★★☆ | ~5GB | **Большинство случаев** |
| `large-v3` | 1.5G | 1x | ★★★★★ | ~10GB | Максимальная точность |
| `turbo` | 809M | ~8x | ★★★★☆ | ~6GB | **Лучшее соотношение скорость/качество** |

---

## Структура проекта

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

## Смотрите также

- [awesome-video-skills](https://github.com/Serennity007/awesome-video-skills) - ИИ-навыки для редактирования и обработки видео
- [awesome-video-prompts](https://github.com/Serennity007/awesome-video-prompts) - Кураторские промпты для видео-ИИ
- [awesome-skills](https://github.com/Serennity007/awesome-skills) - Общая коллекция ИИ-навыков
- [awesome-ai-rules](https://github.com/Serennity007/awesome-ai-rules) - Правила и конфигурации ИИ-ассистентов для программирования

---

## Участие в проекте

Мы приветствуем вклад! Смотрите [CONTRIBUTING.md](CONTRIBUTING.md) для руководства.

- Добавляйте новые навыки
- Улучшайте существующие навыки
- Исправляйте ошибки и опечатки
- Переводите контент

---

## Лицензия

Этот проект лицензирован под лицензией MIT — подробности в файле [LICENSE](LICENSE).

Copyright (c) 2024 liangzhengtao

---

<div align="center">

**Сделано с ❤️ для сообщества видео-в-текст**

</div>
