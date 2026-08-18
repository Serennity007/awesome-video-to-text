# Meeting and Interview Transcription

Transcribe meetings, interviews, and podcasts with speaker diarization, action item extraction, and structured output formats.

## When to Use

- Transcribing business meetings and standups
- Converting interview recordings to text
- Documenting podcast episodes
- Creating meeting minutes from recordings
- Extracting action items from team discussions
- Building searchable archives of conversations

## Tools and Software

### Required

| Tool | Version | Purpose |
|------|---------|---------|
| [OpenAI Whisper](https://github.com/openai/whisper) | Latest | Speech-to-text engine |
| [pyannote-audio](https://github.com/pyannote/pyannote-audio) | 3.0+ | Speaker diarization |

### Optional

| Tool | Purpose |
|------|---------|
| [whisperx](https://github.com/m-bain/whisperX) | Combined transcription + diarization |
| [resemblyzer](https://github.com/resemble-ai/Resemblyzer) | Speaker verification |
| [noisereduce](https://github.com/timsainb/noisereduce) | Audio noise reduction |
| [silero-vad](https://github.com/snakers4/silero-vad) | Voice activity detection |

### Installation

```bash
# Core tools
pip install openai-whisper pyannote.audio

# WhisperX (all-in-one, recommended)
pip install whisperx

# Audio preprocessing
pip install noisereduce pydub scipy numpy

# For pyannote, you need a HuggingFace token:
# 1. Create account at https://huggingface.co
# 2. Accept terms at https://huggingface.co/pyannote/speaker-diarization-3.1
# 3. Get token at https://huggingface.co/settings/tokens

# Set token
export HF_TOKEN="your_huggingface_token"
```

## Step-by-Step Workflow

### Step 1: Preprocess Audio

```bash
# Extract audio from video
ffmpeg -i meeting.mp4 -vn -acodec pcm_s16le -ar 16000 -ac 1 meeting.wav

# Reduce background noise (optional)
python -c "
import noisereduce as nr
import soundfile as sf
data, rate = sf.read('meeting.wav')
reduced = nr.reduce_noise(y=data, sr_rate=rate)
sf.write('meeting_clean.wav', reduced, rate)
"
```

### Step 2: Transcribe with Speaker Diarization

```bash
# Using WhisperX (recommended)
whisperx meeting.wav --model large-v3 --diarize --min_speakers 2 --max_speakers 10
```

### Step 3: Post-process Output

```python
# Clean up speaker labels, add timestamps, extract action items
```

## Code Templates

### Template 1: Full Meeting Transcription Pipeline

```python
#!/usr/bin/env python3
"""Complete meeting transcription with speaker diarization."""

import whisperx
import torch
from pathlib import Path
from datetime import timedelta


class MeetingTranscriber:
    """Transcribe meetings with speaker identification."""
    
    def __init__(
        self,
        model_size: str = "large-v3",
        device: str = None,
        hf_token: str = None
    ):
        self.device = device or ("cuda" if torch.cuda.is_available() else "cpu")
        self.model_size = model_size
        self.hf_token = hf_token
        
        print(f"Loading Whisper model ({model_size}) on {self.device}...")
        self.model = whisperx.load_model(model_size, self.device)
    
    def transcribe(
        self,
        audio_path: str,
        language: str = None,
        min_speakers: int = 2,
        max_speakers: int = 10
    ) -> dict:
        """Transcribe audio with speaker diarization."""
        
        print(f"Loading audio: {audio_path}")
        audio = whisperx.load_audio(audio_path)
        
        # Step 1: Transcribe
        print("Transcribing...")
        result = self.model.transcribe(audio, language=language, batch_size=16)
        detected_lang = result["language"]
        print(f"Detected language: {detected_lang}")
        
        # Step 2: Align timestamps
        print("Aligning timestamps...")
        model_a, metadata = whisperx.load_align_model(
            language_code=detected_lang,
            device=self.device
        )
        result = whisperx.align(
            result["segments"],
            model_a,
            metadata,
            audio,
            self.device,
            return_char_alignments=False
        )
        
        # Step 3: Speaker diarization
        if self.hf_token:
            print("Running speaker diarization...")
            diarize_model = whisperx.DiarizationPipeline(
                use_auth_token=self.hf_token,
                device=self.device
            )
            diarize_segments = diarize_model(
                audio,
                min_speakers=min_speakers,
                max_speakers=max_speakers
            )
            result = whisperx.assign_word_speakers(diarize_segments, result)
        
        return result
    
    def format_meeting_notes(self, result: dict) -> str:
        """Format transcription as meeting notes."""
        
        lines = []
        lines.append("# Meeting Transcript")
        lines.append(f"\n**Date**: {Path.cwd().name}")
        lines.append(f"**Segments**: {len(result['segments'])}")
        lines.append("")
        lines.append("## Transcript")
        lines.append("")
        
        current_speaker = None
        
        for segment in result["segments"]:
            speaker = segment.get("speaker", "Unknown")
            text = segment["text"].strip()
            start = self._format_time(segment["start"])
            
            if speaker != current_speaker:
                lines.append(f"\n### Speaker {speaker}")
                current_speaker = speaker
            
            lines.append(f"**[{start}]** {text}")
        
        return "\n".join(lines)
    
    def extract_action_items(self, result: dict) -> list:
        """Extract potential action items from transcript."""
        
        action_keywords = [
            "need to", "should", "will", "action item",
            "todo", "follow up", "by next", "deadline",
            "responsible for", "assigned to", "deliver",
            "需要", "应该", "待办", "跟进", "负责"
        ]
        
        action_items = []
        for segment in result["segments"]:
            text = segment["text"].lower()
            if any(keyword in text for keyword in action_keywords):
                speaker = segment.get("speaker", "Unknown")
                action_items.append({
                    "speaker": speaker,
                    "text": segment["text"].strip(),
                    "timestamp": self._format_time(segment["start"])
                })
        
        return action_items
    
    def save_output(self, result: dict, output_dir: str, base_name: str):
        """Save transcription in multiple formats."""
        
        output_path = Path(output_dir)
        output_path.mkdir(parents=True, exist_ok=True)
        
        # Save meeting notes
        notes = self.format_meeting_notes(result)
        notes_file = output_path / f"{base_name}_notes.md"
        with open(notes_file, "w", encoding="utf-8") as f:
            f.write(notes)
        
        # Save SRT with speakers
        srt_file = output_path / f"{base_name}.srt"
        with open(srt_file, "w", encoding="utf-8") as f:
            for i, seg in enumerate(result["segments"], 1):
                start = self._format_srt(seg["start"])
                end = self._format_srt(seg["end"])
                speaker = seg.get("speaker", "")
                text = seg["text"].strip()
                if speaker:
                    text = f"[{speaker}] {text}"
                f.write(f"{i}\n{start} --> {end}\n{text}\n\n")
        
        # Save action items
        actions = self.extract_action_items(result)
        if actions:
            actions_file = output_path / f"{base_name}_actions.md"
            with open(actions_file, "w", encoding="utf-8") as f:
                f.write("# Action Items\n\n")
                for item in actions:
                    f.write(f"- **[{item['timestamp']}]** ")
                    f.write(f"({item['speaker']}) {item['text']}\n")
        
        # Save plain text
        txt_file = output_path / f"{base_name}.txt"
        with open(txt_file, "w", encoding="utf-8") as f:
            for seg in result["segments"]:
                speaker = seg.get("speaker", "")
                prefix = f"[{speaker}] " if speaker else ""
                f.write(f"{prefix}{seg['text'].strip()}\n")
        
        print(f"Saved outputs to: {output_path}")
        print(f"  - {notes_file.name}")
        print(f"  - {srt_file.name}")
        print(f"  - {txt_file.name}")
        if actions:
            print(f"  - {actions_file.name} ({len(actions)} action items)")
    
    @staticmethod
    def _format_time(seconds: float) -> str:
        return str(timedelta(seconds=int(seconds)))
    
    @staticmethod
    def _format_srt(seconds: float) -> str:
        h = int(seconds // 3600)
        m = int((seconds % 3600) // 60)
        s = int(seconds % 60)
        ms = int((seconds % 1) * 1000)
        return f"{h:02d}:{m:02d}:{s:02d},{ms:03d}"


def main():
    transcriber = MeetingTranscriber(
        model_size="large-v3",
        hf_token="your_hf_token"
    )
    
    result = transcriber.transcribe(
        audio_path="meeting.wav",
        language="en",
        min_speakers=2,
        max_speakers=6
    )
    
    transcriber.save_output(result, "./meeting_output", "team_standup")


if __name__ == "__main__":
    main()
```

### Template 2: Interview Transcript Template

```python
#!/usr/bin/env python3
"""Generate structured interview transcripts."""

from datetime import timedelta
import json


INTERVIEW_TEMPLATE = """# Interview: {title}

**Date**: {date}
**Duration**: {duration}
**Interviewee**: {interviewee}
**Interviewer**: {interviewer}

---

## Summary

{summary}

---

## Full Transcript

{transcript}

---

## Key Quotes

{quotes}

---

## Topics Discussed

{topics}
"""


def generate_interview_transcript(
    segments: list,
    metadata: dict,
    output_file: str = "interview.md"
):
    """Generate a formatted interview transcript."""
    
    # Build transcript sections
    transcript_lines = []
    current_speaker = None
    
    for seg in segments:
        speaker = seg.get("speaker", "Unknown")
        time_str = str(timedelta(seconds=int(seg["start"])))
        
        if speaker != current_speaker:
            speaker_label = metadata.get("speakers", {}).get(speaker, speaker)
            transcript_lines.append(f"\n**{speaker_label}** ({time_str}):")
            current_speaker = speaker
        
        transcript_lines.append(seg["text"].strip())
    
    # Generate output
    content = INTERVIEW_TEMPLATE.format(
        title=metadata.get("title", "Untitled Interview"),
        date=metadata.get("date", "Unknown"),
        duration=str(timedelta(seconds=int(segments[-1]["end"]))),
        interviewee=metadata.get("interviewee", "Unknown"),
        interviewer=metadata.get("interviewer", "Unknown"),
        summary="[To be filled]",
        transcript="\n".join(transcript_lines),
        quotes="[To be extracted]",
        topics="[To be identified]"
    )
    
    with open(output_file, "w", encoding="utf-8") as f:
        f.write(content)
    
    print(f"Interview transcript saved: {output_file}")


# Meeting minutes template
MEETING_MINUTES_TEMPLATE = """# Meeting Minutes

## Meeting Details
- **Date**: {date}
- **Time**: {time}
- **Attendees**: {attendees}
- **Duration**: {duration}

## Agenda
{agenda}

## Discussion Summary
{discussion}

## Decisions Made
{decisions}

## Action Items
| # | Action | Owner | Deadline | Status |
|---|--------|-------|----------|--------|
{action_items}

## Next Meeting
- **Date**: {next_date}
- **Topics**: {next_topics}
"""
```

### Template 3: Real-time Transcription Setup

```python
#!/usr/bin/env python3
"""Real-time meeting transcription using microphone."""

import whisper
import numpy as np
import pyaudio
import threading
import queue
import time


class RealtimeTranscriber:
    """Transcribe audio in real-time from microphone."""
    
    def __init__(self, model_size: str = "base"):
        self.model = whisper.load_model(model_size)
        self.audio_queue = queue.Queue()
        self.is_running = False
        
        # Audio settings
        self.sample_rate = 16000
        self.chunk_duration = 5  # seconds
        self.chunk_size = self.sample_rate * self.chunk_duration
    
    def start(self):
        """Start real-time transcription."""
        self.is_running = True
        
        # Start audio capture thread
        capture_thread = threading.Thread(target=self._capture_audio)
        capture_thread.daemon = True
        capture_thread.start()
        
        # Start transcription thread
        transcribe_thread = threading.Thread(target=self._transcribe_loop)
        transcribe_thread.daemon = True
        transcribe_thread.start()
        
        print("Real-time transcription started. Press Ctrl+C to stop.")
        
        try:
            while self.is_running:
                time.sleep(0.1)
        except KeyboardInterrupt:
            self.is_running = False
            print("\nTranscription stopped.")
    
    def _capture_audio(self):
        """Capture audio from microphone."""
        p = pyaudio.PyAudio()
        stream = p.open(
            format=pyaudio.paFloat32,
            channels=1,
            rate=self.sample_rate,
            input=True,
            frames_per_buffer=1024
        )
        
        buffer = []
        while self.is_running:
            data = np.frombuffer(
                stream.read(1024, exception_on_overflow=False),
                dtype=np.float32
            )
            buffer.extend(data)
            
            if len(buffer) >= self.chunk_size:
                chunk = np.array(buffer[:self.chunk_size])
                buffer = buffer[self.chunk_size:]
                self.audio_queue.put(chunk)
        
        stream.stop_stream()
        stream.close()
        p.terminate()
    
    def _transcribe_loop(self):
        """Transcribe audio chunks."""
        while self.is_running:
            try:
                audio_chunk = self.audio_queue.get(timeout=1)
                result = self.model.transcribe(audio_chunk, language=None)
                text = result["text"].strip()
                
                if text:
                    timestamp = time.strftime("%H:%M:%S")
                    print(f"[{timestamp}] {text}")
                    
            except queue.Empty:
                continue


if __name__ == "__main__":
    transcriber = RealtimeTranscriber(model_size="base")
    transcriber.start()
```

## Output Format Examples

### Meeting Notes
```markdown
# Meeting Transcript

**Date**: 2024-03-15
**Segments**: 47

## Transcript

### Speaker A
**[0:00:00]** Good morning everyone. Let's start with the project update.
**[0:00:15]** Sarah, can you give us the status on the API integration?

### Speaker B
**[0:00:20]** Sure. We've completed the authentication module.
**[0:00:35]** The remaining work is the data sync pipeline.

### Speaker A
**[0:00:45]** Great. What's the timeline for that?
**[0:00:50]** We need it done by end of month.
```

### Action Items
```markdown
# Action Items

- **[0:05:30]** (Speaker A) We need to schedule a follow-up meeting for next week
- **[0:12:45]** (Speaker B) I'll prepare the technical documentation by Friday
- **[0:18:20]** (Speaker C) TODO: Review the budget proposal before the next meeting
```

## Common Pitfalls

1. **Overlapping Speech**
   - **Problem**: Multiple people talking at once confuses diarization
   - **Solution**: Use noise reduction and set appropriate `min_speakers`/`max_speakers`

2. **Background Noise**
   - **Problem**: Poor audio quality affects transcription accuracy
   - **Solution**: Pre-process with noisereduce:
     ```python
     import noisereduce as nr
     clean = nr.reduce_noise(y=audio, sr_rate=16000)
     ```

3. **Speaker Label Confusion**
   - **Problem**: Same speaker gets different labels
   - **Solution**: Provide known speaker count or post-process to merge labels

4. **Virtual Meeting Audio**
   - **Problem**: Poor quality from Zoom/Teams recordings
   - **Solution**: Record directly from platform if possible, use noise gate

5. **Accents and Dialects**
   - **Problem**: Strong accents cause transcription errors
   - **Solution**: Use `large-v3` model and provide a vocabulary prompt

## Pro Tips

- **Record at 16kHz mono WAV**: Best quality for speech recognition
- **Use WhisperX**: Combines transcription and diarization smoothly
- **Set speaker count**: Knowing the number of speakers improves accuracy
- **Create a vocabulary list**: Names and technical terms improve recognition
- **Use VAD**: Skip silence to speed up processing
- **Post-process with LLM**: Use GPT/Claude to clean up and summarize

---

## 中文版本

### 适用场景

- 转录商务会议和站会
- 将采访录音转换为文本
- 记录播客剧集
- 从录音创建会议纪要
- 从团队讨论中提取行动项
- 构建对话的可搜索存档

### 快速开始

```bash
# 安装
pip install whisperx noisereduce pydub

# 转录音频（带说话人识别）
whisperx meeting.wav --model large-v3 --diarize --language zh
```

### 中文会议处理提示

1. 使用 `large-v3` 模型以获得最佳中文识别效果
2. 指定 `--language zh` 避免语言检测错误
3. 对于方言重的发言者，可能需要后处理校对
4. 使用 LLM 清理和格式化输出
