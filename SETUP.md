# GitHub Setup Instructions

Quick guide to push this repo to GitHub.

## Step 1: Create GitHub Repo

1. Go to https://github.com/new
2. Repository name: `claude-website-qa-skill`
3. Description: `Professional website QA audit skill for Claude Code - detects broken links, spelling/grammar errors, and provides AI-powered suggestions`
4. **Public** repository
5. **DO NOT** initialize with README (we already have one)
6. Click "Create repository"

## Step 2: Push to GitHub

```bash
cd ~/Desktop/claude-website-qa-skill

# Initialize git repo
git init

# Add all files
git add .

# Create initial commit
git commit -m "Initial commit: Website QA Audit skill for Claude Code

Features:
- Broken link detection (nav, footer, content)
- Spelling & grammar checks with real estate term awareness
- Contact & social media consistency validation
- AI-powered title/heading suggestions
- 9-11 min audits for 10-20 page sites
- Professional markdown + Salesforce-ready reports
- Zero external dependencies

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"

# Set main branch
git branch -M main

# Add GitHub remote (replace YOUR-USERNAME with your GitHub username)
git remote add origin https://github.com/YOUR-USERNAME/claude-website-qa-skill.git

# Push to GitHub
git push -u origin main
```

## Step 3: Update README

After pushing, go back and update the README.md file to replace `YOUR-USERNAME` with your actual GitHub username in:
- Badge links
- Clone URL
- Related projects links
- Issues link

Then commit and push:

```bash
git add README.md
git commit -m "Update README with correct GitHub username"
git push
```

## Step 4: Add Topics (Optional but Recommended)

On your GitHub repo page:
1. Click "⚙️ Settings" (top right of repo)
2. Scroll to "Topics"
3. Add: `claude-code`, `website-audit`, `qa-tools`, `real-estate`, `browser-automation`, `ai-tools`

## Step 5: Share!

Your repo URL will be:
```
https://github.com/YOUR-USERNAME/claude-website-qa-skill
```

Share this link with anyone who wants to use the skill!

## Quick Installation for Others

They can install with one command:

```bash
# Clone and install
git clone https://github.com/YOUR-USERNAME/claude-website-qa-skill.git /tmp/skill && \
mkdir -p ~/.claude/skills/website-qa-audit && \
cp /tmp/skill/skill.md ~/.claude/skills/website-qa-audit/ && \
rm -rf /tmp/skill && \
echo "✅ Installed! Restart VS Code to use."
```
