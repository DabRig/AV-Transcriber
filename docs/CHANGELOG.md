# Changelog

All notable changes to AV Transcriber. Newest first.

## 2.1.0 — Voice Memos and public release

- **New format: `.caf`** (Apple Core Audio Format), as saved by Voice Memos and
  other Apple apps. Tested with Apple Lossless, Opus, PCM and IMA4 audio inside
  CAF, through both engines.
- Public release: showcase README, example glossary, docs moved to `docs/`,
  PolyForm Noncommercial license.

## 2.0.1 — Windows engine fix

- **Fixed:** on Windows every file failed with
  `open() got an unexpected keyword argument 'metadata_errors'`. faster-whisper
  1.2.1 passes an option to PyAV that PyAV 19 removed. The app now hands the
  audio to the engine directly, so PyAV never reads audio.
- **Locked versions:** `constraints.txt` pins the tested library set; both setup
  scripts install with it, so a future library release can't break the app the
  same way.
- **GPU safety net:** a GPU check in a throwaway process on the first START of
  each session (a missing cuDNN/cuBLAS can otherwise crash the app), a float32
  fallback for cards without fast float16, a CPU fallback with the reason in the
  log, and a mid-file CPU retry if the GPU fails.
- Windows setup warns when the app folder is inside OneDrive.
- `tests/test_engine.py` runs the **real** faster-whisper engine end to end with a
  tiny model built locally (`tests/tiny_whisper.py`).

## 2.0.0 — AV Transcriber: glass, bigger type, Windows

- Renamed to **AV Transcriber**.
- **Glass UI:** a soft nebula behind blurred, tinted panels with lit rims.
- **Text size:** S / M / L / XL (M = +20% by default) plus ⌘/Ctrl +, −, 0;
  every size follows one setting. Dim text passes WCAG AA contrast. Layout
  adapts at every text size (container queries).
- **Windows 10/11:** `setup_windows.bat` (ffmpeg + Python via winget, NVIDIA CUDA
  engine auto-detected, Desktop and Start-menu shortcuts), `update_windows.bat`,
  Windows wording (File Explorer, Ctrl, PC), long-path support, no flashing
  console windows.
- **Fixed:** the Help pop-up's styles leaked into the Glossary tab and covered it.
- Help Center is a native `<dialog>`.

## 1.2 — Guided tour, Help Center

- **Fixed:** on a Mac, files failed with `No such file or directory: 'ffmpeg'` when
  the app was opened from Finder (apps started from Finder don't see Homebrew's
  folder). The engine now gets the audio directly.
- First-launch guided tour and a searchable Help Center: every control, editor
  guides (Premiere, CapCut, DaVinci, Final Cut, Word), tips, FAQ, troubleshooting.
- Keyboard shortcuts.

## 1.1 — Logo and space theme

- The logo, an animated starfield (pauses when hidden, respects Reduce motion),
  and a new app icon.

## 1.0 — The app

- Plugin-style window: queue rack, START/STOP with a live meter and time left,
  settings (quality knob, language, captions, switches, output folder, formats),
  glossary editor, transcript viewer, log.
- Private local engine: listens on 127.0.0.1 only, with a per-launch secret key.

## 0.3 — The engine

- Batch transcription of `.mov .mp4 .mkv .webm .wmv .mp3 .wav .flac .aac .m4a`.
- Streams big files (a 1.3 GB video uses ~50 MB of memory), probes every file,
  one bad file never stops the batch, resume after stopping, glossary, subtitles
  sized for editors (≤42 characters × 2 lines, ≤7 s), keep-awake, disk check.

---

## How it's tested

`python -m pytest -q` runs:

- the whole pipeline on generated clips of every format (including damaged files,
  silent clips, files with no audio, two-track files and odd file names), using a
  quick stand-in engine;
- the **real faster-whisper engine** with a tiny randomly-initialised model built
  on the fly (its words are gibberish by design; it proves the plumbing);
- the app's local server: security checks, queue, start/stop/resume, settings,
  glossary, viewer.

The Mac engine (mlx-whisper) was checked the same way with a tiny random MLX model.
Real-model accuracy and speed are checked by hand on Apple-silicon and NVIDIA machines.

## Known limits

- No speaker labels yet (who said what).
- On Windows, drag and drop works for files; use **＋ FOLDER** for folders.
- `.caf` files with AAC audio are read the same way as the tested codecs, but
  couldn't be generated for the automated tests.
