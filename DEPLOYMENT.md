# Cloudflare Pages Deployment Guide

## Prerequisites

1. **Cloudflare Account** with your domain added
2. **Wrangler CLI** installed: `npm install -g wrangler`
3. **Ruby & Bundler** for Jekyll

## Setup

### 1. Install Dependencies

```bash
# Install Ruby dependencies
bundle install

# Install Wrangler (if not installed globally)
npm install
```

### 2. Authenticate Wrangler

```bash
wrangler login
```

### 3. Create Cloudflare Pages Project

```bash
# Build the site first
bundle exec jekyll build

# Deploy to Cloudflare Pages
wrangler pages deploy _site --project-name=rootinator
```

On first deploy, Wrangler will create the project automatically.

## Deployment Methods

### Method 1: Manual Deployment (Using Wrangler)

```bash
# Build and deploy
npm run build
npm run deploy
```

### Method 2: Automatic Deployment (GitHub Actions)

The repository includes a GitHub Actions workflow for automatic deployment.

**Setup Steps:**

1. Get your Cloudflare credentials:
   - **Account ID**: Found in Cloudflare dashboard URL or Workers & Pages overview
   - **API Token**: Create at https://dash.cloudflare.com/profile/api-tokens
     - Use "Edit Cloudflare Workers" template
     - Or create custom token with "Cloudflare Pages:Edit" permission

2. Add secrets to GitHub:
   - Go to: `https://github.com/YuvalMil/rootinator/settings/secrets/actions`
   - Add `CLOUDFLARE_API_TOKEN`
   - Add `CLOUDFLARE_ACCOUNT_ID`

3. Push to main branch - deployment happens automatically!

### Method 3: Cloudflare Dashboard Direct Integration

1. Go to Cloudflare Dashboard > Workers & Pages
2. Click "Create application" > "Pages" > "Connect to Git"
3. Select your GitHub repository
4. Configure build settings:
   - **Build command**: `bundle exec jekyll build`
   - **Build output directory**: `_site`
   - **Root directory**: `/`
5. Add environment variables if needed
6. Deploy!

## Custom Domain Setup

1. Go to your Pages project in Cloudflare Dashboard
2. Navigate to "Custom domains"
3. Click "Set up a custom domain"
4. Enter your domain (e.g., `rootinator.com` or `blog.yourdomain.com`)
5. Cloudflare will automatically configure DNS
6. SSL/TLS is enabled by default

## Local Development

```bash
# Serve locally with live reload
bundle exec jekyll serve

# Visit http://localhost:4000
```

## Updating Content

### Add a new writeup:
```bash
# Create file: _writeups/2026-01-22-challenge-name.md
# Commit and push (if using GitHub Actions)
# Or run: npm run deploy (if using Wrangler)
```

### Add a blog post:
```bash
# Create file: _posts/2026-01-22-post-title.md
# Commit and push or deploy
```

## Troubleshooting

- **Build fails**: Ensure Ruby and Bundler are properly installed
- **Wrangler auth issues**: Run `wrangler login` again
- **Domain not working**: Check DNS settings in Cloudflare dashboard
- **Changes not showing**: Clear Cloudflare cache or wait 1-2 minutes

## Performance Tips

- Cloudflare Pages provides automatic:
  - Global CDN distribution
  - Automatic HTTPS
  - HTTP/3 support
  - Image optimization (for supported formats)
  - Brotli compression

## Resources

- [Cloudflare Pages Documentation](https://developers.cloudflare.com/pages/)
- [Wrangler CLI Documentation](https://developers.cloudflare.com/workers/wrangler/)
- [Jekyll Documentation](https://jekyllrb.com/docs/)