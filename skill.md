---
name: website-qa-audit
description: Use when the user asks to audit, QA, or quality-check a website. Performs comprehensive website audit finding broken links, spelling/grammar errors, and suggesting title improvements. Completes in under 9 minutes for sites with 10-20 pages.
---

# Website QA Audit

## Overview

Automated website quality assurance audit using browser automation tools. Systematically checks for broken links, spelling errors, grammar issues, and provides AI-powered title/heading suggestions. Generates professional markdown report in `.tmp/`.

**Core principle:** Speed through smart sampling - audit 100% of navigation, sample content intelligently, use `browser_snapshot` to extract all text at once (no multiple scrolls).

## When to Use

Use this skill when:
- User says "audit this site: <url>"
- User wants to QA a website before client delivery
- User needs to check for broken links, typos, or content issues
- User mentions website quality check, QA, or review

**Do NOT use for:**
- SEO audits (this focuses on QA, not SEO)
- Performance testing (no page speed analysis)
- Security audits (no vulnerability scanning)

## Workflow Checklist

**YOU MUST create these TodoWrite tasks before starting:**

```
- Phase 0: Understand the client - extract homepage context
- Phase 1: Map all pages - build complete page list
- Phase 2: Audit each page - run 4 priority checks
- Phase 3: Test links - verify navigation and content links
- Phase 4: Generate report - compile findings to markdown AND Salesforce format
```

## The 5-Phase Audit Workflow

### Phase 0: Understand the Client (2-3 min)

**Goal:** Learn the client's brand, tone, social media presence, and contact info for later title suggestions and consistency checking.

1. Navigate to homepage
2. Use `mcp__plugin_playwright_playwright__browser_snapshot` to extract ALL text at once
3. Use `mcp__plugin_playwright_playwright__browser_evaluate` with `filter="interactive"` to extract all social media links (Facebook, Instagram, LinkedIn, Twitter, Zillow, etc.)
4. Extract contact information from footer: email, phone number
5. Visit 1-2 primary social media profiles (Facebook + Instagram preferred, or next available)
6. Use `browser_snapshot` on each social profile to extract: agent name, bio, recent posts, brand voice
7. Synthesize and store:
   - Business name, industry, location
   - Brand tone (professional, luxury, casual, family-friendly, etc.)
   - Unique terminology or style
   - Social media presence (active platforms, content themes)
   - Baseline contact info (email, phone)
   - Baseline social media links

**Example:**
```
Site: Lynne Hendricks | Newburyport Real Estate
Agent: Lynne Hendricks
Brokerage: Gibson Sotheby's International Realty
Industry: Real estate
Location: Newburyport, MA
Tone: Professional, calm, thoughtful, personable
Social: Facebook (active - listings + community), Instagram (property photos)
Contact: lynne.hendricks@gibsonsir.com, (123) 456-7890
```

### Phase 1: Map All Pages (1-2 min)

**Goal:** Build complete list of pages to audit.

1. Check for hamburger menu - click if present using `mcp__plugin_playwright_playwright__browser_click`
2. Use `mcp__plugin_playwright_playwright__browser_evaluate` with `filter="interactive"` to extract all nav links
3. Scroll to footer and extract footer links
4. Build master page list with URLs

**Optimization:** Group pages by template type (e.g., all property listings, all agent pages) for smart sampling later.

### Phase 2: Systematic Page Audit (4-5 min)

**Goal:** Visit each page and run all 4 priority checks, plus consistency checks.

For each page:
1. Navigate using `mcp__plugin_playwright_playwright__browser_navigate` with 10-second timeout
   - If page doesn't load in 10 seconds, log "Page [X] timed out - skipped" and continue to next page
   - Don't fail the entire audit due to one slow page
2. **Extract content:** Use `browser_snapshot` (gets ALL text instantly - no scrolling)
3. **Extract links:** Use `browser_evaluate(filter="interactive")` to get clickable elements with refs
4. **Extract contact info:** Email, phone from this page
5. **Extract social media links:** All social links from this page
6. **Run consistency checks:**
   - Compare contact info against Phase 0 baseline - flag any differences as Priority 1 HIGH severity
   - Compare social media links against Phase 0 baseline - flag any differences as Priority 1 MEDIUM severity
7. **Run checks:** All 4 priorities (see Error Detection Logic below)

**Template Detection:** If you see repeating page types (e.g., 10 property detail pages), fully audit first 2, then spot-check the rest.

### Phase 3: Smart Link Testing (1-2 min)

**Goal:** Find broken links efficiently without testing every single link.

**Testing strategy:**
- ✅ **100%:** All navigation menu links
- ✅ **100%:** All footer links
- ✅ **20% sample:** Content area links (test ~5 out of 25)
- ⏭️ **Skip:** External social media links (just verify href exists)

**How to test:**
- Use `mcp__plugin_playwright_playwright__browser_click` on link ref
- Check for errors:
  - 404 Not Found
  - Blank/empty page
  - Timeout
  - Unexpected redirect

**Capture for each broken link:**
- Link text (what the user sees)
- Destination URL
- Error type
- Location (Page → Section)

### Phase 4: Generate Report (<1 min)

