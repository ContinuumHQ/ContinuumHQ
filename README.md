# doc_writer

Automated technical documentation writer for software repositories.

Generates ISO/IEC 26514-structured Markdown from a live Git repository on every CI/CD run. No manual documentation maintenance required.

---

## Output

| File | Description |
|---|---|
| `docs/TECHNICAL_DOSSIER.md` | Full dossier — overwritten each run |
| `docs/CHANGELOG.md` | Append-only log per CI run |
| `docs/module_status.json` | Machine-readable state snapshot |

---

## Requirements

- Python 3.9+
- Git in `PATH`
- `tomli` (Python < 3.11 only): `pip install tomli`

No other dependencies. Uses only the standard library + Git CLI.

---

## Installation

```bash
# Place in your repository
cp doc_writer.py docs/doc_writer.py

# Optional: create config file from template
cp doc_writer.toml.example doc_writer.toml
# Edit doc_writer.toml — do NOT commit if it contains sensitive values
```

---

## Usage

```bash
# Full run: dossier + changelog + JSON
python docs/doc_writer.py --mode full

# Append changelog entry only
python docs/doc_writer.py --mode changelog

# JSON status snapshot only
python docs/doc_writer.py --mode status

# Explicit config path
python docs/doc_writer.py --config /path/to/doc_writer.toml
```

---

## GitHub Actions Integration

Add as a post-step to any job:

```yaml
- name: Update documentation
  if: always()
  run: python docs/doc_writer.py --mode changelog
```

For a full dossier rebuild (e.g. monthly):

```yaml
- name: Rebuild Technical Dossier
  run: python docs/doc_writer.py --mode full
```

In CI, the script automatically commits and pushes the updated `docs/` directory. Locally it writes the files only.

---

## Configuration

All project-specific values are read from `doc_writer.toml` in the repository root. If no config file is present, the script runs with generic defaults derived from the repository itself.

See `doc_writer.toml.example` for all available keys:

- `project_name`, `doc_id`, `responsible`, `contact`, `repo_url`
- `modules` — submodules or directories to track
- `core_files` — individual files to track with size/mtime
- `tiers`, `phases`, `open_items` — optional structured sections
- `disclaimer` — appended as a final section

---

## Doc-ID Versioning

`doc_id` in `doc_writer.toml` follows the pattern:

```
DOC-YYYY-NNN-REV-X
```

Increment `REV-X` (A, B, C…) for each significant schema change. The script does not auto-increment — versioning is a deliberate decision.

---

## Standards

- **ISO/IEC 26514** — Software and systems engineering: documentation
- **ISO-8601** — Date and time format (all timestamps in UTC)

---

## License

MIT License. See [LICENSE](LICENSE).
