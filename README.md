# 🎣 Z-Fish

**Sonar Field Guide — Powered by Z-Man®**

Z-Fish is a mobile-first fishing companion. Tell it *where* you’re fishing (and optionally *when*), and it tells you what species are likely biting, how to catch them, and which **Z-Man** lures to throw given the live conditions on the water.

### 🔗 Live demo → **[davidfliesen.github.io/z-fish](https://davidfliesen.github.io/z-fish)**

![Status](https://img.shields.io/badge/status-prototype%20v001-3ff0c4)
![Platform](https://img.shields.io/badge/platform-iOS%20%7C%20Android%20%7C%20Web-072730)
![License](https://img.shields.io/badge/license-MIT-ffc24b)
![AI](https://img.shields.io/badge/AI-open%20source-ff3b3b)

<p align="center">
  <a href="https://davidfliesen.github.io/z-fish">
    <img src="assets/z-fish-preview.jpeg" alt="Z-Fish app preview" width="360" />
  </a>
</p>

-----

## What it does

- **Enter a location** — a lake, river, bay, town, or raw `lat, lon`. One tap can also grab your GPS position.
- **Pick a water type** — Freshwater, Inshore Saltwater, or Nearshore.
- **(Optional) add a date & time** — Z-Fish pulls live weather and recalculates everything.
- **Get a scan report** — likely species ranked by how active they are, a short how-to-catch brief for each, and 2–3 matched **Z-Man** lure picks with suggested colors and a reason for the choice.
- **Watch the bite window** — falling pressure ahead of a front is flagged as prime; bluebird post-front high pressure is flagged as a tough, finesse bite; low-light windows get their due.
- **Stay current** — a *New From Z-Man* feed surfaces the latest products as they emerge.

It installs to the iOS/Android home screen as a PWA, so it behaves like a native app once added.

-----

## How it works

### Current prototype (`zfish_v001.html`)

A single self-contained HTML file — no build step, no server, no API keys.

|Layer                  |Implementation                                                                                |
|-----------------------|----------------------------------------------------------------------------------------------|
|Geocoding              |Open-Meteo Geocoding API *(free, no key)*                                                     |
|Weather                |Open-Meteo Forecast API — temp, wind, cloud, precipitation, surface pressure *(16-day window)*|
|Water-temp estimate    |Latitude + season + air-temp heuristic                                                        |
|Bite window            |Rules engine over time-of-day, cloud cover, wind, and pressure trend                          |
|Species & lure matching|On-device rules engine over a curated species dataset and the Z-Man catalog                   |
|Fish & lure art        |Inline SVG (works fully offline, never breaks)                                                |
|Install                |Web App Manifest + Apple touch meta tags (PWA)                                                |


> The prototype runs entirely on-device so it’s instant and key-free. The AI layer below replaces the hardcoded matching in the production build.

### Planned production architecture

The full system moves the intelligence to a lightweight backend using **free, open-source models** — the parts that genuinely need persistence:

1. **Vectorized Z-Man catalog** — the full product line embedded with `sentence-transformers/all-MiniLM-L6-v2` into a **FAISS** index, so conditions and species map to the best lures by *meaning* rather than fixed lists.
1. **New-product monitor** — a scheduled job that polls `zmanfishing.com/collections/2026-new-products`, diffs against the index, and auto-embeds anything new so the catalog stays current on its own.
1. **Natural-language reasoning** — **Llama-3.3-70B** (via Groq) writes the “why and how” for each recommendation instead of templates.
1. **Real species-by-coordinates data** — sourced from the free **iNaturalist** / **GBIF** occurrence APIs, replacing the curated seed dataset.

Backend stack: **FastAPI + ngrok** (Google Colab–friendly), **FAISS**, **sentence-transformers**, **Groq**.

-----

## Getting started

**Run it now:**

1. Download `zfish_v001.html`.
1. Open it in any modern mobile or desktop browser.
1. On a phone: use *Add to Home Screen* to install it as an app.

**Host it (GitHub Pages):**

1. Rename the latest version to `index.html` (or link to it directly).
1. Push to a repo and enable Pages.

No keys or accounts are required — the weather and geocoding services are free and open.

-----

## Roadmap

- [x] Mobile-first prototype with live weather + bite window (`v001`)
- [ ] FastAPI backend: FAISS-vectorized Z-Man catalog + semantic lure matching
- [ ] Automated new-product monitor with auto-embedding
- [ ] Llama-3.3-70B recommendation reasoning
- [ ] Live species data via iNaturalist / GBIF
- [ ] Real species photos
- [ ] True native apps (Expo / React Native) for the App Store & Google Play

-----

## Open-source & free by design

Z-Fish is built on services and models that are free to use and, where models are involved, open source:

- **Open-Meteo** — weather & geocoding (no API key)
- **sentence-transformers (all-MiniLM-L6-v2)** — embeddings *(planned)*
- **FAISS** — vector search *(planned)*
- **Llama 3.3** — open-weight LLM reasoning *(planned)*
- **iNaturalist / GBIF** — biodiversity data *(planned)*

-----

## Disclaimer

Z-Fish recommends **Z-Man** products simply because they’re the lures the developer personally prefers and fishes with — **not** because of any sponsorship or paid relationship. This is an independent, unofficial project and is **not affiliated with, endorsed by, or sponsored by Z-Man Fishing Products** in any way. *Z-Man®*, *ChatterBait®*, *ElaZtech®*, and all related product names are trademarks of their respective owner and are referenced here for identification and educational purposes only. Always check local regulations, licenses, and conditions before fishing.

-----

## License

Released under the **MIT License**. See `LICENSE` for details.

-----

## 👤 About the Developer

**David Fliesen** — *Hybrid Generative AI & Multimedia Developer* and founder of **Cibola Studios** (Summerville, SC).

A U.S. Navy Combat Camera veteran with 20+ years across photography, video, voice-over, and character animation, David has worked on DoD simulation and virtual-agent projects and completed the **Purdue / Simplilearn Applied Generative AI Specialization**. He builds practical, AI-powered applications through an iterative, prototype-first workflow — Z-Fish being a recent example of pairing free open-source AI with real-world utility.

### Connect

- 🌐 **Portfolio:** [davidfliesen.github.io](https://davidfliesen.github.io)
- 💼 **LinkedIn:** [linkedin.com/in/fliesen](https://www.linkedin.com/in/fliesen/)
- 🐙 **GitHub:** [github.com/DavidFliesen](https://github.com/DavidFliesen)

-----

<p align="center"><sub>Built with 🎣 by David Fliesen · Cibola Studios · Tight lines.</sub></p>