**Goal:** Compile all findings into professional markdown report AND team-friendly Salesforce format.

1. Organize findings by priority (broken links → spelling → grammar → suggestions)
2. Generate filename: `.tmp/qa-audit-[domain]-[timestamp].md`
3. Write full detailed report using `Write` tool (see Report Format below)
4. **ALWAYS provide team-friendly Salesforce format** at the end (see Salesforce Format below)
5. **Cleanup old reports:** After generating the new report, keep only the 3 most recent `qa-audit-*.md` files in `.tmp/` and delete older ones (use `ls -t .tmp/qa-audit-*.md | tail -n +4 | xargs rm -f` or equivalent)

## Error Detection Logic

### Priority 1: Broken Links & Inconsistencies (CRITICAL)

#### Broken Links

**Method:**
- Click each link using `browser_click`
- Check response for errors

**Severity:**
- **HIGH:** Broken navigation menu links, footer links, or primary CTAs
- **MEDIUM:** Broken content area links

**Capture:**
```
- Severity: HIGH
- Link text: "View Properties"
- Destination: /properties/search
- Error: 404 Not Found
- Location: Homepage → Hero Section → CTA button
```

#### Contact & Social Media Inconsistencies

**Method:**
- Compare contact info (email, phone) on each page against Phase 0 baseline
- Compare social media links on each page against Phase 0 baseline
- Flag any differences

**Severity:**
- **HIGH:** Email or phone number inconsistencies (could cause client confusion or lost leads)
- **MEDIUM:** Social media link inconsistencies (less critical, but unprofessional)

**Capture:**
```
- Severity: HIGH
- Type: Email inconsistency
- Current (this page): lynneh@rognest.com
- Expected (baseline): lynne.hendricks@gibsonsir.com
- Location: About Page → Contact Details Section
- Impact: CRITICAL - potential client confusion, emails may go to wrong address
```

### Priority 2: Spelling Errors

**Method:**
- Extract text via `browser_snapshot`
- Use your language model to identify misspellings
- **IGNORE:** Proper nouns, brand terms, industry jargon

**Real Estate Term Ignore List:**
- MLS, REALTOR, Realtor, realtor (trademarked terms, capitalization varies)
- luxe, lux (luxury abbreviations)
- FSBO (For Sale By Owner)
- HOA (Homeowners Association)
- Sotheby's, Coldwell, Keller Williams, Re/Max (brokerage names)
- Zillow, Trulia, Realtor.com (platform names)
- Any agent/team/brokerage proper nouns

**Severity:**
- **HIGH:** Client-facing text (homepage, about, contact pages)
- **MEDIUM:** Secondary pages (blog posts, property descriptions)

**Capture:**
```
- Severity: HIGH
- Misspelled: "recieve"
- Should be: "receive"
- Location: About Page → Agent Bio → Paragraph 2
```

### Priority 3: Grammar Issues

**Method:**
- Scan text for obvious grammar problems:
  - Subject-verb disagreement ("The team are...")
  - Missing punctuation
  - Sentence fragments
- **IGNORE:** Minor style preferences

**Severity:**
- **HIGH:** Obvious errors in client-facing content (homepage, about, contact)
- **MEDIUM:** Minor issues or errors in secondary pages

**Capture:**
```
- Severity: HIGH
- Problem: "The team are available"
- Fix: "The team is available"
- Location: Contact Page → Hours Section
```

### Priority 4: Title & Heading Suggestions (VALUE-ADD)

**Method:**
- Review all page titles, section headings, CTA buttons
- Compare against client context from Phase 0 (including social media brand voice)
- Suggest improvements when:
  - Title is generic ("Services", "About Us")
  - Heading doesn't match content
  - CTA is weak ("Click Here", "Learn More")
  - Tone is inconsistent with brand (check social media voice for reference)

**Severity:**
- **MEDIUM:** Generic or weak titles on key pages (homepage, about, services)
- **LOW:** Minor improvements or secondary pages

**Capture:**
```
- Severity: MEDIUM
- Current: "Services"
- Suggested: "Luxury Real Estate Services in Miami"
- Rationale: More specific, includes location, matches professional tone observed on Facebook and Instagram
- Location: Services Page → Page Title
```

## Report Format

Generate: `.tmp/qa-audit-[domain]-[timestamp].md`

**Note:** Reports are temporary files—only the 3 most recent audits are kept, older ones are auto-deleted.

**Structure:**

