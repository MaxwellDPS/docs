# 📚 Documentation

> 🤖 **ROBO CODED** — This documentation was made with AI and may not be 100% sane. But the code does work! 🎉

This directory contains the documentation for the SIP AI Assistant.

---

## 📁 Directory Structure

```
docs/
├── 📄 README.md              # 👈 You are here
├── 📄 index.md               # 📖 Overview & architecture
├── 📄 getting-started.md     # 🚀 Installation guide
├── 📄 configuration.md       # ⚙️ Environment variables
├── 📄 api-reference.md       # 🌐 REST API endpoints
├── 📄 tools.md               # 🔧 Built-in tools
├── 📄 plugins.md             # 🔌 Creating custom plugins
├── 📄 examples.md            # 📖 Integration examples
└── 📂 screenshots/           # 🖼️ Documentation images
    └── 📄 README.md          # 📋 Screenshot checklist
```

---

## 🔄 Syncing to ReadMe.io

### Option 1: 🤖 GitHub Actions (Recommended)

Automatic sync on every push to `main`:

**Step 1: Get your ReadMe API Key**

```
ReadMe Dashboard → Configuration → API Keys → Copy
```

**Step 2: Add GitHub Secrets**

```bash
# Navigate to:
GitHub Repo → Settings → Secrets → Actions → New repository secret
```

| Secret Name | Value |
|-------------|-------|
| `README_API_KEY` | Your ReadMe API key |
| `README_API_DEFINITION_ID` | OpenAPI definition ID (optional) |

**Step 3: Create Categories in ReadMe**

Before syncing, create these categories in your ReadMe dashboard:

| URI | Display Name | Pages |
|-----|--------------|-------|
| `overview` | 📖 Overview | `index.md` |
| `setup` | 🚀 Setup | `getting-started.md`, `configuration.md` |
| `api` | 🌐 API | `api-reference.md` |
| `features` | ✨ Features | `tools.md` |
| `development` | 🔧 Development | `plugins.md` |
| `guides` | 📚 Guides | `examples.md` |

**Step 4: Push to main**

```bash
git add docs/
git commit -m "📚 Update documentation"
git push origin main
```

The workflow at `.github/workflows/readme-sync.yml` will automatically sync! ✨

**Workflow status:**

```
┌─────────────────────────────────────────────────────────────┐
│ 🔄 GitHub Actions: readme-sync                              │
├─────────────────────────────────────────────────────────────┤
│ ✅ Trigger: Push to main (docs/**)                         │
│ ✅ Job 1: sync-docs                                         │
│    └─ rdme docs upload ./docs                              │
│ ✅ Job 2: sync-openapi                                      │
│    └─ rdme openapi upload ./openapi.json                   │
└─────────────────────────────────────────────────────────────┘
```

---

### Option 2: 💻 CLI (Manual)

```bash
# Install rdme CLI
npm install -g rdme

# Login to ReadMe
rdme login
```

**Sync documentation:**

```bash
rdme docs upload ./docs --branch=main
```

**Expected output:**

```
🚀 Uploading docs...
✅ index.md → overview
✅ getting-started.md → setup  
✅ configuration.md → setup
✅ api-reference.md → api
✅ tools.md → features
✅ plugins.md → development
✅ examples.md → guides
📚 7 pages synced successfully!
```

**Sync OpenAPI spec:**

```bash
# Generate from running server
curl http://localhost:8080/openapi.json > openapi.json

# Upload to ReadMe
rdme openapi upload ./openapi.json --key=YOUR_API_KEY
```

---

### Option 3: 🔗 Bi-directional Sync

If using ReadMe Refactored:

1. **Connect Repository**
   ```
   ReadMe Dashboard → Configuration → GitHub Sync
   ```

2. **Select Directory**
   ```
   Repository: your-org/sip-agent
   Directory: docs/
   Branch: main
   ```

3. **Enable Sync**
   - ✅ Push changes from GitHub to ReadMe
   - ✅ Push changes from ReadMe to GitHub

Changes sync automatically in both directions! 🔄

---

## 📝 Frontmatter Format

