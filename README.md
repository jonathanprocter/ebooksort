# EbookSort

AI-powered audiobook organizer, tagger, and metadata pipeline. Point it at a messy folder of audio files and it will build a clean Author/Series/Title library, fetch rich metadata from Google Books + Gemini, download high-quality cover art, and embed perfectly formatted tags into every file.

---

## ✨ Capabilities
- **Structure anything** – groups loose files into an Author ▶ Title hierarchy with optional staging so nothing is lost mid-run.
- **Dual metadata engines** – queries Google Books first, then falls back to Google Gemini for complex or obscure titles.
- **Smart cover art** – prioritizes embedded/local art, then searches iTunes and the web for the best square, high-resolution image.
- **Robust tagging** – writes ID3 / MP4 / FLAC tags (title, album, author, narrator, description, series, track numbers, cover art, etc.).
- **Maintenance toolkit** – validators, duplicate finders, cover auditors, inventory builders, and m4b packaging scripts.
- **Config-driven** – behavior controlled by `config.ini`; no code edits required for most workflows.

---

## 📁 Repository Layout
| Path | Description |
| --- | --- |
| `ebooksort.py` | Primary orchestrator. Organizes, fetches metadata/cover art, and writes tags. |
| `write_tags.py` | Retag existing libraries, fix covers, perform bulk edits. |
| `update_covers.py` | Audits/replaces cover art based on quality thresholds. |
| `validate_names.py` | Flags poorly named folders/files before processing. |
| `create_m4b.py` | Builds `.m4b` audiobooks with chapters (requires FFmpeg). |
| `generate_inventory.py` | Rebuilds `inventory.csv` from metadata. |
| `find_duplicates.py` | Detects duplicates via filesystem or metadata scans. |
| `extract_covers.py` | Pulls embedded cover art out of files. |
| `populate_authors.py` | Harvests author names into `known_authors.txt`. |
| `config_manager.py` | Shared configuration helper. |
| `config.ini.example` | Template for all runtime settings (copy to `config.ini`). |
| `requirements.txt` | Python dependencies. |
| `.gitignore` | Ignores private config, virtualenv, logs, etc. |

---

## 🚀 Quickstart
```bash
# 1. Clone & enter
git clone https://github.com/jonathanprocter/ebooksort.git
cd ebooksort

# 2. Create virtual environment & install deps
python3 -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install -r requirements.txt

# 3. Configure API keys & behavior
cp config.ini.example config.ini
$EDITOR config.ini  # add Gemini API key, tweak options

# 4. Run the organizer
env PYTHONPATH=. python ebooksort.py "/path/to/unorganized/audiobooks"
```

> **Heads up:** `config.ini` is ignored by Git on purpose. Never commit real API keys.

---

## ⚙️ Configuration Highlights (`config.ini`)
| Section | Key Settings |
| --- | --- |
| `[General]` | Supported audio/image extensions, default destination paths. |
| `[Gemini]` | API key, model (`gemini-1.5-flash` by default), system prompt. |
| `[Tagging]` | Tag templates, narrator formatting, comment/description sync. |
| `[Covers]` | Minimum resolution, square enforcement, preferred sources. |
| `[Validation]` | Word-count thresholds, junk-word lists for `validate_names.py`. |
| `[M4B]` | Bitrate and container settings for `create_m4b.py`. |

Every script reads from this file via `config_manager.py`, so once you set it up the entire toolkit shares the same behavior.

---

## 🛠️ Core Workflows
### 1. Organize & Tag (recommended)
```bash
python ebooksort.py "/input/library"
```
Flags:
- `-d / --destination_directory`: write to a different output root.
- `--dry-run`: print all planned actions without touching files.
- `--no-tagging`: skip writing tags (still reorganizes).
- `--force-gemini`: bypass Google Books and rely solely on Gemini.

### 2. Validate Names Beforehand
```bash
python validate_names.py "/input/library"
```
Finds folders/files missing both author and title context. Combine with `--authors-file` to point at a curated `known_authors.txt`.

### 3. Maintain an Existing Library
```bash
# Retag only new/changed books
python write_tags.py "/organized/library" --mode smart

# Fix covers everywhere
python update_covers.py "/organized/library"

# Build inventory report
python generate_inventory.py "/organized/library"
```

### 4. Create m4b Audiobooks
```bash
python create_m4b.py "/organized/library/Author/Book" --dry-run
```
Requires FFmpeg in your PATH.

---

## 💡 Tips
- **Stages safely:** The organizer uses a staging temp directory so partial runs dont corrupt data.
- **Dry runs are cheap:** Most scripts have a `--dry-run` to preview changes.
- **Inventory automation:** Schedule `generate_inventory.py` nightly to keep a fresh CSV snapshot.
- **Author hygiene:** Run `populate_authors.py` periodically so `validate_names.py` stays accurate.
- **Gemini budget:** The default prompt is verbose. Adjust tokens/model if you need lighter usage.

---

## 🤝 Contributing
1. Fork/clone the repo.
2. Follow the setup steps above.
3. Create a feature branch (`git checkout -b feature/something`).
4. Run formatting/linters if you add them.
5. Submit a pull request with a concise description of the change.

Bug reports & feature requests are welcome via GitHub issues.

---

## 📄 License
MIT License – see `LICENSE` (inherits from the original project author). Use it, fork it, build automations on top of it.

---

Have fun keeping your audiobook library flawless! If this saves you hours, star the repo ❤️