```markdown
# Website QA Audit Report

**Site:** https://example.com
**Audited:** February 17, 2026 at 2:30 PM
**Duration:** 7.5 minutes
**Pages Audited:** 12

---

## Summary

- ⚠️ **Broken Links:** 2 found
- ⚠️ **Contact/Social Inconsistencies:** 1 found
- ✅ **Spelling Errors:** 1 found
- ✅ **Grammar Issues:** 0 found
- ✅ **Title Suggestions:** 3 recommendations

**Overall Status:** ⚠️ Minor issues found - recommend fixes before launch

---

## PRIORITY 1: BROKEN LINKS / INCONSISTENCIES (CRITICAL)

### Broken Links

#### Issue #1
- **Severity:** HIGH
- **Link text:** "View All Properties"
- **Destination:** `/properties/search`
- **Error:** 404 Not Found
- **Location:** Homepage → Hero Section → CTA Button

### Contact & Social Media Inconsistencies

#### Issue #1: Email Address Inconsistency
- **Severity:** HIGH
- **Current (About page):** lynneh@rognest.com
- **Expected (Footer across site):** lynne.hendricks@gibsonsir.com
- **Error:** Email address mismatch between About page and rest of site
- **Location:** About Page → Contact Details Section
- **Impact:** CRITICAL - potential client confusion, emails may go to wrong address

**Recommendation:** Update About page email to match footer email (lynne.hendricks@gibsonsir.com).

---

## PRIORITY 2: SPELLING ERRORS

### Issue #1
- **Severity:** HIGH
- **Misspelled:** "recieve"
- **Should be:** "receive"
- **Location:** About Page → Agent Bio → Paragraph 2

---

## PRIORITY 3: GRAMMAR ISSUES

✅ No grammar issues found

---

## PRIORITY 4: TITLE & HEADING SUGGESTIONS

### Suggestion #1
- **Severity:** MEDIUM
- **Current:** "About Us"
- **Suggested:** "Meet Your Miami Luxury Real Estate Team"
- **Rationale:** More specific, includes location and service. Matches professional tone observed on social media.
- **Location:** About Page → Page Title

---

## Pages Audited

1. ✅ / (Homepage)
2. ✅ /about (About)
3. ✅ /properties (Properties)
4. ✅ /contact (Contact)
... (full list)

---

**Generated by:** Claude Code Website QA Audit Tool
```

## Salesforce Format (Team-Friendly Output)

**ALWAYS provide this format at the end of EVERY audit** for easy copy-paste into Salesforce.

**Format:**
```
Great work on [business name] site, team! Let's fix these items:

- [Single cohesive sentence combining: what the problem is, where it's located, and the solution]
- [Another issue in same format]
- [etc.]
```

**Rules:**
- Include ALL issues from Priority 1-3 (broken links, spelling, grammar, contact inconsistencies, social media issues)
- Only include Priority 4 (title suggestions) when they add significant value to client or website (use judgment)
- Each issue is ONE cohesive sentence
- Use bullet points (not numbered)
- Keep concise and actionable

**If no issues found:**
```
Great work on the site, team. No additional notes on my end. This site is ready for DPA QA.
```

**Example (with issues):**
```
Great work on the McKenzie Realty site, team! Let's fix these items:

- All 5 social media icons in the footer (Facebook, Instagram, LinkedIn, Twitter, YouTube) currently link to generic platform homepages instead of Tashina's actual profiles—update the footer settings to link to her real social media accounts.

- The test property "TEST TEST TEST TEST" is visible in the Featured Properties carousel on the homepage and agent page—delete or hide this test listing from the backend CMS before launch.
```

## Usage Examples

**Basic audit:**
```
User: "audit https://agent-site.luxurypresence.com"
You: [Follow 5-phase workflow, generate report]
```

**With context:**
```
User: "qa this staging site before I send to client: https://demo.vercel.app"
You: [Run audit, emphasize broken links and spelling in summary]
```

**Quick check:**
```
User: "check this site for broken links: https://mysite.com"
You: [Run full audit, but emphasize broken links section in report]
```

## Speed Optimizations from Perplexity Analysis

**Based on the Perplexity Comet workflow analysis:**

1. **Use `browser_snapshot` not multiple scrolls** - extract ALL text at once (saves 5-10 actions per page)
2. **Don't visit external links** - just verify href exists (saves 3-5 seconds per external link)
3. **Template detection** - if 10 neighborhood pages use same template, audit 2 fully, spot-check rest
4. **Smart link sampling** - 100% nav/footer, 20% content (maintains quality, saves time)
5. **No screenshots for text** - use `browser_snapshot` for instant text extraction

**Time Budget:**
- Phase 0: 2-3 min (includes social media context gathering)
- Phase 1: 1-2 min
- Phase 2: 4-5 min (includes consistency checks)
- Phase 3: 1-2 min
- Phase 4: <1 min
- **Total: ~9-11 minutes for 10-20 page sites** (slightly longer due to social media context and consistency checks, but provides significantly more value)

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Scrolling multiple times to read content | Use `browser_snapshot` once to get ALL text |
| Testing every single link | Sample 20% of content links, 100% of nav/footer |
| Flagging real estate terms as misspellings | Check real estate term ignore list - "MLS", "REALTOR", "luxe" are valid |
| Suggesting title changes that don't match brand | Review Phase 0 context (including social media voice) before suggestions |
| Spending too long on one page | Template detection - spot-check repeating patterns |
| Missing contact inconsistencies | Always compare email/phone/social against Phase 0 baseline |

## Real-World Impact

**Use Case:** Luxury Presence Design PM workflow

**Before:** 20+ min manual QA per staging site
**After:** 7-9 min automated audit
**Time Savings:** 55-65% reduction in QA time

**Value:** Catches issues before clients see staging sites, improves quality, demonstrates AI innovation for promotion case.
