# Resume Site — Claude Instructions

## Page structure

The resume is exactly two A4 pages (210mm × 297mm each), rendered as fixed-height `div.page` blocks with `overflow: hidden`. The page break is a hard HTML split — there is no automatic reflow. Entries on page 1 and page 2 are manually assigned to their respective `.tl-section` divs.

## Page break rule — most important constraint

**Never add, move, or reorder entries without verifying the page fill stays within bounds.**

Each page's `.main` content area is approximately **1050px tall** at 1x screen zoom (accounting for padding). An average single-bullet entry is ~90–110px; a two-bullet entry is ~130–150px.

- **Page 1 max:** content must not visually overflow the bottom of the first grey page. The last entry on page 1 must end cleanly before the page separator.
- **Page 2 max:** content must not overflow the bottom of the second page (gets clipped by `overflow: hidden` and won't appear in the PDF).

### How to check before committing

Open `index.html` in a browser at 100% zoom. Look at the grey bar between the two pages:
- If any entry is cut or bleeds across the bar → move the last entry on page 1 to the top of page 2.
- If either page has excessive blank space at the bottom → move the first entry on the next page up.

## Page 1 / Page 2 split

The split point is marked in the HTML as `</div><!-- /page 1 -->` followed by `<!-- PAGE 2 -->`. Entries above that comment are on page 1; entries below are on page 2. To move an entry, cut the entire `<div class="tl-entry">…</div>` block and paste it on the other side of the page boundary comment.

## Entry ordering

Entries are sorted by **end date, newest first** — not start date. An entry that started in 2022 but ended in 2025 ranks above one that started and ended in Summer 2023.

For entries with multiple date ranges (e.g. "2019–2020, 2021–2022"), use the most recent end date.
For ongoing entries ("Present"), treat end date as today.
For future entries (e.g. "Summer 2026"), treat end date as that future date.

When inserting or reordering, compare end dates across all entries on both pages.

## Accent color

The accent color is applied in **5 places**: CSS variables + 3 SVG polygon `fill` attributes. Always change all 5 at once:
```bash
sed -i '' 's/#OLD/#NEW/g' index.html
```
Current: `#3d3d3d` (charcoal). Original: `#2e3f52` (steel navy).

## PDF export

PDF is generated via html2canvas + jsPDF (NOT window.print). The capture logic in the JS is fragile — do not change:
- `.page` height/width or `overflow`
- `.sidebar` height (must stay `310mm` to avoid white gaps)
- The JS margin-zeroing, body-reset, and `requestAnimationFrame` delay before capture
- The `sx=0, sy=i*ph*sc` slice coordinates (do not use `getBoundingClientRect`)
