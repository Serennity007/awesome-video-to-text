# Subtitle Translation

Translate subtitles for multilingual audiences with timing preservation, cultural adaptation, and bilingual subtitle support.

## When to Use

- Translating YouTube subtitles for global audiences
- Creating bilingual language learning content
- Localizing training videos for international teams
- Producing multilingual educational content
- Making content accessible in multiple languages
- Creating side-by-side subtitle comparisons

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [OpenAI API](https://platform.openai.com/) | GPT-4o | High-quality translation |
| [srt](https://github.com/cdown/srt) | Latest | SRT file parsing |

### Optional

| Tool | Purpose |
|------|---------|
| [deep-translator](https://github.com/nidhaloff/deep-translator) | Multiple translation APIs |
| [argostranslate](https://github.com/argosopentech/argos-translate) | Local offline translation |
| [pysrt](https://github.com/byroot/pysrt) | SRT manipulation |
| [googletrans](https://github.com/ssut/py-googletrans) | Google Translate API |

### Installation

```bash
# Core
pip install openai srt pysrt

# Multiple translation engines
pip install deep-translator

# Local offline translation
pip install argostranslate

# Google Translate (unofficial)
pip install googletrans==4.0.0-rc1
```

## Step-by-Step Workflow

### Step 1: Parse Source Subtitles

```python
import srt
with open("source.srt", "r") as f:
    subtitles = list(srt.parse(f.read()))
```

### Step 2: Translate Content

```python
# Translate each subtitle while preserving timing
```

### Step 3: Adjust Timing

```python
# Adjust timing for language-specific reading speeds
```

### Step 4: Generate Output

```python
# Create translated or bilingual subtitle files
```

## Code Templates

### Template 1: Subtitle Translator with LLM

```python
#!/usr/bin/env python3
"""Translate subtitles using LLM with context awareness."""

import srt
import json
from pathlib import Path
from openai import OpenAI
from datetime import timedelta


class SubtitleTranslator:
    """Translate subtitles with timing preservation."""
    
    def __init__(self, llm_model: str = "gpt-4o"):
        self.llm = OpenAI()
        self.llm_model = llm_model
    
    def translate(
        self,
        input_file: str,
        source_lang: str = "en",
        target_lang: str = "zh",
        output_file: str = None,
        bilingual: bool = False,
        context: str = None,
        batch_size: int = 20
    ) -> str:
        """Translate subtitle file."""
        
        # Parse source
        with open(input_file, "r", encoding="utf-8") as f:
            subtitles = list(srt.parse(f.read()))
        
        print(f"Loaded {len(subtitles)} subtitles")
        print(f"Translating: {source_lang} → {target_lang}")
        
        # Translate in batches
        translated = []
        for i in range(0, len(subtitles), batch_size):
            batch = subtitles[i:i+batch_size]
            print(f"  Translating batch {i//batch_size + 1}...")
            
            batch_translated = self._translate_batch(
                batch, source_lang, target_lang, context
            )
            translated.extend(batch_translated)
        
        # Generate output
        if bilingual:
            content = self._generate_bilingual(subtitles, translated)
        else:
            content = self._generate_translated(translated)
        
        # Save
        if not output_file:
            stem = Path(input_file).stem
            output_file = f"{stem}_{target_lang}.srt"
        
        Path(output_file).write_text(content, encoding="utf-8")
        print(f"Saved: {output_file}")
        
        return output_file
    
    def _translate_batch(
        self,
        subtitles: list,
        source_lang: str,
        target_lang: str,
        context: str = None
    ) -> list:
        """Translate a batch of subtitles."""
        
        # Prepare batch text
        entries = []
        for sub in subtitles:
            entries.append({
                "index": sub.index,
                "text": sub.content.strip()
            })
        
        context_hint = f"\nContext: {context}" if context else ""
        
        lang_names = {
            "en": "English", "zh": "Chinese", "ja": "Japanese",
            "ko": "Korean", "es": "Spanish", "fr": "French",
            "de": "German", "pt": "Portuguese", "ru": "Russian",
            "ar": "Arabic"
        }
        
        src_name = lang_names.get(source_lang, source_lang)
        tgt_name = lang_names.get(target_lang, target_lang)
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": f"""Translate subtitle entries from {src_name} to {tgt_name}.

Rules:
1. Keep translations concise (subtitles should be readable quickly)
2. Preserve the meaning and tone
3. Adapt idioms naturally to the target language
4. Keep proper nouns in their original form or use standard translations
5. Maintain consistent terminology throughout
{context_hint}

Return a JSON array with the same indices, each having "index" and "text" fields."""},
                {"role": "user", "content": json.dumps(entries, ensure_ascii=False)}
            ],
            max_tokens=3000,
            temperature=0.3
        )
        
        try:
            translated = json.loads(response.choices[0].message.content)
        except json.JSONDecodeError:
            translated = entries
        
        # Map back to subtitle objects
        result = []
        for sub, trans in zip(subtitles, translated):
            new_sub = srt.Subtitle(
                index=sub.index,
                start=sub.start,
                end=sub.end,
                content=trans.get("text", sub.content)
            )
            result.append(new_sub)
        
        return result
    
    def _generate_translated(self, subtitles: list) -> str:
        """Generate translated SRT content."""
        return srt.compose(subtitles)
    
    def _generate_bilingual(self, original: list, translated: list) -> str:
        """Generate bilingual SRT with both languages."""
        
        bilingual_subs = []
        for orig, trans in zip(original, translated):
            content = f"{trans.content}\n{orig.content}"
            new_sub = srt.Subtitle(
                index=orig.index,
                start=orig.start,
                end=orig.end,
                content=content
            )
            bilingual_subs.append(new_sub)
        
        return srt.compose(bilingual_subs)
    
    def adjust_timing(
        self,
        subtitle_file: str,
        output_file: str,
        reading_speed: float = 15.0,
        min_duration: float = 1.0,
        max_duration: float = 7.0
    ):
        """Adjust subtitle timing for target language reading speed."""
        
        with open(subtitle_file, "r", encoding="utf-8") as f:
            subtitles = list(srt.parse(f.read()))
        
        adjusted = []
        for sub in subtitles:
            text_len = len(sub.content.replace("\n", ""))
            required_duration = text_len / reading_speed
            current_duration = (sub.end - sub.start).total_seconds()
            
            if current_duration < required_duration:
                # Extend duration
                new_end = sub.start + timedelta(seconds=max(required_duration, min_duration))
                sub = srt.Subtitle(
                    index=sub.index,
                    start=sub.start,
                    end=min(new_end, sub.start + timedelta(seconds=max_duration)),
                    content=sub.content
                )
            
            adjusted.append(sub)
        
        Path(output_file).write_text(srt.compose(adjusted), encoding="utf-8")
        print(f"Adjusted timing saved: {output_file}")


def main():
    translator = SubtitleTranslator(llm_model="gpt-4o")
    
    # Translate to Chinese
    translator.translate(
        input_file="video_en.srt",
        source_lang="en",
        target_lang="zh",
        bilingual=True,
        context="This is a technical tutorial about Python programming"
    )
    
    # Adjust timing for Chinese reading speed
    translator.adjust_timing(
        subtitle_file="video_en_zh.srt",
        output_file="video_en_zh_adjusted.srt",
        reading_speed=12.0  # Chinese reads slightly slower
    )


if __name__ == "__main__":
    main()
```

### Template 2: Multi-language Subtitle Generator

```python
#!/usr/bin/env python3
"""Generate subtitles in multiple languages."""

from pathlib import Path


def generate_multilingual(
    source_file: str,
    target_languages: list,
    output_dir: str = "./multilingual_subs"
):
    """Generate subtitle files in multiple languages."""
    
    translator = SubtitleTranslator()
    output_path = Path(output_dir)
    output_path.mkdir(parents=True, exist_ok=True)
    
    stem = Path(source_file).stem
    
    for lang in target_languages:
        print(f"\nTranslating to {lang}...")
        output_file = output_path / f"{stem}_{lang}.srt"
        
        translator.translate(
            input_file=source_file,
            source_lang="en",
            target_lang=lang,
            output_file=str(output_file)
        )
    
    print(f"\nGenerated subtitles in {len(target_languages)} languages")


# Language codes for common languages
LANGUAGES = {
    "zh": "Chinese (Simplified)",
    "zh-tw": "Chinese (Traditional)",
    "ja": "Japanese",
    "ko": "Korean",
    "es": "Spanish",
    "fr": "French",
    "de": "German",
    "pt": "Portuguese",
    "ru": "Russian",
    "ar": "Arabic",
    "hi": "Hindi",
    "it": "Italian"
}
```

## Output Format Examples

### Bilingual Subtitle
```
1
00:00:00,000 --> 00:00:03,500
欢迎来到Python网络爬虫教程
Welcome to this tutorial on Python web scraping

2
00:00:03,500 --> 00:00:07,200
今天我们将学习如何从HTML页面提取数据
Today we'll learn how to extract data from HTML pages
```

### Translated Subtitle
```
1
00:00:00,000 --> 00:00:03,500
欢迎来到Python网络爬虫教程

2
00:00:03,500 --> 00:00:07,200
今天我们将学习如何从HTML页面提取数据
```

## Common Pitfalls

1. **Text Expansion**
   - **Problem**: Translated text is much longer
   - **Solution**: Adjust timing and use concise translations

2. **Cultural References**
   - **Problem**: Jokes or idioms don't translate well
   - **Solution**: Adapt rather than translate literally

3. **Technical Terms**
   - "API", "URL" etc. may not need translation
   - **Solution**: Keep technical terms in original language

4. **Reading Speed**
   - Chinese: ~12 chars/second
   - English: ~17 chars/second
   - **Solution**: Adjust timing per language

5. **Character Encoding**
   - **Problem**: CJK characters display as ???
   - **Solution**: Always use UTF-8 encoding

## Pro Tips

- **Use LLM translation**: Better quality than Google Translate for context
- **Provide context**: Tell the translator what the video is about
- **Bilingual for learning**: Great for language learners
- **Review translations**: Always have a native speaker review
- **Consistent terminology**: Keep a glossary for series of videos

---

## 中文版本

### 适用场景

- 为全球观众翻译 YouTube 字幕
- 创建双语学习内容
- 为国际团队本地化培训视频
- 制作多语言教育内容
- 使内容在多种语言中可用
- 创建并排字幕比较

### 快速开始

```bash
pip install openai srt pysrt

python subtitle_translator.py --input video_en.srt --source en --target zh --bilingual
```

### 翻译质量提示

1. **提供上下文**：告知翻译器视频的主题
2. **技术术语**：保留英文或使用标准翻译
3. **口语化处理**：字幕需要自然流畅
4. **时间调整**：中文阅读速度约为每秒 12 个字
5. **人工审核**：机器翻译后务必人工校对
