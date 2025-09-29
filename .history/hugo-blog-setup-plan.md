# Hugo Blog Setup Plan - GitHub/GitLab Pages

**Created:** 2025-09-29T11:02:21-05:00  
**Workspace:** `/Users/sebastiansuarezbenjumea/work/whatever/blogs`

## Current State Analysis

- **Workspace Status:** No existing Hugo site or CI configuration found
- **Current Contents:** `LICENSE`, `litellm/` directory (tooling)
- **Missing:** `config.toml|yaml|json`, `content/`, `.github/workflows/`, `.gitlab-ci.yml`
- **Implication:** Setting up Hugo from scratch with CI pipeline for GitHub Pages or GitLab Pages

## Step-by-Step Implementation Plan

### 1. Decide Hosting Target
- **Choice:** Pick GitHub Pages or GitLab Pages (consider org policies, permissions, analytics)
- **Domain:** Decide on custom domain now or later
- **Priority:** HIGH

### 2. Initialize Hugo Site
- **Install Hugo:** Ensure Hugo extended is installed locally and in CI
- **Initialize:** Run `hugo new site .` in `blogs/` directory to scaffold
- **Gitignore:** Add `.gitignore` for `public/`, `.hugo_build.lock`, etc.
- **Priority:** HIGH

### 3. Choose and Add Theme
- **Option A - Hugo Modules (Recommended):**
  - `hugo mod init github.com/<you>/<repo>`
  - `hugo mod get github.com/<theme>/...`
  - Pros: Fewer submodule headaches in CI/Pages
- **Option B - Git Submodule:**
  - Add theme under `themes/<theme>`
  - Ensure CI fetches submodules
- **Configuration:** Set `theme` (or `module.imports`) in config file
- **Priority:** MEDIUM

### 4. Configure Hugo
- **Config File:** Create `config.toml` (or `config.yaml`) with:
  - `baseURL`: Final Pages URL for production
    - GitHub org/user site: `https://<user>.github.io/`
    - GitHub project site: `https://<user>.github.io/<repo>/`
    - GitLab Pages: `https://<namespace>.gitlab.io/<project>/`
  - `languageCode`, `title`, `paginate`, `taxonomies`, `outputs`
  - `enableRobotsTXT = true`, `sitemap`
- **Content Structure:** Use default `content/`, `static/`, `layouts/`, `archetypes/`
- **Archetype:** Add `archetypes/post.md` with front matter
- **First Post:** Create `hugo new posts/hello-world.md` with content
- **Priority:** HIGH

### 5. Local Development
- **Serve:** `hugo server -D` to preview drafts
- **BaseURL:** For local dev, set `--baseURL /` or rely on `hugo server` defaults
- **Production:** Ensure proper `baseURL` for production builds
- **Priority:** MEDIUM

### 6. CI/CD - GitHub Pages (Option A)
- **Branching:** Source on `main`, publish to `gh-pages` branch
- **Pages Settings:** In repo Settings → Pages:
  - Source: Deploy from branch → `gh-pages` → `/` (root)
- **Workflow:** Add `.github/workflows/hugo.yml`:
  - Triggers on push to `main`
  - Steps:
    - Checkout with submodules if using theme submodule
    - Setup Hugo extended
    - Setup Node (optional) if theme needs npm build
    - Build: `hugo --minify`
    - Deploy: use `peaceiris/actions-gh-pages` to push `public/` to `gh-pages`
  - If using Hugo Modules: ensure `go` is available or vendor modules (`hugo mod vendor`)
- **Assets Build:** If theme needs npm, run `npm ci && npm run build` before `hugo`
- **Priority:** HIGH

### 7. CI/CD - GitLab Pages (Option B)
- **Branching:** Publish to `public/` artifact on default branch
- **Project Settings:** Settings → Pages: enable and configure
- **Pipeline:** Add `.gitlab-ci.yml`:
  - Use `image: registry.gitlab.com/pages/hugo:latest` or custom image with Hugo extended
  - Cache `resources/` and possibly Go mod cache if using Modules
  - Script:
    - If Modules: `hugo --minify`
    - If submodule: ensure `GIT_SUBMODULE_STRATEGY: recursive`
  - Artifacts:
    - `paths: - public/`
    - `expire_in: 1 week`
  - `only: - main` (or chosen branch)
- **Custom Domain:** Add `public/CNAME` or configure domain under Pages settings
- **Priority:** HIGH

### 8. Custom Domain and HTTPS (Optional)
- **GitHub Pages:** Add `CNAME` file at repo root or ensure action writes `public/CNAME`. Configure DNS CNAME to `<user>.github.io`
- **GitLab Pages:** Configure domain in Pages settings and add DNS. GitLab will provision TLS if using GitLab-managed certs
- **Priority:** LOW

### 9. Developer Experience
- **README:** Add `README.md` with:
  - Local dev: `hugo server -D`
  - Build: `hugo --minify`
  - Deploy details (GitHub/GitLab)
- **Previews:** Optionally add PR/merge request previews:
  - GitHub: Additional workflow job or use Netlify/Cloudflare for previews
  - GitLab: Review Apps via environments
- **Priority:** LOW

### 10. Performance and SEO Polish
- **Images:** Set up image processing shortcodes, use `resources/` and image processing pipelines
- **Sitemap/Robots:** Ensure `sitemap` and `robots.txt` are generated
- **Analytics:** Add Google Analytics (or Plausible/Umami) via theme config
- **Cache:** CI caches: `resources/`, `node_modules` (if needed), Go module cache
- **Priority:** LOW

## Implementation Notes

### GitHub Actions Key Points
- **Permissions:** Workflow needs `contents: write` to push to `gh-pages`
- **Actions:** Typical combination:
  - `actions/checkout@v4` with `submodules: recursive` if needed
  - `peaceiris/actions-hugo@v2` to install Hugo extended
  - `actions/setup-node@v4` if theme needs Node
  - `peaceiris/actions-gh-pages@v3` to publish `public/` to `gh-pages`
- **Pages Config:** Settings → Pages: select `gh-pages`

### GitLab CI Key Points
- **Image:** Prefer an image with Hugo extended. If missing, use custom image or install Hugo in job
- **Modules:** For Hugo Modules, ensure `go` is available or vendor modules
- **Submodules:** Set `GIT_SUBMODULE_STRATEGY: recursive` if using theme submodules
- **Job Name:** The job must be named `pages` for GitLab Pages to deploy artifacts

## Next Actions Required
1. **Choose Host:** GitHub Pages or GitLab Pages?
2. **Confirm Repo:** Upstream repo location and configuration
3. **Implementation:** Scaffold Hugo site, add theme, create config, add first post, set up CI workflow, create README

## Status
- ✅ Analysis complete
- ✅ Detailed actionable plan ready
- ⏳ Waiting for hosting choice (GitHub vs GitLab) to proceed with implementation
