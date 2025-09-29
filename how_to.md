# Hugo Blog Setup Documentation

**Created:** 2025-09-29T11:16:31-05:00  
**Target:** GitHub Pages deployment using Hugo and Podman

## Overview

This document provides step-by-step instructions for setting up a Hugo blog with GitHub Pages deployment using Podman containers for local development.

## Prerequisites

- Podman installed and running
- Git repository initialized
- Task (go-task) installed for running tasks

## Step 1: Taskfile Creation ✅

Created `Taskfile.yaml` with comprehensive Hugo tasks using Podman containers:

### Key Features:
- **Container-based Hugo**: Uses `hugomods/hugo:exts` image with Hugo Extended
- **No local Hugo installation required**: All Hugo commands run in containers
- **GitHub Pages ready**: Includes workflow generation

### Available Tasks:
```bash
# Essential tasks
task setup          # Complete setup for new Hugo site
task init            # Initialize Hugo site structure  
task serve           # Start development server (with drafts)
task build           # Build for production
task clean           # Clean build artifacts

# Content creation
task new-post        # Create new blog post
task new-page        # Create new page

# Theme management
task theme-add       # Add theme as git submodule
task hugo-mod-init   # Initialize Hugo modules
task hugo-mod-get    # Add theme via Hugo modules

# Utilities
task hugo-version    # Check Hugo version
task hugo-check      # Validate configuration
task help            # List all available tasks
```

## Step 2: Site Initialization ✅

### 2.1 Directory Structure Created
```bash
mkdir -p content/posts archetypes layouts static themes
```

Created the following Hugo directory structure:
- `content/posts/` - Blog posts location
- `archetypes/` - Content templates
- `layouts/` - Custom templates (optional)
- `static/` - Static assets (images, CSS, JS)
- `themes/` - Hugo themes

### 2.2 Configuration File Created
Created `hugo.toml` with:
- **Base URL**: `https://sebastiansuarezbenjumea.github.io/blogs/`
- **Language**: English (en-us)
- **Title**: "My Blog"
- **Features**: Minification, robots.txt, syntax highlighting
- **Output formats**: HTML, RSS, JSON

### 2.3 Default Archetype Created
Created `archetypes/default.md` with standard front matter template:
- Title (auto-generated from filename)
- Date (auto-generated)
- Draft status (true by default)

### 2.4 Git Ignore Created ✅
Created `.gitignore` with Hugo-specific exclusions:
- `/public/` - Build output directory
- `/resources/_gen/` - Generated resources
- `.hugo_build.lock` - Hugo build lock file
- Standard OS and IDE files

## Step 3: GitHub Pages Setup ✅

### 3.1 GitHub Actions Workflow Created
Created `.github/workflows/hugo.yml` with:
- **Trigger**: Push to main branch + manual dispatch
- **Hugo version**: 0.119.0 (Extended)
- **Build process**: 
  1. Install Hugo CLI and Dart Sass
  2. Checkout code with submodules
  3. Setup GitHub Pages
  4. Install Node.js dependencies (if needed)
  5. Build with Hugo (minified, production)
  6. Upload and deploy to GitHub Pages

### 3.2 Permissions Configuration
- **Contents**: read (for checkout)
- **Pages**: write (for deployment)
- **ID token**: write (for authentication)

## Next Steps Required

### 4. Theme Selection and Installation
Choose one of the following approaches:

#### Option A: Git Submodule Theme
```bash
task theme-add
# Enter theme repository URL when prompted
# Example: https://github.com/adityatelange/hugo-PaperMod.git
```

#### Option B: Hugo Modules Theme
```bash
task hugo-mod-init
# Enter module name: github.com/yourusername/blogs

task hugo-mod-get  
# Enter theme module: github.com/adityatelange/hugo-PaperMod/v7
```

### 5. Content Creation
```bash
# Create your first post
task new-post
# Enter post title when prompted

# Create an about page
task new-page
# Enter "about" when prompted
```

