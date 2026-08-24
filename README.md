<h1 align="center">Hi there, I'm Aayushi 👋</h1>

<h3 align="center">Building things for the web, one commit at a time 🚀</h3>

<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=22&pause=1000&color=2E9EF7&center=true&vCenter=true&width=440&lines=Frontend+%26+Web+Design;Learning+React+%2F+JavaScript;Always+leveling+up" alt="Typing SVG" />
</p>

---

### 🧑‍💻 About Me

- 🎨 I'm focused on **Frontend Development & Web Design** 
- 🌱 I'm currently learning **React & JavaScript frameworks**
- 👯 I'm looking to collaborate on **cool frontend/web projects**
- 💬 Ask me about **UI, web design, or getting started in frontend dev**
- ⚡ Fun fact: **I love journaling** 📝

---

### 🛠️ Tech Stack

<p align="left">
  <img src="https://skillicons.dev/icons?i=html,css,js,react,git,figma" />
</p>

---

### 📊 GitHub Stats

<p align="center">
  <img src="https://github-readme-stats.vercel.app/api?username=aayushiag27&show_icons=true&theme=radical" width="48%" />
  <img src="https://github-readme-streak-stats.herokuapp.com/?user=aayushiag27&theme=radical" width="48%" />
</p>

<p align="center">
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=aayushiag27&layout=compact&theme=radical" />
</p>

---

### 📌 Pinned Projects

> Once you build a few projects, pin your best ones from your GitHub profile settings — they'll show up as cards right below your profile.

---

### 🌐 Connect with Me

<p align="left">
  <a href="https://www.linkedin.com/in/aayushi-agarwal-183a36382/" target="_blank"><img src="https://img.shields.io/badge/LinkedIn-blue?style=for-the-badge&logo=linkedin&logoColor=white" /></a>
  <a href="mailto:aaayushi373@gmail.com"><img src="https://img.shields.io/badge/Email-D14836?style=for-the-badge&logo=gmail&logoColor=white" /></a>
</p>

---

<p align="center"><i>⭐️ From <a href="https://github.com/aayushiag27">aayushiag27</a></i></p>

<img src="pixelated-portrait.png" width="420" alt="portrait" />
#!/usr/bin/env python3
"""
generate_profile.py

Generates a premium, animated GitHub profile README along with three
self-contained SVG assets:

  - assets/github-contribution-animation.svg  (deterministic sample contribution grid)
  - assets/terminal-card.svg                  (ASCII-art avatar in a macOS-style terminal)
  - assets/info-card.svg                      (neofetch-style info panel)

Only edit the CONFIGURATION section below to update your profile.
Run with:  python3 generate_profile.py
"""

import os
import io
import sys
import random
import hashlib
import requests
from PIL import Image

# =============================================================================
# CONFIGURATION — edit this section only
# =============================================================================

GITHUB_USERNAME = "aayushiag27"
DISPLAY_NAME = "Aayushi Agarwal"
BIO = "BTech CSE student - Developer - Builder"
LOCATION = ""  # e.g. "India" — leave blank to omit
CURRENT_FOCUS = "Web development and programming fundamentals"

EMAIL = ""            # e.g. "you@example.com" — leave blank to omit
PORTFOLIO_URL = ""    # e.g. "https://yourportfolio.dev" — leave blank to omit
LINKEDIN_URL = ""     # e.g. "https://linkedin.com/in/yourname" — leave blank to omit
INSTAGRAM_URL = ""    # e.g. "https://instagram.com/yourhandle" — leave blank to omit

CURRENTLY_LEARNING = [
    "JavaScript",
    "Data Structures",
    "Backend Development",
    "Full-Stack Development",
]

# Tech already in use vs. tech being learned. Used for both info-card.svg and README badges.
STACK_CURRENT = ["HTML", "CSS", "Git", "GitHub", "Python"]
STACK_LEARNING = ["JavaScript", "Data Structures & Algorithms", "Backend Development"]

HIGHLIGHTS = [
    "Building aesthetic web projects",
    "Learning Git & GitHub",
    "Exploring real-world development",
    "Building consistently",
]

