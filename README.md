# OpenLR Visualizer

Interactive web tool for decoding and visualizing [OpenLR](https://www.openlr-association.com/) encoded location references on a map.

**Live site:** [kuanbutts.com/openlr](https://kuanbutts.com/openlr)

## What it does

Paste a base64-encoded OpenLR string and the app will:

- Decode it into Location Reference Points (LRPs) with coordinates and bearings
- Render the path on a Mapbox GL map with a gradient from start to end
- Show bearing arrows at each LRP and positive/negative offset markers
- Optionally **map match** the OpenLR against the Mapbox road network (via the [Map Matching API](https://docs.mapbox.com/api/navigation/map-matching/) with TomTom spec/format)

You can also share visualizations by copying the URL, which encodes the OpenLR string as a query parameter.

## Local development

1. Copy `.env.example` to `.env` and add your [Mapbox access token](https://account.mapbox.com/)
2. Generate the config file:
   ```
   echo "var MAPBOX_ACCESS_TOKEN = '$(grep MAPBOX_ACCESS_TOKEN .env | cut -d= -f2)';" > config.js
   ```
3. Serve over HTTP and open in a browser:
   ```
   python3 -m http.server
   ```

## Deployment

Pushes to `main` trigger a GitHub Actions workflow that injects the Mapbox token from the `MB_TOKEN` repo secret and deploys to the `gh-pages` branch.
