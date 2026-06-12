# Dynamic Fabric — B2B website

Astro static site + Decap CMS, built for Cloudflare Pages.

## Pages

Home, Products (list + detail), About, Blog (list + post), Contact. All content — page text, products, blog posts, contact details, footer — is editable at `/admin/` via Decap CMS.

## Local development

```bash
npm install
npm run dev          # site at http://localhost:4321
```

To use the CMS locally (no GitHub login needed): uncomment `local_backend: true` in `public/admin/config.yml`, then run `npx decap-server` in a second terminal and open http://localhost:4321/admin/

## Deploy to Cloudflare Pages

1. **Push to GitHub.** Create a repo (e.g. `dynamic-fabric-site`) and push this folder.

2. **Create the Pages project.** In the Cloudflare dashboard: Workers & Pages → Create → Pages → Connect to Git → pick the repo.
   - Framework preset: **Astro**
   - Build command: `npm run build`
   - Output directory: `dist`

3. **Deploy.** Cloudflare builds on every push to `main`. Decap commits content edits to the repo, which triggers a rebuild automatically.

4. **Set up CMS login (one-time).** Decap needs a GitHub OAuth proxy because Cloudflare Pages has no built-in one:
   1. Deploy the small open-source Worker [sveltia-cms-auth](https://github.com/sveltia/sveltia-cms-auth) to your Cloudflare account (one-click deploy in its README; it works with Decap too).
   2. Create a GitHub OAuth App (GitHub → Settings → Developer settings → OAuth Apps):
      - Homepage URL: your site URL
      - Authorization callback URL: `https://<your-worker>.workers.dev/callback`
   3. Add the OAuth App's Client ID/Secret as the Worker's environment variables (`GITHUB_CLIENT_ID`, `GITHUB_CLIENT_SECRET`).
   4. In `public/admin/config.yml`, set `repo:` to your `username/repo` and `base_url:` to the Worker URL. Push.
   5. Open `https://your-site.pages.dev/admin/` and log in with GitHub.

## Editing content

- **Blog posts / Products** — create, edit, delete from the CMS; new entries appear after the auto-rebuild (~1 min).
- **Pages** — Homepage hero, feature cards, stats, About, Contact details.
- **Site settings** — name, tagline, footer, contact info shown site-wide.
- **Images** — uploaded via the CMS into `public/images/uploads/`.

## Customizing

- Colors/fonts: `src/styles/global.css` (CSS variables at the top).
- Layout/nav: `src/layouts/Base.astro`.
- Content schemas: `src/content.config.ts` (keep in sync with `public/admin/config.yml` if you add fields).

## Contact form

The contact page uses a `mailto:` button. For a real form, easiest options: [Web3Forms](https://web3forms.com) or [Formspree](https://formspree.io) (drop-in HTML form), or a Cloudflare Pages Function if you want it self-hosted.
