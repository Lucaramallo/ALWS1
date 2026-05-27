# NEXUS-7 TECHNICAL ANALYSIS — Round 1

## Problem Statement
Implement a zero-dependency weather dashboard requiring three decoupled API integrations: (1) geocoding to convert city names → lat/lon with O(1) lookup latency, (2) current weather fetch with sub-100ms P95 response, and (3) 5-day forecast parsing into grid-renderable structs—all with graceful degradation for network failures, rate-limit resilience (Open-Meteo: 10,000 req/day free tier), and idempotent search operations to prevent duplicate API calls.

## Solution Architecture

**Geocoding Layer:** Use Open-Meteo's `/v1/geocoding` endpoint with fuzzy-match tolerance (e.g., "New York" → multiple candidates ranked by population). Cache results in localStorage to reduce API pressure and improve repeat-search latency from 200ms → <5ms. Implement debouncing (300ms) on search input to batch rapid keystrokes into single request.

**Weather Data Fetching:** Chain geocoding → current conditions + 5-day forecast via `/v1/forecast` with parameters `current=temperature_2m,relative_humidity_2m,weather_code,wind_speed_10m` and `daily=weather_code,temperature_2m_max,temperature_2m_min,precipitation_sum`. Decode WMO weather codes (01 = clear, 61 = rain, etc.) server-side in JavaScript to avoid additional round-trips. Implement retry logic with exponential backoff (1s → 2s → 4s max) for transient 5xx errors; hard-fail on 400s.

**Error Management:** Segregate error types—invalid city (empty results) → UI toast + retry prompt; network timeout → offline fallback (last cached forecast for 24h); malformed response → console.warn + graceful null-coalesce in render. Use AbortController to cancel in-flight requests on rapid re-searches, preventing race conditions where slow requests overwrite newer results.

## Implementation Checklist

- [ ] **Geocoding function:** `fetchCityCoordinates(cityName)` → `Promise<{lat, lon, name, country}>` with debounce wrapper
- [ ] **Weather function:** `fetchWeatherData(lat, lon)` → `Promise<{current, forecast: Array<DailyForecast>}>` with retry + AbortController
- [ ] **WMO decoder:** Static lookup table `WMO_CODES` mapping code → {icon, description, severity}
- [ ] **Cache layer:** `localStorage` with TTL metadata (`{data, timestamp}`) for 6-hour expiry
- [ ] **Event binding:** Search input → debounced geocoding → weather fetch → render pipeline with loading state
- [ ] **Error UI:** Toast notifications (max 3s visible) for user-facing errors; console logging for dev debugging

**Critical Non-Functional Requirement:** Ensure all API responses parse without external libraries; use native `fetch()` + `JSON.parse()` only. Test with throttled network (2G via DevTools) to validate UX during slow connections.

---

**My position:** This is straightforward if we enforce strict separation—data layer (pure functions, no DOM coupling) must be testable in isolation before wiring to UI. The geocoding cache is load-bearing; without it, repeated searches hammer the API and degrade P50 latency from acceptable to awful. I'm ready to code the implementation.