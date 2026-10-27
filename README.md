# Personal Rclone Drive Client – OAuth Consent Pages

A static site (home, privacy policy, terms of service) for the Google Cloud
OAuth consent screen of a personal Google Drive API client used with rclone
on Linux. Plain HTML + CSS, no build step, ready for GitHub Pages.

```
index.html     Home page (app description, links to policies)
privacy.html   Privacy Policy
terms.html     Terms of Service
style.css      Shared styles (light/dark, mobile friendly)
.nojekyll      Tells GitHub Pages to serve files as-is (skip Jekyll)
robots.txt     Allows crawling (Google needs to reach the pages)
```

## 1. Fill in your details

The pages contain two placeholders: `YOUR_NAME` and `YOUR_EMAIL`.
Replace them in one go:

```bash
sed -i 's/YOUR_NAME/Arsam Javid/g; s/YOUR_EMAIL/arsamjavid@gmail.com/g' *.html
grep -n 'YOUR_' *.html   # should print nothing
```

Preview locally:

```bash
python3 -m http.server 8000
# open http://localhost:8000
```

## 2. Push to GitHub Pages

### Option A – user site (`https://thearsamjaved.github.io/`) — recommended

A user site serves from the root of the domain, which makes Google domain
verification simplest.

1. On GitHub, create a **public** repo named exactly `thearsamjaved.github.io`.
2. Push:

   ```bash
   cd ~/Documents/rclone_site
   git init -b main
   git add .
   git commit -m "Add OAuth consent pages"
   git remote add origin git@github.com:thearsamjaved/thearsamjaved.github.io.git
   git push -u origin main
   ```

3. Repo → **Settings → Pages** → *Source: Deploy from a branch* →
   Branch `main`, folder `/ (root)` → **Save**.
4. After a minute the site is live at `https://thearsamjaved.github.io/`.

### Option B – project site (`https://thearsamjaved.github.io/<repo>/`)

Same steps, but name the repo anything (e.g. `rclone-oauth`). The site
will be at `https://thearsamjaved.github.io/rclone-oauth/`. All links in the
pages are relative, so they work under a sub-path too.

> Using HTTPS instead of SSH? Use
> `https://github.com/thearsamjaved/<repo>.git` as the remote; GitHub will ask
> for a personal access token as the password (or use `gh auth login`).

### Updating later

```bash
git add . && git commit -m "Update pages" && git push
```

## 3. Configure the Google Cloud consent screen

In [Google Cloud Console](https://console.cloud.google.com/) →
**Google Auth Platform → Branding** (formerly *OAuth consent screen*):

| Field                        | Value                                                   |
|------------------------------|---------------------------------------------------------|
| App name                     | Personal Rclone Drive Client                            |
| Application home page        | `https://thearsamjaved.github.io/rclone/`                       |
| Application privacy policy   | `https://thearsamjaved.github.io/rclone/privacy.html`           |
| Application terms of service | `https://thearsamjaved.github.io/rclone/terms.html`             |
| Authorized domains           | `thearsamjaved.github.io`                                  |

### Verifying domain ownership

Google may ask you to prove you own the home page domain:

1. Open [Google Search Console](https://search.google.com/search-console),
   add a **URL-prefix** property for `https://thearsamjaved.github.io/rclone/`.
2. Choose **HTML file** verification and download the file
   (e.g. `google1234abcd.html`).
3. Put it in this folder, commit and push, then click **Verify**.
4. Use the same Google account that owns the Cloud project.

## 4. Notes for personal use with rclone

- Create the credential under **Clients → Create client → Desktop app**, then
  run `rclone config` and paste the client ID and secret when asked
  (see [rclone's guide](https://rclone.org/drive/#making-your-own-client-id)).
- Add the Drive API scopes you use (rclone default: `.../auth/drive`).
- **Testing** publishing status: only listed test users can sign in and
  refresh tokens **expire after 7 days**, so you'd have to re-run
  `rclone config reconnect <remote>:` weekly.
- **In production** without verification: works for yourself — you'll see an
  "unverified app" warning once (click *Advanced → Go to …*), and tokens
  don't expire weekly. Full Google verification is only required if you
  intend other people to use the app.
