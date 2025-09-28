# Architecture (ASCII)

Browser (GitHub Pages, HTTPS)
    │  GET /?lat=...&lon=... [&ahead=&threshold=]
    ▼
AWS Lambda (Function URL)
    • Parse query params (GPS, knobs)
    • Check DynamoDB cache (fresh < 30 min?)
        ├─ Yes → return cached JSON
        └─ No  → fetch Open-Meteo → compute advice → cache → return JSON
    ▼
DynamoDB (commute_summary, TTL)
    • pk = "geo@lat,lon#YYYY-MM-DD" or "CITY#YYYY-MM-DD"
    • sk = "HH:MM" (latest first)

Notes:
- On-demand only (no schedules).
- CORS handled in Lambda response.
- TTL auto-cleans old rows.
- Cost: effectively ₱0 at personal scale (always-free).
