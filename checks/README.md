# checks/

The checker every page build has to pass before I review it.

```
python3 checks/verify_page.py . --content content.md
```

It never edits the page. It runs six checks and writes a receipt to `checks/receipts/` (not committed):

1. **Facts:** every number, date and "X of Y" on the page must appear in `content.md`. An invented or rounded number is a FAIL.
2. **Links:** every link resolves. The email link uses `mailto:`, and outside links use `rel="noopener"`.
3. **HTML basics:** doctype, `lang`, title, viewport, exactly one `h1`, alt text, no scripts loaded from other sites.
4. **Render:** headless Chrome screenshots at 390 and 1280px; no sideways scroll at 320 or 390px.
5. **Contrast:** the colors Chrome actually computed, 4.5:1 target (WARN only).
6. **Size:** the page weight.

Exit codes: `0` = PASS or WARN only, `1` = something FAILed, `2` = couldn't start (for example, no page yet).
It needs Python 3 (standard library only). Check 4 needs Google Chrome at the macOS default path.

## History
- `history/2026-10-24-1259-before-any-code.txt`: its first run, a minute after `content.md` was committed and before any page code existed. It failed: no page found.
- `history/2026-10-24-1349-checker-sha256.txt`: its fingerprint, taken before the first reviewed build. The file here still matches it, so no agent has edited the checker.

`facts.json` holds the rules that aren't in `content.md`: the exact spelling of my name, and claims the page must never make.

Built with Claude in a separate session (Oct 22), before Fredrin was installed. Workers run it; they never edit it.
