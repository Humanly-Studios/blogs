---
trigger: always_on
---

# Windsurf Rules for Hugo Blog Project

## Hugo Development Environment

**CRITICAL: This project uses Podman containers for Hugo operations. NEVER run `hugo` commands directly.**

### Required Tools
- **Podman**: All Hugo operations run in containers
- **Task**: Automation via Taskfile.yaml
- **Git**: For theme submodules and version control

### Development Workflow

#### Starting Development Server
```bash
# ✅ CORRECT - Use Taskfile
task serve

# ❌ WRONG - Don't run hugo directly
hugo server
```

#### Creating New Posts
```bash
# ✅ CORRECT - Use Taskfile with title
task new-post -- "My New Post Title"

# ✅ CORRECT - Interactive prompt
task new-post
```

#### Building the Site
```bash
# ✅ CORRECT - Development build
task build-dev

# ✅ CORRECT - Production build
task build
```

### Available Tasks

| Task | Description | Usage |
|------|-------------|-------|
| `serve` | Start development server | `task serve` |
| `serve-prod` | Start production server | `task serve-prod` |
| `build` | Production build | `task build` |
| `build-dev` | Development build | `task build-dev` |
| `new-post` | Create new blog post | `task new-post -- "Title"` |
| `new-page` | Create new page | `task new-page -- "page-name"` |
| `clean` | Clean build artifacts | `task clean` |
| `hugo-version` | Show Hugo version | `task hugo-version` |
| `hugo-check` | Check configuration | `task hugo-check` |

### Container Configuration

- **Hugo Image**: `hugomods/hugo:exts`
- **Port**: 1313 (mapped to host)
- **Volume Mount**: Project directory mounted to `/src:z`
- **Development URL**: `http://localhost:1313/blogs/`

### Theme Management

- **Theme**: PaperMod (Git submodule)
- **Update themes**: `task theme-update`
- **Add new theme**: `task theme-add -- <repo-url>`

### Mermaid Diagram Support

- **Enabled**: Mermaid.js integration configured
- **Usage**: Use `\`\`\`mermaid` code blocks in markdown
- **Theme**: Auto-adapts to Hugo light/dark theme
- **Files**: 
  - `layouts/partials/extend_head.html` - Mermaid integration
  - `hugo.toml` - Configuration with `params.mermaid.enable = true`

### GitHub Pages Deployment

- **Workflow**: `.github/workflows/hugo.yml`
- **Trigger**: Push to main branch
- **Build**: Uses native Hugo (not Podman) in CI
- **URL**: `https://sebastiansuarezbenjumea.github.io/blogs/`

### File Structure

```
├── content/posts/          # Blog posts
├── layouts/partials/       # Custom partials (Mermaid, etc.)
├── themes/PaperMod/        # Theme submodule
├── static/images/          # Static assets
├── hugo.toml              # Hugo configuration
├── Taskfile.yaml          # Task automation
└── .github/workflows/     # CI/CD
```

### Common Patterns

#### Testing Changes
1. `task serve` - Start development server
2. Edit content/configuration
3. Check `http://localhost:1313/blogs/`
4. `Ctrl+C` to stop server

#### Publishing New Post
1. `task new-post -- "Post Title"`
2. Edit the generated markdown file
3. `task serve` to preview
4. Commit and push to trigger deployment

### Troubleshooting

#### Container Issues
- Check Podman is running: `podman version`
- Clean containers: `podman system prune`
- Restart server: `Ctrl+C` then `task serve`

#### Build Issues
- Clean artifacts: `task clean`
- Check config: `task hugo-check`
- Verify theme: `task theme-update`

### Memory Context

- **New Post Task**: Fixed CLI argument handling to prevent empty filenames
- **Mermaid Integration**: Configured for GitHub Pages compatibility
- **Theme**: PaperMod with custom partials for enhanced functionality

---

**Remember**: Always use `task` commands, never direct `hugo` commands. The containerized approach ensures consistent builds across environments.
## PaperMod Theme Multilingual Customizations

### Critical Multilingual Configuration

**IMPORTANT: This project uses custom PaperMod theme modifications for multilingual support. DO NOT modify these files without understanding the complete setup.**

#### Hugo Configuration Requirements

**File**: `hugo.toml`
```toml
# CRITICAL: These settings are required for multilingual functionality
baseURL = 'https://humanly-studios.github.io/blogs/'
defaultContentLanguage = 'en'
defaultContentLanguageInSubdir = true  # ← ESSENTIAL for proper URL structure
theme = 'PaperMod'

# Language configuration - NO individual baseURL needed
[languages]
  [languages.en]
    languageCode = 'en-us'
    languageName = 'English'
    title = 'Humanly Studios Blog'
    weight = 1
    contentDir = 'content/en'
    # DO NOT add baseURL here
    
  [languages.es]
    languageCode = 'es'
    languageName = 'Español'
    title = 'Blog de Humanly Studios'
    weight = 2
    contentDir = 'content/es'
    # DO NOT add baseURL here

# Menu configuration - MUST use relative paths
[languages.en.menu]
  [[languages.en.menu.main]]
    identifier = "home"
    name = "Home"
    url = "/"           # ← Root path for home
    weight = 10
  [[languages.en.menu.main]]
    identifier = "posts"
    name = "Posts"
    url = "posts/"      # ← Relative path (no leading slash)
    weight = 20
  [[languages.en.menu.main]]
    identifier = "about"
    name = "About"
    url = "about/"      # ← Relative path (no leading slash)
    weight = 30

[languages.es.menu]
  [[languages.es.menu.main]]
    identifier = "home"
    name = "Inicio"
    url = "/"           # ← Root path for home
    weight = 10
  [[languages.es.menu.main]]
    identifier = "posts"
    name = "Artículos"
    url = "posts/"      # ← Relative path (no leading slash)
    weight = 20
  [[languages.es.menu.main]]
    identifier = "about"
    name = "Acerca de"
    url = "about/"      # ← Relative path (no leading slash)
    weight = 30
```

