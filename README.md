# EnergySaver

**Offline AI energy coach that cuts home electricity bills.**

EnergySaver turns an Android phone and a few low-cost smart plugs into a private energy advisor. It shows which appliance is driving your bill, forecasts the month-end total against your real tariff slabs, catches standby waste, and explains what to do in plain English, Hindi or Telugu. Everything runs on the phone: no cloud account, no subscription, and readings never leave the device.

> **Hackathon:** iQOO Grand Finale Hackathon · **Track:** Smart Living
> **Team:** [TEAM NAME] · [Member names]

---

## The problem

- Households see their bill once a month, with no idea which appliance caused it.
- Slab-based tariffs mean a small, unnoticed rise in usage can push a home into a costlier slab.
- Standby loads (set-top boxes, soundbars, chargers) quietly add to the bill every month.
- Existing energy apps stop at graphs, depend on the cloud, and leave users to decide what to change.

## The solution

| Step | What happens |
|---|---|
| **See** | Metered smart plugs report live watts per appliance over the home Wi-Fi. |
| **Understand** | Statistical models learn each appliance's normal load and forecast the bill. A small on-device language model turns the verified numbers into advice. |
| **Act** | One tap cuts power to an idle device, schedules the geyser, or sets an off-peak reminder. A "savings so far" counter shows the impact. |

## Key features

- Live per-appliance power monitoring
- Tariff-aware bill forecast with an alert before you cross into a costlier slab
- Standby and anomaly detection (for example, an AC drawing more than its usual load)
- Multilingual coach (English, Hindi, Telugu) by voice and text, generated on the phone
- Remote control and scheduling of plugs
- Fully offline and private

## Architecture

```
Appliance → Smart plug (energy metering) → Home Wi-Fi (HTTP/MQTT) → Android app
                                                                      ├─ Usage log (SQLite/Room)
                                                                      ├─ Signature matcher (names each appliance)
                                                                      ├─ Statistical models (baselines, anomalies, forecast)
                                                                      ├─ Language model (writes the advice)
                                                                      └─ Control layer (on/off, schedules)
```

**Design principle: the language model never does the maths.** Costs, baselines and forecasts come from the statistical models. The language model only phrases those verified numbers, so the advice stays accurate.

## Tech stack

| Layer | Choice |
|---|---|
| App | Kotlin (Android) or Flutter |
| Storage | SQLite / Room |
| Device link | HTTP / MQTT to Tasmota- or Shelly-type metering plugs |
| Analytics | Rolling baselines, z-score anomaly detection, time-of-day profiles; TFLite optional |
| On-device LLM | 1B-2B quantized model via MediaPipe LLM Inference or llama.cpp |

## This repository: the project website

`EnergySaver.html` is a single, self-contained web page that presents the project and includes an interactive simulated demo. It has no build step and no dependencies other than Google Fonts.

### What is on the page

| Section | What it shows |
|---|---|
| **Hero: Live home** | Simulated live readings for four appliances (bedroom AC, refrigerator, set-top box, TV). Tap a plug switch to cut its power and watch the savings counter and month forecast update. |
| **Slabs** | Slab calculator showing the bill at a given usage, the cost of the next unit, units left in the slab, and the saving from a 10% cut. |
| **How it works** | The four-step flow from plug to plain-language tip. |
| **Power signatures** | Schematic load shapes (fridge cycles, geyser block) used to name appliances. |
| **AI coach** | Sample conversation in English, Hindi and Telugu, with tappable action chips. |
| **Standby** | Calculator for what always-on devices cost per month and how much you save by switching them off. |
| **Privacy** | Diagram of the on-device pipeline and what is never sent anywhere. |
| **Impact** | Sliders to size savings for an apartment community (homes, units per home, reduction). |
| **Roadmap and FAQ** | Solar and inverter data, society dashboards, EV charging, DISCOM tariff data; common questions. |
| **Request a pilot** | Contact call to action. |

### Run it

No installation needed.

```bash
# Option 1: open the file directly
open EnergySaver.html            # macOS
xdg-open EnergySaver.html        # Linux
start EnergySaver.html           # Windows

# Option 2: serve it locally
python3 -m http.server 8000      # then visit http://localhost:8000/EnergySaver.html
```

An internet connection is only needed to load the web fonts. Without it, the page falls back to system fonts.

### Notes for editing

- **Tariff slabs** are defined in the `SLABS` array in the page's script (0-100 units at ₹3, next 100 at ₹4.50, next 100 at ₹6.50, then ₹8). Each rate applies only to the units inside its slab.
- **Demo appliances** are defined in the `DEV` array (id, name, baseline watts, hours per day).
- **Theme:** light and dark colour schemes follow the system setting through CSS variables on `:root`.
- **Contact link:** the "Request a pilot" button uses a placeholder address, `hello@EnergySaver.example`. Replace it with your real address before sharing.
- Fonts: Bricolage Grotesque (headings), Figtree (body), Noto Sans Devanagari and Noto Sans Telugu (Hindi and Telugu text).

## Important: what is real and what is simulated

- The numbers on the website (readings, slab rates, savings, CO₂) are **simulated or illustrative**. In the app, users enter their own tariff.
- The impact calculator assumes about ₹6 per unit saved and roughly 0.7 kg of CO₂ per unit, an approximate figure for the Indian grid. These are estimates, not guarantees.
- For the hackathon demo, live plug readings are real while the 30-day history used for forecasts and anomalies is simulated.

## FAQ

**Which smart plugs work?** Wi-Fi plugs with built-in energy metering and local access, such as plugs with a local API or running open firmware. Cloud-only plugs with no local access are not a fit.

**Does it need the internet?** No. Monitoring, forecasting, anomaly detection and the coach all run on the phone.

**Which appliances should I start with?** The biggest or most forgotten loads: the AC, the geyser and the set-top box.

## Safety

Use certified, enclosed smart plugs. If you build your own metering hardware (for example, ESP32 with a PZEM-004T), use a proper enclosure and never leave mains wiring exposed.

## Roadmap

- Solar and inverter data in the same forecast
- Apartment-society dashboards (shared, opt-in)
- EV charging optimization to avoid costly slab crossings
- DISCOM tariff and billing data, so slabs need not be typed in by hand

