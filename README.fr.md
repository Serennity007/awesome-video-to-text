[English](README.md) | [中文](README.zh.md) | [日本語](README.ja.md) | [Français](README.fr.md) | [Español](README.es.md)

<div align="center">

<img src=".banner.svg" width="100%" alt="banner">

</div>


<div align="center">

# Awesome Video to Text

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/Skills-12-blue)](#compétences)

**Transformez n'importe quelle vidéo en texte structuré.**

12 compétences IA pour la transcription, le résumé, les notes et les sous-titres.

</div>

---

## Aperçu

**Vidéo de cours de 2 heures → Notes d'étude organisées avec points clés, chapitres, flashcards et sous-titres.**

```
Avant : 📹 lecture_recording.mp4 (2 heures, 1.2 Go)
Après : 📝 lecture_notes.md (notes Cornell structurées)
        📋 key_points.json (insights horodatés)
        📑 chapters.txt (chapitres prêts pour YouTube)
        🃏 flashcards.apkg (répétition espacée Anki)
        🎯 quiz.json (questions d'auto-évaluation)
        📄 subtitles.srt (chinois + anglais)
```

Ce dépôt est une collection sélectionnée de **compétences IA** qui extraient le contenu des vidéos et l'organisent en texte structuré et consultable. Utilisez ces compétences dans Cursor, Claude Code, Kimi Code, ou tout assistant IA de programmation.

---

## Compétences

### Transcription

| Compétence | Description | Caractéristiques clés |
|------------|-------------|----------------------|
| [Transcription Whisper](skills/转录技术/whisper-transcription.md) | Transcription vidéo locale avec OpenAI Whisper | Optimisation GPU, traitement par lot, 99 langues |
| [Transcription YouTube](skills/转录技术/youtube-transcript.md) | Extraire les transcriptions des vidéos YouTube | Sous-titres auto-générés, traitement de playlist, multilingue |
| [Transcription de réunion](skills/转录技术/meeting-transcription.md) | Transcription de réunions et entretiens avec identification du locuteur | Diarisation, éléments d'action, mode temps réel |

### Extraction de contenu

| Compétence | Description | Caractéristiques clés |
|------------|-------------|----------------------|
| [Résumé vidéo](skills/内容提取/video-summarization.md) | Résumé de contenu vidéo | Extractif + abstrait, résumés par chapitre, multi-type |
| [Extraction de points clés](skills/内容提取/key-points-extraction.md) | Extraction de points clés et d'insights | Citations, décisions, éléments d'action, extraction de mots-clés |
| [Génération de chapitres](skills/内容提取/chapter-generation.md) | Génération de chapitres et horodatages vidéo | Segmentation thématique, format YouTube/podcast, titres SEO |

### Organisation des notes

| Compétence | Description | Caractéristiques clés |
|------------|-------------|----------------------|
| [Notes vidéo](skills/笔记整理/video-notes.md) | Prise de notes structurée à partir de vidéos | Méthode Cornell, cartes mentales, format plan |
| [Extraction de connaissances](skills/笔记整理/knowledge-extraction.md) | Extraction et organisation des connaissances | Cartes conceptuelles, intégration Obsidian, base de données graphique |

### Traitement des sous-titres

| Compétence | Description | Caractéristiques clés |
|------------|-------------|----------------------|
| [Génération de sous-titres](skills/字幕处理/subtitle-generation.md) | Génération automatique de sous-titres | Formats SRT/ASS/VTT, personnalisation du style, synchronisation temporelle |
| [Traduction de sous-titres](skills/字幕处理/subtitle-translation.md) | Traduction de sous-titres | Sous-titres bilingues, adaptation culturelle, ajustement temporel |

### Aide à l'apprentissage

| Compétence | Description | Caractéristiques clés |
|------------|-------------|----------------------|
| [Notes de cours](skills/学习辅助/lecture-notes.md) | Prise de notes pour cours et conférences | Flashcards Anki, génération de quiz, répétition espacée |
| [Documentation de tutoriels](skills/学习辅助/tutorial-documentation.md) | Documentation de tutoriels à partir de vidéos | Extraction d'étapes, capture de code, intégration MkDocs |

---

## Démarrage rapide

### Cursor

1. Clonez ou téléchargez ce dépôt
2. Ouvrez votre projet dans Cursor
3. Copiez le contenu du fichier de compétence dans votre `.cursorrules` ou référencez-le dans le chat
4. Demandez à Cursor de traiter votre vidéo

```
@whisper-transcription.md Transcribe my lecture video at ./videos/lecture.mp4
```

### Claude Code

1. Clonez ce dépôt dans votre projet
2. Référencez les compétences dans vos prompts

```bash
# Dans le répertoire de votre projet
claude "Using the whisper-transcription skill, transcribe ./video.mp4 and generate study notes"
```

### Kimi Code

1. Ajoutez ce dépôt comme source de compétences
2. Référencez les compétences avec `/skill` ou en incluant le fichier de compétence

```bash
kimi "Follow skills/转录技术/whisper-transcription.md to transcribe my video"
```

---

## Comparaison des outils

| Outil | Vitesse | Qualité | Coût | Hors ligne | Idéal pour |
|-------|---------|---------|------|-----------|------------|
| **Whisper (local)** | Moyen | ★★★★☆ | Gratuit | Oui | Confidentialité, traitement par lot |
| **Whisper API** | Rapide | ★★★★☆ | 0,006 $/min | Non | Transcriptions ponctuelles rapides |
| **faster-whisper** | Rapide | ★★★★☆ | Gratuit | Oui | Pipelines de production |
| **whisper.cpp** | Rapide | ★★★★☆ | Gratuit | Oui | Sans environnement Python |
| **Google Cloud STT** | Rapide | ★★★★☆ | 0,006 $/min | Non | Streaming en temps réel |
| **Azure Speech** | Rapide | ★★★★★ | 1 $/heure | Non | Entreprise, identification du locuteur |
| **AssemblyAI** | Rapide | ★★★★★ | 0,015 $/min | Non | Transcription de réunions |

### Guide de sélection du modèle

| Modèle Whisper | Taille | Vitesse | Qualité | VRAM | Recommandation |
|----------------|--------|---------|---------|------|----------------|
| `tiny` | 39M | ~32x | ★★☆☆☆ | ~1 Go | Brouillons rapides uniquement |
| `base` | 74M | ~16x | ★★★☆☆ | ~1 Go | Audio propre, courts extraits |
| `small` | 244M | ~6x | ★★★★☆ | ~2 Go | Bon équilibre |
| `medium` | 769M | ~2x | ★★★★☆ | ~5 Go | **La plupart des cas** |
| `large-v3` | 1,5G | 1x | ★★★★★ | ~10 Go | Précision maximale |
| `turbo` | 809M | ~8x | ★★★★☆ | ~6 Go | **Meilleur rapport vitesse/qualité** |

---

## Structure du projet

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
    ├── 内容提取/                    # Extraction de contenu
    │   ├── video-summarization.md
    │   ├── key-points-extraction.md
    │   └── chapter-generation.md
    ├── 笔记整理/                    # Organisation des notes
    │   ├── video-notes.md
    │   └── knowledge-extraction.md
    ├── 字幕处理/                    # Traitement des sous-titres
    │   ├── subtitle-generation.md
    │   └── subtitle-translation.md
    └── 学习辅助/                    # Aide à l'apprentissage
        ├── lecture-notes.md
        └── tutorial-documentation.md
```

---

## Voir aussi

- [awesome-video-skills](https://github.com/nicekate/awesome-video-skills) - Compétences IA pour le montage et le traitement vidéo
- [awesome-video-prompts](https://github.com/nicekate/awesome-video-prompts) - Prompts sélectionnés pour la vidéo IA
- [awesome-skills](https://github.com/nicekate/awesome-skills) - Collection générale de compétences IA
- [awesome-ai-rules](https://github.com/nicekate/awesome-ai-rules) - Règles et configurations pour assistants IA de programmation

---

## Contribuer

Nous accueillons les contributions ! Consultez [CONTRIBUTING.md](CONTRIBUTING.md) pour les directives.

- Ajouter de nouvelles compétences
- Améliorer les compétences existantes
- Corriger les bugs et fautes de frappe
- Traduire le contenu

---

## Licence

Ce projet est sous licence MIT — voir le fichier [LICENSE](LICENSE) pour plus de détails.

Copyright (c) 2024 liangzhengtao

---

<div align="center">

**Fait avec ❤️ pour la communauté vidéo-vers-texte**

</div>
