# City Weather API

A simple Express API that looks up current weather and a 3-day forecast using Open-Meteo. It can compare cities, handle failures, and cache successful answers.

## Run

Use Node.js 24.

```bash
npm install
cp .env.example .env
npm run dev
```

On Windows Command Prompt, use `copy .env.example .env`. To run without `.env`, use `npm start`.

## Environment variables

| Variable | Default | Purpose |
|---|---|---|
| PORT | 4000 | Server port |
| GEOCODING_URL | https://geocoding-api.open-meteo.com/v1 | Geocoding API base URL |
| FORECAST_URL | https://api.open-meteo.com/v1 | Forecast API base URL |
| UPSTREAM_TIMEOUT_MS | 5000 | Timeout for each upstream request in ms |
| CACHE_TTL_SECONDS | 600 | Cache lifetime in seconds |

## Endpoints

- `GET /api/weather?city=Toronto` — current weather and three forecast days.
- `GET /api/compare?cities=Toronto,Vancouver,Halifax` — compares 2 to 5 cities, including failures.
- `GET /api/cache/stats` — counts active cache entries, hits, and misses.

## Testing

Run these commands in order on a freshly started server. On Windows, use Git Bash.

| Command | Expected HTTP status |
|---|---|
| `curl -i 'http://localhost:4000/api/weather?city=Toronto'` | 200 |
| `curl -i 'http://localhost:4000/api/weather?city=toronto'` | 200 (cached) |
| `curl -i 'http://localhost:4000/api/weather'` | 400 |
| `curl -i 'http://localhost:4000/api/weather?city=Qwertyville'` | 404 |
| `curl -i 'http://localhost:4000/api/compare?cities=Toronto,Vancouver,Halifax'` | 200 |
| `curl -i 'http://localhost:4000/api/compare?cities=Toronto,Halifax,Qwertyville'` | 200 |
| `curl -i 'http://localhost:4000/api/compare?cities=Toronto,Vancouver,Halifax,Ottawa,Calgary,Montreal'` | 400 |
| `curl -i 'http://localhost:4000/api/compare?cities=Toronto,toronto'` | 400 |
| `curl -i 'http://localhost:4000/api/cache/stats'` | 200 |
| `curl -i 'http://localhost:4000/not-a-route'` | 404 |

To test upstream failures, temporarily set `UPSTREAM_TIMEOUT_MS=1` (504), `FORECAST_URL=https://weather.invalid/v1` (502), or `CACHE_TTL_SECONDS=5` to check expiration. Restore defaults after testing.

## Credit

Weather data provided by [Open-Meteo](https://open-meteo.com/).
