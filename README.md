# PPT Template Skill

A Claude skill that creates PowerPoint presentations from your existing `.pptx` templates. Upload any branded deck and tell Claude what you want — it populates the template using only the layouts, fonts, colors, and elements already in your file.

## What It Does

- Takes your `.pptx` template and fills it with new content
- Preserves your brand: fonts, colors, logos, shapes, layouts — all untouched
- Creatively repurposes complex layouts (funnel diagrams become timelines, pricing tables become comparison charts, etc.)
- Varies slide layouts across the deck instead of repeating the same one
- Manages content density so nothing overflows or looks cramped
- Runs visual QA to catch issues before delivering

## How to Use

1. **Upload your template** — any `.pptx` file with the layouts you want
2. **Tell Claude what the presentation should cover** — a topic, an outline, or pair it with a content skill (like an IC deck spec)
3. **Claude builds the deck** — studies your template's layouts, maps content to the best-fitting slides, and populates everything

### Example Prompts

- "Create a presentation about walruses from this template"
- "Use this template to build an IC deck for the Acme Corp acquisition"
- "Make a quarterly board update using our branded deck"

## Installation

### Claude.ai (Personal)
Copy `pptx-template-skill.md` to `~/.claude/skills/` and toggle it on in **Settings → Capabilities → Skills**.

### Claude.ai (Projects)
Add `pptx-template-skill.md` to your Project's knowledge base alongside your `.pptx` template.

## Design Philosophy

The skill follows one hard rule: **use what's in the template, nothing more.** No invented text boxes, no new fonts, no added shapes. Everything else is creative judgment — which layouts to use, how to split content across slides, when to remove elements that don't fit.

Key principles:

- **Vary layouts based on content.** A funnel slide becomes a process flow. A pricing table becomes a feature comparison. The structure inspires the content, not the other way around.
- **Don't avoid complex slides.** A slide with 30+ text elements is a richer canvas, not a harder problem. Every `<a:t>` tag is just text to replace.
- **Reshape and remove.** If a layout has 6 slots but you only need 4, remove the extras cleanly. If a section doesn't fit, delete the whole group rather than leaving it empty.
- **Title slides as dividers.** For longer decks, duplicate the title slide between sections to create breathing room.

## Files

- `pptx-template-skill.md` — The full skill instructions that Claude reads at runtime

## Works With

This is a general-purpose execution skill. It handles the *how* of building from a template. Pair it with content skills that handle the *what* — any skill that specifies what goes on each slide.
