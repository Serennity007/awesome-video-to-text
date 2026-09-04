# OrkasVideoStudio Transcript-Driven Editing

> Transcribe local footage, make evidence-based cuts, and deliver reviewed captions with an editable video workflow.

## When to Use

- Turn an interview, podcast, lecture, or talking-head recording into a tighter cut.
- Create a timed transcript before deciding which sections to remove.
- Remove silence or filler words while preserving speaker meaning.
- Produce reviewed SRT or ASS captions and burn them into a delivery copy.
- Keep the edit reproducible through local CLI operations and recorded evidence.
- Use the same capabilities from a coding agent through CLI or MCP.

Do not use automatic cuts without reviewing the transcript and the resulting media.

## Tools and Software

### Required

- Node.js 22 or newer.
- FFmpeg and ffprobe available on `PATH`.
- Git and pnpm for the current source-install path.
- OrkasVideoStudio built from its canonical GitHub repository.

### Installation

```bash
git clone https://github.com/Orkas-AI/Orkas-VideoStudio.git
cd Orkas-VideoStudio
pnpm install
pnpm build
node packages/cli/dist/index.js doctor
```

Create an alias while working inside the repository:

```bash
alias ovs="node $PWD/packages/cli/dist/index.js"
ovs doctor
```

The npm packages are still being published, so this skill uses the working source-install path.

## Step-by-Step Workflow

### Step 1: Inspect the source

```bash
ovs edit probe interview.mp4
ovs quality interview.mp4
```

Record duration, resolution, frame rate, audio presence, and any quality flags.
Do not begin automated cutting if the file is incomplete or its audio is unusable.

### Step 2: Create a timed transcript

```bash
ovs transcribe interview.mp4 --language en --out transcript.json
```

For non-English speech, use an appropriate language code and review the selected model.
The output should retain word or segment timing needed for evidence-based edits.

### Step 3: Review the transcript

Check the following before any cut:

- speaker names and proper nouns;
- numbers, dates, URLs, and product names;
- sentence boundaries and punctuation;
- overlapping speech and music;
- intentional pauses that should be preserved;
- filler words whose removal would change meaning or cadence.

### Step 4: Make conservative cuts

Create a silence-trimmed version:

```bash
ovs edit trim-silence interview.mp4 \
  --min-silence 0.7 \
  --pad 0.12 \
  --out interview-tight.mp4
```

Or remove common filler words using the reviewed transcript:

```bash
ovs edit remove-fillers interview.mp4 \
  --transcript transcript.json \
  --fillers um,uh,erm \
  --pad 0.08 \
  --out interview-clean.mp4
```

Read the returned evidence and compare the output with the source around every cut.

### Step 5: Prepare captions

Convert the reviewed transcript into SRT or ASS using the project's caption workflow.
Keep captions as a separate text artifact until wording and timing are approved.
Prefer short readable lines and avoid covering important visual content.

### Step 6: Burn a delivery copy

```bash
ovs edit burnsubs interview-clean.mp4 \
  --srt subtitles.srt \
  --out interview-captioned.mp4
```

Preserve an unburned master when the destination may need alternate languages or styles.

### Step 7: Normalize and verify audio

```bash
ovs edit loudness interview-captioned.mp4
ovs edit normalize-loudness interview-captioned.mp4 --out final.mp4
ovs edit loudness final.mp4
ovs quality final.mp4
```

Watch the finished file from start to finish before delivery.

## Code Templates

### Template 1: English interview cleanup

```bash
ovs transcribe source.mp4 --language en --out transcript.json
ovs edit remove-fillers source.mp4 \
  --transcript transcript.json \
  --fillers um,uh,erm \
  --pad 0.08 \
  --out clean.mp4
ovs quality clean.mp4
```

### Template 2: Local caption delivery

```bash
ovs edit burnsubs clean.mp4 --srt subtitles.srt --out captioned.mp4
ovs edit normalize-loudness captioned.mp4 --out final.mp4
ovs quality final.mp4
```

### Template 3: Silence-only tightening

```bash
ovs edit trim-silence lecture.mp4 \
  --noise-db -40 \
  --min-silence 0.8 \
  --pad 0.15 \
  --min-keep 0.25 \
  --out lecture-tight.mp4
```

Use longer silence thresholds for lectures and interviews with deliberate pacing.

## Output Format Examples

### Edit report

```markdown
Source: interview.mp4
Transcript: transcript.json
Edit method: remove-fillers
Cut policy: um, uh, erm; 0.08-second padding
Caption master: subtitles.srt
Delivery copy: final.mp4
Review: all cuts and caption names checked
Limitations: [none or concise list]
```

### Caption QA report

```markdown
- Language: English
- Format: SRT
- Proper nouns checked: yes
- Numbers and dates checked: yes
- Reading speed checked: yes
- Line breaks checked: yes
- Burned copy reviewed: yes
```

## Common Pitfalls

1. **Cutting every detected silence**
   - Solution: preserve intentional pauses and use conservative thresholds.
2. **Removing fillers without word timings**
   - Solution: create and review `transcript.json` first.
3. **Changing speaker meaning**
   - Solution: compare the source around every proposed cut.
4. **Burning captions before proofreading**
   - Solution: approve the text artifact before rendering the delivery copy.
5. **Overlong caption lines**
   - Solution: split at phrase boundaries and keep reading time reasonable.
6. **Discarding the clean master**
   - Solution: keep an unburned video and separate caption file.
7. **Claiming npm installation is available**
   - Solution: use the canonical source-install instructions until publication.

## Pro Tips

- Run `probe` and `quality` before transcription so source defects are visible early.
- Use large or multilingual transcription models only when the content needs them.
- Keep cut evidence with the project so a reviewer can restore any disputed phrase.
- Normalize loudness after caption rendering and final assembly.
- Store captions separately even when the final platform requires burned subtitles.
- Use ASS when detailed styling is required and SRT for broad compatibility.
- Recheck the first and last caption after every timeline-changing edit.
- Deliver the transcript, caption source, and edit report with the final media.

## 中文版本

### 适用场景

适合访谈、播客、课程和口播视频的本地转写、证据化删减、字幕制作与交付。
工作流通过 CLI 或 MCP 调用，并保留可复核的转写和剪辑证据。

### 操作步骤

1. 使用 `probe` 和 `quality` 检查原始视频。
2. 使用 `transcribe` 输出带时间信息的 `transcript.json`。
3. 复核人名、数字、专有名词、断句和停顿。
4. 选择 `trim-silence` 或 `remove-fillers`，并使用保守参数。
5. 逐个检查剪切点，确认没有改变说话者原意。
6. 生成并校对 SRT 或 ASS 字幕。
7. 使用 `burnsubs` 输出带字幕版本，同时保留无硬字幕母版。
8. 标准化音量，检查画面质量并完整播放成片。

### 交付清单

- [ ] 最终视频可完整播放。
- [ ] 转写文本已复核。
- [ ] 所有自动剪切点已检查。
- [ ] 字幕人名、数字和时间轴准确。
- [ ] 保留无硬字幕母版。
- [ ] 提供字幕源文件和剪辑报告。

## Source and License

Adapted from the OrkasVideoStudio editing and routing skills at immutable commit
[`d5197ab5e625fc44ab719a940711d82b46c16ef5`](https://github.com/Orkas-AI/Orkas-VideoStudio/tree/d5197ab5e625fc44ab719a940711d82b46c16ef5/packages/skills).
The upstream project and this adaptation are MIT-licensed.
