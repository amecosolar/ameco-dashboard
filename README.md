# Publishing the dashboard with password protection

This folder publishes an **encrypted** copy of `../dashboard.html` to GitHub Pages. The encrypted page asks for a password in the browser; without it, visitors see only a password prompt — nothing in the underlying HTML reveals the dashboard content.

## Files in this folder
- `encrypt_dashboard.py` — reads `../dashboard.html`, encrypts with AES-256-GCM + PBKDF2 (250k iterations), writes `index.html`
- `refresh.sh` — runs encryption + commits + pushes
- `.password` — your single password (gitignored, never pushed)
- `index.html` — the encrypted page that gets published
- `.gitignore` — locks the repo to only the four files that should be public

## One-time setup
1. Create a private repo at https://github.com/new (any name, e.g. `ameco-dashboard`)
2. In its **Settings → Pages**: source = "Deploy from a branch", branch = `main`, folder = `/ (root)`, save
3. Run in Terminal:
   ```bash
   cd /Users/eyalshimony/Downloads/Ameco/publish
   git init -b main
   git remote add origin git@github.com:YOURUSER/ameco-dashboard.git
   git add index.html .gitignore README.md
   git commit -m "Initial publish"
   git push -u origin main
   ```
4. Open `https://YOURUSER.github.io/ameco-dashboard/` — you should see a password prompt. Enter your password to see the dashboard.

## Refreshing
Anytime the dashboard regenerates:
```bash
/Users/eyalshimony/Downloads/Ameco/publish/refresh.sh
```
Or ask Claude: "publish the latest dashboard."

## Changing the password
Edit `.password` (one line, plain text), then run `refresh.sh`. Anyone with the old password will lose access on the next refresh.

## Security notes
- AES-256-GCM with PBKDF2-SHA256, 250 000 iterations. A brute-force attempt would have to perform 250k hash ops per password guess in-browser. A strong password is your only defence — pick something not used elsewhere.
- The `.password` file is gitignored. The encrypted `index.html` does not contain the password — only the ciphertext and salt.
- If you suspect leak: change the password, run `refresh.sh`, and the published URL immediately requires the new password.
