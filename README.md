# BeTheme BeBuilder Skill Plugin

A complete agent skill plugin to guide AI assistants (such as Antigravity, Manus, Claude, and GPT models) in building, styling, and publishing custom layouts and HTML sections using BeTheme's BeBuilder in WordPress.

## Directory Structure

```
betheme-bebuilder-skill/
├── plugin.json                                 # Plugin metadata
├── README.md                                   # This documentation file
└── skills/
    └── betheme-bebuilder-builder/
        └── SKILL.md                            # Comprehensive layout guidelines & hacks
```

## Installation

### In Google Antigravity
To register this plugin locally, clone the repository or configure the local path in your Antigravity settings:

```bash
# Clone this repository into your local plugins directory
git clone https://github.com/judahmcniel-eng/betheme-bebuilder-skill.git ~/.gemini/config/plugins/betheme-bebuilder-skill
```

### In Claude Code (CLI)
For agents using Claude Code, place the skill instructions where your agent can reference them, or install them into the project workspace directory.

## Skill Content Summary

The `SKILL.md` inside this plugin covers:
1. **BeBuilder Hierarchy & Targeting**: Correct structuring using `Section > Wrap > Element` and how to apply custom CSS IDs and classes.
2. **WordPress Content Sanitization Bypasses**: Bypassing WordPress stripping of inline `<style>` and scripts using media query scoping, SVG source URLs, IIFE-scoped JavaScript execution checks, and Gutenberg HTML block wrappers.
3. **BeTheme CSS Hacks**: Constructing custom tables using pure CSS table layout (`display: table-cell`) to bypass aggressive theme defaults, vertical connection line alignment, and alignment rules.
4. **Publishing Best Practices**: Handling WordPress MCP connector tool chains (tags, categories, draft previews) safely.

For details, refer to the full skill file: [SKILL.md](skills/betheme-bebuilder-builder/SKILL.md).

## License
MIT License.
