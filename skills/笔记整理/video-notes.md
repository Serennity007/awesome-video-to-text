# Structured Note-Taking from Videos

Create well-organized, structured notes from video content using proven note-taking methods like Cornell Notes, mind mapping, and outline format.

## When to Use

- Creating study notes from university lectures
- Taking notes from online courses (Coursera, edX, YouTube)
- Documenting technical tutorials and workshops
- Building reference notes from conference talks
- Organizing information from documentary content
- Preparing review materials from educational videos

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [OpenAI Whisper](https://github.com/openai/whisper) | Latest | Video transcription |
| [OpenAI API](https://platform.openai.com/) | GPT-4o | Note structuring |

### Optional

| Tool | Purpose |
|------|---------|
| [Obsidian](https://obsidian.md/) | Note storage and linking |
| [Notion](https://www.notion.so/) | Note organization |
| [Mermaid](https://mermaid.js.org/) | Mind map diagrams |
| [pandoc](https://pandoc.org/) | Format conversion |

### Installation

```bash
# Core
pip install openai-whisper openai

# For Mermaid diagrams
npm install -g @mermaid-js/mermaid-cli

# For format conversion
# macOS
brew install pandoc

# Ubuntu/Debian
sudo apt install pandoc

# Windows
winget install Pandoc.Pandoc
```

## Step-by-Step Workflow

### Step 1: Transcribe Video

```python
import whisper
model = whisper.load_model("medium")
result = model.transcribe("lecture.mp4")
```

### Step 2: Identify Structure

```python
# Detect topics, subtopics, definitions, examples
```

### Step 3: Apply Note-Taking Method

```python
# Cornell, outline, or mind map format
```

### Step 4: Generate and Refine

```python
# Create formatted notes, add links and references
```

## Code Templates

### Template 1: Cornell Notes Generator

```python
#!/usr/bin/env python3
"""Generate Cornell-style notes from video transcripts."""

import whisper
import json
from pathlib import Path
from openai import OpenAI
from datetime import datetime


class CornellNotesGenerator:
    """Generate structured Cornell Notes from video content."""
    
    def __init__(self, whisper_model: str = "medium", llm_model: str = "gpt-4o"):
        self.whisper = whisper.load_model(whisper_model)
        self.llm = OpenAI()
        self.llm_model = llm_model
    
    def generate(
        self,
        video_path: str,
        title: str = None,
        language: str = None,
        subject: str = None
    ) -> dict:
        """Generate Cornell Notes from a video."""
        
        # Transcribe
        print("Transcribing...")
        result = self.whisper.transcribe(video_path, language=language)
        transcript = result["text"]
        segments = result["segments"]
        
        # Extract structured content
        print("Analyzing content...")
        analysis = self._analyze_content(transcript, subject)
        
        # Generate notes
        print("Generating Cornell Notes...")
        notes = {
            "title": title or analysis.get("title", "Video Notes"),
            "date": datetime.now().strftime("%Y-%m-%d"),
            "subject": subject or analysis.get("subject", "General"),
            "source": video_path,
            "cue_column": analysis.get("questions", []),
            "note_column": analysis.get("notes", []),
            "summary": analysis.get("summary", ""),
            "key_terms": analysis.get("key_terms", []),
            "duration": segments[-1]["end"] if segments else 0
        }
        
        return notes
    
    def _analyze_content(self, transcript: str, subject: str = None) -> dict:
        """Analyze transcript for Cornell Notes components."""
        
        subject_hint = f"Subject: {subject}\n" if subject else ""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": f"""You are an expert note-taker. Analyze this lecture transcript and create Cornell Notes.

{subject_hint}Return a JSON object with:
- "title": lecture title
- "subject": subject area
- "questions": list of study questions for the cue column (5-10 questions)
- "notes": list of note entries for the main column, each with:
  - "topic": main topic/subtopic
  - "points": list of key points
  - "examples": list of examples (if any)
- "summary": 2-3 sentence summary
- "key_terms": list of important terms with definitions

Return ONLY valid JSON."""},
                {"role": "user", "content": transcript[:10000]}
            ],
            max_tokens=3000,
            temperature=0.3
        )
        
        try:
            return json.loads(response.choices[0].message.content)
        except json.JSONDecodeError:
            return {
                "title": "Video Notes",
                "subject": "General",
                "questions": [],
                "notes": [{"topic": "Main Content", "points": [response.choices[0].message.content], "examples": []}],
                "summary": "",
                "key_terms": []
            }
    
    def format_cornell(self, notes: dict) -> str:
        """Format notes in Cornell Notes style."""
        
        lines = []
        
        # Header
        lines.append(f"# {notes['title']}")
        lines.append(f"**Date**: {notes['date']} | **Subject**: {notes['subject']}")
        lines.append(f"**Source**: {notes['source']}")
        lines.append("")
        lines.append("---")
        lines.append("")
        
        # Key Terms
        if notes.get("key_terms"):
            lines.append("## Key Terms")
            lines.append("")
            for term in notes["key_terms"]:
                if isinstance(term, dict):
                    lines.append(f"- **{term.get('term', '')}**: {term.get('definition', '')}")
                else:
                    lines.append(f"- {term}")
            lines.append("")
        
        # Two-column layout (rendered as sections)
        lines.append("## Notes")
        lines.append("")
        
        for note in notes.get("note_column", []):
            lines.append(f"### {note.get('topic', 'Topic')}")
            lines.append("")
            
            for point in note.get("points", []):
                lines.append(f"- {point}")
            
            if note.get("examples"):
                lines.append("")
                lines.append("**Examples:**")
                for ex in note["examples"]:
                    lines.append(f"  - {ex}")
            
            lines.append("")
        
        # Cue Column (Questions)
        lines.append("## Study Questions (Cue Column)")
        lines.append("")
        for i, q in enumerate(notes.get("cue_column", []), 1):
            lines.append(f"{i}. {q}")
        lines.append("")
        
        # Summary
        lines.append("## Summary")
        lines.append("")
        lines.append(notes.get("summary", ""))
        
        return "\n".join(lines)
    
    def format_outline(self, notes: dict) -> str:
        """Format notes in outline style."""
        
        lines = []
        lines.append(f"# {notes['title']}")
        lines.append("")
        
        for i, note in enumerate(notes.get("note_column", []), 1):
            lines.append(f"## {i}. {note.get('topic', 'Topic')}")
            
            for j, point in enumerate(note.get("points", []), 1):
                lines.append(f"   {i}.{j} {point}")
            
            if note.get("examples"):
                lines.append(f"   **Examples:**")
                for ex in note["examples"]:
                    lines.append(f"     - {ex}")
            
            lines.append("")
        
        return "\n".join(lines)
    
    def format_mindmap(self, notes: dict) -> str:
        """Format notes as a Mermaid mind map."""
        
        lines = ["mindmap"]
        lines.append(f"  root(({notes['title']}))")
        
        for note in notes.get("note_column", []):
            topic = note.get("topic", "Topic").replace("(", "\\(").replace(")", "\\)")
            lines.append(f"    {topic}")
            
            for point in note.get("points", [])[:3]:  # Limit for readability
                safe_point = point[:30].replace("(", "\\(").replace(")", "\\)")
                lines.append(f"      {safe_point}")
        
        return "\n".join(lines)
    
    def save(self, notes: dict, output_dir: str, base_name: str):
        """Save notes in multiple formats."""
        
        output_path = Path(output_dir)
        output_path.mkdir(parents=True, exist_ok=True)
        
        # Cornell format
        cornell = output_path / f"{base_name}_cornell.md"
        cornell.write_text(self.format_cornell(notes), encoding="utf-8")
        
        # Outline format
        outline = output_path / f"{base_name}_outline.md"
        outline.write_text(self.format_outline(notes), encoding="utf-8")
        
        # Mind map
        mindmap = output_path / f"{base_name}_mindmap.mmd"
        mindmap.write_text(self.format_mindmap(notes), encoding="utf-8")
        
        # JSON data
        json_file = output_path / f"{base_name}.json"
        json_file.write_text(json.dumps(notes, indent=2, ensure_ascii=False), encoding="utf-8")
        
        print(f"Saved notes to {output_path}")
        print(f"  - {cornell.name}")
        print(f"  - {outline.name}")
        print(f"  - {mindmap.name}")
        print(f"  - {json_file.name}")


def main():
    gen = CornellNotesGenerator(whisper_model="medium")
    notes = gen.generate(
        video_path="lecture.mp4",
        title="Introduction to Machine Learning",
        language="en",
        subject="Computer Science"
    )
    gen.save(notes, "./notes", "ml_lecture")


if __name__ == "__main__":
    main()
```

### Template 2: Study Guide Generator

```python
#!/usr/bin/env python3
"""Generate study guides from video lectures."""

from openai import OpenAI


def generate_study_guide(
    transcript: str,
    title: str = "Study Guide",
    include_quiz: bool = True,
    model: str = "gpt-4o"
) -> str:
    """Generate a comprehensive study guide."""
    
    client = OpenAI()
    
    response = client.chat.completions.create(
        model=model,
        messages=[
            {"role": "system", "content": """Create a comprehensive study guide from this lecture transcript.

Include:
1. **Overview**: Brief summary of the lecture
2. **Learning Objectives**: What students should know after this lecture
3. **Key Concepts**: Main ideas with definitions
4. **Important Details**: Supporting facts and examples
5. **Connections**: How concepts relate to each other
6. **Review Questions**: 5-10 questions for self-assessment
7. **Further Reading**: Suggested topics to explore

Format in clean markdown."""},
            {"role": "user", "content": f"Title: {title}\n\n{transcript[:10000]}"}
        ],
        max_tokens=3000,
        temperature=0.3
    )
    
    return response.choices[0].message.content


def generate_flashcards(
    transcript: str,
    count: int = 20,
    model: str = "gpt-4o"
) -> list:
    """Generate flashcards from transcript."""
    
    client = OpenAI()
    
    response = client.chat.completions.create(
        model=model,
        messages=[
            {"role": "system", "content": f"""Create {count} flashcards from this content.
Return a JSON array where each item has:
- "front": question or term
- "back": answer or definition
- "category": topic category

Return ONLY valid JSON."""},
            {"role": "user", "content": transcript[:8000]}
        ],
        max_tokens=3000,
        temperature=0.3
    )
    
    try:
        return json.loads(response.choices[0].message.content)
    except:
        return []
```

## Output Format Examples

### Cornell Notes
```markdown
# Introduction to Neural Networks

**Date**: 2024-03-15 | **Subject**: Machine Learning
**Source**: lecture_01.mp4

---

## Key Terms

- **Neuron**: Basic unit of a neural network that processes inputs
- **Activation Function**: Function that determines neuron output
- **Backpropagation**: Algorithm for training neural networks

## Notes

### What are Neural Networks?
- Computing systems inspired by biological neural networks
- Consist of layers of interconnected nodes (neurons)
- Learn patterns from data through training

**Examples:**
  - Image recognition (CNN)
  - Language processing (Transformer)

### Types of Neural Networks
- Feedforward: Simplest type, data flows one direction
- Recurrent (RNN): Has memory, good for sequences
- Convolutional (CNN): Specialized for grid-like data

## Study Questions (Cue Column)

1. What inspired the creation of neural networks?
2. What are the main types of neural networks?
3. How does backpropagation work?

## Summary

Neural networks are computing systems inspired by biological brains...
```

## Common Pitfalls

1. **Too Much Detail**
   - **Problem**: Notes are as long as the transcript
   - **Solution**: Focus on key points, not every sentence

2. **Missing Structure**
   - **Problem**: Notes are just a wall of text
   - **Solution**: Use clear headings, bullets, and sections

3. **No Review Questions**
   - **Problem**: Notes are passive, not interactive
   - **Solution**: Always include study questions

4. **Inconsistent Format**
   - **Problem**: Different styles in different sections
   - **Solution**: Use consistent formatting throughout

## Pro Tips

- **Use Obsidian**: Store notes with wikilinks for easy navigation
- **Add timestamps**: Link key points to video timestamps
- **Create flashcards**: Convert notes to spaced repetition cards
- **Review within 24h**: Reinforce learning with quick review
- **Connect concepts**: Draw relationships between topics

---

## 中文版本

### 适用场景

- 从大学讲座创建学习笔记
- 从在线课程（Coursera、edX、YouTube）做笔记
- 记录技术教程和研讨会
- 从会议演讲构建参考笔记
- 从纪录片内容组织信息
- 从教育视频准备复习材料

### 快速开始

```bash
pip install openai-whisper openai

python3 video_notes.py --video 讲座.mp4 --title "机器学习导论" --language zh
```

### 康奈尔笔记法

康奈尔笔记法将页面分为三个区域：
- **右侧主栏**：记录课堂笔记
- **左侧提示栏**：写下关键词和问题
- **底部总结栏**：课后总结要点

### 中文笔记技巧

1. 使用简洁的中文表述
2. 保留专业术语的英文原文
3. 使用缩写提高记录速度
4. 课后 24 小时内复习
