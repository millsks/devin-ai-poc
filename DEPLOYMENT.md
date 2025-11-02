# Next Steps for GitHub Pages Deployment

After merging this PR, follow these steps to enable GitHub Pages:

## 1. Enable GitHub Pages

1. Go to your repository settings: https://github.com/millsks/devin-ai-poc/settings
2. Click on "Pages" in the left sidebar
3. Under "Build and deployment":
   - **Source**: Select "GitHub Actions"
4. Save the settings

## 2. Merge to Main Branch

Once this PR is merged to the `main` branch, the GitHub Actions workflow will automatically:
- Build the documentation
- Deploy it to GitHub Pages
- Make it available at: https://millsks.github.io/devin-ai-poc/

## 3. Verify Deployment

After the merge:
1. Go to the "Actions" tab in your repository
2. Watch the "Deploy Documentation" workflow run
3. Once completed, visit: https://millsks.github.io/devin-ai-poc/

## 4. Update Documentation

To add or modify documentation:
1. Edit markdown files in the `docs/` directory
2. Test locally with `mkdocs serve`
3. Commit and push to `main`
4. GitHub Actions will automatically rebuild and deploy

## Optional: Custom Domain

To use a custom domain:
1. Add a CNAME file to the `docs/` directory with your domain
2. Configure DNS settings with your domain provider
3. Update `site_url` in `mkdocs.yml` to your custom domain

## Troubleshooting

If deployment fails:
- Check the Actions tab for error logs
- Ensure the workflow has proper permissions
- Verify that GitHub Pages is enabled in settings

## Local Development Commands

```bash
# Install dependencies
pip install -e ".[docs]"

# Start development server (with live reload)
mkdocs serve

# Build static site
mkdocs build

# Deploy manually (alternative to GitHub Actions)
mkdocs gh-deploy
```

## Adding New Pages

1. Create a new `.md` file in the `docs/` directory
2. Add it to the `nav:` section in `mkdocs.yml`
3. Commit and push

Example:
```yaml
nav:
  - Home: index.md
  - Getting Started: getting-started.md
  - Your New Page: your-new-page.md  # Add this
  - User Guide:
      - Overview: user-guide/overview.md
      - Configuration: user-guide/configuration.md
```

## Support

For questions or issues:
- Check the [MkDocs documentation](https://www.mkdocs.org/)
- Review [Material theme docs](https://squidfunk.github.io/mkdocs-material/)
- Open an issue in this repository
