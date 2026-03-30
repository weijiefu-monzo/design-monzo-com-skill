---
name: design-monzo-com
description: Assemble Monzo.com pages in Figma by inserting page templates and editing content blocks. Use when building or editing product pages for monzo.com.
user-invocable: true
argument-hint: "[description of what to design]"
---

# Monzo.com Design Workflow

You are assembling Monzo.com pages in Figma by inserting page templates and editing content blocks. You should **never** create custom components, variables, or design tokens — only use the existing library of page templates and content blocks.

## Step 1: Content blocks, usage, keys, then Figma

**Start here** — do not open foundation or general guideline docs unless the user asks (see below).

1. **Fetch the content blocks catalogue first** and use it as the primary reference for what to build:
   - Notion doc ID: `29169fe426d28067a1d3d7f51a12ba3f` (Content blocks)
   - Read the list of blocks, **usage recommendations**, and any guidance on when and how to use each block.
   - Use the **Component Key** column for each block you will insert in Figma.

2. **Fetch page templates** when you need structure or a template component key:
   - Notion doc ID: `29269fe426d28041bb90cb09e3d22b7e` (Page templates)
   - Pick the right template (e.g. Product, Feature — always confirm against this doc for the latest list) and read its **Component Key**.

3. **Look up component keys** from those Notion tables only:
   - Identify which content blocks and templates the task needs; extract their component keys.
   - Only use blocks that have a component key — blocks without one are not yet available for use.
   - Do **not** use `mcp__figma__search_design_system` to find components — Notion is the source of truth for component keys.

4. **Proceed to insert and edit in Figma** (Step 2): instantiate the page template, then add and configure content blocks in the Content Blocks slot using the keys and usage guidance from this step.

**Optional documentation — only if the user asks** (or the task explicitly requires a specific foundation topic):

- Main design guidelines: `28569fe426d2805ba607ff1e5c22afb5` (Design Guidelines for Monzo.com)
- Foundation pages:
  - Colours: `28e69fe426d280bb8994f684f6bf2046`
  - Typography: `28e69fe426d280a19fd0cea4fd82aefd`
  - Dimensions: `28f69fe426d28022b982f91c35e749f6`
  - Elevation: `29169fe426d2807197a4ddb32b25f7e2`
  - Motion: `28f69fe426d28067a494d0645d5070a5`
  - Iconography: `28f69fe426d280aea5cfe509af0e78c2`
  - Components: `29169fe426d2802eb8d8d96b6266ff0f`

Use `mcp__monzo__ReadNotionDoc` with these doc IDs when fetching.

## Step 2: Design in Figma

1. **Always start with a page template.** Insert a page template instance first (using its component key from Step 1), then edit, remove, or rearrange the content blocks within its **Content Blocks slot**.
   - If you are unsure which page template to use, prompt the user with the available options to confirm which starter to begin with. Current templates include **"Product"** and **"Feature"** — more will be added over time, so always check the Notion page templates database for the latest list.

2. **Inserting component instances (CRITICAL — avoid font errors):**
   - Monzo custom fonts (e.g. "Monzo Sans Display", "Monzo Sans Text") are **not available** in the MCP Plugin API runtime. Calling `figma.loadFontAsync()` for these fonts will fail.
   - **`appendChild()` triggers font loading** when moving nodes that contain text. This means moving an instance into a Section or Frame via `appendChild()` will fail with font errors.
   - **Correct approach:** Use `component.createInstance()` which places the instance directly on the current page without triggering font loading. Do **not** attempt to wrap it in a Section or Frame via `appendChild()`.
   - **Setting the current page:** Use `await figma.setCurrentPageAsync(page)` — do **not** use `figma.currentPage = page` (not supported in this runtime).
   - **Standard pattern for inserting a template:**
     ```javascript
     const page = await figma.getNodeByIdAsync('PAGE_NODE_ID');
     await figma.setCurrentPageAsync(page);
     const component = await figma.importComponentByKeyAsync('COMPONENT_KEY');
     const instance = component.createInstance();
     instance.x = 0;
     instance.y = 0;
     figma.currentPage.selection = [instance];
     figma.viewport.scrollAndZoomIntoView([instance]);
     ```

