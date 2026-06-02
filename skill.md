---
name: wp-blog-scheduler
description: >
  WordPress blog content planner and auto-scheduler skill for Claude Cowork. Generates a
  7-day blog content calendar with SEO-optimised posts and schedules them directly to
  WordPress via a local Python API. Trigger this skill whenever a user wants to write blog
  posts, plan a content calendar, schedule WordPress content, rank blog posts on Google,
  generate SEO articles, or says things like "write blogs for my website", "plan my content
  for the week", "schedule posts to WordPress", "help me rank on Google with blog content",
  or "generate SEO blog content". Connects directly to WordPress via a local Python bridge
  script — no plugin required. This skill runs exclusively in Claude Cowork (desktop app).
---

# WordPress Blog Scheduler Skill

## IMPORTANT: This skill requires Claude Cowork (desktop app)
If the user is not in Claude Cowork, tell them:
"This skill requires Claude Cowork to run the local WordPress API bridge.
Download it at claude.ai/cowork and reopen this conversation there."
Then stop. Do not proceed without Cowork.

---

You are an expert SEO content strategist, blog writer, and WordPress automation specialist.

## Full Flow Overview

```
Step 0 — Collect WordPress credentials via form widget
Step 1 — Generate wp_scheduler.py pre-filled with their credentials
Step 2 — User downloads and runs the script
Step 3 — Claude Cowork installs dependencies and tests the connection
Step 4 — Collect niche + keywords via form widget
Step 5 — Research keywords, generate 7 blog idea cards
Step 6 — User selects posts to publish
Step 7 — Claude writes each post (1500 words, SEO-optimised)
Step 8 — Claude Cowork posts each article via local Python API
Step 9 — Show scheduling results dashboard
```

---

## Core Rules

1. Collect all info via single multi-step form widgets — never plain text questions.
2. Generate the Python script FIRST before doing anything with WordPress.
3. All WordPress API calls go through the local Python API — never direct from Claude.
4. Use Claude Cowork terminal for: pip installs, running curl tests, health checks.
5. Only proceed to content generation after health check passes.
6. Write full 1500-word posts — not outlines, not summaries. Complete publish-ready content.
7. All prices in Rs. — never use ₹ symbol.
8. Type "Generate Now" at any point to proceed with available data.

---

## UI Rendering Rules — MANDATORY

Never ask questions as plain text. Use `visualize:show_widget` for all intake forms.
Single multi-step widget per stage — renders once, no reload between steps.

### Mandatory CSS

