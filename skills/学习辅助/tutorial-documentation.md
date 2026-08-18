# Tutorial Documentation from Videos

Create written tutorials, how-to guides, and step-by-step documentation from video content. Transform video tutorials into searchable, scannable text documentation.

## When to Use

- Creating written documentation from video tutorials
- Building internal knowledge bases from training videos
- Generating README files from setup walkthroughs
- Creating step-by-step guides from coding tutorials
- Documenting software features from demo videos
- Building searchable documentation from YouTube tutorials

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [OpenAI Whisper](https://github.com/openai/whisper) | Latest | Tutorial transcription |
| [OpenAI API](https://platform.openai.com/) | GPT-4o | Documentation generation |

### Optional

| Tool | Purpose |
|------|---------|
| [ffmpeg](https://ffmpeg.org/) | Screenshot extraction |
| [Pillow](https://pillow.readthedocs.io/) | Image processing |
| [pandoc](https://pandoc.org/) | Format conversion |
| [MkDocs](https://www.mkdocs.org/) | Documentation site |

### Installation

```bash
# Core
pip install openai-whisper openai

# For screenshots
pip install Pillow
# macOS
brew install ffmpeg
# Ubuntu/Debian
sudo apt install ffmpeg

# For documentation site
pip install mkdocs mkdocs-material
```

## Step-by-Step Workflow

### Step 1: Transcribe Tutorial

```python
import whisper
model = whisper.load_model("medium")
result = model.transcribe("tutorial.mp4")
```

### Step 2: Extract Steps

```python
# Identify sequential steps in the tutorial
```

### Step 3: Extract Screenshots

```python
# Capture key frames at each step
```

### Step 4: Generate Documentation

```python
# Create structured tutorial document
```

## Code Templates

### Template 1: Tutorial Documentation Generator

```python
#!/usr/bin/env python3
"""Generate written tutorials from video content."""

import whisper
import json
import subprocess
from pathlib import Path
from openai import OpenAI
from datetime import datetime


class TutorialGenerator:
    """Generate step-by-step tutorials from videos."""
    
    def __init__(self, whisper_model: str = "medium", llm_model: str = "gpt-4o"):
        self.whisper = whisper.load_model(whisper_model)
        self.llm = OpenAI()
        self.llm_model = llm_model
    
    def generate(
        self,
        video_path: str,
        title: str = None,
        language: str = None,
        include_screenshots: bool = True,
        screenshot_interval: float = 30.0,
        audience: str = "intermediate"
    ) -> dict:
        """Generate tutorial documentation from video."""
        
        # Transcribe
        print("Transcribing tutorial...")
        result = self.whisper.transcribe(video_path, language=language)
        transcript = result["text"]
        segments = result["segments"]
        
        # Extract steps
        print("Extracting tutorial steps...")
        steps = self._extract_steps(transcript, audience)
        
        # Extract screenshots
        screenshots = []
        if include_screenshots:
            print("Extracting screenshots...")
            screenshots = self._extract_screenshots(
                video_path, segments, screenshot_interval
            )
        
        # Generate documentation
        print("Generating documentation...")
        documentation = self._generate_documentation(
            transcript, steps, title, audience
        )
        
        return {
            "metadata": {
                "title": title or steps.get("title", "Tutorial"),
                "source": video_path,
                "date": datetime.now().strftime("%Y-%m-%d"),
                "audience": audience,
                "duration": segments[-1]["end"] if segments else 0,
                "steps_count": len(steps.get("steps", []))
            },
            "documentation": documentation,
            "steps": steps,
            "screenshots": screenshots
        }
    
    def _extract_steps(self, transcript: str, audience: str) -> dict:
        """Extract tutorial steps from transcript."""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": f"""Extract tutorial steps from this transcript.
Target audience: {audience}

Return a JSON object with:
- "title": tutorial title
- "description": brief description
- "prerequisites": list of prerequisites
- "steps": array of steps, each with:
  - "number": step number
  - "title": step title
  - "description": what to do
  - "commands": any commands or code (if applicable)
  - "expected_result": what should happen
  - "tips": helpful tips (if any)
- "conclusion": wrap-up text

Return ONLY valid JSON."""},
                {"role": "user", "content": transcript[:10000]}
            ],
            max_tokens=3000,
            temperature=0.3
        )
        
        try:
            return json.loads(response.choices[0].message.content)
        except json.JSONDecodeError:
            return {"title": "Tutorial", "steps": [], "description": ""}
    
    def _extract_screenshots(
        self,
        video_path: str,
        segments: list,
        interval: float
    ) -> list:
        """Extract key screenshots from video."""
        
        output_dir = Path("./screenshots")
        output_dir.mkdir(exist_ok=True)
        
        screenshots = []
        duration = segments[-1]["end"] if segments else 0
        
        timestamp = 0
        while timestamp < duration:
            output_file = output_dir / f"screenshot_{timestamp:.0f}s.png"
            
            cmd = [
                "ffmpeg",
                "-ss", str(timestamp),
                "-i", video_path,
                "-frames:v", "1",
                "-q:v", "2",
                str(output_file),
                "-y"
            ]
            
            try:
                subprocess.run(cmd, capture_output=True, check=True)
                screenshots.append({
                    "timestamp": timestamp,
                    "file": str(output_file)
                })
            except subprocess.CalledProcessError:
                pass
            
            timestamp += interval
        
        print(f"Extracted {len(screenshots)} screenshots")
        return screenshots
    
    def _generate_documentation(
        self,
        transcript: str,
        steps: dict,
        title: str = None,
        audience: str = "intermediate"
    ) -> str:
        """Generate formatted tutorial documentation."""
        
        response = self.llm.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": f"""Create comprehensive tutorial documentation.
Target audience: {audience}

Format:
# [Title]

## Overview
Brief description of what this tutorial covers

## Prerequisites
- Required knowledge
- Required tools/software

## Steps

### Step 1: [Title]
Clear instructions with:
- What to do
- Code/commands (if applicable)
- Expected result
- Tips or warnings

[Continue for all steps]

## Troubleshooting
Common issues and solutions

## Summary
Key takeaways and next steps

Use clean markdown. Include code blocks for commands."""},
                {"role": "user", "content": f"Steps data:\n{json.dumps(steps, indent=2)}\n\nOriginal transcript:\n{transcript[:5000]}"}
            ],
            max_tokens=4000,
            temperature=0.3
        )
        
        return response.choices[0].message.content
    
    def save(self, result: dict, output_dir: str, base_name: str):
        """Save tutorial documentation."""
        
        output_path = Path(output_dir)
        output_path.mkdir(parents=True, exist_ok=True)
        
        # Save markdown documentation
        doc_file = output_path / f"{base_name}_tutorial.md"
        doc_file.write_text(result["documentation"], encoding="utf-8")
        
        # Save steps as JSON
        steps_file = output_path / f"{base_name}_steps.json"
        steps_file.write_text(
            json.dumps(result["steps"], indent=2, ensure_ascii=False),
            encoding="utf-8"
        )
        
        # Save metadata
        meta_file = output_path / f"{base_name}_metadata.json"
        meta_file.write_text(
            json.dumps(result["metadata"], indent=2, ensure_ascii=False),
            encoding="utf-8"
        )
        
        # Create MkDocs page if applicable
        self._create_mkdocs_page(result, output_path, base_name)
        
        print(f"Saved tutorial to {output_path}")
    
    def _create_mkdocs_page(self, result: dict, output_path: Path, base_name: str):
        """Create MkDocs-compatible documentation page."""
        
        # Add MkDocs frontmatter
        frontmatter = f"""---
title: {result['metadata']['title']}
date: {result['metadata']['date']}
audience: {result['metadata']['audience']}
---

"""
        
        mkdocs_file = output_path / f"{base_name}_mkdocs.md"
        mkdocs_file.write_text(
            frontmatter + result["documentation"],
            encoding="utf-8"
        )


def main():
    gen = TutorialGenerator(whisper_model="medium")
    
    result = gen.generate(
        video_path="python_tutorial.mp4",
        title="Python Web Scraping with BeautifulSoup",
        language="en",
        include_screenshots=True,
        audience="beginner"
    )
    
    gen.save(result, "./tutorials", "web_scraping")


if __name__ == "__main__":
    main()
```

### Template 2: How-To Guide Generator

```python
#!/usr/bin/env python3
"""Generate concise how-to guides from videos."""

from openai import OpenAI


def generate_howto_guide(
    transcript: str,
    title: str = None,
    model: str = "gpt-4o"
) -> str:
    """Generate a concise how-to guide."""
    
    client = OpenAI()
    
    response = client.chat.completions.create(
        model=model,
        messages=[
            {"role": "system", "content": """Create a concise how-to guide from this transcript.

Format:
# How to [Action]

**Time required**: X minutes
**Difficulty**: Easy/Medium/Hard

## What You'll Need
- Item 1
- Item 2

## Steps

1. **[Step title]**
   - What to do
   - Command: `command here`
   - Expected result

2. **[Step title]**
   ...

## Tips
- Pro tip 1
- Pro tip 2

## Common Mistakes
- Mistake: How to avoid/fix

Keep it concise and actionable."""},
            {"role": "user", "content": transcript[:6000]}
        ],
        max_tokens=2000,
        temperature=0.3
    )
    
    return response.choices[0].message.content


def generate_api_docs(
    transcript: str,
    model: str = "gpt-4o"
) -> str:
    """Generate API documentation from tutorial video."""
    
    client = OpenAI()
    
    response = client.chat.completions.create(
        model=model,
        messages=[
            {"role": "system", "content": """Extract API documentation from this tutorial transcript.

Format:
# API Reference: [API Name]

## Installation
```bash
pip install package-name
```

## Quick Start
```python
from package import api
# Basic usage example
```

## Methods

### `method_name(param1, param2)`
Description of the method.

**Parameters**:
- `param1` (type): Description
- `param2` (type): Description

**Returns**: Description of return value

**Example**:
```python
result = api.method_name("value1", "value2")
```

[Continue for all methods]

## Error Handling
Common errors and solutions."""},
            {"role": "user", "content": transcript[:8000]}
        ],
        max_tokens=3000,
        temperature=0.3
    )
    
    return response.choices[0].message.content
```

## Output Format Examples

### Tutorial Documentation
```markdown
# Python Web Scraping with BeautifulSoup

## Overview
Learn how to extract data from websites using Python and BeautifulSoup.

## Prerequisites
- Python 3.8+ installed
- Basic Python knowledge
- Text editor or IDE

## Steps

### Step 1: Install Required Packages
Install BeautifulSoup and requests using pip.

**Command**:
```bash
pip install beautifulsoup4 requests
```

**Expected result**: Packages installed successfully.

### Step 2: Fetch the Web Page
Use requests to download the HTML content.

**Command**:
```python
import requests
response = requests.get("https://example.com")
html = response.text
```

**Expected result**: HTML content stored in `html` variable.

## Troubleshooting
- **ConnectionError**: Check your internet connection
- **ModuleNotFoundError**: Ensure packages are installed

## Summary
You've learned how to scrape web pages using Python!
```

## Common Pitfalls

1. **Missing Code Blocks**
   - **Problem**: Commands are in plain text
   - **Solution**: Always use fenced code blocks

2. **No Expected Results**
   - **Problem**: User doesn't know if step worked
   - **Solution**: Include expected output for each step

3. **Outdated Commands**
   - **Problem**: Video shows old versions
   - **Solution**: Verify commands work with current versions

4. **Missing Context**
   - **Problem**: Steps assume too much knowledge
   - **Solution**: Include prerequisites and explanations

## Pro Tips

- **Test commands**: Verify every command works
- **Version info**: Note software versions used
- **Screenshots**: Include visual aids where helpful
- **Searchable**: Use clear headings for easy scanning
- **Link to source**: Reference the original video

---

## 中文版本

### 适用场景

- 从视频教程创建书面文档
- 从培训视频构建内部知识库
- 从设置演练生成 README 文件
- 从编码教程创建分步指南
- 从演示视频记录软件功能
- 从 YouTube 教程构建可搜索文档

### 快速开始

```bash
pip install openai-whisper openai ffmpeg Pillow

python tutorial_generator.py --video 教程.mp4 --title "Python 爬虫入门" --language zh
```

### 教程编写建议

1. **清晰的标题**：每个步骤使用动作性标题
2. **代码块**：所有命令和代码使用代码块格式
3. **预期结果**：每步都说明预期输出
4. **常见错误**：列出可能的问题和解决方案
5. **版本信息**：注明使用的软件版本
