# Amazon: No AI

Hides Amazon's AI surfaces: Rufus (the chat button, search-bar prompts, and the
side panel) and the AI-generated review summary on product pages.

Amazon exposes no policy or account setting for this — unlike Chrome's
`AIModeSettings` — so it has to be hidden in the page.

## Install (Chrome)

1. `chrome://extensions` → enable **Developer mode**.
2. **Load unpacked** → select this folder.

No permissions are requested beyond running on `amazon.*` pages.

## How it works

A stylesheet, and nothing else. `block.css` is declared in the manifest, so
Chrome injects it before the page renders and it also covers anything Amazon
adds later — no content script runs, so nothing of ours touches Amazon's DOM
or script timing.

That constraint is deliberate. The first version of this removed matching
nodes from a `MutationObserver` at `document_start`; Amazon's page boots
through chained `P.when(...)` callbacks on inline `<script>` tags, and
interfering there blanks the whole site. Hiding with CSS cannot.

Selectors are anchored (`[id^="rufus"]`, `[id*="-rufus"]`) and scoped to
content elements. A bare `[id*="rufus"]` is what to avoid — it can match a
structural wrapper and take the page down with it. If a new Rufus surface
slips through, prefer adding another anchored line over widening one.

## Alternative: uBlock Origin

Same effect without an extra extension — paste into uBlock's
*Settings → My filters*:

```
amazon.*##div[id^="rufus" i]
amazon.*##div[id*="-rufus" i]
amazon.*##div[class^="rufus" i]
amazon.*##div[data-cel-widget^="rufus" i]
amazon.*##button[aria-label*="rufus" i]
amazon.*###product-summary
amazon.*###cr-product-insights-cards
amazon.*###cr-summarization-content
amazon.*##[data-hook="cr-insights-widget"]
amazon.*##[data-hook="cr-summarization-attributes-list"]
```
