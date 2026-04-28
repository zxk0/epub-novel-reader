# EPUB Novel Reader

> Convert any EPUB ebook into an HTML reader with table-of-contents navigation and Microsoft Azure Neural TTS voice narration.

[English](README_en.md) | [中文](README.md)

---

## Features

- **EPUB Parsing** — Extract all chapters with intact TOC structure
- **HTML Reader** — TOC navigation, full-text search, page controls, theme switching, reading progress memory
- **Edge TTS Narration** — Microsoft Azure Neural voices, natural Chinese, **completely free**
- **Local TTS Server** — Voice selection, speed control, automatic caching

## Supported Voices

| Voice | Style | Recommended For |
|-------|-------|----------------|
| Xiaoxiao (zh-CN-XiaoxiaoNeural) | Standard female | **Best for reading** |
| Xiaoyi (zh-CN-XiaoyiNeural) | Soft female | Romance novels |
| Yunxi (zh-CN-YunxiNeural) | Emotional male | Male perspective |
| Yunjian (zh-CN-YunjianNeural) | Clear male | Non-fiction |
| HiuGaai (zh-HK-HiuGaaiNeural) | Cantonese | Cantonese novels |
| HsiaoChen (zh-TW-HsiaoChenNeural) | Taiwanese | Taiwanese novels |
| Xiaoni (zh-CN-shaanxi-XiaoniNeural) | Shaanxi dialect | Fun novelty |
| Xiaobei (zh-CN-liaoning-XiaobeiNeural) | Northeastern | Fun novelty |

## Quick Start

### Requirements

- Python 3.11+
- `edge-tts` library

```bash
pip install edge-tts
```

### Method 1: WorkBuddy Skill (Recommended)

In WorkBuddy, just say:
```
Read the EPUB novel at xxx.epub
```

AI handles: parse → generate reader → start TTS server → open browser.

### Method 2: Manual

#### Step 1: Start TTS Server

```bash
cd scripts
python tts_server.py
```

Or double-click `start_tts.bat`.

> Server defaults to port **8766**. First synthesis needs internet (~10–30s), then auto-cached to `.tts_cache/`.

#### Step 2: Parse EPUB and Generate Reader

```bash
python extract_epub.py <epub_file> chapters.json
python build_reader.py chapters.json reader.html
```

#### Step 3: Start Reader HTTP Server

```bash
python -m http.server 8765 --directory .
```

#### Step 4: Open Reader

Visit: `http://127.0.0.1:8765/reader.html`

## Project Structure

```
epub-novel-reader/
├── README.md                    # This file
├── README_en.md                 # English version
├── SKILL.md                     # WorkBuddy Skill definition
├── start_tts.bat                # Windows quick-start TTS service
└── scripts/
    ├── extract_epub.py          # EPUB parsing
    ├── build_reader.py          # HTML reader generation
    ├── tts_server.py            # Edge TTS local proxy server
    └── .tts_cache/              # TTS audio cache (auto-generated, not uploaded)
```

## Core Scripts

### extract_epub.py

Parse EPUB, extract chapter text as JSON.

```bash
python extract_epub.py <epub_file> [output_json_path]
```

Output format:
```json
[
  {"id": 0, "title": "Chapter 1 Light in the Dark", "content": ["Para 1...", "Para 2..."]},
  ...
]
```

### build_reader.py

Generate HTML reader from chapter JSON.

```bash
python build_reader.py <chapters.json> <output_html_path>
```

### tts_server.py

Edge TTS local proxy server.

```bash
python tts_server.py [--port 8766]
```

API:
- `GET /voices` — List available voices
- `POST /tts` — body: `{text, voice, rate, volume}` → returns `audio/mpeg`
- `GET /tts?text=...&voice=...&rate=1.0` — Direct GET synthesis

## TTS Server Parameters

| Param | Description | Default |
|-------|-------------|---------|
| `text` | Text to synthesize | Required |
| `voice` | Voice name | zh-CN-XiaoxiaoNeural |
| `rate` | Speed (0.5–2.0) | 1.0 |
| `volume` | Volume (0.0–1.0) | 1.0 |

## Notes

1. **First TTS synthesis is slow** — needs internet, then auto-cached
2. **Port conflict** — make sure port 8766 is free before starting
3. **Multiple Python versions** — on Windows `python` may point to uv-managed 3.11
4. **Cache cleanup** — delete `.tts_cache/` to clear all cache

## Tech Stack

- Python 3.11+
- [edge-tts](https://github.com/rany2/edge-tts) — Microsoft Edge TTS Python wrapper
- HTML5 + CSS3 + JavaScript — Reader frontend
- Python http.server — Built-in HTTP server

## License

MIT License
