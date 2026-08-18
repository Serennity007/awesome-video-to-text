# Lecture and Course Note-Taking

Create comprehensive study materials from educational videos using evidence-based learning techniques like active recall, spaced repetition, and the Cornell method.

## When to Use

- Taking notes from university lecture recordings
- Creating study guides from online courses
- Preparing for exams from video lectures
- Building course reference materials
- Generating quiz questions from lecture content
- Organizing semester-long course materials

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [OpenAI Whisper](https://github.com/openai/whisper) | Latest | Lecture transcription |
| [OpenAI API](https://platform.openai.com/) | GPT-4o | Note generation |

### Optional

| Tool | Purpose |
|------|---------|
| [Anki](https://apps.ankiweb.net/) | Spaced repetition flashcards |
| [Obsidian](https://obsidian.md/) | Linked note storage |
| [Pandoc](https://pandoc.org/) | Format conversion |
| [LaTeX](https://www.latex-project.org/) | PDF generation |

### Installation

```bash
# Core
pip install openai-whisper openai tiktoken

# For Anki flashcard generation
pip install genanki

# For format conversion
# macOS
brew install pandoc
# Ubuntu/Debian
sudo apt install pandoc
```

## Step-by-Step Workflow

### Step 1: Transcribe Lecture

```python
import whisper
model = whisper.load_model("medium")
result = model.transcribe("lecture.mp4", language="en")
```

### Step 2: Analyze Content Structure

```python
# Identify topics, definitions, examples, formulas
```

### Step 3: Generate Study Materials

```python
# Create notes, flashcards, quiz questions
```

### Step 4: Export for Review

```python
# Save in formats compatible with study tools
```

## Code Templates

### Template 1: Lecture Notes Generator

```python
#!/usr/bin/env python3
"""Generate comprehensive lecture notes with study materials."""

import whisper
import json
from pathlib import Path
from openai import OpenAI
from datetime import datetime
import genanki
import random


class LectureNotesGenerator:
    """Generate study materials from lecture recordings."""
    
    # Anki note model
    LECTURE_MODEL = genanki.Model(
        1607392319,
        'Lecture Notes',
        fields=[
            {'name': 'Question'},
            {'name': 'Answer'},
            {'name': 'Context'},
        ],
        templates=[{
            'name': 'Card 1',
            'qfmt': '<div class="question">{{Question}}</div>',
            'afmt': '{{FrontSide}}<hr><div class="answer">{{Answer}}</div><div class="context">{{Context}}</div>',
        }]
    )
    
    def __init__(self, whisper_model: str = "medium", llm_model: str = "gpt-4o"):
        self.whisper = whisper.load_model(whisper_model)
        self.llm = OpenAI()
        self.llm_model = llm_model
    
    def generate(
        self,
        video_path: str,
        course_name: str = None,
        lecture_title: str = None,
        language: str = None,
        include_flashcards: bool = True,
        include_quiz: bool = True
    ) -> dict:
        """Generate complete lecture notes and study materials."""
        
        # Transcribe
        print("Transcribing lecture...")
        result = self.whisper.transcribe(video_path, language=language)
        transcript = result["text"]
        segments = result["segments"]
        
        # Analyze content
        print("Analyzing content...")
        analysis = self._analyze_lecture(transcript, course_name, lecture_title)
        
        # Generate notes
        print("Generating notes...")
        notes = self._generate_notes(transcript, analysis)
        
        # Generate flashcards
        flashcards = []
        if include_flashcards:
            print("Creating flashcards...")
            flashcards = self._generate_flashcards(transcript)
        
        # Generate quiz
        quiz = []
        if include_quiz:
            print("Creating quiz questions...")
            quiz = self._generate_quiz(transcript)
        
        return {
            "metadata": {
                "course": course_name,
                "lecture": lecture_title or analysis.get("title", "Untitled"),
                "date": datetime.now().strftime("%Y-%m-%d"),
                "duration": segments[-1]["end"] if segments else 0,
                "source": video_path
            },
            "notes": notes,
            "flashcards": flashcards,
            "quiz": quiz,
            "analysis": analysis
        }
    
    def _analyze_lecture(self, transcript: str, course: str = None, title: str = None) -> dict:
        """Analyze lecture content structure."""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": """Analyze this lecture transcript and identify:
1. Main topic and title
2. Learning objectives
3. Key concepts with definitions
4. Important formulas or rules
5. Examples discussed
6. Connections to previous topics
7. Difficulty level

Return as JSON with these keys: title, objectives, concepts, formulas, examples, connections, difficulty."""},
                {"role": "user", "content": transcript[:8000]}
            ],
            max_tokens=2000,
            temperature=0.3
        )
        
        try:
            return json.loads(response.choices[0].message.content)
        except json.JSONDecodeError:
            return {"title": title or "Lecture Notes", "objectives": [], "concepts": []}
    
    def _generate_notes(self, transcript: str, analysis: dict) -> str:
        """Generate structured lecture notes."""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": """Create detailed lecture notes from this transcript. Include:

# [Lecture Title]

## Learning Objectives
- Clear, measurable objectives

## Key Concepts
For each concept:
- Definition
- Explanation
- Examples
- Common mistakes

## Detailed Notes
Organized by topic with:
- Main points
- Supporting details
- Examples and illustrations
- Formulas (if applicable)

## Summary
- Key takeaways
- Important connections
- What to review

Use clean markdown formatting."""},
                {"role": "user", "content": f"Analysis: {json.dumps(analysis)}\n\nTranscript:\n{transcript[:8000]}"}
            ],
            max_tokens=3000,
            temperature=0.3
        )
        
        return response.choices[0].message.content
    
    def _generate_flashcards(self, transcript: str, count: int = 20) -> list:
        """Generate Anki-compatible flashcards."""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": f"""Create {count} flashcards from this lecture content.
Each flashcard should test understanding, not just recall.

Return a JSON array where each item has:
- "question": clear, specific question
- "answer": concise answer
- "context": brief context or hint

Focus on:
- Key definitions
- Important concepts
- Cause and effect relationships
- Application of principles

Return ONLY valid JSON."""},
                {"role": "user", "content": transcript[:8000]}
            ],
            max_tokens=3000,
            temperature=0.3
        )
        
        try:
            return json.loads(response.choices[0].message.content)
        except json.JSONDecodeError:
            return []
    
    def _generate_quiz(self, transcript: str, count: int = 10) -> list:
        """Generate quiz questions for self-assessment."""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": f"""Create {count} quiz questions from this lecture.
Mix question types:
- Multiple choice (4 options)
- True/False with explanation
- Short answer
- Problem solving

Return a JSON array where each item has:
- "type": "multiple_choice", "true_false", "short_answer", or "problem"
- "question": the question text
- "options": array of options (for multiple choice)
- "answer": correct answer
- "explanation": why this is correct

Return ONLY valid JSON."""},
                {"role": "user", "content": transcript[:8000]}
            ],
            max_tokens=3000,
            temperature=0.3
        )
        
        try:
            return json.loads(response.choices[0].message.content)
        except json.JSONDecodeError:
            return []
    
    def export_anki(self, flashcards: list, output_file: str, deck_name: str = "Lecture Notes"):
        """Export flashcards as Anki deck."""
        
        deck_id = random.randrange(1 << 30, 1 << 31)
        deck = genanki.Deck(deck_id, deck_name)
        
        for card in flashcards:
            note = genanki.Note(
                model=self.LECTURE_MODEL,
                fields=[
                    card.get("question", ""),
                    card.get("answer", ""),
                    card.get("context", "")
                ]
            )
            deck.add_note(note)
        
        genanki.Package(deck).write_to_file(output_file)
        print(f"Anki deck saved: {output_file}")
    
    def save(self, result: dict, output_dir: str, base_name: str):
        """Save all generated materials."""
        
        output_path = Path(output_dir)
        output_path.mkdir(parents=True, exist_ok=True)
        
        # Save notes
        notes_file = output_path / f"{base_name}_notes.md"
        notes_file.write_text(result["notes"], encoding="utf-8")
        
        # Save flashcards as JSON
        cards_file = output_path / f"{base_name}_flashcards.json"
        cards_file.write_text(
            json.dumps(result["flashcards"], indent=2, ensure_ascii=False),
            encoding="utf-8"
        )
        
        # Save quiz
        quiz_file = output_path / f"{base_name}_quiz.json"
        quiz_file.write_text(
            json.dumps(result["quiz"], indent=2, ensure_ascii=False),
            encoding="utf-8"
        )
        
        # Export Anki deck
        if result["flashcards"]:
            anki_file = output_path / f"{base_name}_anki.apkg"
            self.export_anki(result["flashcards"], str(anki_file), base_name)
        
        # Save metadata
        meta_file = output_path / f"{base_name}_metadata.json"
        meta_file.write_text(
            json.dumps(result["metadata"], indent=2, ensure_ascii=False),
            encoding="utf-8"
        )
        
        print(f"Saved all materials to {output_path}")


def main():
    gen = LectureNotesGenerator(whisper_model="medium")
    
    result = gen.generate(
        video_path="lecture_01.mp4",
        course_name="CS229 - Machine Learning",
        lecture_title="Linear Regression",
        language="en",
        include_flashcards=True,
        include_quiz=True
    )
    
    gen.save(result, "./study_materials", "cs229_lecture_01")


if __name__ == "__main__":
    main()
```

## Output Format Examples

### Lecture Notes
```markdown
# Linear Regression

## Learning Objectives
- Understand the concept of linear regression
- Derive the normal equation
- Implement gradient descent for linear regression
- Evaluate model performance using R² score

## Key Concepts

### Linear Regression
**Definition**: A method to model the relationship between variables by fitting a linear equation.

**Formula**: y = θ₀ + θ₁x₁ + θ₂x₂ + ... + θₙxₙ

**Example**: Predicting house price from size and location.

### Cost Function
**Definition**: Measures how well the model fits the data.

**Formula**: J(θ) = (1/2m) Σ(hθ(xⁱ) - yⁱ)²

## Summary
- Linear regression finds the best-fit line through data points
- The normal equation provides a closed-form solution
- Gradient descent is an iterative optimization approach
```

## Common Pitfalls

1. **Too Much Detail**
   - **Problem**: Notes are overwhelming
   - **Solution**: Focus on key concepts, not every word

2. **No Active Recall**
   - **Problem**: Passive reading doesn't aid learning
   - **Solution**: Include questions and flashcards

3. **Missing Context**
   - **Problem**: Isolated facts without connections
   - **Solution**: Link to previous lectures and concepts

4. **No Review Schedule**
   - **Problem**: Notes are created but never reviewed
   - **Solution**: Use Anki for spaced repetition

## Pro Tips

- **Use active recall**: Test yourself, don't just re-read
- **Spaced repetition**: Review at increasing intervals (1, 3, 7, 14 days)
- **Connect concepts**: Build a concept map of the course
- **Practice problems**: Include worked examples in notes
- **Review within 24h**: Most effective for memory consolidation

---

## 中文版本

### 适用场景

- 从大学讲座录音做笔记
- 从在线课程创建学习指南
- 从视频讲座准备考试
- 构建课程参考材料
- 从讲座内容生成测验问题
- 组织学期课程材料

### 快速开始

```bash
pip install openai-whisper openai genanki

python lecture_notes.py --video 讲座.mp4 --course "机器学习" --language zh
```

### 学习方法建议

1. **康奈尔笔记法**：将笔记分为三栏
2. **主动回忆**：合上笔记，尝试回忆关键点
3. **间隔重复**：使用 Anki 在 1、3、7、14 天后复习
4. **概念图**：绘制概念之间的关系图
5. **练习题**：每章至少做 5 道练习题