3. **Working with content blocks inside the template:**
   - Add content blocks into the Content Blocks slot by instantiating them using their component keys from Step 1.
   - To read or modify content block properties, first try reading them from the page template instance. If properties cannot be read from the instance directly, **read the children of the Content Blocks slot** — this will give you the relevant content block instances and their properties.
   - Rearrange or remove content blocks within the slot as needed for the page.

4. **Working with Slot-type properties (CRITICAL):**
   - When a component property has `type: "SLOT"`, its children are **not** exposed as properties on the parent instance. You **must** read the Slot frame's children directly to discover and modify sub-instances.
   - Use `mcp__figma__use_figma` to traverse into the Slot's children: access the Slot frame via `instance.children`, then iterate over its children to find sub-instances and their `componentProperties`.
   - **`figma_set_instance_properties` will fail** on deeply nested slot children (Figma API returns "instance sublayer does not exist"). Instead, use `mcp__figma__use_figma` with `node.setProperties({...})` on nodes found via direct traversal.
   - If `setProperties()` fails at one level, **never detach the instance**. Instead, traverse deeper into the frame/node hierarchy to find the actual instance node at a lower level and set properties on that. Detaching breaks the component link and should always be avoided.
   - **Pattern for editing deeply nested slot children:**
     ```javascript
     // 1. Get the parent instance
     const instance = await figma.getNodeByIdAsync('nodeId');
     // 2. Traverse deeper to find the actual editable instances
     function findEditableInstances(node, targetName, depth = 0) {
       const results = [];
       if (depth > 10) return results;
       try {
         if (node.type === 'INSTANCE' && node.name === targetName) {
           results.push(node);
         }
         if ('children' in node) {
           for (const child of node.children) {
             try { results.push(...findEditableInstances(child, targetName, depth + 1)); } catch(e) {}
           }
         }
       } catch(e) {}
       return results;
     }
     // 3. Find and edit — if setProperties fails at this level, go one level deeper
     const items = findEditableInstances(instance, 'TargetChild');
     for (const item of items) {
       try {
         item.setProperties({ 'PropKey': 'value' });
       } catch(e) {
         // Traverse into this item's children to find the next editable level
         const deeper = findEditableInstances(item, 'DeeperChild');
         deeper.forEach(d => d.setProperties({ 'PropKey': 'value' }));
       }
     }
     ```

5. **Never create custom components, variables, or design tokens.** Only instantiate and edit existing page templates and content blocks from the library.

## Step 3: Screenshot and Validate

After every creation or modification:

1. Use `mcp__figma__get_screenshot` to capture the result.
2. Check for:
   - Correct alignment and spacing
   - Consistent use of design tokens (colours, typography, dimensions)
   - Elements using "fill container" not "hug contents"
   - Text/inputs filling available width
   - Proper centering within containers
3. Fix any issues found and re-screenshot (max 3 iterations).
4. Confirm the final result with a screenshot.

## Key Principles

- **Only use existing components**: Never create custom components, variables, or design tokens. Work exclusively with page templates and content blocks from the library.
- **Accessibility first**: Ensure sufficient colour contrast, write good alt text, use semantic structure.
- **Consistency**: Follow usage recommendations from the content blocks (and page templates) Notion docs; pull in the main design guidelines or foundation docs only when the user asks or the task clearly needs them — don't invent new patterns.
- **Trust and clarity**: Monzo's brand is built on transparency — designs should be clean, clear, and honest.

## Step 4: CMS Handoff

Once the user is satisfied with the design:

1. **Ask the user** if they'd like instructions to recreate the design in the CMS (Contentful).
2. If yes, for each content block used in the final design:
   - Look up its **Contentful link** from the content blocks Notion database (the "Contentful Link" column).
   - Surface the relevant **prop definitions** that match the current design — map the content block's configured properties (text, images, CTAs, variants, etc.) to the corresponding Contentful fields.
   - Provide a clear summary of what the user needs to enter in Contentful to reproduce the design, including field names and the values to use.

## User's request

$ARGUMENTS
