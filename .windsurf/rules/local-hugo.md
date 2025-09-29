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