# Trackside Bengaluru

**Namma Metro in 3D.** An isometric diorama of Bengaluru's metro, with every train running on the timetable: elevated viaducts, trains glowing through the ground in the tunnels, Vidhana Soudha and Chinnaswamy Stadium, monsoon storms and pink trumpet trees. Follow your train, or leave it running as a day-and-night screensaver.

![Trackside Bengaluru: the whole Namma Metro network at morning peak](screenshots/network.jpg)

**[Open the live map →](https://YOUR-USERNAME.github.io/Trackside_Blr/)**

It's one HTML file, plain JavaScript and [three.js](https://threejs.org/), with no build step or server.

---

## What you can do

- **Find your train.** Pick any of the Purple, Green or Yellow line trains and the camera follows it. The panel shows the next stop, arrival time and speed, and where the train is right now: "Passing Brigade Road" or "Underground, beneath KSR Bengaluru City Junction".
- **Watch it Live.** Live mode matches the real time in Bengaluru (IST) and stretches the map 50× so trains visibly glide along.
- **See the whole network.** The Network button zooms out to all 83 stations, with every running train as a coloured dot.
- **Leave it on.** Screensaver mode hides the controls, keeps the screen awake and tours the city from train to train. The sky follows Bengaluru's real sunrise and sunset.
- **Change the season.** Auto follows the date, or you can pick one:
  - **Winter:** pink trumpet trees in bloom.
  - **Summer:** purple jacaranda, red gulmohar and yellow copper pod, with dry red-earth ground.
  - **Monsoon:** heavy rain and lightning.
  - **Post-monsoon:** clear and green.

| Following a train down MG Road | Monsoon screensaver | On a phone |
| --- | --- | --- |
| ![Purple Line viaduct along MG Road](screenshots/city.jpg) | ![Heavy monsoon rain over Majestic](screenshots/monsoon.jpg) | ![Phone layout following a Green Line train underground](screenshots/phone.jpg) |

## Features

- **Three lines, 83 stations**, on real OpenStreetMap track geometry.
- **Viaducts on piers, and tunnels** under Cubbon Park, Vidhana Soudha, Sir M. Visvesvaraya, Majestic, KSR Railway Station and Chickpete. Trains in a tunnel show through the ground, so you never lose them.
- **A typical week, repeated:** Monday, Tuesday–Saturday and Sunday timetables, 2,444 trips in all.
- **Landmarks:** Vidhana Soudha (lit at night), Bangalore Palace, Lalbagh Glass House, UB City, Chinnaswamy Stadium, World Trade Center, ISKCON Temple, the Infosys pyramid and Bull Temple.
- **About 130 place labels:** neighbourhoods, landmarks and lakes. Edge markers point to nearby places just off-screen, with the distance.
- **Terrain:** the Bengaluru plateau ridge, the Hebbal, Koramangala–Challaghatta and Vrishabhavathi valleys, and 25 lakes.
- **Garden City trees:** rain trees, coconut palms and flowering trees that change with the season.
- **Line filter, tap a train to follow it, map stretch 1–50×.**
- **Works on phones:** a bottom sheet with one row of controls.

## Run it

Open `index.html` in a modern browser. Everything is inside the file except three.js and Google Fonts, which load from a CDN, so you need to be online.

### Publish on GitHub Pages

1. Push this folder to a GitHub repository, for example `Trackside_Blr`.
2. Go to **Settings → Pages**, set **Source** to *Deploy from a branch*, and pick `main` and `/ (root)`.
3. After a minute the map is live at `https://YOUR-USERNAME.github.io/Trackside_Blr/`. Update the link at the top of this README.

## How it works

```
BMRCL timetable images ──► Vonter/bmrcl-gtfs (unofficial GTFS + OpenStreetMap geometry)
                                   │
                    pack_blr.py ───┤──► network.json   lines, stations, track shapes,
                                   │                   19 stopping patterns, a typical week
             terrain_blr.py ───────┴──► terrain.json   heightfield + lake outlines
                                   │
                  build_blr.py ────┴──► index.html     page_blr.html with both JSON files inlined
```

In the browser, the page:
1. Unpacks the terrain (deflate plus `DecompressionStream`).
2. Densifies every track shape and works out where each train is from the timetable, easing in and out of stations.
3. Places the trains with instanced meshes, so 60+ six-car trains cost a handful of draw calls.
4. Flags each stretch of track as elevated or underground: a portal sits halfway between an underground station and an elevated one.

### Rebuild the data

```bash
curl -L -o bmrcl.zip https://raw.githubusercontent.com/Vonter/bmrcl-gtfs/main/gtfs/bmrcl.zip
mkdir gtfs && unzip bmrcl.zip -d gtfs
python3 pack_blr.py gtfs network.json        # needs Python 3
python3 terrain_blr.py network.json terrain.json   # needs numpy, scipy, matplotlib
python3 build_blr.py                         # writes index.html (and Trackside_Blr.html)
```

## Files

| File | What it is |
| --- | --- |
| `index.html` | The finished page, ready for GitHub Pages |
| `page_blr.html` | Page source with `__NET__` and `__TER__` placeholders. Edit this one. |
| `network.json` | Packed network and timetable |
| `terrain.json` | Packed heightfield and lakes |
| `pack_blr.py`, `terrain_blr.py`, `build_blr.py` | Data pipeline |
| `Trackside_Blr.html` | Same page without a document wrapper (used for the Claude artifact) |
| `Trackside-Bengaluru-Quick-Guide.pdf` | One-page annotated guide |
| `screenshots/` | Images for this README |

## Good to know

- **Scheduled, not live.** BMRCL doesn't offer a public real-time feed, so trains run on the timetable.
- **Stop times are modelled.** BMRCL publishes headways and first and last trains, not station-level times, so the GTFS estimates them. Expect a minute or two of difference, and some short-loop trains may be missing.
- **The week is a typical one.** BMRCL runs a holiday timetable on the 2nd and 4th Saturdays; this uses the regular Saturday service every week.
- **Approximate geography.** Tunnel portals, terrain and lake outlines are approximate, landmarks are simplified models, and generic buildings and trees are decoration.
- **Pink Line not included yet.** It isn't in the feed. It'll be added once it opens.

## Credits and licences

- **Timetable:** [Vonter/bmrcl-gtfs](https://github.com/Vonter/bmrcl-gtfs), the unofficial BMRCL GTFS built from [BMRCL's published timetables](https://www.bmrc.co.in/metro-timings/). It's ODbL.
- **Track and stations:** © [OpenStreetMap contributors](https://www.openstreetmap.org/copyright), ODbL.
- **3D:** [three.js](https://threejs.org/) (MIT).
- **Fonts:** Barlow Condensed, Public Sans and JetBrains Mono (SIL Open Font License).

The code is MIT-licensed (see [LICENSE](LICENSE)). `network.json`, and the copy embedded in the HTML, is derived from ODbL data and is shared under the [ODbL 1.0](https://opendatacommons.org/licenses/odbl/1-0/).

This project isn't affiliated with or endorsed by Bengaluru Metro Rail Corporation Limited (BMRCL).
