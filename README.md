# Lumi Reports

Static report archive, published with GitHub Pages.

## Layout

Reports are grouped by project, feature, and run date:

```text
projects/<project>/<feature>/<YYYY-MM-DD>-<run-name>/index.html
```

Keep each report's evidence and log files beside its `index.html`; use relative links so they work both locally and on Pages. Add a card for every new report to the root `index.html`.

The first report is under `projects/lumi_iot/notifications/2026-11-05-notification-additions-01/`.

## GitHub Pages

The `Deploy GitHub Pages` workflow publishes the root `index.html` and `projects/` tree whenever a commit reaches `main`.
