# Video to Obsidian

Drop course, meeting, and interview videos into a queue; they are transcribed on your Mac and turned into Markdown notes inside your Obsidian vault—fully offline, no uploads.

[简体中文](./README.md)

## What it is

A local transcription pipeline for Apple Silicon Macs, with a console page bound only to `127.0.0.1:8765`.

It breaks "process a pile of videos" into a resumable pipeline: discover → extract audio → Whisper transcription → compose the note → write to Obsidian. Failed tasks never disappear silently, and notes you have edited are never overwritten.

## What it does

- **Batch queue**: add multiple local audio/video files and watch per-task progress and state.
- **On-device transcription**: `mlx_whisper` runs locally on Apple Silicon; no cloud endpoint.
- **Markdown output**: transcripts are composed into a structured note and written to the chosen Obsidian folder.
- **No-Clobber**: existing target notes are not overwritten by default; conflicts are surfaced before any decision.
- **Explainable failures**: environment missing, unreadable media, transcription error, composition error, vault write blocked, conflict, and transient errors are classified separately instead of all being retried the same way.
- **Resumable**: task state is persisted, so restarting the console continues rather than starting over.
- **Live console**: a plain web page showing listener state, current tasks, blocked tasks, recovery/retry, and completed history.

## Run it

Requires an Apple Silicon Mac, Python 3.12, and `ffmpeg` (`brew install ffmpeg`).

```bash
python3.12 -m venv .venv
source .venv/bin/activate
pip install mlx-whisper          # install any remaining dependencies per docs
./app/start.sh                   # start the console
open http://127.0.0.1:8765/
```

Choose a source folder and an Obsidian target folder in the console, confirm, and add to the queue. Real video folders and vaults are only read/write targets; the repository contains no media or notes.

### Tests

```bash
python3 -m pytest tests
```

## Data and privacy

- Transcription happens on-device; neither audio nor text leaves the machine.
- The console listens on loopback only, so other devices on the LAN cannot reach it by default.
- The repository, tests, and examples contain no real videos, notes, or client information.

## Known limitations

- Apple Silicon only (it depends on `mlx_whisper`); the `windows/` directory is a separate implementation line.
- Runtime and memory for long videos lack a complete baseline; try a small batch before scaling up.
- If a source file is moved or deleted, the task stops at "source path missing" and needs manual re-binding.

## Layout

```text
app/            # local console page and Python server (stdlib only)
src/stage1..10  # pipeline stages: discover, extract, transcribe, compose, write
tests/          # pipeline and state-machine tests
docs/           # plans, reviews, QA, handoff, governance
```

See `docs/pm/PRODUCT_PLAN.md` for scope and current state, and `docs/review/RESEARCH_REVIEW.md` for technical review.
