---
name: design-monzo-com
description: Assemble Monzo.com pages in Figma by inserting page templates and editing content blocks. Use when building or editing product pages for monzo.com.
user-invocable: true
argument-hint: "[optional description or PRD]"
---

# Monzo.com Design Workflow

You are assembling Monzo.com pages in Figma by inserting page templates and editing content blocks. This skill is for **designing in Figma only** — never generate application code.

Never create custom components, variables, or design tokens — only use the existing library of page templates and content blocks.

## Step 1: Read the content blocks and page templates

Before making any changes, fetch the Notion docs that drive what you can insert in Figma.

1. **Content blocks catalogue** — Notion doc ID: `29169fe426d28067a1d3d7f51a12ba3f`
2. **Page templates** — Notion doc ID: `29269fe426d28041bb90cb09e3d22b7e`

Use `mcp__monzo__ReadNotionDoc` with these doc IDs. Do not open foundation docs (colours, typography, etc.) unless the user explicitly asks.

## Step 2: Look Up Component Keys from Notion

1. Find "Component Key" column in the content blocks and page templates Notion docs.
2. Build a reference list of all available content blocks and page templates with their names, descriptions, and component keys.
3. Only use blocks that have a component key. Prioritise blocks that are also available in Contentful (have a Contentful link) — these are production-ready. Blocks without component keys are not yet available; if one would be a good fit, mention it to the user as "coming soon" but do not plan around it.
4. Check the **"Use directly"** column. Blocks marked **Yes** must be inserted as-is — do **not** edit their properties (text, variants, assets, or visibility). They are pre-designed and ready to use without modification. Only edit blocks where "Use directly" is **No**.
5. Do **not** use `search_design_system` to find components — Notion databases are the source of truth.

Present the available content blocks and page templates to the user as a summary so they know what building blocks are available.

## Step 3: Plan the design with the user

Before touching Figma, align with the user on what to build. This step produces a page plan — an ordered list of content blocks with a content direction and asset direction for each. Keep this lightweight; the user can fine-tune after seeing the design in Figma.

**Best practices:** Read `best-practices.md` (in the same directory as this skill) for proven patterns on content narrative, asset usage, visual rhythm, and conversion strategy. Use these patterns to inform block selection, ordering, and asset direction during planning.

