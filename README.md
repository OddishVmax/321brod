# 321brod

This site now uses a Svelte app that reads a Google Sheets CSV with two columns:
- `config`
- `value`

The app loads values from the sheet and renders the page content dynamically.

## Local development

1. Install dependencies with `npm install`
2. Start the dev server with `npm run dev`
3. Build for GitHub Pages with `npm run build`

## CSV format

The sheet should contain rows like:

| config | value |
| --- | --- |
| eyebrow | Hjemmebakeri · Etterstadkroken |
| title_prefix | 3-2-1- |
| accent | Brød! |
| tagline | Nybakt hver morgen — levert til din dør |
