# 📖 EPUB Novel Reader

> 将任意 EPUB 电子书转换为带目录导航的 HTML 阅读器，并支持微软 Azure Neural 语音（TTS）自然朗读。

[English](#english) | [中文](#中文)

---

## ✨ 核心功能

- **📚 EPUB 解析** — 提取全部章节，保留目录结构
- **🌐 HTML 阅读器** — 带目录导航、全文搜索、翻页控制、主题切换、阅读进度记忆
- **🔊 Edge TTS 语音朗读** — 微软 Azure Neural 语音，中文自然度高，**完全免费**
- **🎛️ 本地 TTS 服务器** — 支持语音选择、语速调节、自动缓存

## 🗣️ 支持的语音

| 语音 | 风格 | 推荐场景 |
|------|------|---------|
| 晓晓（zh-CN-XiaoxiaoNeural） | 标准女声 | **阅读首选** |
| 晓伊（zh-CN-XiaoyiNeural） | 柔和女声 | 情感小说 |
| 云希（zh-CN-YunxiNeural） | 情感男声 | 男性视角 |
| 云健（zh-CN-YunjianNeural） | 清晰男声 | 知识类 |
| 香港-晓积（zh-HK-HiuGaaiNeural） | 粤语 | 粤语小说 |
| 台湾-晓臻（zh-TW-HsiaoChenNeural） | 台语 | 台语小说 |
| 陕西话-小妮（zh-CN-shaanxi-XiaoniNeural） | 陕西方言 | 趣味 |
| 东北话-小北（zh-CN-liaoning-XiaobeiNeural） | 东北话 | 趣味 |

## 🚀 快速开始

### 环境要求

- Python 3.11+
- `edge-tts` 库

```bash
pip install edge-tts
```

### 方式一：使用 WorkBuddy Skill（推荐）

在 WorkBuddy 中直接说：
```
阅读 EPUB 小说《xxx.epub》
```

AI 会自动完成解析 → 生成阅读器 → 启动 TTS 服务器 → 打开浏览器。

### 方式二：手动运行

#### Step 1：启动 TTS 服务器

```bash
cd scripts
python tts_server.py
```

或双击 `start_tts.bat`

> 服务器默认占用端口 **8766**，首次合成需要联网等待（约10-30秒），之后自动缓存到 `.tts_cache/` 目录。

#### Step 2：解析 EPUB 并生成阅读器

```bash
# 提取章节
python extract_epub.py <epub文件路径> chapters.json

# 生成 HTML 阅读器
python build_reader.py chapters.json reader.html
```

#### Step 3：启动阅读器 HTTP 服务器

```bash
python -m http.server 8765 --directory .
```

#### Step 4：打开阅读器

访问：`http://127.0.0.1:8765/reader.html`

## 📁 项目结构

```
epub-novel-reader/
├── README.md                    # 本文件
├── SKILL.md                     # WorkBuddy Skill 定义
├── start_tts.bat                # Windows 快速启动 TTS 服务
└── scripts/
    ├── extract_epub.py          # EPUB 解析脚本
    ├── build_reader.py          # HTML 阅读器生成脚本
    ├── tts_server.py            # Edge TTS 本地代理服务器
    └── .tts_cache/              # TTS 音频缓存（自动生成，不上传）
```

## 🛠️ 核心脚本

### extract_epub.py

解析 EPUB 文件，提取章节文本为 JSON。

```bash
python extract_epub.py <epub文件路径> [输出JSON路径]
```

输出格式：
```json
[
  {"id": 0, "title": "第一章 黑暗中的光", "content": ["第一段...", "第二段..."]},
  ...
]
```

### build_reader.py

从章节 JSON 生成 HTML 阅读器。

```bash
python build_reader.py <chapters.json> <输出HTML路径>
```

### tts_server.py

Edge TTS 本地代理服务器。

```bash
python tts_server.py [--port 8766]
```

API 接口：
- `GET /voices` — 返回可用语音列表
- `POST /tts` — body: `{text, voice, rate, volume}` → 返回 `audio/mpeg`
- `GET /tts?text=...&voice=...&rate=1.0` — 直接 GET 合成

## ⚙️ TTS 服务器参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `text` | 要合成的文本 | 必填 |
| `voice` | 语音名称 | zh-CN-XiaoxiaoNeural |
| `rate` | 语速（0.5-2.0） | 1.0 |
| `volume` | 音量（0.0-1.0） | 1.0 |

## ⚠️ 注意事项

1. **首次 TTS 合成较慢** — 需要联网请求，之后自动缓存
2. **端口冲突** — 启动前确保端口 8766 未被占用
3. **多 Python 版本** — Windows 下 `python` 可能指向 uv 管理的 3.11，需明确指定版本
4. **缓存清理** — 删除 `.tts_cache/` 目录可清除所有缓存

## 🔧 技术栈

- Python 3.11+
- [edge-tts](https://github.com/rany2/edge-tts) — 微软 Edge TTS Python 接口
- HTML5 + CSS3 + JavaScript — 阅读器前端
- HTTP Server — 内置 Python http.server

## 📝 License

MIT License

---

## English

### EPUB Novel Reader

Convert any EPUB ebook to an HTML reader with table-of-contents navigation and Microsoft Azure Neural TTS voice narration.

**Features:** EPUB parsing, HTML reader with TOC/search/themes, Edge TTS voice synthesis, local TTS proxy server

**Quick Start:**
```bash
pip install edge-tts
cd scripts && python tts_server.py
python extract_epub.py book.epub chapters.json
python build_reader.py chapters.json reader.html
python -m http.server 8765
# Open http://127.0.0.1:8765/reader.html
```