```css
* { box-sizing: border-box; }
.wrap { max-width: 600px; padding: 20px 0; font-family: inherit; }
.prog-label { font-size: 12px; color: var(--color-text-tertiary); margin-bottom: 6px; letter-spacing: .3px; }
.prog-bar { height: 3px; background: var(--color-border-tertiary); border-radius: 2px; margin-bottom: 20px; }
.prog-fill { height: 3px; background: var(--color-text-primary); border-radius: 2px; transition: width .3s; }
.step-title { font-size: 18px; font-weight: 500; color: var(--color-text-primary); margin: 0 0 3px; }
.step-sub { font-size: 13px; color: var(--color-text-tertiary); margin: 0 0 18px; }
hr.dv { border: none; border-top: 0.5px solid var(--color-border-tertiary); margin: 14px 0; }
.field { margin-bottom: 16px; }
.field label { display: block; font-size: 11px; font-weight: 500; color: var(--color-text-secondary); letter-spacing: .4px; text-transform: uppercase; margin-bottom: 6px; }
.field input, .field select, .field textarea { width: 100%; padding: 10px 14px; font-size: 14px; color: var(--color-text-primary); background: var(--color-background-secondary); border: 1.5px solid var(--color-border-secondary); border-radius: 8px; outline: none; font-family: inherit; transition: border-color .15s; }
.field input:focus, .field select:focus, .field textarea:focus { border-color: var(--color-text-primary); }
.field input::placeholder, .field textarea::placeholder { color: var(--color-text-tertiary); font-size: 13px; }
.field textarea { resize: vertical; min-height: 90px; }
.field select option { background: var(--color-background-primary); }
.row { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
.badge-req { font-size: 10px; color: #e05c5c; background: rgba(224,92,92,.1); padding: 2px 7px; border-radius: 20px; margin-left: 5px; vertical-align: middle; text-transform: none; letter-spacing: 0; }
.badge-opt { font-size: 10px; color: var(--color-text-tertiary); background: var(--color-background-secondary); padding: 2px 7px; border-radius: 20px; margin-left: 5px; border: 0.5px solid var(--color-border-tertiary); vertical-align: middle; text-transform: none; letter-spacing: 0; }
.sec { font-size: 11px; font-weight: 500; color: var(--color-text-secondary); letter-spacing: .4px; text-transform: uppercase; margin: 0 0 8px; }
.tgrid { display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 14px; }
.tb, .rb { padding: 7px 14px; border-radius: 8px; font-size: 13px; border: 1.5px solid var(--color-border-secondary); background: transparent; color: var(--color-text-primary); cursor: pointer; transition: all .12s; }
.tb:hover, .rb:hover:not(.sel) { background: var(--color-background-secondary); }
.tb.sel, .rb.sel { background: var(--color-text-primary) !important; color: var(--color-background-primary) !important; border-color: var(--color-text-primary) !important; font-weight: 500; }
.btn-row { display: flex; gap: 10px; margin-top: 20px; }
.btn-back { flex: 1; padding: 10px; font-size: 14px; background: transparent; color: var(--color-text-secondary); border: 1.5px solid var(--color-border-secondary); border-radius: 8px; cursor: pointer; }
.btn-back:hover { background: var(--color-background-secondary); }
.btn-next { flex: 2; padding: 10px; font-size: 14px; font-weight: 500; background: var(--color-text-primary); color: var(--color-background-primary); border: none; border-radius: 8px; cursor: pointer; }
.btn-next:hover { opacity: .85; }
.hint { font-size: 12px; color: var(--color-text-tertiary); margin-top: 5px; }
.card { border: 1.5px solid var(--color-border-secondary); border-radius: 10px; padding: 14px 16px; margin-bottom: 10px; cursor: pointer; transition: all .15s; }
.card:hover { border-color: var(--color-border-primary); background: var(--color-background-secondary); }
.card.sel { border-color: var(--color-text-primary) !important; background: var(--color-background-secondary); }
.card-title { font-size: 14px; font-weight: 500; color: var(--color-text-primary); margin: 0 0 6px; }
.card-meta { font-size: 12px; color: var(--color-text-tertiary); display: flex; gap: 12px; flex-wrap: wrap; }
.card-check { float: right; width: 18px; height: 18px; border: 1.5px solid var(--color-border-secondary); border-radius: 4px; display: inline-flex; align-items: center; justify-content: center; font-size: 11px; }
.card.sel .card-check { background: var(--color-text-primary); color: var(--color-background-primary); border-color: var(--color-text-primary); }
.counter { font-size: 13px; color: var(--color-text-secondary); margin-bottom: 12px; }
.footer-link { font-size: 11px; color: var(--color-text-tertiary); text-align: center; margin-top: 14px; }
.footer-link a { color: var(--color-text-secondary); }
```

---

## STAGE 0 — WordPress Credentials Form

Render this as the very first widget. Collect:

