# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Static single-page web application that visualizes OpenLR (Open Location Reference) encoded geographic locations. Decodes binary OpenLR strings into coordinates and bearing angles, then renders them on an interactive Mapbox GL map with gradient coloring, bearing arrows, and offset indicators.

Deployed as a GitHub Pages site. Development happens on `main`; a GitHub Actions workflow generates `config.js` (with the Mapbox token from the `MB_TOKEN` repo secret) and pushes deploy artifacts to the `gh-pages` branch.

## Development

No build process, package manager, or test framework. The entire app is a single `index.html` with embedded JavaScript, styled by `arrowstyle.css`, using CDN-loaded dependencies.

### Local setup

1. Copy `.env.example` to `.env` and fill in your Mapbox access token
2. Generate `config.js` from your `.env`:
   ```
   echo "var MAPBOX_ACCESS_TOKEN = '$(grep MAPBOX_ACCESS_TOKEN .env | cut -d= -f2)';" > config.js
   ```
3. Serve `index.html` over HTTP (e.g., `python3 -m http.server`) and open in a browser

Both `.env` and `config.js` are gitignored. In CI, `config.js` is generated from the `MB_TOKEN` GitHub repo secret.

## Dependencies (all CDN-loaded)

- **Mapbox GL JS** (v1.8.x) — map rendering
- **OpenLR JS** (v3.0.6, locked) — OpenLR binary decoding
- **Turf.js** — geographic distance/interpolation calculations
- **D3.js** (v5) — color scale (`d3.interpolateCool`) for line gradient

## Architecture

All logic lives in `index.html`. Key functions:

- `process()` — main pipeline: decodes OpenLR base64 string → extracts LRP coordinates/bearings → subdivides line into 200 segments → builds GeoJSON with gradient colors → renders map layers with bearing arrow markers and offset labels
- `createSubdividedCoordsWithPreservedPoints()` — subdivides a LineString into evenly-spaced points while preserving original vertices
- `updateURL(olrString)` — syncs browser URL query param `?id=` for sharing

Input: OpenLR string via text field or `?id=<base64>` URL parameter.

## Assets

`arrows/` contains 360 PNG files (0.png–359.png), one per degree of bearing, used as Mapbox marker icons.
