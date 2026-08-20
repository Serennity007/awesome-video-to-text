[English](README.md) | [中文](README.zh.md) | [日本語](README.ja.md) | [Français](README.fr.md) | [Español](README.es.md) | [العربية](README.ar.md) | [한국어](README.ko.md) | [Português](README.pt.md) | [Русский](README.ru.md) | [Deutsch](README.de.md)

<div align="center">

<img src=".banner.svg" width="100%" alt="banner">

</div>


<div align="center">

# Awesome Video to Text

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/Skills-12-blue)](#habilidades)

**Convierte cualquier vídeo en texto estructurado.**

12 habilidades de IA para transcripción, resumen, notas y subtítulos.

</div>

---

## Descripción general

**Vídeo de conferencia de 2 horas → Notas de estudio organizadas con puntos clave, capítulos, tarjetas de memoria y subtítulos.**

```
Antes: 📹 lecture_recording.mp4 (2 horas, 1.2 GB)
Después: 📝 lecture_notes.md (notas Cornell estructuradas)
         📋 key_points.json (ideas con marca de tiempo)
         📑 chapters.txt (capítulos listos para YouTube)
         🃏 flashcards.apkg (repetición espaciada Anki)
         🎯 quiz.json (preguntas de autoevaluación)
         📄 subtitles.srt (chino + inglés)
```

Este repositorio es una colección seleccionada de **habilidades de IA** que extraen contenido de vídeos y lo organizan en texto estructurado y consultable. Usa estas habilidades en Cursor, Claude Code, Kimi Code o cualquier asistente de IA de programación.

---

## Habilidades

### Transcripción

| Habilidad | Descripción | Características clave |
|-----------|-------------|----------------------|
| [Transcripción Whisper](skills/转录技术/whisper-transcription.md) | Transcripción local de vídeo con OpenAI Whisper | Optimización GPU, procesamiento por lotes, 99 idiomas |
| [Transcripción YouTube](skills/转录技术/youtube-transcript.md) | Extraer transcripciones de vídeos de YouTube | Subtítulos generados automáticamente, lotes de playlist, multilingüe |
| [Transcripción de reuniones](skills/转录技术/meeting-transcription.md) | Transcripción de reuniones e entrevistas con identificación de hablante | Diarización de hablantes, elementos de acción, modo en tiempo real |

### Extracción de contenido

| Habilidad | Descripción | Características clave |
|-----------|-------------|----------------------|
| [Resumen de vídeo](skills/内容提取/video-summarization.md) | Resumen de contenido de vídeo | Extractivo + abstractivo, resúmenes por capítulo, múltiples tipos |
| [Extracción de puntos clave](skills/内容提取/key-points-extraction.md) | Extracción de puntos clave e ideas | Citas, decisiones, elementos de acción, extracción de palabras clave |
| [Generación de capítulos](skills/内容提取/chapter-generation.md) | Generación de capítulos y marcas de tiempo de vídeo | Segmentación temática, formato YouTube/podcast, títulos SEO |

### Organización de notas

| Habilidad | Descripción | Características clave |
|-----------|-------------|----------------------|
| [Notas de vídeo](skills/笔记整理/video-notes.md) | Toma de notas estructurada a partir de vídeos | Método Cornell, mapas mentales, formato esquema |
| [Extracción de conocimiento](skills/笔记整理/knowledge-extraction.md) | Extracción y organización de conocimiento | Mapas conceptuales, integración con Obsidian, base de datos de grafos |

### Procesamiento de subtítulos

| Habilidad | Descripción | Características clave |
|-----------|-------------|----------------------|
| [Generación de subtítulos](skills/字幕处理/subtitle-generation.md) | Generación automática de subtítulos | Formatos SRT/ASS/VTT, personalización de estilo, sincronización temporal |
| [Traducción de subtítulos](skills/字幕处理/subtitle-translation.md) | Traducción de subtítulos | Subtítulos bilingües, adaptación cultural, ajuste temporal |

### Apoyo al aprendizaje

| Habilidad | Descripción | Características clave |
|-----------|-------------|----------------------|
| [Notas de clase](skills/学习辅助/lecture-notes.md) | Toma de notas para clases y conferencias | Tarjetas Anki, generación de cuestionarios, repetición espaciada |
| [Documentación de tutoriales](skills/学习辅助/tutorial-documentation.md) | Documentación de tutoriales a partir de vídeos | Extracción de pasos, captura de código, integración MkDocs |

---

## Inicio rápido

### Cursor

1. Clona o descarga este repositorio
2. Abre tu proyecto en Cursor
3. Copia el contenido del archivo de habilidad en tu `.cursorrules` o haz referencia en el chat
4. Pide a Cursor que procese tu vídeo

```
@whisper-transcription.md Transcribe my lecture video at ./videos/lecture.mp4
```

### Claude Code

1. Clona este repositorio en tu proyecto
2. Haz referencia a las habilidades en tus prompts

```bash
# En el directorio de tu proyecto
claude "Using the whisper-transcription skill, transcribe ./video.mp4 and generate study notes"
```

### Kimi Code

1. Añade este repositorio como fuente de habilidades
2. Haz referencia a las habilidades con `/skill` o incluyendo el archivo de habilidad

```bash
kimi "Follow skills/转录技术/whisper-transcription.md to transcribe my video"
```

---

## Comparación de herramientas

| Herramienta | Velocidad | Calidad | Coste | Sin conexión | Ideal para |
|-------------|-----------|---------|-------|-------------|------------|
| **Whisper (local)** | Media | ★★★★☆ | Gratis | Sí | Privacidad, procesamiento por lotes |
| **Whisper API** | Rápida | ★★★★☆ | $0.006/min | No | Transcripciones puntuales rápidas |
| **faster-whisper** | Rápida | ★★★★☆ | Gratis | Sí | Pipelines de producción |
| **whisper.cpp** | Rápida | ★★★★☆ | Gratis | Sí | Sin entorno Python |
| **Google Cloud STT** | Rápida | ★★★★☆ | $0.006/min | No | Streaming en tiempo real |
| **Azure Speech** | Rápida | ★★★★★ | $1/hora | No | Empresarial, identificación de hablante |
| **AssemblyAI** | Rápida | ★★★★★ | $0.015/min | No | Transcripción de reuniones |

### Guía de selección de modelo

| Modelo Whisper | Tamaño | Velocidad | Calidad | VRAM | Recomendación |
|----------------|--------|-----------|---------|------|----------------|
| `tiny` | 39M | ~32x | ★★☆☆☆ | ~1 GB | Solo borradores rápidos |
| `base` | 74M | ~16x | ★★★☆☆ | ~1 GB | Audio limpio, clips cortos |
| `small` | 244M | ~6x | ★★★★☆ | ~2 GB | Buen equilibrio |
| `medium` | 769M | ~2x | ★★★★☆ | ~5 GB | **La mayoría de casos** |
| `large-v3` | 1.5G | 1x | ★★★★★ | ~10 GB | Máxima precisión |
| `turbo` | 809M | ~8x | ★★★★☆ | ~6 GB | **Mejor relación velocidad/calidad** |

---

## Estructura del proyecto

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
    ├── 转录技术/                    # Transcripción
    │   ├── whisper-transcription.md
    │   ├── youtube-transcript.md
    │   └── meeting-transcription.md
    ├── 内容提取/                    # Extracción de contenido
    │   ├── video-summarization.md
    │   ├── key-points-extraction.md
    │   └── chapter-generation.md
    ├── 笔记整理/                    # Organización de notas
    │   ├── video-notes.md
    │   └── knowledge-extraction.md
    ├── 字幕处理/                    # Procesamiento de subtítulos
    │   ├── subtitle-generation.md
    │   └── subtitle-translation.md
    └── 学习辅助/                    # Apoyo al aprendizaje
        ├── lecture-notes.md
        └── tutorial-documentation.md
```

---

## Ver también

- [awesome-video-skills](https://github.com/nicekate/awesome-video-skills) - Habilidades de IA para edición y procesamiento de vídeo
- [awesome-video-prompts](https://github.com/nicekate/awesome-video-prompts) - Prompts seleccionados para vídeo con IA
- [awesome-skills](https://github.com/nicekate/awesome-skills) - Colección general de habilidades de IA
- [awesome-ai-rules](https://github.com/nicekate/awesome-ai-rules) - Reglas y configuraciones para asistentes de IA de programación

---

## Contribuir

¡Aceptamos contribuciones! Consulta [CONTRIBUTING.md](CONTRIBUTING.md) para las directrices.

- Añadir nuevas habilidades
- Mejorar habilidades existentes
- Corregir errores y erratas
- Traducir contenido

---

## Licencia

Este proyecto está bajo la licencia MIT — consulta el archivo [LICENSE](LICENSE) para más detalles.

Copyright (c) 2024 liangzhengtao

---

<div align="center">

**Hecho con ❤️ para la comunidad de vídeo-a-texto**

</div>
