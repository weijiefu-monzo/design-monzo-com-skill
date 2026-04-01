---
name: design-monzo-com
description: Assemble Monzo.com pages in Figma by inserting page templates and editing content blocks. Use when building or editing product pages for monzo.com.
user-invocable: true
argument-hint: "[description of what to design]"
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
2. Identify which blocks and templates are needed and extract their component keys.
3. Only use blocks that have a component key.
4. Do **not** use `search_design_system` to find components — Notion databases are the source of truth.

## Step 3: Design in Figma

### 3.1 Inserting templates and content blocks

**Always start with a page template.** Insert a page template instance first, then edit, remove, or rearrange content blocks within its Content Blocks slot.

**Standard pattern for inserting a template:**
```javascript
const page = await figma.getNodeByIdAsync('PAGE_NODE_ID');
await figma.setCurrentPageAsync(page);
const component = await figma.importComponentByKeyAsync('COMPONENT_KEY');
const instance = component.createInstance();
instance.x = 0;
instance.y = 0;
```

**Font loading caveat:** Monzo custom fonts are not available in the MCP Plugin API runtime. Use `component.createInstance()` which places directly on the page. Do not use `appendChild()` on nodes containing text — it triggers font loading and will fail.

**Adding content blocks into the Content Blocks slot:**
```javascript
const slot = findByName(templateInstance, 'Content Blocks', 0);
const component = await figma.importComponentByKeyAsync('COMPONENT_KEY');
const instance = component.createInstance();
slot.insertChild(INDEX, instance);
```

### 3.2 Reading instance properties

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

### 3.3 Setting instance properties

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

**To show/hide elements:** Set `visible = true/false` on nodes found via traversal.

### 3.4 Inspecting the Content Blocks slot

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

## Step 4: Screenshot and Validate

After every creation or modification:

1. Use `get_screenshot` to capture the result.
2. Check for correct alignment, spacing, consistent design tokens, fill vs hug, proper centering.
3. Fix issues and re-screenshot (max 3 iterations).
4. Confirm the final result with a screenshot.

## Step 5: CMS Handoff

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
