# Best Practices for Monzo.com Page Design

Reference file for the `design-monzo-com` skill. Read this file during Step 3 (planning) to inform content block selection, ordering, and asset direction.

## Content Narrative

### Problem → Solution Arc

Every page should follow a clear persuasion structure:

1. **Open emotionally** — Lead with aspiration or a pain point, not a feature list. Examples: "From tax fear to all clear", "Make saving smooth sailing", "An award-winning credit card."
2. **Move to rational proof** — Feature highlights, comparisons, step-by-step flows that demonstrate how the product delivers on the emotional promise.
3. **Close with trust + conversion** — Social proof, FAQ for objection handling, then a clear conversion CTA.

No page should dump all features upfront. Sequence information so each section builds on the previous one.

### Block Ordering Patterns (from successful pages)

| Position | Purpose | Typical Blocks |
|---|---|---|
| 1 | Hero — emotional hook + core value prop (includes built-in trust cues: customer count, app ratings, Trustpilot, badges) | Product Hero |
| 2–4 | Feature showcase — visual storytelling | Cards Carousel, Feature Highlights, Feature Card Highlights |
| 4–6 | Rational proof — comparison, how-it-works | Feature or Product Comparison, Progressive Content |
| 6–8 | Trust reinforcement + objection handling | Media Text Block (awards), Customer Count & Trustpilot, FAQ, UI Spotlight |
| Last | Conversion CTA | Open Account, Current Account Switch Service |

**Note:** The Product Hero already includes trust cues (customer count, app store ratings, Trustpilot score, badges) built into its layout. The standalone Customer Count & Trustpilot block belongs in the trust reinforcement section (not immediately after the hero) to reinforce credibility later in the page when the user is closer to a decision.

### Pacing and Visual Rhythm

- **Alternate dense and simple blocks.** After a content-heavy section (comparison table, feature grid), insert a visually simpler block (media text, social proof strip, open account CTA) to prevent cognitive overload.
- **Alternate light and dark sections.** Dark Navy full-bleed sections (UI spotlight, media text blocks) should break up white background sections, creating a light → dark → light rhythm.
- **Layer trust, don't dump it.** Social proof should appear at multiple touchpoints throughout the page (hero trust cues, mid-page Trustpilot, award badges, customer count near bottom), not in a single block.

## Asset Usage

### Asset Variety in Rows

Never use the same asset type twice in a row. When a block contains multiple cards/items in a row, vary the asset types:

- **Good (3-up):** Dark Navy solid → Phone bezel with app UI → Lifestyle photography
- **Good (3-up):** Dark Navy with brand graphic → Lifestyle photography → Hot Coral solid
- **Good (4-up):** Hot Coral solid → Photography + UI overlay → Hot Coral solid → Lifestyle photography
- **Bad:** Photography → Photography → Photography (monotonous)
- **Bad:** Hot Coral → Hot Coral → Hot Coral (violates colour rules)

### Assets Should Reinforce the Narrative

Choose assets based on what story the block is telling, not just to fill space:

- **Lifestyle photography** — conveys warmth, everyday life, emotional connection. Use when the block is about the human benefit (e.g. "makes sense of money", "smooth sailing").
- **Phone bezel with app UI** — showcases specific functionality. Use when the block is about a concrete feature (e.g. "Manage tax" screen, ISA selection screen).
- **Solid brand colour backgrounds** (Hot Coral / Dark Navy) — visual breathing room, brand presence. Use to balance out photography-heavy rows.
- **Photography + card composite** — establishes the physical product. Best for heroes where you want both emotional warmth and product recognition.
- **Contrast pairs** — use side-by-side assets to tell a before/after story (e.g. dark stormy lockscreen vs clean Monzo UI for "tax fear → all clear").

### Colour Rules Recap

- **Personal Banking:** Hot Coral backgrounds
- **Business Banking:** Dark Navy + Hot Coral
- **In rows:** Never Hot Coral alone — always alternate (Hot Coral / Dark Navy / Hot Coral, or Hot Coral / Photography / Hot Coral)

## Conversion Strategy

- **Multiple CTAs at different commitment levels.** Offer a primary action ("Open a free Monzo account") and secondary actions ("Learn more") throughout the page. Don't be pushy, but provide exits at every section.
- **At least one primary CTA in every block.** Every content block that supports a CTA should have a primary button enabled, giving the user an opportunity to sign up at any point on the page. Don't hide CTAs to "clean up" the design — a visible primary action in each section is a conversion opportunity.
- **Place comparison tables before conversion CTAs.** Help users self-select and feel confident before the final conversion block.
- **Use CASS / switch service as a closer.** For current account pages, the switch service block near the bottom removes the final "but switching is hard" objection.
- **FAQ before or near the final CTA.** Address remaining doubts just before asking for commitment.