**Detailed block guidelines:** Each content block has a detailed design guidelines page in Notion. The "Page ID" column in the content blocks catalogue links to these pages. **Before proposing the page plan** (or when iterating on blocks with the user), read the detailed Notion page (`mcp__monzo__ReadNotionDoc` with the block's Page ID) for every content block you plan to use. When reading these pages, **focus only on the Overview, Layout and behaviour, and Examples sections** — skip the "How to add in Contentful" section entirely during planning (it is only needed at Step 6: CMS Handoff). This saves significant context. The key information to extract is:
- The block's layout structure (columns, grid vs carousel thresholds, stacking behaviour)
- Minimum/maximum item counts (e.g. min 3 cards, max 4 steps)
- Animation and interaction details (auto-transition, expand/collapse)
- Asset aspect ratios and media requirements (e.g. 1:1 square, all steps need media if any do)
- Available optional sections to enable/disable

Use this information to make better-informed suggestions about content direction, which optional sections to enable/disable, and how to configure each block.

**Asset planning:** For every content block that contains an image, also suggest an asset direction. Read `assets-reference.md` (in the same directory as this skill) for the photography component table and asset guidelines. Consider:
- **Asset type** — photography, app UI in phone bezel, simplified UI on contrast background, or a combination
- **Photography selection** — suggest a specific photography component from the reference table that fits the block's narrative. Use the alt text to judge relevance.
- **Background treatment** — Hot Coral, Dark Navy, or lifestyle photography (follow the colour rules: Hot Coral for Personal Banking, Dark Navy + Hot Coral for Business Banking; in rows, never Hot Coral alone — alternate with Dark Navy or photography)
- **Composition** — standalone photo, photo + card, photo + UI overlay, UI in bezel, or simplified UI element

### If the user provides a PRD or detailed brief:

1. Read and process the PRD.
2. Map the PRD's requirements to available content blocks from Step 2.
3. Propose an ordered page plan: which content blocks to use, in what order, with a brief content direction for each (e.g. "Hero — introduce the credit card, lead with no-annual-fee message"). For blocks with images, include an asset direction. Do NOT specify every field — just the overall direction and tone.
4. Present the plan to the user for review.

### If the user provides a brief description or no description:

1. Ask the user a maximum of **3 questions** to understand the page at a strategic level. Focus on the big picture — audience, journey, and narrative — not on individual block details like CTAs or legal copy (those come later when editing blocks). Good questions include:
   - **Who is this page for?** — e.g. existing Monzo customers being upsold, prospects comparing providers, people arriving from search
   - **Where does this page sit in the user journey?** — e.g. linked from the app, standalone product page for organic traffic, campaign landing page
   - **What's the narrative arc / key story?** — e.g. "simple insurance built into your bank", "premium perks that pay for themselves", a specific angle or differentiator to lead with
   Avoid tactical questions about specific CTAs, copy, legal text, or individual block content — those are decisions for when editing blocks, not when planning the page structure.
2. Based on the answers, suggest which content blocks to use and in what order, with a brief content direction and asset direction for each. Explain why each block fits.
3. The user may adjust — add, remove, reorder blocks, or refine direction. Keep this to **one or two rounds** max. Don't over-interview; the user can fine-tune after seeing the design.

### Output of this step:

A confirmed page plan. Include the component key for each block so that Step 4 can proceed without re-reading the Notion docs. For blocks with images, include the asset direction and photography component key.

```
Page template: [name] (key: ...)

1. [Content block name] (key: ...)
   Direction: ...
   Asset: [photography name] (key: ...) on [background] — [composition note]

2. [Content block name] (key: ...)
   Direction: ...

3. [Content block name] (key: ...)
   Direction: ...
   Asset: App UI in phone bezel on Dark Navy background
```

### How to proceed:

Ask the user which path they prefer:

- **Option A: Review the plan as text** — confirm the plan above, then assemble in Figma.
- **Option B: Assemble a rough draft in Figma first** — skip text approval, build directly from the plan, and let the user iterate visually.

Proceed to Step 4 based on the user's choice.

## Step 4: Design in Figma

### 4.1 Inserting templates and content blocks

**"Use directly" blocks:** Before editing any content block, check whether it is marked "Use directly: Yes" in the Notion catalogue (from Step 2). If yes, skip all property edits for that block — insert it and move on. Only edit blocks where "Use directly" is No.

**Always start with a page template.** Insert a page template instance first, then edit, remove, or rearrange content blocks within its Content Blocks slot.

**Standard pattern for inserting a template — always insert AND detach in the same step:**
```javascript
const page = await figma.getNodeByIdAsync('PAGE_NODE_ID');
await figma.setCurrentPageAsync(page);
const component = await figma.importComponentByKeyAsync('COMPONENT_KEY');
const instance = component.createInstance();
instance.x = 0;
instance.y = 0;
// MUST detach immediately — do not inspect or edit before detaching
const detached = instance.detachInstance();
return { id: detached.id, children: detached.children.map(c => ({ id: c.id, name: c.name, type: c.type })) };
```

**Font loading caveat:** Monzo custom fonts are not available in the MCP Plugin API runtime. Use `component.createInstance()` which places directly on the page. Do not use `appendChild()` on nodes containing text — it triggers font loading and will fail.

**Detaching page templates is mandatory, not optional.** Always detach immediately after inserting a page template (`instance.detachInstance()`). Without detaching, nested nodes have instance-relative IDs that cannot be addressed with `getNodeByIdAsync`, making all subsequent editing much harder. Do not attempt to inspect or edit the template before detaching. You can tell it's a page template because its component key comes from the **Page templates** Notion doc.

**Never detach content blocks.** Content blocks inserted into the Content Blocks slot must remain as instances — detaching them breaks the component link and design system updates. You can tell it's a content block because its component key comes from the **Content blocks** Notion doc.

**Adding content blocks into the Content Blocks slot:**
```javascript
const slot = findByName(templateInstance, 'Content Blocks', 0);
const component = await figma.importComponentByKeyAsync('COMPONENT_KEY');
const instance = component.createInstance();
slot.insertChild(INDEX, instance);
// MUST set after insertChild — content blocks need to fill the slot width
instance.layoutSizingHorizontal = 'FILL';
```

**All content blocks must fill horizontally.** After inserting a content block into the Content Blocks slot, always set `instance.layoutSizingHorizontal = 'FILL'` so it stretches to the full page width. This must be set **after** `insertChild`, not before.

### 4.2 Reading instance properties

When you need to inspect or edit an instance, follow this process:

**Step A: Parse the URL and locate the node.**
Extract `fileKey` and `nodeId` from the Figma URL. Use `use_figma` to call `figma.getNodeByIdAsync(nodeId)` and check:
- If it's a **FRAME** (detached parent) → traverse its children directly
- If it's an **INSTANCE** → this is the root, traverse from here to find nested instances by name
- If **null** → the node is nested inside an instance and not directly addressable; ask the user for the parent ID

Instance-relative IDs (`I...;...`) do NOT work with `getNodeByIdAsync`. Always traverse from a directly-addressable root node.

**Step B: Read current property values.**
Use `node.componentProperties` on the target instance. This returns the exact current values, types, and property keys (including the `#hash:suffix` keys needed for `setProperties`):
```javascript
const node = await figma.getNodeByIdAsync('NODE_ID')
return node.componentProperties
// Returns: { 'Title#6882:0': { type: 'TEXT', value: 'Built-in security' }, ... }
```

**Step C: Discover available variant options.**
From the instance, call `getMainComponentAsync()`, then access the parent component set's `componentPropertyDefinitions`:
```javascript
const mainComp = await instance.getMainComponentAsync()
const compSet = mainComp.parent
return compSet.componentPropertyDefinitions
// Returns: { 'Page': { type: 'VARIANT', defaultValue: 'Start', variantOptions: ['Start', 'Middle', 'End'] }, ... }
```

**For nested instances**, traverse from the root:
```javascript
function findInstance(node, name) {
  if (node.type === 'INSTANCE' && node.name === name) return node
  if (node.children) {
    for (const child of node.children) {
      const found = findInstance(child, name)
      if (found) return found
    }
  }
  return null
}
const target = findInstance(root, '[Web] Step Item')
```

**Fallback for large pages:** If `use_figma` silently drops return values (known issue on very large/complex pages), use the temp frame technique — write property keys into a temp frame's name, read via `get_metadata`, then clean up:
```javascript
const keys = Object.keys(target.componentProperties);
const details = keys.map(k => {
  const p = target.componentProperties[k];
  return k + ' [' + p.type + ']=' + String(p.value).substring(0, 40);
});
const frame = figma.createFrame();
frame.name = 'PROPS: ' + details.join(' || ');
frame.x = 2000; frame.y = 0; frame.resize(100, 100);
return { frameId: frame.id };
```
Then parse the `PROPS:` frame from `get_metadata` output. Always delete the temp frame afterwards.

### 4.3 Setting instance properties

Use `setProperties()` with the exact property keys from `componentProperties` and valid values from `componentPropertyDefinitions`:

```javascript
instance.setProperties({
  'Title#6882:0': 'New title',
  '- Description#6882:7': 'New description',
  'Breakpoint': 'Mobile',           // VARIANT
  'Description#7458:17': true,       // BOOLEAN
})
```

| Property type | Value format | Example |
|---|---|---|
| TEXT | String | `'Title#6882:0': 'Hello'` |
| VARIANT | String from `variantOptions` | `'Breakpoint': 'Mobile'` |
| BOOLEAN | `true` / `false` | `'Actions#8582:53': false` |
| INSTANCE_SWAP | Node ID string | `'- Icon#11338:7': '1234:5678'` |

**For deeply nested slot children:** Update ONE AT A TIME in separate `use_figma` calls. Batch updates on deeply nested instances often fail. Run separate scripts in parallel for speed.

**Editing nested button labels:** CTA buttons inside content blocks are `[Web] Button` instances with their own component properties. The button label is controlled by `Children#2069:0` (TEXT). To edit, traverse from the content block to find `[Web] Button` instances, then call `setProperties({ 'Children#2069:0': 'Get started' })` on each. Always set real button text — never leave generic defaults like "Primary button" or "Secondary button" visible.

**To show/hide elements:** Set `visible = true/false` on nodes found via traversal.

### 4.4 Inspecting the Content Blocks slot

Use `get_metadata` to see the slot structure. If the result is too large, parse with a Python script:
```python
python3 -c "
import json, re
with open('PERSISTED_OUTPUT_FILE_PATH') as f:
    data = json.load(f)
text = data[0]['text']
lines = text.split('\n')
in_slot = False
slot_indent = 0
children = []
for line in lines:
    if 'name=\"Content Blocks\"' in line:
        in_slot = True
        slot_indent = len(line) - len(line.lstrip())
        continue
    if in_slot:
        current_indent = len(line) - len(line.lstrip())
        if current_indent == slot_indent + 2 and ('<instance' in line or '<frame' in line or '<slot' in line):
            id_match = re.search(r'id=\"([^\"]+)\"', line)
            name_match = re.search(r'name=\"([^\"]+)\"', line)
            if id_match and name_match:
                children.append(f'{id_match.group(1)} | {name_match.group(1)}')
        if current_indent <= slot_indent and '</slot>' in line:
            break
for c in children:
    print(c)
"
```

Always re-inspect after modifying the slot to confirm changes.

## Step 4b: Editing Assets

When the user needs to place, swap, or edit image assets, read `assets-reference.md` (in the same directory as this skill) for component keys, alt text, and asset guidelines. Only read this file when asset editing is needed — not on every run.

**Every content block with an image has a `[Web] Media` instance containing a `Media` SLOT.** This is where assets go. Never hide or disable the image — always keep `Media` boolean properties enabled and insert the appropriate asset into the SLOT. Do not toggle `Media#...` to `false` to "hide" an image — this collapses the image area and breaks the layout.

There are three types of assets you can insert:

**1. Photography** — Import the photography component via `figma.importComponentByKeyAsync(KEY)`, create an instance, clear the slot's existing children, and `appendChild` the instance into the `Media` SLOT. The user only needs to handle non-photography layers (UI overlays, card composites) manually.

**2. Phone Bezel** — Import the Phone Bezel component (`da71ba...`), create an instance, insert into the `Media` SLOT the same way as photography. Set the background colour variant via `setProperties()` after insertion.

**3. Simplified UI on solid background** — Do NOT insert any asset into the slot. Set the fill on the `[Web] Media` instance to the appropriate brand colour (Hot Coral or Dark Navy). Leave the slot contents as-is — describe the intended UI composition to the user so they can create it manually.

**Fitting assets inside Media slots.** After inserting an asset into a `Media` SLOT, you **must** set **both** `layoutSizingHorizontal` and `layoutSizingVertical` to `'FILL'` so the asset fully fills the container in both directions:
```javascript
slot.appendChild(instance);
instance.layoutSizingHorizontal = 'FILL';
instance.layoutSizingVertical = 'FILL';
```

**Important:** Both sizing properties must be set **after** the asset is appended to the slot (`slot.appendChild(instance)`), not before — setting them before append will throw.

## Step 5: Verify Assets and Validate

### 5.1 Asset verification (mandatory)

Before taking a screenshot, verify that every content block with an image has an asset inserted. Run a verification script that traverses the page and checks every `[Web] Media` instance for whether its `Media` SLOT has children:

```javascript
// Find all [Web] Media instances and check their Media slots
function findAllImages(node, path, results, depth = 0) {
  if (!node || depth > 8) return;
  if (node.type === 'INSTANCE' && node.name === '[Web] Media') {
    const slot = node.children ? node.children.find(c => c.name === 'Media' && c.type === 'SLOT') : null;
    const hasAsset = slot && slot.children && slot.children.length > 0;
    results.push({
      path: path,
      nodeId: node.id,
      hasAsset: hasAsset,
      slotChildCount: slot ? slot.children.length : 0,
    });
    return; // Don't recurse deeper into [Web] Media
  }
  if ('children' in node && node.children) {
    for (const child of node.children) {
      findAllImages(child, path + ' > ' + child.name, results, depth + 1);
    }
  }
}
```

For every `[Web] Media` where `hasAsset` is `false` and the block's `Media` boolean property is enabled, insert the appropriate asset from the page plan. Do not skip media slots — empty media slots result in blank areas on the page.

**Common places where assets are missed:**
- Carousel card slides — each slide has its own `[Web] Media`
- Progressive content steps — each step may have a media area
- Feature card highlights — each card has an image
- Media text blocks — the main image area

### 5.2 Asset annotations (mandatory)

After verifying assets, annotate every visible `[Web] Media` instance with asset direction. This helps designers understand the rationale and what manual work remains.

**First, clear all existing annotations** on the page to avoid stale notes from previous iterations:
```javascript
function clearAnnotations(node, depth = 0) {
  if (!node || depth > 10) return;
  if ('annotations' in node && node.annotations && node.annotations.length > 0) {
    node.annotations = [];
  }
  if ('children' in node && node.children) {
    for (const child of node.children) clearAnnotations(child, depth + 1);
  }
}
clearAnnotations(pageRoot);
```

**Then, get or create the "Asset direction" annotation category:**
```javascript
let categories = await figma.annotations.getAnnotationCategoriesAsync();
let cat = categories.find(c => c.label === 'Asset direction');
if (!cat) {
  cat = await figma.annotations.addAnnotationCategoryAsync({ label: 'Asset direction', color: 'orange' });
}
```

**Then, annotate each `[Web] Media` instance** with:
- **Rationale** — why this asset type was chosen (e.g. "Lifestyle photography conveys warmth", "Phone bezel showcases specific app feature", "Hot Coral for brand breathing room")
- **TODO** — what still needs to be done (e.g. "Add simplified UI overlay showing Savings Pots", "Replace placeholder bezel with actual app screenshot", or "Asset is final — no action needed")

```javascript
imageNode.annotations = [{
  label: '**Rationale:** ...\n\n**TODO:** ...',
  categoryId: cat.id,
}];
```

### 5.3 Screenshot

After verifying all assets and adding annotations, take a single screenshot of the complete page to verify the result. Only take additional screenshots if debugging a specific visual issue.

Do **not** screenshot after every small property edit — this wastes time. Only screenshot at major milestones (e.g. full page assembled, or after fixing a visual bug).

## Step 6: CMS Handoff

Once the user is satisfied with the design:

1. Ask the user if they'd like instructions to recreate in Contentful.
2. If yes, for each content block:
   - Look up its Contentful link from the Notion database.
   - Surface prop definitions matching the current design.
   - Provide a clear summary of what to enter in Contentful.

## Key Principles

- **Only use existing components**: Never create custom components, variables, or design tokens.
- **Accessibility first**: Sufficient colour contrast, good alt text, semantic structure.
- **Consistency**: Follow the catalogues — don't invent patterns outside the library.

## User's request

$ARGUMENTS