#### Custom Layout Files

**CRITICAL FILES - DO NOT DELETE OR MODIFY WITHOUT UNDERSTANDING:**

##### 1. `layouts/partials/extend_head.html`
- **Purpose**: Contains JavaScript fix for menu URL issues
- **Function**: Automatically corrects wrong menu URLs after page load
- **Key Features**:
  - Detects and fixes Spanish "Inicio" links from `/es/blogs/` to `/blogs/es/`
  - Detects and fixes English "Home" links from `/en/blogs/` to `/blogs/en/`
  - Provides URL redirect for users who reach wrong URLs
  - Console logging for debugging

##### 2. `layouts/partials/header.html`
- **Purpose**: Custom header with hardcoded correct menu URLs
- **Function**: Overrides theme's default menu generation
- **Key Features**:
  - Language-specific menu structure
  - Hardcoded correct URLs that bypass Hugo's URL transformation
  - Fallback solution when JavaScript fails

#### Content Structure Requirements

**File Structure**:
```
content/
├── en/
│   ├── _index.md          # MUST have aliases: ["en/blogs/"]
│   ├── about.md
│   └── posts/
│       └── *.md
└── es/
    ├── _index.md          # MUST have aliases: ["es/blogs/"]
    ├── about.md
    └── posts/
        └── *.md
```

**Required Aliases in Index Files**:

`content/en/_index.md`:
```yaml
---
title: "Humanly Studios Blog"
aliases:
  - "en/blogs/"           # ← CRITICAL: Provides fallback URL access
---
```

`content/es/_index.md`:
```yaml
---
title: "Blog de Humanly Studios"
aliases:
  - "es/blogs/"           # ← CRITICAL: Provides fallback URL access
---
```

### URL Structure

**Correct URLs (WORKING)**:
- English Home: `http://localhost:1313/blogs/en/`
- English Posts: `http://localhost:1313/blogs/en/posts/`
- Spanish Home: `http://localhost:1313/blogs/es/`
- Spanish Posts: `http://localhost:1313/blogs/es/posts/`

**Wrong URLs (HANDLED BY JAVASCRIPT)**:
- `http://localhost:1313/en/blogs/` → Redirects to `/blogs/en/`
- `http://localhost:1313/es/blogs/` → Redirects to `/blogs/es/`

### Troubleshooting Multilingual Issues

#### Menu Links Not Working
1. Check `layouts/partials/extend_head.html` exists
2. Verify JavaScript console for "URL Fix script loaded"
3. Check browser console for fix messages
4. Ensure `layouts/partials/header.html` has correct hardcoded URLs

#### Translation Links 404
1. Verify `defaultContentLanguageInSubdir = true` in `hugo.toml`
2. Check content files exist in both `content/en/` and `content/es/`
3. Ensure no individual `baseURL` in language sections

#### Wrong URL Generation
1. **DO NOT** try to fix with Hugo template functions - Hugo transforms ALL URLs
2. **USE** JavaScript solution in `extend_head.html`
3. **MAINTAIN** hardcoded URLs in `header.html` as fallback

### Development Guidelines

#### When Adding New Menu Items
1. Add to both English and Spanish menu sections in `hugo.toml`
2. Use relative paths (e.g., `"newpage/"` not `"/newpage/"`)
3. Update hardcoded menu in `layouts/partials/header.html`
4. Test both languages thoroughly

#### When Modifying Theme
1. **NEVER** modify files in `themes/PaperMod/` directly
2. **ALWAYS** create overrides in `layouts/` directory
3. **PRESERVE** existing customizations in `extend_head.html` and `header.html`

#### When Updating PaperMod Theme
1. **BACKUP** custom files before updating
2. **TEST** multilingual functionality after update
3. **VERIFY** JavaScript fixes still work
4. **CHECK** menu URLs are correct

### Root Cause Documentation

**Why These Customizations Are Needed**:
- Hugo's `absLangURL` function with `defaultContentLanguageInSubdir = true` and baseURL containing path component creates URL generation conflicts
- PaperMod theme applies URL transformations that cannot be overridden at template level
- Hugo processes ALL `href` attributes and applies transformations, even to hardcoded URLs
- Solution requires multi-layered approach: JavaScript fixes + hardcoded fallbacks + Hugo aliases

**DO NOT ATTEMPT TO**:
- Fix URLs purely through Hugo configuration
- Override `absLangURL` behavior in templates
- Use only static redirects (won't work with Hugo dev server)
- Modify theme files directly

---

**REMEMBER**: This multilingual setup is complex and interdependent. All customizations work together to provide seamless user experience. Test thoroughly after any changes.