# Each project: name is required. repo_url is optional — if left empty,
# the project is rendered as plain text with no link (never fabricate a URL).
PROJECTS = [
    {
        "name": "Unfold",
        "description": "Aesthetic journaling web application",
        "note": "Built as a personal development project",
        "repo_url": "",  # fill in once the repository exists / is public
    },
    # Add future projects here, following the same structure, e.g.:
    # {
    #     "name": "Project Name",
    #     "description": "Short description",
    #     "note": "",
    #     "repo_url": "https://github.com/aayushiag27/project-repo",
    # },
]

SOCIAL_LINKS = {
    "email": EMAIL,
    "portfolio": PORTFOLIO_URL,
    "linkedin": LINKEDIN_URL,
    "instagram": INSTAGRAM_URL,
}

# Badge icon slugs (simple-icons via shields.io) for the tech stack section.
TECH_BADGE_SLUGS = {
    "HTML": ("HTML5", "html5", "E34F26"),
    "CSS": ("CSS3", "css3", "1572B6"),
    "JavaScript": ("JavaScript", "javascript", "F7DF1E"),
    "Git": ("Git", "git", "F05032"),
    "GitHub": ("GitHub", "github", "181717"),
    "Python": ("Python", "python", "3776AB"),
    "Data Structures & Algorithms": None,
    "Backend Development": None,
}

# Paths
ASSETS_DIR = "assets"
README_PATH = "README.md"
CONTRIBUTION_SVG = os.path.join(ASSETS_DIR, "github-contribution-animation.svg")
TERMINAL_SVG = os.path.join(ASSETS_DIR, "terminal-card.svg")
INFO_SVG = os.path.join(ASSETS_DIR, "info-card.svg")

GENERATED_START = "<!-- PROFILE_GENERATED_START -->"
GENERATED_END = "<!-- PROFILE_GENERATED_END -->"

# =============================================================================
# Palette (dark premium theme)
# =============================================================================

BG = "#0d1117"
BG_PANEL = "#111722"
BORDER = "#22283a"
CYAN = "#56e0ff"
BLUE = "#4d8dff"
PURPLE = "#a970ff"
GREEN = "#39ff9d"
ORANGE = "#ff9f4d"
WHITE = "#f2f5fa"
MUTED = "#7d8aa3"

MONOSPACE_STACK = (
    "'SF Mono','Cascadia Code','Fira Code',ui-monospace,"
    "'JetBrains Mono',Consolas,monospace"
)


# =============================================================================
# Helpers
# =============================================================================

def ensure_assets_dir():
    os.makedirs(ASSETS_DIR, exist_ok=True)


def fetch_github_avatar(username, size=200):
    """
    Fetch the GitHub avatar for the given username using GitHub's public
    avatar endpoint. Returns raw bytes, or None if the request fails.
    No authentication / tokens are used or required.
    """
    url = f"https://github.com/{username}.png?size={size}"
    try:
        resp = requests.get(url, timeout=10)
        resp.raise_for_status()
        return resp.content
    except requests.RequestException as exc:
        print(f"[warn] Could not fetch avatar for '{username}': {exc}", file=sys.stderr)
        return None


ASCII_CHARS = "@%#*+=-:. "  # dense -> sparse