### 6. Local Development Testing
```bash
# Start development server
task serve
# Visit http://localhost:1313 to preview

# Build for production (test)
task build
```

### 7. GitHub Repository Setup
1. **Create GitHub repository** named `blogs`
2. **Push code** to repository:
   ```bash
   git add .
   git commit -m "Initial Hugo site setup"
   git push origin main
   ```
3. **Enable GitHub Pages**:
   - Go to repository Settings → Pages
   - Source: GitHub Actions
   - The workflow will automatically deploy on push

### 8. Domain Configuration (Optional)
- **Custom domain**: Add CNAME file or configure in GitHub Pages settings
- **DNS**: Point domain to GitHub Pages servers

## Troubleshooting

### Podman Issues
- **Permission errors**: Ensure Podman has proper volume mount permissions
- **Image pull errors**: Check internet connection and registry access
- **Port conflicts**: Ensure port 1313 is available for development server

### Hugo Issues
- **Build errors**: Check `hugo.toml` syntax and theme compatibility
- **Theme errors**: Verify theme installation and configuration
- **Content errors**: Check front matter syntax in markdown files

### GitHub Actions Issues
- **Workflow failures**: Check Actions tab in GitHub repository
- **Permission errors**: Verify repository has Pages enabled
- **Build errors**: Check Hugo version compatibility and dependencies

## Step 4: Theme Installation ✅

### 4.1 PaperMod Theme Added
Added PaperMod theme as git submodule:
```bash
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

### 4.2 Configuration Updated
Enhanced `hugo.toml` with PaperMod-specific settings:
- **Theme**: Set to "PaperMod"
- **Pagination**: Updated to new format (`pagination.pagerSize = 10`)
- **PaperMod Features**: Reading time, share buttons, breadcrumbs, code copy buttons
- **Menu**: Home, Posts, About navigation
- **Social Icons**: GitHub link configured

### 4.3 Content Created
- **About page**: Created `content/about.md` with site information
- **Hello World post**: Created `content/posts/hello-world.md` as sample content

## Step 5: Podman Permissions Fix ✅

### 5.1 SELinux Volume Mount Issue Resolved
**Problem**: Permission denied errors when mounting volumes in Podman
**Solution**: Added `:z` flag to all volume mounts in Taskfile.yaml

**Before**: `-v {{.SITE_DIR}}:/src`
**After**: `-v {{.SITE_DIR}}:/src:z`

This allows proper SELinux context sharing between host and container.

### 5.2 Build Testing ✅
```bash
task build
# Output: Successfully built 14 pages in 107ms
```

### 5.3 Development Server Testing ✅
```bash
task serve
# Server running at http://localhost:1313/blogs/
```

## Status

- ✅ **Taskfile created** with Podman-based Hugo tasks
- ✅ **Site structure initialized** with directories and basic files
- ✅ **Configuration created** (`hugo.toml`) with PaperMod theme
- ✅ **Git ignore configured** for Hugo artifacts
- ✅ **GitHub Actions workflow** created for automatic deployment
- ✅ **Theme installation** - PaperMod theme added and configured
- ✅ **Content creation** - Sample posts and about page created
- ✅ **Podman permissions** - SELinux volume mount issues resolved
- ✅ **Local testing** - Development server working at localhost:1313
- ✅ **Production build** - Build process tested and working
- ⏳ **GitHub setup** - Push to repository and enable Pages

## Commands Reference

```bash
# Quick start after setup
task serve              # Start development
task new-post          # Create content
task build             # Test production build
task clean             # Clean artifacts

# Theme management
task theme-add         # Add git submodule theme
task hugo-mod-init     # Initialize Hugo modules
task hugo-mod-get      # Add module theme

# Utilities
task hugo-version      # Check Hugo version
task hugo-check        # Validate site
task help              # Show all tasks
```
