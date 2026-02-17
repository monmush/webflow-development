# Webflow Development Skill for Claude Code

A [Claude Code](https://claude.com/claude-code) skill for building and styling websites in the Webflow Designer using the Webflow MCP Bridge and browser automation.

## What It Does

This skill teaches Claude how to work with Webflow's visual designer programmatically — building elements, applying styles, managing components, handling responsive breakpoints, and publishing sites. It covers three main workflows:

- **Local → Webflow Transfer** — Replicate a local web project (React, Next.js, HTML/CSS, Vue, etc.) in Webflow with pixel-perfect accuracy
- **Build from Scratch** — Create Webflow sites from Figma designs or from scratch with proper design token management
- **Fix & Iterate** — Debug styling issues, fix responsive layouts, update content, and manage components

## Key Knowledge Included

- Webflow MCP tool reference (`element_tool`, `style_tool`, `element_builder`, etc.)
- Desktop-first breakpoint system and mobile-first → desktop-first CSS mapping
- Code Embed patterns for SVGs, custom CSS, and complex layouts
- JSX SVG → HTML SVG attribute conversion
- CodeMirror content injection for Webflow's code editor
- 8+ documented gotchas with workarounds (e.g., `pseudo: "noPseudo"` requirement)
- Section container pattern for consistent responsive layouts
- Component creation and instance management

## Prerequisites

- [Webflow MCP Bridge](https://chromewebstore.google.com/detail/webflow-mcp-bridge) Chrome extension
- [Claude in Chrome](https://chromewebstore.google.com/detail/claude-in-chrome) extension (for browser automation)
- Webflow Designer open in Chrome

## Installation

### Claude Code (recommended)

```bash
# Install to user scope (available across all projects)
claude skill add --user monmush/webflow-development
```

### Manual Installation

```bash
# Clone and copy to user skills directory
git clone https://github.com/monmush/webflow-development.git
cp -r webflow-development ~/.claude/skills/webflow-development
```

## Skill Structure

```
webflow-development/
├── SKILL.md                                # Core workflow, tool reference, gotchas
└── references/
    ├── breakpoints-and-styles.md           # Breakpoint mapping, typography, style patterns
    ├── local-to-webflow.md                 # Step-by-step local project transfer guide
    ├── code-embeds-and-svgs.md             # SVG conversion, CodeMirror injection, custom CSS
    └── gotchas.md                          # Complete issue catalog with workarounds
```

## Example Usage

Once installed, Claude will automatically use this skill when you ask things like:

- *"Build the hero section in Webflow to match my local React component"*
- *"Fix the responsive layout on the about page in Webflow"*
- *"Create a reusable card component in Webflow"*
- *"Transfer my Next.js landing page to Webflow"*
- *"Add an SVG illustration to the capabilities section in Webflow"*

## Built From Real Experience

This skill was distilled from dozens of hours of real Webflow development sessions — transferring a full multi-page website from a local React + CSS Modules codebase to Webflow via MCP. Every gotcha, pattern, and workflow was discovered and validated through hands-on implementation.

## License

MIT
