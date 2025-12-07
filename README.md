# 🌦️ Weather Jams: AI-Powered Atmospheric DJ

**Weather Jams** is an autonomous AI agent that curates Spotify playlists based on real-time weather conditions. Unlike simple "if/else" bots, it uses **Google Gemini (LLM)** to semantically interpret live weather data and translate it into musical “vibes” (e.g., *“moody piano for cold rainy evenings”*).

<div align="center">
  <img src="assets/demo_dashboard.png" alt="Weather Jams Dashboard" width="800">
  <br />
  <sub><em>Weather Jams dashboard: weather → vibe → curated Spotify tracks in Notion</em></sub>
</div>

---

## 📁 Project Structure

```bash
weather-jams-agent/
├── assets/
│   └── demo_dashboard.png        # Screenshot used in this README
├── workflows/
│   └── weather_jams.json         # Main n8n workflow definition
├── .gitignore
├── README.md                     # You are here
└── LICENSE                       # Project license (if present)
```

**Key pieces:**

- `workflows/weather_jams.json` – n8n workflow that:
  - Polls weather data
  - Calls Gemini for reasoning
  - Calls Spotify Search API
  - Writes results into Notion
- `assets/demo_dashboard.png` – UI preview for the dashboard
- `README.md` – Documentation & setup guide

---

## 🧠 The Architecture

The system operates on a polling architecture:

1. **Sensor:**  
   Fetches live weather metrics (Temperature, Condition, Humidity) via **OpenWeatherMap**.

2. **Reasoning Engine:**  
   **Google Gemini 1.5** analyzes the data to determine the *Vibe*  
   - Example:  
     - *Rain at 30°C* → **Tropical Indie**  
     - *Rain at 10°C* → **Cozy Jazz**

3. **Action:**  
   Queries the **Spotify Search API** to fetch tracks that match the semantic description (e.g., “melancholic acoustic guitar”, “sunny upbeat house”).

4. **Presentation:**  
   Formats the metadata into **Notion Blocks** to build an interactive UI (track cards, covers, artist, mood notes, etc.).

---

## 🛠️ Technical Stack

- **Orchestration:** n8n (Node-based automation)
- **AI Model:** Google Gemini 1.5 Flash (prompt-engineered for “vibe” generation)
- **APIs:** Spotify Web API, OpenWeatherMap REST API
- **Database / Frontend:** Notion (Gallery View as a lightweight UI)

---

## 💡 Engineering Challenge: The "API Pivot"

During development, I discovered that Spotify deprecated their `Recommendations` endpoint (Valence/Energy features) in late 2024.

- **Problem:**  
  Could no longer request songs by “Mood Numbers” (e.g., `valence: 0.2`, `energy: 0.8`).

- **Solution:**  
  Re-engineered the logic to use **semantic search** instead of hard numerical mood features:
  - Prompt Gemini to translate raw weather data into rich textual descriptions like:
    - “melancholic acoustic guitar for cold rainy nights”
    - “uplifting indie pop for warm sunny afternoons”
  - Use these descriptions as **Spotify search queries**, effectively performing semantic playlist curation.

Result: The system became **more flexible and expressive** than the original numeric approach.

---

## 🚀 How to Run

1. **Import the workflow into n8n**
   - Open your n8n instance.
   - Go to **Workflows → Import from File**.
   - Select `workflows/weather_jams.json`.

2. **Set up Spotify OAuth2 credentials**
   - Create a Spotify app on the [Spotify Developer Dashboard](https://developer.spotify.com/dashboard).
   - Configure OAuth2 credentials in n8n (Client ID, Client Secret, Redirect URI).
   - Connect the credentials to the Spotify nodes in the workflow.

3. **Configure OpenWeatherMap**
   - Create an API key at [OpenWeatherMap](https://openweathermap.org/api).
   - Add the API key as an n8n credential or environment variable.
   - Update the HTTP Request node for weather with your key and location.

4. **Create a Notion Database**
   - In Notion, create a new database with a **Gallery** view.
   - Add properties for fields like:
     - `Title` (track name)
     - `Artist`
     - `Album Cover` (file / URL)
     - `Vibe Description`
     - `Weather` (e.g., “Light Rain, 12°C”)
   - Add the Notion integration and link the database ID in the workflow.

5. **Run the Agent**
   - Set a **Cron** or **Schedule** node in n8n to poll every X minutes.
   - Deploy or keep n8n running.
   - Watch new curated tracks appear in the Notion gallery based on the current weather.

---

## 📌 Future Ideas

- Add **location-based personalization** (per-city vibes).
- Add **user preferences** (e.g., “no metal”, “more electronic”).
- Multi-model comparison (Gemini vs. Claude vs. GPT for vibe generation).