def convert_image_to_ascii(image_bytes, cols=60, char_aspect=2.0):
    """
    Convert raw image bytes into a dense ASCII-art grid (list of strings)
    using Pillow. char_aspect compensates for terminal characters being
    taller than they are wide. Returns None if conversion fails.
    """
    if image_bytes is None:
        return None
    try:
        img = Image.open(io.BytesIO(image_bytes)).convert("L")
    except Exception as exc:
        print(f"[warn] Could not process avatar image: {exc}", file=sys.stderr)
        return None

    width, height = img.size
    aspect_ratio = height / width
    new_width = cols
    new_height = max(1, int(aspect_ratio * new_width / char_aspect))
    img = img.resize((new_width, new_height))

    pixels = list(img.getdata())
    rows = []
    for r in range(new_height):
        row_pixels = pixels[r * new_width:(r + 1) * new_width]
        row_chars = "".join(
            ASCII_CHARS[min(len(ASCII_CHARS) - 1, p * len(ASCII_CHARS) // 256)]
            for p in row_pixels
        )
        rows.append(row_chars)
    return rows


def fallback_ascii_art(cols=60, rows_count=22):
    """
    Deterministic fallback ASCII pattern used when the avatar cannot be
    downloaded/processed, so the script never crashes and always produces
    a usable terminal card.
    """
    seed_str = GITHUB_USERNAME or "developer"
    rng = random.Random(int(hashlib.sha256(seed_str.encode()).hexdigest(), 16))
    rows = []
    for r in range(rows_count):
        row = "".join(
            ASCII_CHARS[rng.randrange(0, len(ASCII_CHARS))]
            if rng.random() > 0.35 else " "
            for _ in range(cols)
        )
        rows.append(row)
    return rows


def esc(text):
    """Minimal XML escaping for text placed inside SVG <text> elements."""
    return (
        text.replace("&", "&amp;")
        .replace("<", "&lt;")
        .replace(">", "&gt;")
        .replace('"', "&quot;")
    )


# =============================================================================
# FILE 1 — Contribution animation SVG
# =============================================================================

def generate_contribution_svg(path=CONTRIBUTION_SVG, cols=53, rows=7):
    """
    Generates a deterministic, GitHub-style contribution calendar with a
    diagonal 'slant reveal' animation (bottom-left -> top-right), using
    pure SVG/SMIL. This is SAMPLE data, not real GitHub contribution data
    (GitHub's public REST API does not expose the contribution calendar).
    To wire in real data later, replace `generate_contribution_levels()`
    with a function that supplies real per-day levels in the same shape.
    """
    cell = 11
    gap = 3
    padding = 24
    label_height = 30

    width = padding * 2 + cols * (cell + gap) - gap
    height = padding * 2 + label_height + rows * (cell + gap) - gap

    level_colors = {
        0: "#161b22",
        1: "#0e4429",
        2: "#136e3a",
        3: "#26a648",
        4: "#39ff9d",
    }
    glow_color = "#7dffce"

    levels = generate_contribution_levels(cols, rows)

    # Diagonal delay: cells on the same (col+row) diagonal animate together,
    # sweeping from bottom-left toward top-right.
    max_diag = (cols - 1) + (rows - 1)
    step_delay = 0.014

    svg_parts = []
    svg_parts.append(
        f'<svg xmlns="http://www.w3.org/2000/svg" width="{width}" height="{height}" '
        f'viewBox="0 0 {width} {height}">'
    )
    svg_parts.append(f'''
  <defs>
    <filter id="softGlow" x="-50%" y="-50%" width="200%" height="200%">
      <feGaussianBlur stdDeviation="1.4" result="blur"/>
      <feMerge>
        <feMergeNode in="blur"/>
        <feMergeNode in="SourceGraphic"/>
      </feMerge>
    </filter>
    <linearGradient id="panelGrad" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0%" stop-color="{BG_PANEL}"/>
      <stop offset="100%" stop-color="{BG}"/>
    </linearGradient>
  </defs>

  <rect x="0" y="0" width="{width}" height="{height}" rx="14" fill="url(#panelGrad)" stroke="{BORDER}" stroke-width="1"/>
  <text x="{padding}" y="20" font-family="{MONOSPACE_STACK}" font-size="11" letter-spacing="2"
        fill="{MUTED}">CONTRIBUTION ACTIVITY</text>
''')

    for row in range(rows):
        for col in range(cols):
            level = levels[row][col]
            color = level_colors[level]
            x = padding + col * (cell + gap)
            y = padding + label_height + row * (cell + gap)
            diag = col + row
            delay = round(diag * step_delay, 3)
            use_glow = ' filter="url(#softGlow)"' if level >= 3 else ""

            svg_parts.append(
                f'  <g opacity="0" transform="translate({x + cell/2},{y + cell/2}) scale(0.3)">'
            )
            svg_parts.append(
                f'    <animateTransform attributeName="transform" type="scale" '
                f'additive="sum" from="0.3" to="1" begin="{delay}s" dur="0.28s" '
                f'fill="freeze" calcMode="spline" keySplines="0.2 0.7 0.3 1"/>'
            )
            svg_parts.append(
                f'    <animate attributeName="opacity" from="0" to="1" begin="{delay}s" '
                f'dur="0.2s" fill="freeze"/>'
            )
            svg_parts.append(
                f'    <rect x="{-cell/2}" y="{-cell/2}" width="{cell}" height="{cell}" '
                f'rx="3" fill="#ffffff"{use_glow}>'
            )
            svg_parts.append(
                f'      <animate attributeName="fill" values="#ffffff;{glow_color};{color}" '
                f'begin="{delay}s" dur="0.42s" fill="freeze"/>'
            )
            svg_parts.append('    </rect>')
            svg_parts.append('  </g>')

    svg_parts.append('</svg>')

    with open(path, "w", encoding="utf-8") as f:
        f.write("\n".join(svg_parts))
    print(f"[ok] wrote {path}")


def generate_contribution_levels(cols, rows):
    """
    Deterministic sample contribution levels (0-4), seeded from the
    configured GitHub username so the pattern is stable across runs but
    unique per user. NOT real GitHub contribution data.
    """
    seed_str = f"{GITHUB_USERNAME}-contrib"
    rng = random.Random(int(hashlib.sha256(seed_str.encode()).hexdigest(), 16))
    levels = []
    for r in range(rows):
        row_levels = []
        for c in range(cols):
            weight = rng.random()
            if weight < 0.35:
                lvl = 0
            elif weight < 0.60:
                lvl = 1
            elif weight < 0.80:
                lvl = 2
            elif weight < 0.93:
                lvl = 3
            else:
                lvl = 4
            row_levels.append(lvl)
        levels.append(row_levels)
    return levels


# =============================================================================
# FILE 2 — Terminal ASCII portrait SVG
# =============================================================================

def generate_terminal_svg(path=TERMINAL_SVG, ascii_rows=None):
    """
    Generates a premium macOS-style terminal card containing the ASCII-art
    avatar (or a deterministic fallback pattern if the avatar could not be
    fetched/processed), revealed row-by-row with a typewriter-style
    `$ whoami` line at the bottom.
    """
    if not ascii_rows:
        ascii_rows = fallback_ascii_art()

    char_w = 6.2
    line_h = 11
    font_size = 10

    padding_x = 18
    header_h = 34
    top_pad = 14
    bottom_pad = 46  # room for the whoami line

    max_len = max(len(r) for r in ascii_rows)
    content_w = max_len * char_w
    width = int(padding_x * 2 + content_w)
    width = max(width, 420)
    height = int(header_h + top_pad + len(ascii_rows) * line_h + bottom_pad)

    row_delay_step = 0.045
    ascii_start_delay = 0.3

    svg_parts = []
    svg_parts.append(
        f'<svg xmlns="http://www.w3.org/2000/svg" width="{width}" height="{height}" '
        f'viewBox="0 0 {width} {height}">'
    )
    svg_parts.append(f'''
  <defs>
    <linearGradient id="termGrad" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0%" stop-color="{BG_PANEL}"/>
      <stop offset="100%" stop-color="{BG}"/>
    </linearGradient>
    <filter id="textGlow" x="-50%" y="-50%" width="200%" height="200%">
      <feGaussianBlur stdDeviation="0.5" result="b"/>
      <feMerge>
        <feMergeNode in="b"/>
        <feMergeNode in="SourceGraphic"/>
      </feMerge>
    </filter>
  </defs>

  <rect x="0" y="0" width="{width}" height="{height}" rx="12" fill="url(#termGrad)" stroke="{BORDER}" stroke-width="1"/>

  <rect x="0" y="0" width="{width}" height="{header_h}" rx="12" fill="#151b26"/>
  <rect x="0" y="{header_h - 12}" width="{width}" height="12" fill="#151b26"/>
  <circle cx="20" cy="{header_h/2}" r="5.5" fill="#ff5f57"/>
  <circle cx="38" cy="{header_h/2}" r="5.5" fill="#febc2e"/>
  <circle cx="56" cy="{header_h/2}" r="5.5" fill="#28c840"/>
  <text x="{width/2}" y="{header_h/2 + 4}" text-anchor="middle" font-family="{MONOSPACE_STACK}"
        font-size="11" fill="{MUTED}">aayushi@github ~</text>
''')

    # ASCII rows, revealed top to bottom with slight stagger + moving cursor.
    for i, row in enumerate(ascii_rows):
        y = header_h + top_pad + i * line_h + font_size
        delay = round(ascii_start_delay + i * row_delay_step, 3)
        color = [CYAN, WHITE, PURPLE][i % 3]
        safe_row = esc(row)
        svg_parts.append(
            f'  <text x="{padding_x}" y="{y}" font-family="{MONOSPACE_STACK}" '
            f'font-size="{font_size}" fill="{color}" opacity="0" filter="url(#textGlow)" '
            f'xml:space="preserve">{safe_row}'
            f'<animate attributeName="opacity" from="0" to="1" begin="{delay}s" '
            f'dur="0.18s" fill="freeze"/></text>'
        )
        # moving cursor block for this row
        cursor_x = padding_x
        cursor_w = char_w
        svg_parts.append(
            f'  <rect x="{cursor_x}" y="{y - font_size + 1}" width="{cursor_w}" height="{font_size + 2}" '
            f'fill="{WHITE}" opacity="0">'
            f'<animate attributeName="opacity" values="0;0.85;0" begin="{delay}s" dur="0.18s" fill="freeze"/>'
            f'<animate attributeName="x" from="{padding_x}" to="{padding_x + max_len * char_w}" '
            f'begin="{delay}s" dur="0.16s" fill="freeze"/>'
            f'</rect>'
        )

    # whoami line, typewriter style, after ASCII finishes
    whoami_delay = round(ascii_start_delay + len(ascii_rows) * row_delay_step + 0.2, 3)
    prompt = "$ whoami"
    answer = DISPLAY_NAME
    prompt_y = height - bottom_pad + 18
    answer_y = height - bottom_pad + 34

    svg_parts.append(f'''
  <text x="{padding_x}" y="{prompt_y}" font-family="{MONOSPACE_STACK}" font-size="12"
        fill="{GREEN}" opacity="0">{esc(prompt)}
    <animate attributeName="opacity" from="0" to="1" begin="{whoami_delay}s" dur="0.15s" fill="freeze"/>
  </text>
  <text x="{padding_x}" y="{answer_y}" font-family="{MONOSPACE_STACK}" font-size="12"
        fill="{WHITE}" opacity="0">{esc(answer)}
    <animate attributeName="opacity" from="0" to="1" begin="{whoami_delay + 0.35}s" dur="0.15s" fill="freeze"/>
  </text>
''')

    svg_parts.append('</svg>')

    with open(path, "w", encoding="utf-8") as f:
        f.write("\n".join(svg_parts))
    print(f"[ok] wrote {path}")


# =============================================================================
# FILE 3 — Neofetch-style info card SVG
# =============================================================================

def generate_info_svg(path=INFO_SVG):
    """
    Generates a neofetch-style information panel with ABOUT / STACK /
    CURRENTLY LEARNING / HIGHLIGHTS sections, each line staggered in with
    a fade + slide-up animation (~0.06s between rows), pure SMIL.
    """
    width = 460
    padding_x = 22
    line_h = 17
    section_gap = 10
    header_h = 30
    row_delay_step = 0.06

    lines = []  # list of (text, style) where style in {"header","body"}

    lines.append(("ABOUT", "header"))
    lines.append((f"{DISPLAY_NAME}", "body"))
    for chunk in _wrap(BIO, 46):
        lines.append((chunk, "body"))
    lines.append(("", "spacer"))

    lines.append(("STACK", "header"))
    for tech in STACK_CURRENT:
        lines.append((f"  {tech}", "body"))
    lines.append(("", "spacer"))

    lines.append(("CURRENTLY LEARNING", "header"))
    for item in CURRENTLY_LEARNING:
        lines.append((f"  {item}", "body"))
    lines.append(("", "spacer"))

    lines.append(("HIGHLIGHTS", "header"))
    for item in HIGHLIGHTS:
        lines.append((f"  {item}", "body"))

    content_height = 0
    row_positions = []
    y_cursor = header_h + 22
    for text, style in lines:
        if style == "spacer":
            y_cursor += section_gap
            continue
        row_positions.append((text, style, y_cursor))
        y_cursor += line_h
    content_height = y_cursor + 18

    height = content_height

    svg_parts = []
    svg_parts.append(
        f'<svg xmlns="http://www.w3.org/2000/svg" width="{width}" height="{height}" '
        f'viewBox="0 0 {width} {height}">'
    )
    svg_parts.append(f'''
  <defs>
    <linearGradient id="infoGrad" x1="0" y1="0" x2="1" y2="1">
      <stop offset="0%" stop-color="{BG_PANEL}"/>
      <stop offset="100%" stop-color="{BG}"/>
    </linearGradient>
  </defs>
  <rect x="0" y="0" width="{width}" height="{height}" rx="12" fill="url(#infoGrad)" stroke="{BORDER}" stroke-width="1"/>

  <rect x="0" y="0" width="{width}" height="{header_h}" rx="12" fill="#151b26"/>
  <rect x="0" y="{header_h - 12}" width="{width}" height="12" fill="#151b26"/>
  <circle cx="20" cy="{header_h/2}" r="5.5" fill="#ff5f57"/>
  <circle cx="38" cy="{header_h/2}" r="5.5" fill="#febc2e"/>
  <circle cx="56" cy="{header_h/2}" r="5.5" fill="#28c840"/>
  <text x="{width/2}" y="{header_h/2 + 4}" text-anchor="middle" font-family="{MONOSPACE_STACK}"
        font-size="11" fill="{MUTED}">neofetch</text>
''')

    header_colors = {
        "ABOUT": CYAN,
        "STACK": BLUE,
        "CURRENTLY LEARNING": PURPLE,
        "HIGHLIGHTS": ORANGE,
    }

    for i, (text, style, y) in enumerate(row_positions):
        delay = round(i * row_delay_step, 3)
        if style == "header":
            color = header_colors.get(text, CYAN)
            font_size = 12
            weight = "font-weight=\"700\""
            letter_spacing = 'letter-spacing="1.5"'
        else:
            color = WHITE if not text.startswith("  ") else MUTED
            font_size = 11.5
            weight = ""
            letter_spacing = ""

        safe_text = esc(text)
        svg_parts.append(
            f'  <text x="{padding_x}" y="{y}" font-family="{MONOSPACE_STACK}" '
            f'font-size="{font_size}" {weight} {letter_spacing} fill="{color}" opacity="0" '
            f'xml:space="preserve">{safe_text}'
            f'<animate attributeName="opacity" from="0" to="1" begin="{delay}s" dur="0.22s" fill="freeze"/>'
            f'<animate attributeName="y" from="{y + 6}" to="{y}" begin="{delay}s" dur="0.22s" '
            f'fill="freeze" calcMode="spline" keySplines="0.2 0.7 0.3 1"/>'
            f'</text>'
        )

    svg_parts.append('</svg>')

    with open(path, "w", encoding="utf-8") as f:
        f.write("\n".join(svg_parts))
    print(f"[ok] wrote {path}")


def _wrap(text, width):
    """Simple word-wrap helper returning a list of lines <= width chars."""
    words = text.split()
    lines = []
    current = ""
    for word in words:
        candidate = (current + " " + word).strip()
        if len(candidate) > width and current:
            lines.append(current)
            current = word
        else:
            current = candidate
    if current:
        lines.append(current)
    return lines or [""]


# =============================================================================
# README generation
# =============================================================================

def _badge(label, slug, color):
    label_enc = label.replace(" ", "%20")
    return f"![{label}](https://img.shields.io/badge/{label_enc}-{color}?style=for-the-badge&logo={slug}&logoColor=white)"


def _tech_badges(tech_list):
    badges = []
    for tech in tech_list:
        entry = TECH_BADGE_SLUGS.get(tech)
        if entry:
            label, slug, color = entry
            badges.append(_badge(label, slug, color))
        else:
            badges.append(f"`{tech}`")
    return " ".join(badges)


def _social_links_markdown():
    parts = []
    if SOCIAL_LINKS.get("email"):
        parts.append(f"[Email](mailto:{SOCIAL_LINKS['email']})")
    if SOCIAL_LINKS.get("portfolio"):
        parts.append(f"[Portfolio]({SOCIAL_LINKS['portfolio']})")
    if SOCIAL_LINKS.get("linkedin"):
        parts.append(f"[LinkedIn]({SOCIAL_LINKS['linkedin']})")
    if SOCIAL_LINKS.get("instagram"):
        parts.append(f"[Instagram]({SOCIAL_LINKS['instagram']})")
    return parts


def _projects_markdown():
    if not PROJECTS:
        return "_Projects will be listed here as they're built._"

    rows = []
    for proj in PROJECTS:
        name = proj.get("name", "").strip()
        if not name:
            continue
        desc = proj.get("description", "").strip()
        note = proj.get("note", "").strip()
        repo_url = proj.get("repo_url", "").strip()

        if repo_url:
            title_line = f"### [{name}]({repo_url})"
        else:
            title_line = f"### {name}"

        block = [title_line]
        if desc:
            block.append(desc)
        if note:
            block.append(f"_{note}_")
        rows.append("\n".join(block))

    return "\n\n".join(rows)


def generate_readme(path=README_PATH):
    """
    Generates/updates README.md. Content between GENERATED_START and
    GENERATED_END markers is replaced on every run; anything outside those
    markers (if the file already exists) is preserved.
    """
    stack_badges = _tech_badges(STACK_CURRENT)
    learning_list = "\n".join(f"- {item}" for item in CURRENTLY_LEARNING)
    projects_md = _projects_markdown()
    social_links = _social_links_markdown()
    social_line = " &nbsp;|&nbsp; ".join(social_links) if social_links else ""

    location_line = f"\n📍 {LOCATION}" if LOCATION else ""

    generated_block = f'''{GENERATED_START}

<div align="center">

# Hi, I'm {DISPLAY_NAME} 👋

{BIO}{location_line}

</div>

---

## About Me

I'm a BTech Computer Science student focused on {CURRENT_FOCUS.lower()}. I like building
practical, aesthetically considered projects and documenting my progress as I go —
gradually moving from front-end fundamentals toward full-stack development.

## Tech Stack

{stack_badges}

## What I'm Learning

{learning_list}

## Featured Projects

{projects_md}

## GitHub Activity

<table>
<tr>
<td width="50%">

<img src="assets/terminal-card.svg" alt="Terminal card" width="100%" />

</td>
<td width="50%">

<img src="assets/info-card.svg" alt="Info card" width="100%" />

</td>
</tr>
</table>

<div align="center">
<img src="assets/github-contribution-animation.svg" alt="Contribution activity" width="100%" />
</div>

{"## Connect With Me" if social_line else ""}

{social_line}

{GENERATED_END}'''

    if os.path.exists(path):
        with open(path, "r", encoding="utf-8") as f:
            existing = f.read()
        if GENERATED_START in existing and GENERATED_END in existing:
            before = existing.split(GENERATED_START)[0]
            after = existing.split(GENERATED_END)[1]
            new_content = before + generated_block + after
        else:
            # No markers found yet — append the generated block, preserving
            # whatever was already in the file.
            new_content = existing.rstrip() + "\n\n" + generated_block + "\n"
    else:
        new_content = generated_block + "\n"

    with open(path, "w", encoding="utf-8") as f:
        f.write(new_content)
    print(f"[ok] wrote {path}")


# =============================================================================
# main
# =============================================================================

def main():
    if GITHUB_USERNAME in ("", "YOUR_USERNAME"):
        print(
            "[error] Please set GITHUB_USERNAME in the CONFIGURATION section "
            "of generate_profile.py before running.",
            file=sys.stderr,
        )
        sys.exit(1)

    ensure_assets_dir()

    print(f"[info] generating profile for '{GITHUB_USERNAME}'...")

    avatar_bytes = fetch_github_avatar(GITHUB_USERNAME)
    ascii_rows = convert_image_to_ascii(avatar_bytes) if avatar_bytes else None
    if not ascii_rows:
        print("[info] using fallback ASCII pattern (avatar unavailable or unprocessable).")
        ascii_rows = fallback_ascii_art()

    generate_contribution_svg()
    generate_terminal_svg(ascii_rows=ascii_rows)
    generate_info_svg()
    generate_readme()

    print("[done] Profile generated. Open the SVGs in a browser or preview README.md.")


if __name__ == "__main__":
    main()
