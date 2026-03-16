# Ultimate Teams Offline — GitHub Pages package

This package is ready to publish to GitHub Pages.

## Files
- `index.html` — the app
- `manifest.json` — PWA manifest
- `service-worker.js` — offline caching
- `.github/workflows/deploy-pages.yml` — GitHub Pages deployment workflow

## Publish steps
1. Create a new GitHub repository.
2. Upload all files from this folder to the repository root.
3. Commit to the `main` branch.
4. In GitHub, go to **Settings → Pages**.
5. Under **Build and deployment**, set **Source** to **GitHub Actions**.
6. Go to **Actions** and let the workflow run.
7. Open the Pages URL once on your phone while online.
8. In Safari, tap **Share → Add to Home Screen**.
9. Open the installed app once online to finish caching.
10. Then test offline.

## Notes
- The app stores league data in browser storage on the device.
- Use the built-in JSON backup export regularly.
