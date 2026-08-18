# Video Content Summarization

Create concise, structured summaries from video content using extractive and abstractive techniques. Transform long videos into actionable summaries.

## When to Use

- Summarizing long lectures into key takeaways
- Creating executive summaries of meeting recordings
- Generating TL;DR for tutorial videos
- Building preview descriptions for video content
- Extracting main points from conference talks
- Condensing podcast episodes into readable notes

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [OpenAI Whisper](https://github.com/openai/whisper) | Latest | Video transcription |
| [OpenAI API](https://platform.openai.com/) | Latest | Abstractive summarization |
| [transformers](https://huggingface.co/docs/transformers) | Latest | Local summarization models |

### Optional

| Tool | Purpose |
|------|---------|
| [sumy](https://github.com/miso-belica/sumy) | Extractive summarization |
| [gensim](https://radimrehurek.com/gensim/) | TextRank summarization |
| [Anthropic Claude API](https://console.anthropic.com/) | High-quality summaries |
| [tiktoken](https://github.com/openai/tiktoken) | Token counting |

### Installation

```bash
# Core
pip install openai-whisper tiktoken

# Summarization
pip install transformers torch
pip install sumy gensim

# API clients
pip install openai anthropic
```

## Step-by-Step Workflow

### Step 1: Transcribe the Video

```python
import whisper

model = whisper.load_model("medium")
result = model.transcribe("lecture.mp4")
transcript = result["text"]
```

### Step 2: Chunk the Transcript

```python
def chunk_text(text, max_tokens=3000):
    """Split text into chunks that fit in model context."""
    sentences = text.split('. ')
    chunks, current = [], []
    token_count = 0
    
    for sentence in sentences:
        tokens = len(sentence.split()) * 1.3  # rough estimate
        if token_count + tokens > max_tokens:
            chunks.append('. '.join(current) + '.')
            current, token_count = [], 0
        current.append(sentence)
        token_count += tokens
    
    if current:
        chunks.append('. '.join(current))
    return chunks
```

### Step 3: Summarize Each Chunk

```python
# Use LLM to summarize each chunk
```

### Step 4: Combine and Refine

```python
# Merge chunk summaries into final summary
```

## Code Templates

### Template 1: Full Video Summarization Pipeline

```python
#!/usr/bin/env python3
"""Complete video summarization pipeline."""

import whisper
import tiktoken
from pathlib import Path
from openai import OpenAI


class VideoSummarizer:
    """Summarize video content using transcription + LLM."""
    
    def __init__(self, whisper_model: str = "medium", llm_model: str = "gpt-4o"):
        self.whisper_model = whisper.load_model(whisper_model)
        self.llm = OpenAI()
        self.encoding = tiktoken.encoding_for_model(llm_model)
        self.llm_model = llm_model
    
    def summarize(
        self,
        video_path: str,
        language: str = None,
        summary_type: str = "detailed",
        max_tokens: int = 2000
    ) -> dict:
        """Generate a structured summary from video."""
        
        # Step 1: Transcribe
        print("Transcribing video...")
        transcript_result = self.whisper_model.transcribe(
            video_path, 
            language=language,
            verbose=False
        )
        transcript = transcript_result["text"]
        segments = transcript_result["segments"]
        
        # Step 2: Chunk if needed
        chunks = self._chunk_text(transcript, max_chunk_tokens=6000)
        print(f"Split into {len(chunks)} chunk(s)")
        
        # Step 3: Summarize each chunk
        chunk_summaries = []
        for i, chunk in enumerate(chunks):
            print(f"Summarizing chunk {i+1}/{len(chunks)}...")
            summary = self._summarize_chunk(chunk, summary_type)
            chunk_summaries.append(summary)
        
        # Step 4: Create final summary
        if len(chunk_summaries) > 1:
            final = self._combine_summaries(chunk_summaries, summary_type)
        else:
            final = chunk_summaries[0]
        
        return {
            "summary": final,
            "transcript": transcript,
            "segments": segments,
            "chunk_count": len(chunks),
            "type": summary_type
        }
    
    def _summarize_chunk(self, text: str, summary_type: str) -> str:
        """Summarize a text chunk using LLM."""
        
        prompts = {
            "brief": "Provide a 2-3 sentence summary of the following content:",
            "detailed": """Provide a detailed summary with:
- Main topic and context
- Key points (bullet list)
- Important details and examples
- Conclusions or takeaways""",
            "executive": """Create an executive summary:
- One paragraph overview
- 3-5 key decisions or findings
- Action items (if any)
- Important numbers/dates mentioned""",
            "study": """Create study notes:
- Main concept/topic
- Key definitions
- Important formulas or rules
- Examples discussed
- Practice questions based on content"""
        }
        
        prompt = prompts.get(summary_type, prompts["detailed"])
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": "You are a skilled content summarizer. Be accurate and concise."},
                {"role": "user", "content": f"{prompt}\n\n{text}"}
            ],
            max_tokens=2000,
            temperature=0.3
        )
        
        return response.choices[0].message.content
    
    def _combine_summaries(self, summaries: list, summary_type: str) -> str:
        """Combine multiple chunk summaries into one."""
        
        combined = "\n\n---\n\n".join(summaries)
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": "Combine these partial summaries into one coherent summary. Remove redundancy."},
                {"role": "user", "content": combined}
            ],
            max_tokens=2000,
            temperature=0.3
        )
        
        return response.choices[0].message.content
    
    def _chunk_text(self, text: str, max_chunk_tokens: int = 6000) -> list:
        """Split text into chunks that fit model context."""
        
        sentences = text.replace('\n', ' ').split('. ')
        chunks = []
        current_chunk = []
        current_tokens = 0
        
        for sentence in sentences:
            tokens = len(self.encoding.encode(sentence))
            
            if current_tokens + tokens > max_chunk_tokens and current_chunk:
                chunks.append('. '.join(current_chunk) + '.')
                current_chunk = []
                current_tokens = 0
            
            current_chunk.append(sentence)
            current_tokens += tokens
        
        if current_chunk:
            chunks.append('. '.join(current_chunk))
        
        return chunks
    
    def save(self, result: dict, output_path: str):
        """Save summary to file."""
        
        output = Path(output_path)
        output.parent.mkdir(parents=True, exist_ok=True)
        
        content = f"""# Video Summary

## Type: {result['type'].title()}

{result['summary']}

---

## Metadata
- Chunks processed: {result['chunk_count']}
- Transcript length: {len(result['transcript'])} characters
"""
        
        output.write_text(content, encoding="utf-8")
        print(f"Summary saved to: {output}")


def main():
    summarizer = VideoSummarizer(
        whisper_model="medium",
        llm_model="gpt-4o"
    )
    
    result = summarizer.summarize(
        video_path="lecture.mp4",
        language="en",
        summary_type="study"
    )
    
    summarizer.save(result, "output/lecture_summary.md")


if __name__ == "__main__":
    main()
```

### Template 2: Chapter-based Summary

```python
#!/usr/bin/env python3
"""Generate chapter-based summaries for long videos."""

from datetime import timedelta
from openai import OpenAI


def generate_chapter_summary(
    segments: list,
    chapters: list = None,
    model: str = "gpt-4o"
) -> str:
    """Generate summaries for each chapter/section."""
    
    client = OpenAI()
    
    if not chapters:
        chapters = detect_chapters(segments)
    
    chapter_summaries = []
    
    for chapter in chapters:
        # Get segments for this chapter
        chapter_text = " ".join(
            seg["text"] for seg in segments
            if chapter["start"] <= seg["start"] < chapter["end"]
        )
        
        # Summarize
        response = client.chat.completions.create(
            model=model,
            messages=[
                {"role": "system", "content": "Summarize this section concisely."},
                {"role": "user", "content": f"Chapter: {chapter['title']}\n\n{chapter_text}"}
            ],
            max_tokens=300
        )
        
        start_time = str(timedelta(seconds=int(chapter["start"])))
        chapter_summaries.append({
            "title": chapter["title"],
            "timestamp": start_time,
            "summary": response.choices[0].message.content
        })
    
    # Format output
    output = ["# Chapter Summaries\n"]
    for cs in chapter_summaries:
        output.append(f"## [{cs['timestamp']}] {cs['title']}")
        output.append(f"\n{cs['summary']}\n")
    
    return "\n".join(output)


def detect_chapters(segments: list, min_gap: float = 3.0) -> list:
    """Detect chapter boundaries from silence gaps."""
    
    chapters = []
    current_start = segments[0]["start"]
    current_text = []
    
    for i in range(1, len(segments)):
        gap = segments[i]["start"] - segments[i-1]["end"]
        
        if gap >= min_gap:
            # Chapter boundary detected
            combined = " ".join(current_text)
            chapters.append({
                "start": current_start,
                "end": segments[i-1]["end"],
                "title": _generate_title(combined),
                "text": combined
            })
            current_start = segments[i]["start"]
            current_text = []
        
        current_text.append(segments[i]["text"])
    
    # Add last chapter
    if current_text:
        chapters.append({
            "start": current_start,
            "end": segments[-1]["end"],
            "title": _generate_title(" ".join(current_text)),
            "text": " ".join(current_text)
        })
    
    return chapters


def _generate_title(text: str, max_words: int = 6) -> str:
    """Generate a short title from text."""
    words = text.split()[:max_words]
    return " ".join(words) + "..."
```

## Output Format Examples

### Detailed Summary
```markdown
# Video Summary

## Overview
This lecture covers the fundamentals of neural networks, including 
perceptrons, activation functions, and backpropagation.

## Key Points
- Neural networks are inspired by biological neurons
- Perceptrons are the simplest neural network unit
- Activation functions introduce non-linearity
- Backpropagation is the primary training algorithm

## Important Details
- Sigmoid and ReLU are common activation functions
- Learning rate controls the step size during optimization
- Overfitting occurs when the model memorizes training data

## Takeaways
- Start with simple architectures before complex ones
- Always validate on held-out data
- Consider regularization techniques
```

### Chapter Summary
```markdown
# Chapter Summaries

## [0:00:00] Introduction
Brief overview of what neural networks are and why they matter.

## [0:05:30] Perceptrons
Explanation of the simplest neural network unit with visual examples.

## [0:15:00] Activation Functions
Comparison of sigmoid, tanh, and ReLU with use cases.

## [0:25:00] Training with Backpropagation
Step-by-step walkthrough of the backpropagation algorithm.
```

## Common Pitfalls

1. **Lost Context in Chunking**
   - **Problem**: Splitting mid-sentence loses meaning
   - **Solution**: Chunk at paragraph/section boundaries

2. **Token Limits**
   - **Problem**: Transcript exceeds model context window
   - **Solution**: Use chunk-then-merge strategy

3. **Hallucination in Summaries**
   - **Problem**: LLM adds information not in the video
   - **Solution**: Use lower temperature (0.1-0.3) and include "based only on the content" in prompt

4. **Missing Important Details**
   - **Problem**: Summary omits critical information
   - **Solution**: Use structured prompts that ask for specific categories

5. **Repetitive Summaries**
   - **Problem**: Multiple chunks produce redundant summaries
   - **Solution**: Deduplicate in the final merge step

## Pro Tips

- **Use `gpt-4o`** for best summarization quality
- **Temperature 0.3** balances accuracy and readability
- **Include timestamps** in chapter summaries for navigation
- **Save transcripts** alongside summaries for reference
- **Customize prompts** for different video types (lecture, meeting, tutorial)

---

## 中文版本

### 适用场景

- 将长讲座总结为关键要点
- 创建会议录音的执行摘要
- 为教程视频生成 TL;DR
- 为视频内容构建预览描述
- 从会议演讲中提取主要观点
- 将播客剧集浓缩为可读笔记

### 快速开始

```bash
pip install openai-whisper openai tiktoken

python3 -c "
from video_summarizer import VideoSummarizer
s = VideoSummarizer()
result = s.summarize('视频.mp4', language='zh', summary_type='detailed')
s.save(result, 'summary.md')
"
```

### 中文视频摘要提示

1. 使用 `large-v3` 模型获得最佳中文转录
2. 在提示中指定"用中文总结"以获得中文输出
3. 对于技术视频，提供术语表以提高准确率
