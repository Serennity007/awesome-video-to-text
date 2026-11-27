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

**Transforme qualquer vídeo em texto estruturado.**

12 habilidades de IA para transcrição, resumo, notas e legendas.

</div>

---

## Visão Geral

**Vídeo de aula de 2 horas → notas de estudo organizadas com pontos-chave, capítulos, flashcards e legendas.**

```
Antes: 📹 lecture_recording.mp4 (2 horas, 1.2GB)
Depois:  📝 lecture_notes.md (notas Cornell estruturadas)
         📋 key_points.json (insights com carimbo de tempo)
         👑 chapters.txt (capítulos prontos para YouTube)
         🃏 flashcards.apkg (repetição espaçada Anki)
         🎯 quiz.json (questões de autoavaliação)
         📄 subtitles.srt (chinês + inglês)
```

Este repositório é uma coleção curada de **habilidades de IA** que extraem conteúdo de vídeos e o organizam em texto estruturado e pesquisável. Use estas habilidades no Cursor, Claude Code, Kimi Code ou qualquer assistente de código com IA.

---

## Habilidades

### Transcrição

| Habilidade | Descrição | Recursos Principais |
|-------|-------------|--------------|
| [Whisper Transcription](skills/转录技术/whisper-transcription.md) | Transcrição local de vídeos com OpenAI Whisper | Otimização de GPU, processamento em lote, 99 idiomas |
| [YouTube Transcript](skills/转录技术/youtube-transcript.md) | Extraia transcrições de vídeos do YouTube | Legendas automáticas, lote de playlists, multilíngue |
| [Meeting Transcription](skills/转录技术/meeting-transcription.md) | Transcrição de reuniões e entrevistas com identificação de falante | Diarização de falantes, itens de ação, modo em tempo real |

### Extração de Conteúdo

| Habilidade | Descrição | Recursos Principais |
|-------|-------------|--------------|
| [Video Summarization](skills/内容提取/video-summarization.md) | Resumo do conteúdo do vídeo | Extrativo + abstrativo, resumos por capítulo, múltiplos tipos |
| [Key Points Extraction](skills/内容提取/key-points-extraction.md) | Extração de pontos-chave e insights | Citações, decisões, itens de ação, extração de palavras-chave |
| [Chapter Generation](skills/内容提取/chapter-generation.md) | Geração de capítulos e timestamps do vídeo | Segmentação por tópico, formato YouTube/podcast, títulos SEO |

### Organização de Notas

| Habilidade | Descrição | Recursos Principais |
|-------|-------------|--------------|
| [Video Notes](skills/笔记整理/video-notes.md) | Anotações estruturadas a partir de vídeos | Método Cornell, mapas mentais, formato de esboço |
| [Knowledge Extraction](skills/笔记整理/knowledge-extraction.md) | Extração e organização de conhecimento | Mapas conceituais, integração Obsidian, banco de dados de grafos |

### Processamento de Legendas

| Habilidade | Descrição | Recursos Principais |
|-------|-------------|--------------|
| [Subtitle Generation](skills/字幕处理/subtitle-generation.md) | Geração automática de legendas | Formatos SRT/ASS/VTT, personalização de estilo, sincronização de tempo |
| [Subtitle Translation](skills/字幕处理/subtitle-translation.md) | Tradução de legendas | Legendas bilíngues, adaptação cultural, ajuste de tempo |

### Auxílio de Aprendizagem

| Habilidade | Descrição | Recursos Principais |
|-------|-------------|--------------|
| [Lecture Notes](skills/学习辅助/lecture-notes.md) | Anotações de aulas e cursos | Flashcards Anki, geração de quizzes, repetição espaçada |
| [Tutorial Documentation](skills/学习辅助/tutorial-documentation.md) | Documentação de tutoriais a partir de vídeos | Extração de etapas, captura de código, integração MkDocs |

---

## Início Rápido

### Cursor

1. Clone ou baixe este repositório
2. Abra seu projeto no Cursor
3. Copie o conteúdo do arquivo de habilidade para seu `.cursorrules` ou referencie-o no chat
4. Peça ao Cursor para processar seu vídeo

```
@whisper-transcription.md Transcribe my lecture video at ./videos/lecture.mp4
```

### Claude Code

1. Clone este repositório no seu projeto
2. Referencie as habilidades nos seus prompts

```bash
# In your project directory
claude "Using the whisper-transcription skill, transcribe ./video.mp4 and generate study notes"
```

### Kimi Code

1. Adicione este repositório como fonte de habilidades
2. Referencie habilidades usando `/skill` ou incluindo o arquivo de habilidade

```bash
kimi "Follow skills/转录技术/whisper-transcription.md to transcribe my video"
```

---

## Comparação de Ferramentas

| Ferramenta | Velocidade | Qualidade | Custo | Offline | Melhor Para |
|------|-------|---------|------|---------|----------|
| **Whisper (local)** | Média | ★★★★☆ | Grátis | Sim | Privacidade, processamento em lote |
| **Whisper API** | Rápida | ★★★★☆ | $0.006/min | Não | Transcrições rápidas pontuais |
| **faster-whisper** | Rápida | ★★★★☆ | Grátis | Sim | Pipelines de produção |
| **whisper.cpp** | Rápida | ★★★★☆ | Grátis | Sim | Sem ambiente Python |
| **Google Cloud STT** | Rápida | ★★★★☆ | $0.006/min | Não | Streaming em tempo real |
| **Azure Speech** | Rápida | ★★★★★ | $1/hora | Não | Empresarial, identificação de falante |
| **AssemblyAI** | Rápida | ★★★★★ | $0.015/min | Não | Transcrição de reuniões |

### Guia de Seleção de Modelo

| Modelo Whisper | Tamanho | Velocidade | Qualidade | VRAM | Recomendação |
|---------------|------|-------|---------|------|----------------|
| `tiny` | 39M | ~32x | ★★☆☆☆ | ~1GB | Rascunhos rápidos apenas |
| `base` | 74M | ~16x | ★★★☆☆ | ~1GB | Áudio limpo, clipes curtos |
| `small` | 244M | ~6x | ★★★★☆ | ~2GB | Bom equilíbrio |
| `medium` | 769M | ~2x | ★★★★☆ | ~5GB | **Maioria dos casos** |
| `large-v3` | 1.5G | 1x | ★★★★★ | ~10GB | Precisão máxima |
| `turbo` | 809M | ~8x | ★★★★☆ | ~6GB | **Melhor velocidade/qualidade** |

---

## Estrutura do Projeto

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

## Veja Também

- [awesome-video-skills](https://github.com/Serennity007/awesome-video-skills) - Habilidades de IA para edição e processamento de vídeo
- [awesome-video-prompts](https://github.com/Serennity007/awesome-video-prompts) - Prompts curados para IA de vídeo
- [awesome-skills](https://github.com/Serennity007/awesome-skills) - Coleção geral de habilidades de IA
- [awesome-ai-rules](https://github.com/Serennity007/awesome-ai-rules) - Regras e configurações de assistentes de código com IA

---

## Contribuindo

Recebemos contribuições! Consulte [CONTRIBUTING.md](CONTRIBUTING.md) para diretrizes.

- Adicionar novas habilidades
- Melhorar habilidades existentes
- Corrigir erros e digitações
- Traduzir conteúdo

---

## Licença

Este projeto está licenciado sob a Licença MIT - consulte o arquivo [LICENSE](LICENSE) para detalhes.

Copyright (c) 2024 liangzhengtao

---

<div align="center">

**Feito com ❤️ para a comunidade vídeo-texto**

</div>
