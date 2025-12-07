# 🌦️ Weather Jams: AI-Powered Atmospheric DJ

**Weather Jams** is an autonomous AI agent that curates Spotify playlists based on real-time weather conditions. Unlike simple "if/else" bots, it uses **Google Gemini (LLM)** to semantically interpret meteorological data and generate context-aware search queries.

![Project Screenshot](assets/demo_dashboard.png)

## 🧠 The Architecture
The system operates on a polling architecture:
1.  **Sensor:** Fetches live weather metrics (Temp, Condition, Humidity) via OpenWeatherMap.
2.  **Reasoning Engine:** Google Gemini 1.5 analyzes the data to determine the "Vibe" (e.g., "Rain at 30°C" = *Tropical Indie*, "Rain at 10°C" = *Cozy Jazz*).
3.  **Action:** Queries the Spotify Search API to fetch tracks matching the semantic description.
4.  **Presentation:** Formats the metadata into **Notion Blocks** to build an interactive UI.

## 🛠️ Technical Stack
* **Orchestration:** n8n (Node-based automation)
* **AI Model:** Google Gemini 1.5 Flash (Prompt Engineering)
* **APIs:** Spotify Web API, OpenWeatherMap REST API
* **Database/Frontend:** Notion

## 💡 Engineering Challenge: The "API Pivot"
During development, I discovered that Spotify deprecated their `Recommendations` endpoint (Valence/Energy features) in late 2024.
* **Problem:** I could no longer request songs by "Mood Numbers" (e.g., Valence: 0.2).
* **Solution:** I re-engineered the logic to use **Semantic Search**. I prompted the LLM to translate numerical weather data into descriptive search strings (e.g., "melancholic acoustic guitar"), effectively bypassing the limitation while retaining high relevance.

## 🚀 How to Run
1.  Import `workflows/weather_jams_n8n_v2.json` into n8n.
2.  Set up Spotify OAuth2 credentials.
3.  Create a Notion Database with a "Gallery" view.