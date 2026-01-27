# Cartography of Physics

An interactive map visualizing the birthplaces and final resting places of 164 physicists who have units, constants, laws, effects, or particles named after them.

![Map Preview](https://img.shields.io/badge/Physicists-164-d4a574?style=flat-square)
![Locations](https://img.shields.io/badge/Locations-271-81d4fa?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-a5d6a7?style=flat-square)

## Overview

From Newton's birthplace in rural Lincolnshire to Einstein's ashes scattered in the Delaware River, this project maps the geographic footprint of the scientists whose names became synonymous with the fundamental concepts of physics.

**Live Demo:** [View the Map](https://janalexanderzak.github.io/physicists-map)

## Features

### Interactive Map
- **Dark theme** cartography optimized for data visualization
- **Marker clustering** to handle overlapping locations
- **Smooth zoom** and pan with mouse or touch controls
- **Responsive design** works on desktop and mobile

### Search
- **Real-time search** as you type (minimum 2 characters)
- Search by **physicist name** (e.g., "Curie", "Tesla")
- Search by **eponymous terms** (e.g., "volt", "newton", "joule")
- **Keyboard navigation** — Arrow keys to navigate, Enter to select, Escape to close
- **Auto-zoom** to selected physicist's location

### Display Modes
- Toggle between **Birthplaces** and **Burial Sites**
- Automatic mode switching when searching for a physicist only present in one mode

### Timeline
Travel through 2,300 years of physics history:
- **Slider control** — Drag to any year from 287 BC to 2025
- **Play/Pause** — Animated playback through history
- **Variable speed** — Fast, Normal, or Slow playback
- **Smart stepping** — Faster jumps through sparse centuries, slower through the 1800s-1900s boom
- **Two filter modes:**
  - **Cumulative** — Shows all physicists born up to the selected year
  - **Single Year** — Shows only physicists alive during that specific year

### Category Filtering
Filter physicists by their contribution type:
- **SI Units** (19) — Ampère, Kelvin, Newton, Pascal, etc.
- **Non-SI Units** (27) — Gauss, Ångström, Curie, etc.
- **Physical Constants** (15) — Planck, Boltzmann, Avogadro, etc.
- **Laws of Physics** (45) — Kepler, Hooke, Faraday, etc.
- **Effects/Phenomena** (42) — Doppler, Zeeman, Hawking, etc.
- **Particles/Theory** (9) — Fermi, Bose, Higgs, etc.
- **Equations** (7) — Schrödinger, Navier-Stokes, Euler, etc.

### Rich Tooltips
Click any marker to see:
- Physicist name and lifespan
- Category badge (color-coded)
- Full location details
- Eponymous terms (what's named after them)
- Fields of physics

## Technical Details

### Libraries Used

| Library | Version | Purpose |
|---------|---------|---------|
| [Leaflet.js](https://leafletjs.com/) | 1.9.4 | Core mapping library |
| [Leaflet.markercluster](https://github.com/Leaflet/Leaflet.markercluster) | 1.4.1 | Marker clustering for overlapping points |
| [CartoDB Dark Matter](https://carto.com/basemaps/) | — | Dark-themed map tiles |
| [Google Fonts](https://fonts.google.com/) | — | Typography (Cormorant Garamond, JetBrains Mono) |

### Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     index.html                          │
├─────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────┐ │
│  │   Leaflet   │  │  Marker     │  │  Custom CSS     │ │
│  │   Map Core  │  │  Cluster    │  │  (Dark Theme)   │ │
│  └──────┬──────┘  └──────┬──────┘  └────────┬────────┘ │
│         │                │                   │          │
│         └────────────────┼───────────────────┘          │
│                          │                              │
│                  ┌───────▼───────┐                      │
│                  │  Application  │                      │
│                  │    State      │                      │
│                  └───────┬───────┘                      │
│                          │                              │
│         ┌────────────────┼────────────────┐             │
│         │                │                │             │
│  ┌──────▼──────┐  ┌──────▼──────┐  ┌─────▼─────┐       │
│  │   Search    │  │   Toggle    │  │  Category │       │
│  │   Module    │  │   (Mode)    │  │  Filters  │       │
│  └─────────────┘  └─────────────┘  └───────────┘       │
│                          │                              │
│                  ┌───────▼───────┐                      │
│                  │   Timeline    │                      │
│                  │   Controller  │                      │
│                  └───────────────┘                      │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                     data.json                           │
│  (External data file for easy updates)                  │
└─────────────────────────────────────────────────────────┘
```

### Data Loading Strategy

The application uses a **dual-source data strategy**:

1. **Embedded Data** — The physicist data is embedded directly in `index.html`, ensuring the map works when opened locally (via `file://` protocol) without a web server.

2. **External Data** — On deployment, the app first attempts to fetch `data.json`. If successful, it uses the external file, allowing data updates without modifying the HTML.

```javascript
async function loadData() {
    try {
        // Try external file first (for easy updates)
        const response = await fetch('data.json');
        if (response.ok) {
            PHYSICISTS_DATA = await response.json();
            return true;
        }
    } catch (e) {
        // Fallback to embedded data (works locally)
    }
    PHYSICISTS_DATA = EMBEDDED_DATA;
    return true;
}
```

### Data Structure

Each physicist entry in `data.json` contains:

```json
{
  "name": "Albert Einstein",
  "birth_year": 1879,
  "death_year": 1955,
  "still_alive": false,
  "category": "Non-SI Units",
  "birthplace": "Ulm, Germany",
  "burial": "Cremated; ashes scattered in Delaware River",
  "birth_coords": [48.4011, 9.9876],
  "burial_coords": [40.0583, -74.9058],
  "eponymous_terms": "Einstein (E); General relativity; Special relativity",
  "fields": "Gravitation; Quantum mechanics; Relativity",
  "notes": "Cremated same day he died..."
}
```

### Geocoding

Coordinates were determined through a combination of:
- Manual lookup of specific cemeteries and churches
- City-level coordinates for birthplaces
- Historical research for lost or destroyed graves

**Coverage:**
- 147 of 164 birthplaces geocoded (90%)
- 124 of 164 burial sites geocoded (76%)

Some locations remain unknown (e.g., Hubble's burial site was never disclosed) or were destroyed (e.g., Kepler's grave was destroyed in 1632).

## Installation & Deployment

### Local Development

Simply open `index.html` in a web browser. The embedded data ensures it works without a server.

```bash
# Clone the repository
git clone https://github.com/yourusername/cartography-of-physics.git

# Open in browser
open index.html
# or
xdg-open index.html  # Linux
start index.html     # Windows
```

### GitHub Pages Deployment

1. Push both files to your repository:
   ```
   index.html
   data.json
   ```

2. Go to **Settings** → **Pages**

3. Under "Source", select **main branch** and **/ (root)**

4. Your map will be live at:
   ```
   https://yourusername.github.io/repository-name/
   ```

### Updating Data

To add or modify physicist data:

1. Edit `data.json`
2. Ensure coordinates are in `[latitude, longitude]` format
3. Commit and push — changes appear immediately on GitHub Pages

No need to modify `index.html` for data updates.

## Data Sources

The physicist data was compiled from:
- Wikipedia biographical articles
- Find A Grave database
- Westminster Abbey records
- Père Lachaise Cemetery records
- Academic biographies and obituaries
- Historical society publications

## File Structure

```
cartography-of-physics/
├── index.html      # Main application (self-contained)
├── data.json       # Physicist data (for easy updates)
└── README.md       # This file
```

## Browser Support

| Browser | Supported |
|---------|-----------|
| Chrome 80+ | ✅ |
| Firefox 75+ | ✅ |
| Safari 13+ | ✅ |
| Edge 80+ | ✅ |
| Mobile Safari | ✅ |
| Chrome Mobile | ✅ |

## Contributing

Contributions are welcome! If you find:
- Incorrect coordinates
- Missing burial locations
- New physicists to add
- Bugs or UI improvements

Please open an issue or submit a pull request.

### Adding a New Physicist

Add an entry to `data.json`:

```json
{
  "name": "Physicist Name",
  "birth_year": 1900,
  "death_year": 2000,
  "still_alive": false,
  "category": "One of the 7 categories",
  "birthplace": "City, Country",
  "burial": "Cemetery Name, City, Country",
  "birth_coords": [latitude, longitude],
  "burial_coords": [latitude, longitude],
  "eponymous_terms": "What is named after them",
  "fields": "Their fields of physics",
  "notes": "Optional interesting facts"
}
```

## License

MIT License — see [LICENSE](LICENSE) for details.

## Acknowledgments

- Map tiles by [CARTO](https://carto.com/)
- Base map data © [OpenStreetMap](https://www.openstreetmap.org/) contributors
- Mapping library by [Leaflet](https://leafletjs.com/)

---

*"The important thing is not to stop questioning. Curiosity has its own reason for existence."*  
— Albert Einstein (1879–1955), Ulm → Delaware River
