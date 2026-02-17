# Website QA Audit Skill for Claude Code

A professional website audit skill for Claude Code that performs comprehensive content QA checks using browser automation.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## ✨ Features

- 🔗 **Broken Link Detection** - Tests navigation, footer, and content links
- 📝 **Spelling & Grammar Checks** - With real estate term awareness
- 📧 **Contact Consistency** - Validates email, phone across all pages
- 📱 **Social Media Validation** - Ensures consistent social links
- 🎯 **AI-Powered Suggestions** - Title and heading improvements based on brand context
- ⚡ **Fast** - 9-11 minutes for 10-20 page sites
- 📊 **Professional Reports** - Markdown + Salesforce-ready format

## 🚀 Quick Start

### Prerequisites

- [Claude Code](https://claude.ai/code) installed in VS Code
- Playwright MCP plugin (enabled by default)

### Installation

```bash
# 1. Clone or download this repo
git clone https://github.com/YOUR-USERNAME/claude-website-qa-skill.git

# 2. Copy skill to Claude directory
mkdir -p ~/.claude/skills/website-qa-audit
cp claude-website-qa-skill/skill.md ~/.claude/skills/website-qa-audit/

# 3. Verify installation
ls -la ~/.claude/skills/website-qa-audit/skill.md

# 4. Restart VS Code
```

### Usage

Open Claude Code in VS Code and say:

```
audit this site: https://example.com
```

Or for pre-launch checks:

```
qa this staging site before I send to client: https://staging.example.com
```

## 📋 What It Checks

### Priority 1: Critical Issues (BLOCKING)
- ❌ Broken navigation/footer links
- ❌ Broken content links
- ❌ Email address inconsistencies
- ❌ Phone number inconsistencies
- ❌ Social media link mismatches

### Priority 2: Spelling Errors
- ✍️ Detects misspellings in all content
- 🏠 **Real estate aware** - ignores MLS, REALTOR, luxe, FSBO, HOA, brokerage names

### Priority 3: Grammar Issues
- 📖 Subject-verb agreement
- 🔤 Punctuation problems
- ✂️ Sentence fragments

### Priority 4: Enhancement Suggestions
- 📄 Generic page titles → Specific, SEO-friendly alternatives
- 🎯 Weak CTAs → Stronger calls-to-action
- 🎨 Brand voice alignment (analyzed from social media)

## 📊 Output Format

### 1. Detailed Markdown Report

Saved to: `.tmp/qa-audit-[domain]-[timestamp].md`

```markdown
# Website QA Audit Report

**Site:** https://example.com
**Audited:** February 17, 2026 at 2:30 PM
**Duration:** 9.2 minutes
**Pages Audited:** 15

## Summary
- ⚠️ **Broken Links:** 2 found
- ⚠️ **Contact/Social Inconsistencies:** 1 found
- ✅ **Spelling Errors:** 0 found
- ✅ **Grammar Issues:** 0 found
- ✅ **Title Suggestions:** 3 recommendations
```

**Cleanup:** Only the 3 most recent reports are kept; older ones auto-delete.

### 2. Team-Friendly Salesforce Format

Ready to copy-paste into project management tools:

```
Great work on the McKenzie Realty site, team! Let's fix these items:

- All 5 social media icons in the footer (Facebook, Instagram, LinkedIn, Twitter, YouTube) currently link to generic platform homepages instead of Tashina's actual profiles—update the footer settings to link to her real social media accounts.

- The test property "TEST TEST TEST TEST" is visible in the Featured Properties carousel on the homepage and agent page—delete or hide this test listing from the backend CMS before launch.
```

## 🔍 How It Works

### 5-Phase Workflow

**Phase 0: Understand the Client (2-3 min)**
- Extracts homepage context, contact info, social media links
- Visits primary social profiles to understand brand voice
- Creates baseline for consistency checking

**Phase 1: Map All Pages (1-2 min)**
- Discovers all navigation and footer links
- Builds complete page list

**Phase 2: Systematic Page Audit (4-5 min)**
- Visits each page (10-second timeout)
- Extracts all content using `browser_snapshot` (no scrolling)
- Runs all 4 priority checks
- Validates consistency against Phase 0 baseline

**Phase 3: Smart Link Testing (1-2 min)**
- 100% of nav/footer links tested
- 20% sample of content links
- External links verified (not clicked)

**Phase 4: Generate Report (<1 min)**
- Compiles findings into markdown report
- Generates Salesforce-friendly format
- Cleans up old reports (keeps most recent 3)

## 🎯 Real-World Impact

**Use Case:** Luxury Presence Design PM workflow

| Metric | Before | After |
|--------|--------|-------|
| Time per site | 20+ min | 9-11 min |
| Time savings | - | **55-65%** |
| Issues caught | Manual spotting | Systematic detection |
| Report format | Custom per PM | Standardized + Salesforce-ready |

## 🛠️ Technical Details

### Dependencies

**None!** Uses only built-in Claude Code tools:
- `mcp__plugin_playwright_playwright__browser_navigate`
- `mcp__plugin_playwright_playwright__browser_snapshot`
- `mcp__plugin_playwright_playwright__browser_evaluate`
- `mcp__plugin_playwright_playwright__browser_click`

No Python scripts. No API keys. No external services.

### Speed Optimizations

- ⚡ **Single-pass extraction** - `browser_snapshot` gets all text at once (no scrolling)
- 🔍 **Template detection** - Recognizes repeating page types, spot-checks instead of full audit
- 🎯 **Smart sampling** - 100% critical links, 20% content links
- ⏱️ **Timeout protection** - 10-second limit per page (doesn't fail entire audit)

### Real Estate Term Handling

Automatically ignores industry-standard terms to avoid false positives:
- MLS, REALTOR, Realtor
- luxe, lux
- FSBO (For Sale By Owner)
- HOA (Homeowners Association)
- Brokerage names: Sotheby's, Coldwell, Keller Williams, Re/Max
- Platform names: Zillow, Trulia, Realtor.com

## 🐛 Troubleshooting

### "Skill not found"

1. Verify file location: `~/.claude/skills/website-qa-audit/skill.md`
2. Check directory name matches exactly: `website-qa-audit`
3. Restart VS Code completely
4. Try: `list available skills` in Claude Code

### "Browser tools not available"

Playwright plugin should be enabled by default. If not:

```bash
# Check plugin status in Claude Code settings
cat ~/.claude/settings.json | grep playwright
```

### "Audit taking too long"

- Large sites (50+ pages) naturally take longer
- Template detection automatically optimizes for repeating page types
- Each page has a 10-second timeout to prevent hanging

### "Too many spelling false positives"

If you find industry terms being incorrectly flagged:

1. Edit `skill.md`
2. Find the "Real Estate Term Ignore List" in Priority 2 section
3. Add your terms to the list
4. Restart Claude Code

## 📝 Example Output

<details>
<summary>Click to see a full example report</summary>

```markdown
# Website QA Audit Report

**Site:** https://mckenzie-realty.com
**Audited:** February 17, 2026 at 11:20 AM
**Duration:** 9.2 minutes
**Pages Audited:** 15

---

## Summary

- ⚠️ **Broken Links:** 0 found
- ⚠️ **Contact/Social Inconsistencies:** 1 found
- ✅ **Spelling Errors:** 0 found
- ✅ **Grammar Issues:** 0 found
- ✅ **Title Suggestions:** 2 recommendations

**Overall Status:** ⚠️ Minor issues found - recommend fixes before launch

---

## PRIORITY 1: BROKEN LINKS / INCONSISTENCIES (CRITICAL)

### Contact & Social Media Inconsistencies

#### Issue #1: Social Media Links Point to Generic Pages
- **Severity:** MEDIUM
- **Current:** All 5 social media icons link to platform homepages
- **Expected:** Links to actual agent profiles
- **Error:** Footer social icons not configured
- **Location:** Footer (all pages)
- **Impact:** Lost opportunity for social engagement, unprofessional appearance

**Recommendation:** Update footer settings to link to agent's actual social media profiles.

---

## PRIORITY 2: SPELLING ERRORS

✅ No spelling errors found

---

## PRIORITY 3: GRAMMAR ISSUES

✅ No grammar issues found

---

## PRIORITY 4: TITLE & HEADING SUGGESTIONS

### Suggestion #1
- **Severity:** MEDIUM
- **Current:** "About"
- **Suggested:** "About Tashina McKenzie | Pembroke Pines Real Estate Expert"
- **Rationale:** More specific, includes agent name and location for SEO. Matches professional tone observed on Instagram.
- **Location:** About Page → Page Title

---

## Pages Audited

1. ✅ / (Homepage)
2. ✅ /about (About Tashina)
3. ✅ /properties (Featured Properties)
4. ✅ /communities (Communities)
5. ✅ /contact (Contact)
... (full list)

---

**Salesforce Format:**

Great work on the McKenzie Realty site, team! Let's fix these items:

- All 5 social media icons in the footer (Facebook, Instagram, LinkedIn, Twitter, YouTube) currently link to generic platform homepages instead of Tashina's actual profiles—update the footer settings to link to her real social media accounts.
```

</details>

## 🤝 Contributing

Contributions welcome! Please:

1. Fork the repo
2. Create a feature branch
3. Test your changes thoroughly
4. Submit a pull request

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

## 👤 Author

**Ivan Aguilar**
- Design PM optimizing real estate website workflows
- Built for the Luxury Presence ecosystem

## 🙏 Acknowledgments

- Inspired by real-world Design PM workflows at Luxury Presence
- Optimized for speed through smart sampling and template detection
- Real estate term awareness based on industry standards

## 📚 Related

Looking for more comprehensive audits including design consistency checks? Check out the [Website Design QA Skill](https://github.com/YOUR-USERNAME/claude-website-design-qa-skill) (coming soon).

---

**Questions or issues?** [Open an issue](https://github.com/YOUR-USERNAME/claude-website-qa-skill/issues) or reach out!