- WordPress Site URL *(required — e.g. https://mybusiness.com)*
- WordPress Username *(required)*
- Application Password *(required)*
- Publish Time *(required — e.g. 09:00)*
- Timezone *(required — dropdown: Asia/Kolkata / Asia/Dubai / UTC / Other)*
- API Port *(optional — default 5678)*

Show this helper note inside the form:
```
How to get your Application Password:
1. WP Admin → Users → Your Profile
2. Scroll to "Application Passwords"
3. Name it "Claude Scheduler" → click Add New
4. Copy the password shown (only shown once)
```

On submit → `sendPrompt('WP_SETUP | site: X | user: X | password: X | time: X | tz: X | port: X')`

Store all values in memory as: WP_SITE, WP_USER, WP_PASS, PUBLISH_TIME, TIMEZONE, API_PORT

---

## STAGE 1 — Generate Python API Script

After receiving WP_SETUP, immediately generate the complete Python script.
The script must be pre-filled with the actual credentials from WP_SETUP — not placeholders.

### Python Script: wp_scheduler.py

Generate this exact script with credentials substituted in:

```python
#!/usr/bin/env python3
"""
WordPress Blog Scheduler API
Generated by Claude Cowork — wp-blog-scheduler skill
Site: {WP_SITE}
Run: python wp_scheduler.py
API: http://localhost:{API_PORT}
"""

import json
import base64
import logging
from datetime import datetime
from flask import Flask, request, jsonify
import requests

# ── Configuration (pre-filled by Claude) ──────────────────────
WP_SITE     = "{WP_SITE}"
WP_USER     = "{WP_USER}"
WP_PASS     = "{WP_PASS}"
PUBLISH_TIME = "{PUBLISH_TIME}"
TIMEZONE    = "{TIMEZONE}"
API_PORT    = {API_PORT}
# ──────────────────────────────────────────────────────────────

app = Flask(__name__)
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s %(levelname)s %(message)s',
    handlers=[
        logging.FileHandler('schedule_log.txt'),
        logging.StreamHandler()
    ]
)

def wp_headers():
    token = base64.b64encode(f"{WP_USER}:{WP_PASS}".encode()).decode()
    return {
        "Authorization": f"Basic {token}",
        "Content-Type": "application/json"
    }

def get_or_create_term(taxonomy, name):
    """Get existing category/tag ID or create it."""
    url = f"{WP_SITE}/wp-json/wp/v2/{taxonomy}"
    r = requests.get(url, headers=wp_headers(), params={"search": name}, timeout=15)
    items = r.json()
    if isinstance(items, list) and items:
        return items[0]["id"]
    r2 = requests.post(url, headers=wp_headers(), json={"name": name}, timeout=15)
    data = r2.json()
    if "id" in data:
        return data["id"]
    logging.warning(f"Could not create {taxonomy}: {name}")
    return None


@app.route("/health", methods=["GET"])
def health():
    """Test WordPress connection."""
    try:
        r = requests.get(
            f"{WP_SITE}/wp-json/wp/v2/users/me",
            headers=wp_headers(),
            timeout=10
        )
        if r.status_code == 200:
            user = r.json()
            return jsonify({
                "status": "ok",
                "site": WP_SITE,
                "connected_as": user.get("name", WP_USER),
                "wp_version": r.headers.get("X-WP-Total", "unknown")
            })
        return jsonify({
            "status": "error",
            "code": r.status_code,
            "message": r.text[:200]
        }), 400
    except Exception as e:
        return jsonify({"status": "error", "message": str(e)}), 500


@app.route("/schedule-post", methods=["POST"])
def schedule_post():
    """Schedule a single blog post to WordPress."""
    data = request.get_json()
    if not data:
        return jsonify({"error": "No JSON body"}), 400

    required = ["title", "content", "slug", "scheduled_date"]
    for field in required:
        if field not in data:
            return jsonify({"error": f"Missing field: {field}"}), 400

    try:
        # Resolve category ID
        category_id = None
        if data.get("category"):
            category_id = get_or_create_term("categories", data["category"])

        # Resolve tag IDs
        tag_ids = []
        for tag in data.get("tags", []):
            tid = get_or_create_term("tags", tag)
            if tid:
                tag_ids.append(tid)

        # Build post payload
        payload = {
            "title":          data["title"],
            "content":        data["content"],
            "slug":           data["slug"],
            "status":         "future",
            "date":           data["scheduled_date"],
            "excerpt":        data.get("meta_description", ""),
            "comment_status": "open",
            "meta": {
                "_yoast_wpseo_title":    data.get("seo_title", data["title"]),
                "_yoast_wpseo_metadesc": data.get("meta_description", ""),
                "_yoast_wpseo_focuskw":  data.get("focus_keyword", ""),
                "rank_math_title":       data.get("seo_title", data["title"]),
                "rank_math_description": data.get("meta_description", ""),
                "rank_math_focus_keyword": data.get("focus_keyword", "")
            }
        }
        if category_id:
            payload["categories"] = [category_id]
        if tag_ids:
            payload["tags"] = tag_ids

        r = requests.post(
            f"{WP_SITE}/wp-json/wp/v2/posts",
            headers=wp_headers(),
            json=payload,
            timeout=30
        )

        result = r.json()
        if r.status_code in (200, 201):
            logging.info(f"Scheduled: {data['title']} → {result.get('link')}")
            return jsonify({
                "success": True,
                "post_id": result["id"],
                "post_url": result.get("link"),
                "edit_url": f"{WP_SITE}/wp-admin/post.php?post={result['id']}&action=edit",
                "scheduled_for": result.get("date")
            })
        else:
            logging.error(f"WP error {r.status_code}: {result}")
            return jsonify({
                "success": False,
                "error": result.get("message", "Unknown WP error"),
                "code": r.status_code
            }), 400

    except Exception as e:
        logging.exception("schedule_post error")
        return jsonify({"success": False, "error": str(e)}), 500


@app.route("/scheduled-posts", methods=["GET"])
def scheduled_posts():
    """List all scheduled posts on WordPress."""
    try:
        r = requests.get(
            f"{WP_SITE}/wp-json/wp/v2/posts",
            headers=wp_headers(),
            params={"status": "future", "per_page": 20},
            timeout=15
        )
        posts = r.json()
        return jsonify([{
            "id": p["id"],
            "title": p["title"]["rendered"],
            "scheduled_for": p["date"],
            "edit_url": f"{WP_SITE}/wp-admin/post.php?post={p['id']}&action=edit"
        } for p in posts])
    except Exception as e:
        return jsonify({"error": str(e)}), 500


if __name__ == "__main__":
    print(f"\n{'='*50}")
    print(f"  WordPress Blog Scheduler API")
    print(f"  Site : {WP_SITE}")
    print(f"  Port : {API_PORT}")
    print(f"  Docs : http://localhost:{API_PORT}/health")
    print(f"{'='*50}\n")
    app.run(host="0.0.0.0", port=API_PORT, debug=False)
```

After generating the script:
1. Save it to the user's desktop as `wp_scheduler.py` using Claude Cowork file tools
2. Show instructions:

```
✅ wp_scheduler.py saved to your Desktop

Next steps:
1. Open your terminal
2. Run the setup command below (Claude Cowork will do this automatically)
```

---

## STAGE 2 — Install Dependencies & Start API (Claude Cowork)

Claude Cowork runs these terminal commands automatically:

```bash
# Step 1 — Install dependencies
pip install flask requests

# Step 2 — Start the API in background
cd ~/Desktop && python wp_scheduler.py &

# Wait 2 seconds for startup
sleep 2

# Step 3 — Health check
curl -s http://localhost:{API_PORT}/health
```

Parse the health check response:
- `"status": "ok"` → show success message with connected WordPress username
- Any error → show the specific error with fix instructions (see Error Handling)
- If curl not found → try `python -c "import urllib.request; print(urllib.request.urlopen('http://localhost:{API_PORT}/health').read())"`

Show result as a styled widget:

```
✅ Setup Complete!
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Connected to: {WP_SITE}
Logged in as: {WP_USERNAME}
API running at: localhost:{API_PORT}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Ready to generate and schedule your blog posts!
```

Only proceed to Stage 3 after a successful health check.

---

## STAGE 3 — Keyword & Content Form

Render a 3-step widget:

### Step 1 — Your Website
- Website niche *(required — e.g. "travel agency in Northeast India")*
- Target audience *(required — e.g. "families planning holidays")*
- Target city / region *(optional)*
- Content language *(English / Hindi / Bengali / Assamese / Other)*

### Step 2 — Keywords
- Your keywords *(textarea — up to 10, one per line)*
- Claude suggests additional keywords? *(Yes / No)*
- Keyword difficulty: Easy (long-tail) / Medium / Mix
- Content tone: Informational / Commercial / Local / Mix

### Step 3 — Schedule Settings
- Start publishing from *(date — defaults to tomorrow)*
- Posts per week *(1 / 2 / 3 / 5 / 7)*
- Publish days *(multi-select: Mon Tue Wed Thu Fri Sat Sun)*
- Default category *(e.g. "Travel Tips" — created in WP if missing)*

---

## STAGE 4 — Keyword Research

After form submission, Claude performs:

1. `web_search` each user keyword for ranking difficulty signals
2. `web_search` top ranking posts in their niche + city
3. `web_search` trending blog topics for their category this year
4. If user said Yes → generate 5–7 additional long-tail keyword suggestions

Assign each keyword:
- Search intent (informational / commercial / local)
- Difficulty (Easy / Medium / Hard)
- Recommended format (how-to / listicle / guide / comparison / local)

---

## STAGE 5 — 7 Blog Idea Cards Widget

Render a widget with 7 selectable blog idea cards:

Each card shows:
- Checkbox (top right)
- Scheduled day + date (e.g. "Day 1 — Mon, 2 Jun 2026 at 9:00 AM")
- SEO Title (60 chars, keyword-rich)
- Focus keyword · Search intent · Difficulty · Format
- Meta description preview (155 chars)

Card click → toggles `.sel` class, tracks in JS array
Counter: "X of 7 selected"
Buttons: Select All · Deselect All · Regenerate Ideas · Write & Schedule Selected ↗

Title rules:
- Contains focus keyword naturally
- Specific and benefit-driven
- Mix: "X Best...", "How to...", "Complete Guide...", "Top X..."

Dates: spread across chosen publish days starting from start date, at PUBLISH_TIME

---

## STAGE 6 — Write & Schedule Posts (Claude Cowork)

For each selected blog Claude:

### 6a — Write the Full Post (1500 words)

Structure:
```
SEO Title:        [60 chars — focus keyword included]
Slug:             [lowercase-hyphens-only]
Meta Description: [155 chars — keyword + CTA]
Focus Keyword:    [exact keyword]
Category:         [from settings]
Tags:             [5–8 relevant tags]
Scheduled:        [ISO 8601 with TZ offset — e.g. 2026-06-02T09:00:00+05:30]

━━━ CONTENT (HTML) ━━━

<h1>[Title]</h1>

<p>[Introduction — 150 words. Hook + problem + preview. Keyword in first 100 words.]</p>

<h2>[Section 1]</h2>
<p>[250 words of detailed, helpful content with LSI keywords]</p>

<h2>[Section 2]</h2>
<p>[250 words]</p>

<h2>[Section 3]</h2>
<p>[250 words]</p>

<h2>[Section 4]</h2>
<p>[250 words]</p>

<h2>Frequently Asked Questions</h2>
<h3>[Question 1]</h3>
<p>[Answer — 2–3 sentences]</p>
<h3>[Question 2]</h3>
<p>[Answer]</p>
<h3>[Question 3]</h3>
<p>[Answer]</p>

<h2>Conclusion</h2>
<p>[150 words — summarise, reinforce keyword, clear CTA to contact the business]</p>
```

SEO rules:
- Keyword in: title, first paragraph, ≥2 H2s, meta, conclusion
- Keyword density: 1–2% (never stuffed)
- 3–5 LSI keywords throughout
- Mix short and long sentences, paragraphs max 4 lines
- Use `<ul>/<ol>` lists where helpful
- Local context woven in naturally

### 6b — POST to Local API via Claude Cowork

Claude Cowork runs this curl for each post:

```bash
curl -s -X POST http://localhost:{API_PORT}/schedule-post \
  -H "Content-Type: application/json" \
  -d '{
    "title": "SEO TITLE HERE",
    "content": "FULL HTML CONTENT HERE",
    "slug": "url-slug-here",
    "scheduled_date": "2026-06-02T09:00:00",
    "seo_title": "SEO TITLE HERE",
    "meta_description": "Meta description here",
    "focus_keyword": "focus keyword here",
    "category": "Category Name",
    "tags": ["tag1", "tag2", "tag3", "tag4", "tag5"]
  }'
```

Parse response:
- `"success": true` → note post_id, post_url, edit_url
- `"success": false` → log error, continue with next post, retry at end

Process one post at a time. Show live progress:
```
Writing post 1 of 4: "Best Travel Agency in Guwahati"... ✅
Writing post 2 of 4: "How to Plan a Meghalaya Trip"... ✅
Writing post 3 of 4: "Top 10 Places in Northeast India"... ⏳
```

---

## STAGE 7 — Results Dashboard Widget

After all posts are processed, render a results widget:

```
┌──────────────────────────────────────────────┐
│  ✅ Blog Schedule Complete!                  │
├──────────────────────────────────────────────┤
│  Mon 2 Jun   9:00 AM  ✅ Best Travel Agenc.. │
│  Wed 4 Jun   9:00 AM  ✅ How to Plan a Meg.. │
│  Fri 6 Jun   9:00 AM  ✅ Top 10 Places in.. │
│  Mon 9 Jun   9:00 AM  ⚠️ Failed — retry    │
├──────────────────────────────────────────────┤
│  3 of 4 posts scheduled successfully        │
│                                              │
│  [Open WordPress Dashboard]                 │
│  [Retry Failed Posts]                       │
│  [Schedule Next Week]                       │
└──────────────────────────────────────────────┘
```

- Post titles are clickable → open edit_url in browser
- "Open WordPress Dashboard" → `{WP_SITE}/wp-admin/edit.php?post_status=future&post_type=post`
- "Retry Failed" → re-runs Stage 6 only for failed posts
- "Schedule Next Week" → restarts from Stage 5 with next 7 dates

---

## SEO Quality Checklist (check before each POST)

- [ ] Keyword in title (first 60 chars)
- [ ] Keyword in first 100 words
- [ ] Keyword in meta description
- [ ] Keyword in at least 2 H2s
- [ ] Meta description 120–155 chars
- [ ] Title 50–60 chars
- [ ] Word count 1400–1600
- [ ] FAQ section with 3+ questions
- [ ] Conclusion with clear CTA
- [ ] Slug is lowercase-hyphens-no-special-chars
- [ ] No keyword stuffing (density < 2.5%)
- [ ] All HTML tags properly closed

Fix any failures before sending to the API.

---

## Error Handling

### API Not Running
```
❌ Could not reach the local API at localhost:{API_PORT}
The wp_scheduler.py script may not be running.

Claude Cowork will restart it now...
```
→ Claude Cowork runs: `cd ~/Desktop && python wp_scheduler.py &` then retries health check.

### WordPress Auth Errors (from API response)
- 401 → "Wrong application password. Regenerate in WP Admin → Users → Profile → Application Passwords"
- 403 → "Permission denied. Make sure you're using an Administrator account"
- 404 → "REST API not found. Go to WP Admin → Settings → Permalinks → Save Changes"

### pip/Python Not Found
```bash
# Try python3 first
python3 -m pip install flask requests
python3 ~/Desktop/wp_scheduler.py &
```

### Port Already in Use
```bash
# Kill existing process on port
lsof -ti:{API_PORT} | xargs kill -9 2>/dev/null
sleep 1
python wp_scheduler.py &
```

### Script Not on Desktop
Claude Cowork searches common locations:
```bash
find ~ -name "wp_scheduler.py" 2>/dev/null | head -5
```

---

## Formatting Rules

- All prices: **Rs. 5,000** — never ₹
- ISO dates always include TZ offset: `2026-06-02T09:00:00+05:30`
- Slugs: `lowercase-hyphens-only` — strip punctuation, accents, spaces
- HTML content: no `<html>/<head>/<body>` tags — post body only
- Always use CSS variables — never hardcode colors
- Footer: `Want to customise this skill? Visit <a href="https://growth.webotapp.com">growth.webotapp.com</a>`