Each markdown file uses this format for ReadMe.io (rdme v10):

```yaml
---
title: "Page Title"
excerpt: "Short description for SEO"
category:
  uri: category-slug
slug: page-slug
---

> 🤖 **ROBO CODED** — This documentation was made with AI...

# 📖 Page Title

Content here...
```

**Category URIs:**

| Category | URI |
|----------|-----|
| Overview | `overview` |
| Setup | `setup` |
| API | `api` |
| Features | `features` |
| Development | `development` |
| Guides | `guides` |

---

## 🖼️ Screenshots

Screenshots are stored in the `screenshots/` directory.

**Quick stats:**

```
📊 Screenshots Required: 19
📁 Location: docs/screenshots/
📐 Min Resolution: 1200px wide
🎨 Format: PNG (UI), GIF (animations)
```

See `screenshots/README.md` for the full checklist.

**Adding a screenshot:**

```bash
# 1. Take screenshot
# 2. Save to docs/screenshots/
# 3. Reference in markdown:

![Description](screenshots/filename.png)
<!-- TODO: What to capture -->
```

---

## ✏️ Contributing to Docs

### 🔧 Local Development

```bash
# Option 1: MkDocs preview
pip install mkdocs mkdocs-material
mkdocs serve
# Open http://localhost:8000

# Option 2: VS Code preview
# Install "Markdown Preview Enhanced" extension
# Press Ctrl+Shift+V to preview
```

### 📋 Style Guide

| Element | Format |
|---------|--------|
| **Headers** | Use emojis: `# 🚀 Getting Started` |
| **Code blocks** | Include expected output |
| **Tables** | Use for quick reference |
| **Diagrams** | Mermaid for architecture |
| **Screenshots** | Add TODO placeholder |

### 🚀 Submitting Changes

```bash
# 1. Create branch
git checkout -b docs/update-xyz

# 2. Make changes
nano docs/getting-started.md

# 3. Commit with emoji
git commit -m "📚 docs: update getting started guide"

# 4. Push and PR
git push origin docs/update-xyz
```

**Commit prefixes:**

| Prefix | Use |
|--------|-----|
| `📚 docs:` | Documentation changes |
| `🖼️ screenshots:` | New/updated screenshots |
| `🔧 fix:` | Fix typos or errors |

---

## 📊 OpenAPI Sync

To sync the interactive API reference:

**Generate spec from running server:**

```bash
curl http://localhost:8080/openapi.json | jq > openapi.json
```

**Expected output:**

```json
{
  "openapi": "3.1.0",
  "info": {
    "title": "SIP AI Assistant API",
    "version": "1.0.0"
  },
  "paths": {
    "/health": { ... },
    "/call": { ... },
    "/tools": { ... },
    "/schedule": { ... }
  }
}
```

**Upload to ReadMe:**

```bash
rdme openapi upload ./openapi.json --key=YOUR_API_KEY --id=YOUR_DEFINITION_ID
```

**For automated sync**, add these GitHub secrets:

| Secret | Description |
|--------|-------------|
| `README_API_KEY` | ReadMe API key |
| `README_API_DEFINITION_ID` | OpenAPI definition ID |

---

## 🔍 Validation

**Check markdown syntax:**

```bash
# Install markdownlint
npm install -g markdownlint-cli

# Lint all docs
markdownlint docs/*.md
```

**Check links:**

```bash
# Install markdown-link-check
npm install -g markdown-link-check

# Check all docs
find docs -name "*.md" -exec markdown-link-check {} \;
```

**Validate frontmatter:**

```bash
# Check each file has required frontmatter
for f in docs/*.md; do
  echo "📄 $f"
  head -10 "$f" | grep -E "(title|category|slug):"
  echo "---"
done
```

---

## 📞 Support

| Resource | Link |
|----------|------|
| 📖 Documentation | https://docs.example.com |
| 🐛 Issues | https://github.com/your-org/sip-agent/issues |
| 💬 Discussions | https://github.com/your-org/sip-agent/discussions |
| 📧 Email | support@example.com |

---

## 📜 License

Documentation is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

Code examples are licensed under [MIT](../LICENSE).
