#!/usr/bin/env bash
# deploy-to-github.sh
# Push the Citadel site to GitHub and publish it on GitHub Pages.
#
# Run this from the folder that contains index.html:
#     chmod +x deploy-to-github.sh
#     ./deploy-to-github.sh                       # repo: youngtravis03.github.io (clean root)
#     ./deploy-to-github.sh citadel-site          # repo: a named project site
#
# If the GitHub CLI (gh) is installed and logged in, this creates the repo,
# pushes, and turns on Pages automatically. Without gh, it pushes to a repo you
# created on github.com first, then tells you the one click to enable Pages.

set -euo pipefail

# ----- config -------------------------------------------------------------
GH_USER="YoungTravis03"                       # your GitHub username
REPO="${1:-youngtravis03.github.io}"          # default = user site (served at root)
VISIBILITY="public"                           # Pages free tier requires public
USER_LC="$(echo "$GH_USER" | tr '[:upper:]' '[:lower:]')"

# ----- preflight ----------------------------------------------------------
if [ ! -f index.html ]; then
  echo "[-] index.html not found in $(pwd). cd into the site folder and re-run."
  exit 1
fi

# ----- secret scan (do NOT ship keys to a public repo) --------------------
echo "[*] Scanning for secrets before commit..."
PATTERN='sk-[A-Za-z0-9]{20,}|AKIA[0-9A-Z]{16}|-----BEGIN [A-Z ]*PRIVATE KEY-----|(OPENAI|API|SECRET)_?KEY[[:space:]]*=[[:space:]]*["'"'"']?[A-Za-z0-9_-]{12,}'
if grep -RInE --exclude-dir=.git "$PATTERN" . >/dev/null 2>&1; then
  echo "[-] Possible secret detected:"
  grep -RInE --exclude-dir=.git "$PATTERN" . | sed 's/^/      /'
  echo "[-] Aborting. Remove the secret (use a .gitignore / env var) and re-run."
  echo "    Note: your Formspree endpoint is safe to expose; this is for real keys."
  exit 1
fi
echo "[+] No secrets found."

# ----- generate supporting files if missing -------------------------------
if [ ! -f .gitignore ]; then
  echo "[*] Creating .gitignore"
  cat > .gitignore <<'GI'
# secrets / env
.env
.env.*
*.key
*.pem
secrets.*

# os / editor
.DS_Store
Thumbs.db
.vscode/
.idea/

# deps / build
node_modules/
__pycache__/
*.log
GI
fi

if [ ! -f README.md ]; then
  echo "[*] Creating README.md"
  cat > README.md <<'RM'
# Citadel Threat Intelligence Group, LLC

Public site for Citadel Threat Intelligence Group — guided paths through
CompTIA Security+, CySA+, and PenTest+, taught from real SOC and bug-bounty work.

- **Live site:** published via GitHub Pages
- **Owner:** Travis Young
- **Contact:** CTIG2026@outlook.com

## Editing
Everything is in `index.html` (HTML/CSS/JS in one file).
- Videos: edit the `videos` array in the script block.
- Registration: set `FORMSPREE_ENDPOINT` to your Formspree form URL.

> Public repo. Never commit API keys or secrets.
RM
fi

# ----- git init + commit --------------------------------------------------
if [ ! -d .git ]; then
  echo "[*] Initializing git repo"
  git init -q
fi
git add .
git commit -qm "Deploy Citadel site" || echo "[*] Nothing new to commit."
git branch -M main

# ----- path A: GitHub CLI available --------------------------------------
if command -v gh >/dev/null 2>&1 && gh auth status >/dev/null 2>&1; then
  echo "[*] GitHub CLI detected and authenticated."
  if gh repo view "$GH_USER/$REPO" >/dev/null 2>&1; then
    echo "[*] Repo exists; pushing."
    git remote get-url origin >/dev/null 2>&1 \
      || git remote add origin "https://github.com/$GH_USER/$REPO.git"
    git push -u origin main
  else
    echo "[*] Creating repo $GH_USER/$REPO ($VISIBILITY) and pushing."
    gh repo create "$GH_USER/$REPO" "--$VISIBILITY" --source=. --remote=origin --push
  fi

  echo "[*] Enabling GitHub Pages (main / root)..."
  gh api --method POST "repos/$GH_USER/$REPO/pages" \
      -f 'source[branch]=main' -f 'source[path]=/' >/dev/null 2>&1 \
      || echo "    (Pages may already be enabled — check Settings > Pages.)"

# ----- path B: plain git (repo must already exist on github.com) ----------
else
  echo "[*] GitHub CLI not found. Using plain git."
  echo "    First create an EMPTY repo at: https://github.com/new"
  echo "    Name it exactly: $REPO   (Public, no README/license)."
  git remote get-url origin >/dev/null 2>&1 \
    || git remote add origin "https://github.com/$GH_USER/$REPO.git"
  git push -u origin main
  echo
  echo "[!] Now enable Pages once: repo > Settings > Pages >"
  echo "    Source 'Deploy from a branch' > Branch 'main' / '/ (root)' > Save."
fi

# ----- final URL ----------------------------------------------------------
if [ "$REPO" = "${USER_LC}.github.io" ]; then
  SITE="https://${USER_LC}.github.io/"
else
  SITE="https://${USER_LC}.github.io/${REPO}/"
fi
echo
echo "[+] Done. In ~1 minute your site will be live at:"
echo "      $SITE"
echo "    (HTTPS is automatic. Add a custom domain later in Settings > Pages.)"
