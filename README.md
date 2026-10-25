# about-me

Built with Fredrin for the Forward Deployed Engineer application, Oct 2026.

Live: https://javierxelias-svg.github.io/about-me/

## Preview locally

```
python3 -m http.server
```

Then open http://localhost:8000. Opening `index.html` directly drops the fonts.

## Run the checker

```
python3 checks/verify_page.py . --content content.md
```

Exit code 0 means PASS or WARN only, 1 means something failed. Receipts go to `checks/receipts/` (not committed).

## Files

- `index.html`, `style.css`: the page. HTML and CSS only, no JavaScript.
- `content.md`: every sentence, name, date and number on the page.
- `AGENTS.md`: the rules for any agent working in this repo.
- `DESIGN.md`: the look of the page.
- `design/`: the approved screenshots and the glass recipe.
- `checks/`: the checker every build has to pass.
- `fonts/`: Geist and Doto, self-hosted (SIL Open Font License).
