# Rootinator

A Jekyll-based blog for CTF writeups, bug bounty research, and information security content.

## Local Development

1. Install Ruby and Bundler
2. Run `bundle install`
3. Run `bundle exec jekyll serve`
4. Visit `http://localhost:4000`

## Adding Content

### CTF Writeups
Create new files in `_writeups/` directory:
```bash
_writeups/2026-01-22-challenge-name.md
```

### Blog Posts
Create new files in `_posts/` directory:
```bash
_posts/2026-01-22-post-title.md
```

## GitHub Pages Deployment

1. Go to repository Settings > Pages
2. Select source: "Deploy from a branch"
3. Select branch: "main" and folder: "/ (root)"
4. Save and wait a few minutes
5. Your site will be live at `https://yuvalmil.github.io/rootinator/`

## Structure

- `_config.yml` - Site configuration
- `_posts/` - Blog posts
- `_writeups/` - CTF writeups collection
- `index.md` - Homepage
- `writeups.md` - Writeups listing page
- `about.md` - About page