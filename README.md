# Eat & Drink Nawabshah: Restaurants and Cafes Web GIS

PostgreSQL/PostGIS -> Flask API (GeoJSON) -> Leaflet map.

```
nawabshah-webgis/
  database/01_schema.sql        PostGIS extension, places + study_area tables, GiST index
  database/02_sample_data.sql   24 sample points (placeholders, see note below)
  backend/app.py                Flask API + serves the website
  backend/import_osm.py         optional: load real data from OpenStreetMap
  frontend/                     index.html, style.css, app.js (Leaflet)
```

## Run it

The app is configured for the pgAdmin server at `localhost:2003`, database `webgis`,
user `postgres`. If that database already contains the `places` and `study_area` tables, keep
them as-is. To create the tables in an empty database, run these from the project root:

```bash
psql -h localhost -p 2003 -U postgres -d webgis -f database/01_schema.sql
psql -h localhost -p 2003 -U postgres -d webgis -f database/02_sample_data.sql
```

**Warning:** `01_schema.sql` drops and recreates `places` and `study_area`. Back up your data
and only run it if you intend to replace those tables.

Install the Python dependencies and start Flask from the project root:

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r backend\requirements.txt
.\backend\run.ps1
```

The launcher prompts for the PostgreSQL password without putting it in the command line.
Keep Flask running in a terminal while Live Server is open; the frontend sends API requests to
port 5000. Open `http://127.0.0.1:5000` to use Flask to serve both the page and API.

## API
| Endpoint | Returns |
|---|---|
| `GET /api/places` | all points as GeoJSON |
| `GET /api/places?category=cafe` | filter: `cafe` or `restaurant` |
| `GET /api/places?lat=26.248&lon=68.410&radius=1500` | points within 1500 m (PostGIS `ST_DWithin`) |
| `GET /api/study-area` | study-area polygon |
| `GET /api/stats` | counts per category |

## Important: replace the sample data
The 24 rows in `02_sample_data.sql` are placeholders so the app runs. Only three names (Lakha Hotel,
Mirchi 360, Sukoon) come from public listings, and **every coordinate is approximate and unverified**.
Before submitting, collect real points: run `python import_osm.py` (real OSM data, cite
"OpenStreetMap contributors, ODbL"), and/or add points from your own GPS or Google Maps survey and
set `data_source` to `field survey`. Check each location on the basemap.
