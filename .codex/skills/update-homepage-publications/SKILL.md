---
name: update-homepage-publications
description: Use when updating Tianfan Xue's homepage publication list, including accepted-paper venue labels, ordering, paper links, local final PDFs, teaser crops, hover-switch demo figures, resized JPEG thumbnails, and project-page discovery.
---

# Homepage Publication Updates

Use this skill when editing the homepage at `/Users/tianfan/Documents/GitHub/homepage` to add, reorder, or update research project/publication entries.

## Repository Conventions

- Main page: `index.html`.
- Publication thumbnails live in `image/`, usually named like `2026-short-project-name.jpg`.
- Public or final local PDFs live in `papers/`, usually named like `ECCV26_Title_With_Underscores.pdf`.
- Keep the existing HTML style for publication rows: `<tr id='..._tr'>`, `proj_thumb` images, `pub_title`, `pub_author`, a venue line, and bracketed links such as `[PDF]`, `[Website]`, `[Code]`, `[OpenReview]`.
- Newly accepted papers should be moved near the top of the relevant year section in the requested order.
- Preserve unrelated dirty worktree changes. Read the current diff before editing and do not revert files the user changed.

## Agent Workflow

1. Inspect the current homepage structure before editing.
   - Locate the relevant publication row by title or row id.
   - Check nearby entries to match citation format, author styling, venue wording, thumbnail dimensions, and link labels.

2. Verify source information.
   - Prefer final paper PDFs, OpenReview pages, arXiv pages, project pages, and the user's Google Scholar profile for titles/authors/venues.
   - If a URL points to a PDF, download it only when needed and verify it is a real PDF with `file` and `pdfinfo`.
   - If a download returns HTML, a browser-verification page, or an error page, do not save or link it as a local PDF.

3. Handle draft PDFs carefully.
   - Draft PDFs supplied by the user are reference-only unless the user explicitly asks to upload them.
   - It is okay to render draft PDFs to choose and crop a teaser image.
   - Do not copy draft PDFs into `papers/` and do not link them from the webpage.

4. Add final PDFs only when appropriate.
   - If the user supplies a final version, copy it into `papers/` with a clear venue/year/title filename.
   - Link the homepage entry to that local PDF.
   - If the paper is already public on arXiv or OpenReview, link the public PDF instead of duplicating it locally unless the user asks otherwise.

5. Create teaser thumbnails from papers.
   - Render candidate PDF pages at a useful resolution with Poppler tools such as `pdftoppm`.
   - Visually inspect rendered pages and choose a compact crop of the important teaser/overview figure.
   - Avoid crops with too much paper text, legends, margins, or unrelated panels.
   - Save the final web thumbnail as a JPEG under `image/` and verify it opens correctly.

6. Create hover-switch demo figures when the user supplies before/after or input/result images.
   - Follow existing interactive rows such as UltraFusion: wrap the thumbnail cell with `td class="proj_interactive"`, a `div class="one"`, a nested `div class="two" id='project_id_two'`, an `img` with `id='project_id_one'`, and `addImageSwitcher('project_id')`.
   - Treat `one` as the default/input image and `two` as the hover/result image unless the user says otherwise.
   - Resize large source images to the actual homepage viewing scale before saving. Use JPEG for photographic thumbnails to reduce page load time; 2x the rendered `proj_thumb` width is a good default for high-density screens.
   - Use stable names such as `image/YYYY-project-input.jpg` and `image/YYYY-project-result.jpg`; avoid committing the original oversized PNGs when resized JPEGs are sufficient.
   - Visually inspect both resized assets and verify the HTML references use existing files.

7. Search for project pages and code.
   - Search by exact paper title.
   - Add a project webpage link when a credible official project page exists.
   - Add arXiv links when available, and code links when the existing homepage convention for that project type includes them.

8. Patch `index.html` conservatively.
   - Update titles, authors, venue labels, oral/award notes, links, and image paths.
   - Move accepted papers to the requested order without reformatting the whole file.
   - Avoid broad line-ending or whitespace churn, especially in `index.html`.

9. Final verification.
   - Check every referenced local file exists.
   - Check for duplicate row ids.
   - Review `git diff --stat` and the relevant diff hunks.
   - If visual verification in the browser is available, reload the local page and inspect the updated publication block.

## Useful Commands

```bash
rg -n "Paper Title|row_id|publications_recent" index.html
file /path/to/downloaded.pdf
pdfinfo /path/to/downloaded.pdf
pdftoppm -jpeg -r 180 -f 1 -l 1 paper.pdf /tmp/rendered-page
sips --cropToHeightWidth HEIGHT WIDTH --cropOffset OFFSET_Y OFFSET_X input.jpg --out image/2026-project.jpg
sips -s format jpeg -s formatOptions 75 -Z 360 input.png --out image/2026-project-input.jpg
git diff --stat
git diff -- index.html
```
