[English](README.md) | [中文](README.zh.md) | [日本語](README.ja.md) | [Français](README.fr.md) | [Español](README.es.md)

<div align="center">

<img src=".banner.svg" width="100%" alt="banner">

</div>


<div align="center">

# Awesome Video to Text

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/Skills-12-blue)](#スキル一覧)

**あらゆる動画を構造化テキストに変換。**

転写、要約、ノート、字幕のための 12 の AI スキル。

</div>

---

## 概要

**2 時間の講義動画 → キーポイント、章、フラッシュカード、字幕付きの整理済み学習ノート。**

```
処理前: 📹 lecture_recording.mp4 (2時間, 1.2GB)
処理後: 📝 lecture_notes.md (構造化コーネル式ノート)
        📋 key_points.json (タイムスタンプ付き洞察)
        📑 chapters.txt (YouTube チャプター形式)
        🃏 flashcards.apkg (Anki 間隔反復カード)
        🎯 quiz.json (自己評価クイズ)
        📄 subtitles.srt (中英二重字幕)
```

本リポジトリは、動画からコンテンツを抽出し、構造化された検索可能なテキストに整理する **AI スキル** のキュレーションコレクションです。Cursor、Claude Code、Kimi Code、または任意の AI コーディングアシスタントで使用できます。

---

## スキル一覧

### 転写技術

| スキル | 説明 | 主な特徴 |
|--------|------|----------|
| [Whisper 転写](skills/转录技术/whisper-transcription.md) | OpenAI Whisper によるローカル動画転写 | GPU 最適化、バッチ処理、99 言語対応 |
| [YouTube 転写](skills/转录技术/youtube-transcript.md) | YouTube 動画からトランスクリプトを抽出 | 自動生成キャプション、プレイリスト一括処理、多言語対応 |
| [会議転写](skills/转录技术/meeting-transcription.md) | 会議・インタビューの転写（話者識別付き） | 話者分離、アクションアイテム抽出、リアルタイムモード |

### コンテンツ抽出

| スキル | 説明 | 主な特徴 |
|--------|------|----------|
| [動画要約](skills/内容提取/video-summarization.md) | 動画コンテンツの要約 | 抽出式＋生成式、チャプター要約、複数タイプ対応 |
| [キーポイント抽出](skills/内容提取/key-points-extraction.md) | キーポイントとインサイトの抽出 | 引用、決定事項、アクションアイテム、キーワード抽出 |
| [チャプター生成](skills/内容提取/chapter-generation.md) | 動画チャプターとタイムスタンプの生成 | トピック分割、YouTube/ポッドキャスト形式、SEO タイトル |

### ノート整理

| スキル | 説明 | 主な特徴 |
|--------|------|----------|
| [動画ノート](skills/笔记整理/video-notes.md) | 動画からの構造化ノート作成 | コーネル式、マインドマップ、アウトライン形式 |
| [知識抽出](skills/笔记整理/knowledge-extraction.md) | 知識の抽出と整理 | 概念マップ、Obsidian 連携、グラフデータベース |

### 字幕処理

| スキル | 説明 | 主な特徴 |
|--------|------|----------|
| [字幕生成](skills/字幕处理/subtitle-generation.md) | 自動字幕生成 | SRT/ASS/VTT 形式、スタイルカスタマイズ、タイミング同期 |
| [字幕翻訳](skills/字幕处理/subtitle-translation.md) | 字幕翻訳 | 二重字幕、文化的適応、タイミング調整 |

### 学習支援

| スキル | 説明 | 主な特徴 |
|--------|------|----------|
| [講義ノート](skills/学习辅助/lecture-notes.md) | 講義・コースのノート作成 | Anki フラッシュカード、クイズ生成、間隔反復 |
| [チュートリアル文書化](skills/学习辅助/tutorial-documentation.md) | 動画からのチュートリアル文書作成 | ステップ抽出、コードキャプチャ、MkDocs 連携 |

---

## クイックスタート

### Cursor

1. 本リポジトリをクローンまたはダウンロード
2. Cursor でプロジェクトを開く
3. スキルファイルの内容を `.cursorrules` にコピーするか、チャットで参照
4. Cursor に動画の処理を依頼

```
@whisper-transcription.md Transcribe my lecture video at ./videos/lecture.mp4
```

### Claude Code

1. 本リポジトリをプロジェクトにクローン
2. プロンプトでスキルを参照

```bash
# プロジェクトディレクトリで
claude "Using the whisper-transcription skill, transcribe ./video.mp4 and generate study notes"
```

### Kimi Code

1. 本リポジトリをスキルソースとして追加
2. `/skill` またはスキルファイルの参照でスキルを利用

```bash
kimi "Follow skills/转录技术/whisper-transcription.md to transcribe my video"
```

---

## ツール比較

| ツール | 速度 | 品質 | コスト | オフライン | 最適用途 |
|--------|------|------|--------|-----------|----------|
| **Whisper (ローカル)** | 中程度 | ★★★★☆ | 無料 | ○ | プライバシー保護、バッチ処理 |
| **Whisper API** | 高速 | ★★★★☆ | $0.006/分 | × | 簡易的な単発転写 |
| **faster-whisper** | 高速 | ★★★★☆ | 無料 | ○ | 本番パイプライン |
| **whisper.cpp** | 高速 | ★★★★☆ | 無料 | ○ | Python 環境不要 |
| **Google Cloud STT** | 高速 | ★★★★☆ | $0.006/分 | × | リアルタイムストリーミング |
| **Azure Speech** | 高速 | ★★★★★ | $1/時間 | × | エンタープライズ、話者識別 |
| **AssemblyAI** | 高速 | ★★★★★ | $0.015/分 | × | 会議転写 |

### モデル選定ガイド

| Whisper モデル | サイズ | 速度 | 品質 | VRAM | 推奨用途 |
|---------------|--------|------|------|------|----------|
| `tiny` | 39M | ~32x | ★★☆☆☆ | ~1GB | 簡易ドラフト専用 |
| `base` | 74M | ~16x | ★★★☆☆ | ~1GB | クリーンな音声、短いクリップ |
| `small` | 244M | ~6x | ★★★★☆ | ~2GB | バランス良好 |
| `medium` | 769M | ~2x | ★★★★☆ | ~5GB | **ほとんどの用途に対応** |
| `large-v3` | 1.5G | 1x | ★★★★★ | ~10GB | 最高精度 |
| `turbo` | 809M | ~8x | ★★★★☆ | ~6GB | **最高の速度/品質バランス** |

---

## プロジェクト構成

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
    ├── 转录技术/                    # 転写技術
    │   ├── whisper-transcription.md
    │   ├── youtube-transcript.md
    │   └── meeting-transcription.md
    ├── 内容提取/                    # コンテンツ抽出
    │   ├── video-summarization.md
    │   ├── key-points-extraction.md
    │   └── chapter-generation.md
    ├── 笔记整理/                    # ノート整理
    │   ├── video-notes.md
    │   └── knowledge-extraction.md
    ├── 字幕处理/                    # 字幕処理
    │   ├── subtitle-generation.md
    │   └── subtitle-translation.md
    └── 学习辅助/                    # 学習支援
        ├── lecture-notes.md
        └── tutorial-documentation.md
```

---

## 関連リンク

- [awesome-video-skills](https://github.com/nicekate/awesome-video-skills) - 動画編集・処理のための AI スキル
- [awesome-video-prompts](https://github.com/nicekate/awesome-video-prompts) - 動画 AI 向けキュレーションプロンプト
- [awesome-skills](https://github.com/nicekate/awesome-skills) - 汎用 AI スキルコレクション
- [awesome-ai-rules](https://github.com/nicekate/awesome-ai-rules) - AI コーディングアシスタントのルールと設定

---

## コントリビューション

コントリビューションを歓迎します！ガイドラインは [CONTRIBUTING.md](CONTRIBUTING.md) をご覧ください。

- 新しいスキルの追加
- 既存スキルの改善
- バグや誤字の修正
- コンテンツの翻訳

---

## ライセンス

本プロジェクトは MIT ライセンスの下で公開されています。詳細は [LICENSE](LICENSE) ファイルをご覧ください。

Copyright (c) 2024 liangzhengtao

---

<div align="center">

**動画からテキストへのコミュニティのために ❤️ を込めて制作**

</div>
