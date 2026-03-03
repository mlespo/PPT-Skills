---
name: template-pptx
description: "Create PowerPoint presentations by populating a user-provided .pptx template. Use this skill whenever the user uploads a .pptx template and asks to create a presentation from it, or when another skill references template-based PowerPoint creation. This skill enforces strict template fidelity — Claude only uses the layouts, text boxes, shapes, fonts, and colors already present in the template. Never invents new elements. Works as the execution layer for any content-specific deck skill (IC decks, board decks, LP updates, etc.)."
---

# Template PPTX Skill

## Core Principle

You are working with someone's branded template. Your job is to make a presentation that looks like their team built it — not like an AI filled in blanks. Use the template's elements creatively, but never add elements that aren't already there.

The only hard rule: **use what's in the template, nothing more.** Everything else is judgment.

## Prerequisites

The user must upload a `.pptx` template. If they haven't, ask for one.

---

## Workflow

### Step 1: Study the Template

Read the pptx SKILL.md and editing.md first, then:

```bash
python scripts/thumbnail.py template.pptx
python -m markitdown template.pptx
```

Don't just catalog the elements — understand the template's personality. What layouts exist? What's the visual rhythm? How does the template use space, color, and hierarchy? What formatting conventions does it follow (bold headers, bullet styles, sub-bullet usage)?

Match your content decisions to what the template is already doing. If it bolds headers, bold headers. If it doesn't, don't. If it uses sub-bullets sparingly, use them sparingly. Mirror the template's own style choices.

### Step 2: Gather Content

Get the content from the user or from a companion content skill. Organize it before touching the template.

### Step 3: Make Creative Decisions

This is where judgment matters most. Before you start editing XML, think hard about how to use every layout the template gives you.

**Vary slide layouts based on the content of the slide.** Templates exist because someone designed multiple layouts for variety. A deck that repeats the same layout for every content slide is a failure of creativity, not a safe choice. Study each slide in the template — even the complex ones with many grouped elements, tables, multi-column grids, or diagrams — and find a way to use them. The XML might be intimidating, but every text element in a complex layout is still just an `<a:t>` tag you can replace. Don't shy away from a layout because it has 30+ text elements; that just means it's a richer canvas.

**Repurpose layouts creatively.** A "funnel diagram" slide can become a "lifecycle" or "process" slide. A "pricing comparison" three-column layout can become "three species of pinniped" or "three market segments." A "talk track / objections" two-column layout can become "pros / cons" or "threats / conservation." Look at the structure and spatial arrangement, not the original business purpose. Ask: what content would thrive in this shape?

**Use title slides as section dividers.** If your deck has more than 4-5 content slides, duplicate the title slide and drop it between major sections with a new heading. This breaks up visual monotony and gives the audience a breath. It's one of the easiest ways to add rhythm to a deck.

**Adapt content to fit the layout, not the other way around.** If a template has a four-column layout but your content has three categories, either find a fourth category or use a different layout. If a two-section split slide gives you an upper and lower block, structure your content to take advantage of that contrast (e.g., "current state" vs. "future state," "problem" vs. "solution"). Let the template's structure inspire how you organize the information.

**Vary content density across slides.** Not every slide needs the same amount of text. A slide with one big statement and a supporting detail can be just as effective as a dense information slide. Mix lighter slides with heavier ones.

**Reshape elements when needed.** If a layout has 6 bullet slots but your content only needs 4, remove the extra paragraphs cleanly — don't leave empties. If a layout has 3 columns but you need the middle one to carry more weight, give it more bullets while keeping the outer columns lighter. The template gives you the bones; you decide the proportions.

**Remove elements you don't need.** If a grouped section within a slide doesn't fit the content, delete the entire group from the XML rather than leaving it with placeholder text or empty space. A slide with two clean sections is better than three sections where one is awkwardly half-empty. This applies to icons, images, shapes, and text boxes — if you can't fill it meaningfully, cut it.

**Images.** If the template has image placeholders, use them. Search for relevant images and insert them. This is the one area where you're adding something the template didn't already contain — but only into slots the template already created for images.

**What NOT to do.** Don't add text boxes, shapes, lines, colors, or fonts that aren't in the template. Don't change element positions. Don't invent layouts. But within these constraints, be bold — use every layout, attempt every complex slide, and find creative mappings between content and structure.

### Step 4: Build the Deck

Follow the pptx editing workflow:

1. **Unpack**: `python scripts/office/unpack.py template.pptx unpacked/`
2. **Structural changes first** (complete ALL before editing content):
   - Delete unwanted slides from `<p:sldIdLst>`
   - Duplicate slides with `add_slide.py`
   - Reorder in `<p:sldIdLst>`
3. **Edit content**: Replace placeholder text in each `slide{N}.xml`
   - Use the Edit tool (not sed or Python scripts)
   - Preserve all `<a:pPr>` formatting attributes
   - Create separate `<a:p>` elements for each bullet — never concatenate into one paragraph
   - Use XML entities for smart quotes (`&#x201C;`, `&#x201D;`)
   - Only use lvl1 and lvl2 bullets (no deeper nesting)
4. **Clean**: `python scripts/clean.py unpacked/`
5. **Pack**: `python scripts/office/pack.py unpacked/ output.pptx --original template.pptx`

### Step 5: QA

**Content QA:**
```bash
python -m markitdown output.pptx
python -m markitdown output.pptx | grep -iE "xxxx|lorem|ipsum|insert|placeholder|\[.*\]"
```

**Visual QA:**
```bash
python scripts/office/soffice.py --headless --convert-to pdf output.pptx
pdftoppm -jpeg -r 150 output.pdf slide
```

Inspect every slide. Check for overlapping elements, text overflow, leftover placeholders, and anything that looks off. Fix and re-verify until clean.
