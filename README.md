# SocialMediaFit — deploy repo

This repo holds the **built** SocialMediaFit site (the `dist` output), not the
source code. It exists so the live site can update itself automatically.

## How it works

1. Dudu builds the site and pushes the built files here.
2. GitHub notifies the hosting (webhook) on every push.
3. cPanel pulls the latest files and runs `.cpanel.yml`, which copies
   everything into `public_html/` — the live site updates with no uploads.

## One-time setup (in cPanel)

1. Open cPanel → **Files → Git Version Control**. (If you don't see it, your
   plan doesn't include it — ask HostGrap support to enable it.)
2. Click **Create**. Keep **Clone a Repository** ON.
3. **Clone URL:** `https://github.com/<your-github-username>/socialmediafit-site.git`
   (use this repo's URL; public repo = no login needed for the clone).
4. **Repository Path:** `/home/convrtly/socialmediafit-repo`
   (NOT `public_html` — cloning straight into `public_html` is risky: if the
   repository is ever deleted from cPanel, the whole folder goes with it.)
5. **Repository Name:** `socialmediafit-site`. Click **Create**.
6. Open the repo → **Manage → Pull or Deploy** tab. Copy the **webhook URL**.
7. On GitHub, repo **Settings → Webhooks → Add webhook**: paste the URL,
   content type `application/json`, "Just the push event". Save.
8. Back in cPanel, click **Deploy HEAD Commit** once to publish the first
   version. Check the live site loads.

After that, every push to this repo updates the live site automatically.

## Notes

- `.cpanel.yml` copies the repo into `public_html/` with rsync (dotfiles
  like `.htaccess` included, `.git` excluded). It never deletes files from
  `public_html/`, so nothing already there is lost.
- The source code (Vite project) is not in this repo — only the built site.
- If rsync is not available on the server, replace the rsync line in
  `.cpanel.yml` with: `/bin/cp -R ./ $DEPLOYPATH` (then delete
  `$DEPLOYPATH/.git` afterwards).
