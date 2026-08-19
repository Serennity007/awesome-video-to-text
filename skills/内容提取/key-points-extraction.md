# Key Points and Insights Extraction

Extract actionable insights, important quotes, decisions, and key points from video content. Transform passive video content into structured, actionable information.

## When to Use

- Extracting key takeaways from conference talks
- Identifying decision points in meeting recordings
- Pulling important quotes from interviews
- Creating highlight reels from long presentations
- Building knowledge bases from educational videos
- Generating action items from team discussions

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [OpenAI Whisper](https://github.com/openai/whisper) | Latest | Transcription |
| [OpenAI API](https://platform.openai.com/) | GPT-4o | Insight extraction |
| [tiktoken](https://github.com/openai/tiktoken) | Latest | Token counting |

### Optional

| Tool | Purpose |
|------|---------|
| [Anthropic Claude](https://console.anthropic.com/) | Alternative LLM |
| [spaCy](https://spacy.io/) | NLP processing |
| [keybert](https://github.com/MaartenGr/KeyBERT) | Keyword extraction |

### Installation

```bash
# Core
pip install openai-whisper openai tiktoken

# NLP tools
pip install spacy keybert
python -m spacy download en_core_web_sm

# Alternative LLM
pip install anthropic
```

## Step-by-Step Workflow

### Step 1: Transcribe with Timestamps

```python
import whisper
model = whisper.load_model("medium")
result = model.transcribe("video.mp4", verbose=False)
segments = result["segments"]
```

### Step 2: Extract Key Points

```python
# Use LLM to identify important points
```

### Step 3: Structure and Format

```python
# Organize into categories
```

### Step 4: Link to Timestamps

```python
# Map key points back to video timestamps
```

## Code Templates

### Template 1: Key Points Extractor

```python
#!/usr/bin/env python3
"""Extract key points and insights from video transcripts."""

import whisper
import json
from pathlib import Path
from openai import OpenAI


class KeyPointsExtractor:
    """Extract structured insights from video content."""
    
    def __init__(self, whisper_model: str = "medium", llm_model: str = "gpt-4o"):
        self.whisper_model = whisper.load_model(whisper_model)
        self.llm = OpenAI()
        self.llm_model = llm_model
    
    def extract(self, video_path: str, language: str = None) -> dict:
        """Extract all key points from a video."""
        
        # Transcribe
        print("Transcribing...")
        result = self.whisper_model.transcribe(video_path, language=language)
        segments = result["segments"]
        full_text = result["text"]
        
        # Extract different types of insights
        print("Extracting key points...")
        key_points = self._extract_key_points(full_text)
        
        print("Extracting quotes...")
        quotes = self._extract_quotes(full_text)
        
        print("Extracting action items...")
        actions = self._extract_actions(full_text)
        
        print("Extracting decisions...")
        decisions = self._extract_decisions(full_text)
        
        # Map back to timestamps
        key_points = self._add_timestamps(key_points, segments)
        
        return {
            "key_points": key_points,
            "quotes": quotes,
            "action_items": actions,
            "decisions": decisions,
            "segment_count": len(segments),
            "duration": segments[-1]["end"] if segments else 0
        }
    
    def _extract_key_points(self, text: str) -> list:
        """Extract main points from text."""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": """Extract the key points from this content.
Return a JSON array where each item has:
- "point": the key point (1-2 sentences)
- "category": one of [main_idea, supporting_detail, example, statistic, recommendation]
- "importance": high/medium/low

Return ONLY valid JSON, no other text."""},
                {"role": "user", "content": text[:8000]}
            ],
            max_tokens=2000,
            temperature=0.2
        )
        
        try:
            return json.loads(response.choices[0].message.content)
        except json.JSONDecodeError:
            return [{"point": response.choices[0].message.content, "category": "main_idea", "importance": "high"}]
    
    def _extract_quotes(self, text: str) -> list:
        """Extract notable quotes."""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": """Extract notable quotes from this content.
Return a JSON array where each item has:
- "quote": the exact or near-exact quote
- "context": brief context for the quote
- "significance": why this quote is important

Return ONLY valid JSON."""},
                {"role": "user", "content": text[:8000]}
            ],
            max_tokens=1500,
            temperature=0.2
        )
        
        try:
            return json.loads(response.choices[0].message.content)
        except json.JSONDecodeError:
            return []
    
    def _extract_actions(self, text: str) -> list:
        """Extract action items and tasks."""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": """Extract action items, tasks, and to-dos from this content.
Return a JSON array where each item has:
- "action": what needs to be done
- "who": responsible person (if mentioned)
- "when": deadline or timeframe (if mentioned)
- "priority": high/medium/low

Return ONLY valid JSON, empty array if none found."""},
                {"role": "user", "content": text[:8000]}
            ],
            max_tokens=1500,
            temperature=0.2
        )
        
        try:
            return json.loads(response.choices[0].message.content)
        except json.JSONDecodeError:
            return []
    
    def _extract_decisions(self, text: str) -> list:
        """Extract decisions made."""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": """Extract decisions, conclusions, and resolutions from this content.
Return a JSON array where each item has:
- "decision": what was decided
- "rationale": why (if mentioned)
- "impact": what this affects

Return ONLY valid JSON, empty array if none found."""},
                {"role": "user", "content": text[:8000]}
            ],
            max_tokens=1500,
            temperature=0.2
        )
        
        try:
            return json.loads(response.choices[0].message.content)
        except json.JSONDecodeError:
            return []
    
    def _add_timestamps(self, key_points: list, segments: list) -> list:
        """Map key points to approximate timestamps."""
        
        for point in key_points:
            point_text = point.get("point", "").lower()
            best_match = None
            best_score = 0
            
            for seg in segments:
                seg_text = seg["text"].lower()
                # Simple word overlap scoring
                point_words = set(point_text.split())
                seg_words = set(seg_text.split())
                overlap = len(point_words & seg_words)
                
                if overlap > best_score:
                    best_score = overlap
                    best_match = seg
            
            if best_match and best_score > 2:
                point["timestamp"] = best_match["start"]
                point["timestamp_str"] = self._fmt_time(best_match["start"])
        
        return key_points
    
    @staticmethod
    def _fmt_time(seconds: float) -> str:
        m, s = divmod(int(seconds), 60)
        h, m = divmod(m, 60)
        if h:
            return f"{h}:{m:02d}:{s:02d}"
        return f"{m}:{s:02d}"
    
    def format_report(self, result: dict) -> str:
        """Format extraction results as a markdown report."""
        
        lines = ["# Key Points & Insights Report\n"]
        
        # Key Points
        lines.append("## Key Points\n")
        for i, point in enumerate(result["key_points"], 1):
            ts = point.get("timestamp_str", "")
            ts_str = f" [{ts}]" if ts else ""
            cat = point.get("category", "")
            imp = point.get("importance", "")
            lines.append(f"{i}. **{point['point']}**{ts_str}")
            lines.append(f"   - Category: {cat} | Importance: {imp}")
        
        # Quotes
        if result.get("quotes"):
            lines.append("\n## Notable Quotes\n")
            for q in result["quotes"]:
                lines.append(f"> \"{q['quote']}\"")
                lines.append(f> — {q.get('context', '')}\n")
        
        # Decisions
        if result.get("decisions"):
            lines.append("\n## Decisions\n")
            for d in result["decisions"]:
                lines.append(f"- **{d['decision']}**")
                if d.get("rationale"):
                    lines.append(f"  - Rationale: {d['rationale']}")
        
        # Action Items
        if result.get("action_items"):
            lines.append("\n## Action Items\n")
            lines.append("| # | Action | Owner | Deadline | Priority |")
            lines.append("|---|--------|-------|----------|----------|")
            for i, action in enumerate(result["action_items"], 1):
                lines.append(
                    f"| {i} | {action['action']} | {action.get('who', '-')} "
                    f"| {action.get('when', '-')} | {action.get('priority', '-')} |"
                )
        
        return "\n".join(lines)
    
    def save(self, result: dict, output_dir: str, base_name: str):
        """Save results in multiple formats."""
        
        output_path = Path(output_dir)
        output_path.mkdir(parents=True, exist_ok=True)
        
        # Save markdown report
        report = self.format_report(result)
        (output_path / f"{base_name}_insights.md").write_text(report, encoding="utf-8")
        
        # Save JSON
        (output_path / f"{base_name}_insights.json").write_text(
            json.dumps(result, indent=2, ensure_ascii=False),
            encoding="utf-8"
        )
        
        print(f"Saved to {output_path}")


def main():
    extractor = KeyPointsExtractor(whisper_model="medium")
    result = extractor.extract("presentation.mp4", language="en")
    extractor.save(result, "./output", "presentation")


if __name__ == "__main__":
    main()
```

### Template 2: Keyword Extraction with KeyBERT

```python
#!/usr/bin/env python3
"""Extract keywords and topics from video transcripts."""

from keybert import KeyBERT
from sklearn.feature_extraction.text import CountVectorizer


def extract_keywords(
    text: str,
    top_n: int = 20,
    ngram_range: tuple = (1, 2),
    diversity: float = 0.5
) -> list:
    """Extract keywords using KeyBERT."""
    
    kw_model = KeyBERT()
    
    keywords = kw_model.extract_keywords(
        text,
        keyphrase_ngram_range=ngram_range,
        stop_words="english",
        use_mmr=True,
        diversity=diversity,
        top_n=top_n
    )
    
    return [{"keyword": kw, "score": round(score, 3)} for kw, score in keywords]


def extract_topics(texts: list, n_topics: int = 5) -> dict:
    """Extract main topics from multiple text segments."""
    
    from sklearn.decomposition import LatentDirichletAllocation
    from sklearn.feature_extraction.text import CountVectorizer
    
    vectorizer = CountVectorizer(max_df=0.95, min_df=2, stop_words='english')
    dtm = vectorizer.fit_transform(texts)
    
    lda = LatentDirichletAllocation(n_components=n_topics, random_state=42)
    lda.fit(dtm)
    
    feature_names = vectorizer.get_feature_names_out()
    topics = []
    
    for topic_idx, topic in enumerate(lda.components_):
        top_words = [feature_names[i] for i in topic.argsort()[:-11:-1]]
        topics.append({
            "topic_id": topic_idx,
            "top_words": top_words
        })
    
    return {"topics": topics}
```

## Output Format Examples

### Key Points Report
```markdown
# Key Points & Insights Report

## Key Points

1. **Machine learning requires large datasets for training** [0:05]
   - Category: main_idea | Importance: high

2. **Transfer learning can reduce data requirements by 80%** [0:12]
   - Category: statistic | Importance: high

3. **Common frameworks include PyTorch and TensorFlow** [0:18]
   - Category: supporting_detail | Importance: medium

## Notable Quotes

> "The best model is the one that solves your specific problem."
> — Speaker discussing model selection

## Decisions

- **Use PyTorch for the new project**
  - Rationale: Better debugging and dynamic computation graphs

## Action Items

| # | Action | Owner | Deadline | Priority |
|---|--------|-------|----------|----------|
| 1 | Set up PyTorch environment | Dev team | Next week | High |
| 2 | Prepare training dataset | Data team | End of month | High |
```

## Common Pitfalls

1. **Missing Context for Quotes**
   - **Problem**: Quotes without context are meaningless
   - **Solution**: Always include surrounding context

2. **Too Many Key Points**
   - **Problem**: Everything seems important
   - **Solution**: Limit to top 5-10 most important points

3. **Inaccurate Timestamps**
   - **Problem**: Key points mapped to wrong timestamps
   - **Solution**: Use segment-level matching, not word-level

4. **Over-categorization**
   - **Problem**: Too many categories dilute meaning
   - **Solution**: Use 4-5 broad categories

## Pro Tips

- **Use JSON mode** in LLM API for consistent output
- **Temperature 0.2** for extraction tasks
- **Process in segments** for long videos to maintain context
- **Save raw JSON** alongside formatted reports
- **Combine extractive + abstractive** methods for best results

---

## 中文版本

### 适用场景

- 从会议演讲中提取关键要点
- 在会议录音中识别决策点
- 从采访中提取重要引言
- 从长演示中创建精彩片段
- 从教育视频构建知识库
- 从团队讨论中生成行动项

### 快速开始

```bash
pip install openai-whisper openai keybert

python key_points_extractor.py --video 讲座.mp4 --language zh
```

### 中文处理提示

1. 使用 `spacy` 中文模型进行关键词提取
2. 在 LLM 提示中明确要求中文输出
3. 中文引言提取注意标点符号处理
